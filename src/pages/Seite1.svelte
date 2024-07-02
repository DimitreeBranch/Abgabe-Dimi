<script>
  import { onMount } from 'svelte';
  import { push } from 'svelte-spa-router';

  let planets = [];
  let error = null;
  let maxDistance = 0;
  let maxRadius = 0;

  onMount(async () => {
    try {
      const response = await fetch('https://api.le-systeme-solaire.net/rest/bodies/');
      if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
      const data = await response.json();
      planets = data.bodies.filter(body => body.isPlanet).sort((a, b) => a.semimajorAxis - b.semimajorAxis);
      maxDistance = Math.max(...planets.map(planet => planet.semimajorAxis));
      maxRadius = Math.max(...planets.map(planet => planet.meanRadius));
    } catch (err) {
      error = err.message;
      console.error('Fetch error: ', err);
    }
  });

  function calculatePosition(semimajorAxis) {
    return `${(semimajorAxis / maxDistance) * 85}%`;
  }

  function calculateSize(meanRadius) {
    const minSize = 50;
    const maxSize = 300;
    const scaledSize = (meanRadius / maxRadius) * maxSize;
    return `${Math.max(minSize, Math.min(maxSize, scaledSize))}px`;
  }

  function navigateToPlanet(id) {
    push(`/planet/${id}`);
  }
</script>

<div class="outer-container">
  {#if error}
    <p class="error">Error loading planets: {error}</p>
  {:else}
    <div class="space">
      {#each planets as planet}
        <div class="planet-item {planet.englishName.toLowerCase()}" on:click={() => navigateToPlanet(planet.id)} style="left: {calculatePosition(planet.semimajorAxis)};">
          <img src={`/planetenbilder/${planet.id}.png`} alt="{planet.englishName}" class="planet-img" style="width: {calculateSize(planet.meanRadius)}; height: {calculateSize(planet.meanRadius)};" />
          <p class="planet-name">{planet.englishName}</p>
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  body, html {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    color: #fff;
    overflow-x: hidden; /* Prevent horizontal scrolling */
  }

  .outer-container {
    width: 80vw;
    height: 70vh;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    
    align-items: flex-start; /* Start from the left */
    overflow: hidden; /* Prevent horizontal scrolling */
  }

  .outer-container h1 {
    text-align: center;
    margin: 0 0 20px;
    width: 100%; /* Make the heading take the full width */
  }

  .error {
    color: red;
    text-align: center;
    width: 100%; /* Make the error message take the full width */
  }

  .space {
    display: flex;
    align-items: center;
    justify-content: flex-start; /* Align content to the start */
    width: 100%; /* Use full width */
    position: relative;
    height: 100%;
    background: transparent; /* Transparent background to see the background image */
    overflow: hidden; /* Prevent horizontal scrolling */
    overflow: hidden; /* Prevent horizontal scrolling */
  }

  .planet-item {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    text-align: center;
    color: white;
    cursor: pointer;
    transition: transform 0.3s, top 0.3s; /* Smooth transform on hover */
  }

  .planet-item img {
    object-fit: cover;
    transition: transform 0.3s; /* Smooth transform on hover */
  }

  .planet-item p {
    margin: 10px 0 0;
    opacity: 0; /* Hide the planet name by default */
    transition: opacity 0.3s; /* Smooth transition for opacity */
    position: relative;
    z-index: -1; /* Hide the planet name by default */
  }

  .planet-item:hover p {
    opacity: 1; /* Show the planet name on hover */
    z-index: 10; /* Ensure the planet name is above others on hover */
  }

  .planet-item:hover {
    z-index: 10; /* Ensure the hovered item is above others */
  }

  .planet-item img:hover {
    transform: scale(1.2); /* Enlarge the planet image on hover */
  }

  .planet-item img[data-size="large"]:hover {
    transform: scale(1.1); /* Less enlargement for larger planets */
  }

  /* Specific hover styles for inner planets (Mercury, Venus, Earth, Mars, Uranus, Neptune) */
  .planet-item.mercury:hover,
  .planet-item.venus:hover,
  .planet-item.earth:hover,
  .planet-item.mars:hover,
  .planet-item.uranus:hover,
  .planet-item.neptune:hover {
    top: 55%;
  }

  /* Specific hover styles for Jupiter and Saturn */
  .planet-item.jupiter:hover,
  .planet-item.saturn:hover {
    top: 60%;
  }
</style>
