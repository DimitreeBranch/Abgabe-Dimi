<script>
    import { onMount } from 'svelte';
    import { push } from 'svelte-spa-router';
  
    let planets = [];
    let error = null;
    let sortField = 'englishName';
    let sortOrder = 'asc';
  
    onMount(async () => {
      try {
        const response = await fetch('https://api.le-systeme-solaire.net/rest/bodies/');
        if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
        const data = await response.json();
        planets = data.bodies.filter(body => body.isPlanet);
        sortPlanets(); // Sort planets initially
      } catch (err) {
        error = err.message;
        console.error('Fetch error: ', err);
      }
    });
  
    function sortPlanets() {
      planets = planets.sort((a, b) => {
        let aValue = a[sortField];
        let bValue = b[sortField];
  
        if (sortField === 'moons.length') {
          aValue = a.moons ? a.moons.length : 0;
          bValue = b.moons ? b.moons.length : 0;
        }
  
        if (aValue < bValue) {
          return sortOrder === 'asc' ? -1 : 1;
        }
        if (aValue > bValue) {
          return sortOrder === 'asc' ? 1 : -1;
        }
        return 0;
      });
    }
  
    function handleSort(field) {
      if (sortField === field) {
        sortOrder = sortOrder === 'asc' ? 'desc' : 'asc';
      } else {
        sortField = field;
        sortOrder = 'asc';
      }
      sortPlanets();
    }
  
    function navigateToPlanet(id) {
      push(`/planet/${id}`);
    }
  </script>
  
  <div class="outer-container">
    {#if error}
      <p class="error">Error loading planets: {error}</p>
    {:else}
      <div class="table-container">
        <div class="grid-header">
          <div on:click={() => handleSort('englishName')}>
            Name <span class="sort-indicator">{sortField === 'englishName' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('meanRadius')}>
            Mean Radius (km) <span class="sort-indicator">{sortField === 'meanRadius' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('semimajorAxis')}>
            Semimajor Axis (km) <span class="sort-indicator">{sortField === 'semimajorAxis' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('gravity')}>
            Gravity (m/s²) <span class="sort-indicator">{sortField === 'gravity' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('sideralOrbit')}>
            Orbital Period (days) <span class="sort-indicator">{sortField === 'sideralOrbit' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('density')}>
            Density (g/cm³) <span class="sort-indicator">{sortField === 'density' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
          <div on:click={() => handleSort('moons.length')}>
            Moons <span class="sort-indicator">{sortField === 'moons.length' ? (sortOrder === 'asc' ? '▲' : '▼') : '▲▼'}</span>
          </div>
        </div>
        <div class="grid-body">
          {#each planets as planet}
            <div class="grid-row" on:click={() => navigateToPlanet(planet.id)}>
              <div>{planet.englishName}</div>
              <div>{planet.meanRadius}</div>
              <div>{planet.semimajorAxis}</div>
              <div>{planet.gravity}</div>
              <div>{planet.sideralOrbit}</div>
              <div>{planet.density}</div>
              <div>{planet.moons ? planet.moons.length : 0}</div>
            </div>
          {/each}
        </div>
      </div>
    {/if}
  </div>
  
  <style>
    body, html {
      width: 100%;
      height: 100%;
      margin: 0;
      padding: 0;
      background-color: #000;
      color: #fff;
      overflow-x: hidden; /* Prevent horizontal scrolling */
    }
  
    .outer-container {
      width: 80vw;
      height: 70vh;
      padding: 20px;
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
  
    .table-container {
      display: grid;
      grid-template-columns: repeat(7, 1fr); /* Adjust the number of columns */
      width: 100%;
      gap: 0px;
    }
  
    .grid-header {
      display: contents;
      font-weight: bold;
      background-color: #333;
    }
  
    .grid-body {
      display: contents;
    }
  
    .grid-row {
      display: contents;
      cursor: pointer; /* Add pointer cursor for grid rows */
      transition: background 0.3s; /* Smooth background transition */
    }
  
    .grid-row:hover {
      background-color: #444; /* Change background color on hover */
    }
  
    .grid-header div,
    .grid-row div {
      padding: 10px;
      border: 1px solid #444;
      text-align: left;
      color: white;
    }
  
    .grid-header div {
      background-color: #333;
      cursor: pointer; /* Add cursor pointer for headers */
    }
  
    .sort-indicator {
      font-size: 0.7em; /* Adjust the size of the arrows */
      margin-left: 5px;
    }
  
    .grid-row:nth-child(even) div {
      background: transparent;
    }
  </style>
  