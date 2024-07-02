<script>
  import { onMount } from 'svelte';

  let planet = null;
  let error = null;

  onMount(() => {
    const url = new URL(window.location.href);
    const id = url.hash.split('/planet/')[1];
    if (id) {
      fetchPlanet(id);
    } else {
      error = "Planet ID not found in URL";
    }
  });

  async function fetchPlanet(id) {
    try {
      const response = await fetch(`https://api.le-systeme-solaire.net/rest/bodies/${id}`);
      if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
      planet = await response.json();
    } catch (err) {
      error = err.message;
      console.error('Fetch error: ', err);
    }
  }
</script>

<style>
  .planet-container {
    display: flex;
    align-items: center;
    color: white;
    padding: 20px;
    box-sizing: border-box;
    background-color: transparent;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    width: 90%; /* Take 90% of the viewport width */
    margin: auto; /* Center the container horizontally */
  }

  .planet-image {
    width: 300px;
    height: 300px;
    object-fit: cover;
    margin-right: 20px; /* Add space between image and details */
    box-shadow: 1px rgba(0, 0, 0, 0.2);
  }

  .planet-details {
    display: flex;
    flex-wrap: wrap; /* Allow items to wrap if necessary */
    gap: 20px;
    justify-content: flex-start; /* Align items to the left */
    width: 100%;
    padding: 20px;
    background-color: transparent;
    border-radius: 10px;
    border: 3px;
    border-color: white;
  }

  .planet-detail {
    border: 3px;
    border-color: white;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: transparent;
    padding: 10px;
    border-radius: 10px;
    text-align: center;
    flex: 1 1 200px; 
  }
  

  .planet-detail h3 {
    margin: 0;
    font-size: 18px;
  }
  .planet-name{
    margin: 40px;
    
    font-size: 24px;
  }

  .planet-detail p {
    margin: 5px 0 0;
    font-size: 14px;
  }

  .error {
    color: red;
    text-align: center;
    padding: 20px;
  }
</style>

{#if error}
  <p class="error">Error loading planet: {error}</p>
{:else if planet}
  <div class="planet-container">
    <h2 class="planet-name">{planet.englishName}</h2>
    <img class="planet-image" src={`/planetenbilder/${planet.id}.png`} alt="{planet.englishName}" />
    <div class="planet-details">
      <div class="planet-detail">
        <p>Mass</p>
        <h3>{planet.mass ? planet.mass.massValue : 'N/A'} kg</h3>
      </div>
      <div class="planet-detail">
        <p>Diameter</p>
        <h3>{planet.meanRadius * 2} km</h3>
      </div>
      <div class="planet-detail">
        <p>Gravity</p>
        <h3>{planet.gravity} m/s²</h3>
      </div>
      <div class="planet-detail">
        <p>Number of Moons</p>
        <h3>{planet.moons ? planet.moons.length : 0}</h3>
      </div>
      <div class="planet-detail">
        <p>Orbital Period</p>
        <h3>{planet.sideralOrbit} days</h3>
      </div>
      <div class="planet-detail">
        <p>Density</p>
        <h3>{planet.density} g/cm³</h3>
      </div>
    </div>
  </div>
{:else}
  <p>Loading...</p>
{/if}
