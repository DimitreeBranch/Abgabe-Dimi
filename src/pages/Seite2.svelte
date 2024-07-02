<script>
  import { onMount } from 'svelte';

  let planets = [];
  let error = null;
  let selectedPlanet1 = null;
  let selectedPlanet2 = null;
  let maxRadius = 0;

  onMount(async () => {
    try {
      const response = await fetch('https://api.le-systeme-solaire.net/rest/bodies/');
      if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
      const data = await response.json();
      planets = data.bodies.filter(body => body.isPlanet).sort((a, b) => a.semimajorAxis - b.semimajorAxis);
      maxRadius = Math.max(...planets.map(planet => planet.meanRadius));

      // Set default selected planets
      selectedPlanet1 = planets.find(planet => planet.englishName === 'Earth');
      selectedPlanet2 = planets.find(planet => planet.englishName === 'Venus');
    } catch (err) {
      error = err.message;
      console.error('Fetch error: ', err);
    }
  });

  function calculateSize(meanRadius) {
    const minSize = 50;
    const maxSize = 300;
    const scaledSize = (meanRadius / maxRadius) * maxSize;
    return `${Math.max(minSize, Math.min(maxSize, scaledSize))}px`;
  }

  function selectPlanet1(planet) {
    selectedPlanet1 = planet;
  }

  function selectPlanet2(planet) {
    selectedPlanet2 = planet;
  }
</script>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    color: #fff;
  }

  .container {
    display: grid;
    grid-template-rows: auto 1fr auto;
    grid-template-columns: 1fr;
    height: 100vh;
    box-sizing: border-box;
    overflow: hidden; /* Prevent horizontal scrolling */
    gap: 20px;
  }

  .comparison {
    display: grid;
    grid-template-columns: 300px 1fr 300px;
    align-items: center;
    padding: 20px;
    box-sizing: border-box;
    overflow: hidden; /* Prevent horizontal scrolling */
    color: #fff;
    gap: 20px;
  }

  .comparison img {
    align-items: center;
    width: 100%;
    height: auto;
    max-width: 300px;
    max-height: 300px;
    justify-self: center;
  }

  .comparison-table-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
  }

  .comparison-table {
    width: 100%;
    max-width: 800px;
    border-collapse: collapse;
  }

  .comparison-table th, .comparison-table td {
    padding: 10px;
    border: 1px solid #444;
    text-align: center;
  }

  .comparison-table th {
    background-color: #333;
  }

  .comparison-table tr:nth-child(even) {
    background: transparent;
  }

  .planet-lists-container {
    display: grid;
    grid-template-columns: 1fr 1fr;
    justify-content: space-between;
    gap: 10px;
    overflow-x: auto;
    padding: 10px;
  }

  .planet-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, 50px);
    gap: 10px;
    list-style: none;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    justify-content: center;
  }

  .planet-list li {
    cursor: pointer;
    background: transparent;
    text-align: center;
    transition: background 0.3s, transform 0.3s;
    color: #fff;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .planet-list li:hover img,
  .planet-list li.selected img {
    filter: none; /* Show color on hover and selected */
  }

  .planet-list img {
    width: 50px;
    height: 50px;
    filter: grayscale(100%); /* Default to black and white */
    transition: filter 0.3s; /* Smooth transition for filter */
  }

  .planet-list p {
    font-size: 12px;
    margin-top: 5px;
  }

  .error {
    color: red;
    text-align: center;
  }
</style>

{#if error}
  <p class="error">Error loading planets: {error}</p>
{:else}
  <div class="container">
    <div class="comparison">
      <img src={selectedPlanet1 ? `/planetenbilder/${selectedPlanet1.id}.png` : ''} alt="{selectedPlanet1 ? selectedPlanet1.englishName : ''}" style="width: {selectedPlanet1 ? calculateSize(selectedPlanet1.meanRadius) : '300px'}; height: {selectedPlanet1 ? calculateSize(selectedPlanet1.meanRadius) : '300px'};" />
      <div class="comparison-table-container">
        <table class="comparison-table">
          <thead>
            <tr>
              <th>Attributes</th>
              <th>{selectedPlanet1 ? selectedPlanet1.englishName : 'Planet 1'}</th>
              <th>{selectedPlanet2 ? selectedPlanet2.englishName : 'Planet 2'}</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>Mean Radius (km)</td>
              <td>{selectedPlanet1 ? selectedPlanet1.meanRadius : ''}</td>
              <td>{selectedPlanet2 ? selectedPlanet2.meanRadius : ''}</td>
            </tr>
            <tr>
              <td>Semimajor Axis (km)</td>
              <td>{selectedPlanet1 ? selectedPlanet1.semimajorAxis : ''}</td>
              <td>{selectedPlanet2 ? selectedPlanet2.semimajorAxis : ''}</td>
            </tr>
            <tr>
              <td>Gravity (m/s²)</td>
              <td>{selectedPlanet1 ? selectedPlanet1.gravity : ''}</td>
              <td>{selectedPlanet2 ? selectedPlanet2.gravity : ''}</td>
            </tr>
            <tr>
              <td>Orbital Period (days)</td>
              <td>{selectedPlanet1 ? selectedPlanet1.sideralOrbit : ''}</td>
              <td>{selectedPlanet2 ? selectedPlanet2.sideralOrbit : ''}</td>
            </tr>
            <tr>
              <td>Density (g/cm³)</td>
              <td>{selectedPlanet1 ? selectedPlanet1.density : ''}</td>
              <td>{selectedPlanet2 ? selectedPlanet2.density : ''}</td>
            </tr>
            <tr>
              <td>Number of Moons</td>
              <td>{selectedPlanet1 ? (selectedPlanet1.moons ? selectedPlanet1.moons.length : 0) : ''}</td>
              <td>{selectedPlanet2 ? (selectedPlanet2.moons ? selectedPlanet2.moons.length : 0) : ''}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <img src={selectedPlanet2 ? `/planetenbilder/${selectedPlanet2.id}.png` : ''} alt="{selectedPlanet2 ? selectedPlanet2.englishName : ''}" style="width: {selectedPlanet2 ? calculateSize(selectedPlanet2.meanRadius) : '300px'}; height: {selectedPlanet2 ? calculateSize(selectedPlanet2.meanRadius) : '300px'};" />
    </div>
    
    <div class="planet-lists-container">
      <ul class="planet-list">
        {#each planets as planet}
          <li class:selected={selectedPlanet1 === planet} on:click={() => selectPlanet1(planet)}>
            <img src={`/planetenbilder/${planet.id}.png`} alt="{planet.englishName}" />
            <p>{planet.englishName}</p>
          </li>
        {/each}
      </ul>
      
      <ul class="planet-list">
        {#each planets as planet}
          <li class:selected={selectedPlanet2 === planet} on:click={() => selectPlanet2(planet)}>
            <img src={`/planetenbilder/${planet.id}.png`} alt="{planet.englishName}" />
            <p>{planet.englishName}</p>
          </li>
        {/each}
      </ul>
    </div>
  </div>
{/if}
