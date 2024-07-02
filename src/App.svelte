<script>
  import Router from "svelte-spa-router";
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';
  import Header from "./components/Header.svelte";
  import Seite1 from "./pages/Seite1.svelte";
  import Seite2 from "./pages/Seite2.svelte";
  import Seite3 from "./pages/Seite3.svelte"; // Import the new page
  import Planet from "./components/Planet.svelte";

  const routes = {
    "/": Seite1,
    "/planet/:id": Planet,
    "/Seite2": Seite2,
    "/table-view": Seite3 // Add the new route
  };

  // Store to hold the current route
  const currentRoute = writable('/');

  onMount(() => {
    // Update the current route when the hash changes
    const updateRoute = () => currentRoute.set(window.location.hash.replace('#', ''));
    window.addEventListener('hashchange', updateRoute);
    updateRoute(); // Set the initial route
    return () => window.removeEventListener('hashchange', updateRoute);
  });
</script>

<div id="wrapper">
  <Header />
  <nav>
    <ul>
      <li class:selected={$currentRoute === "/"}><a href="#/">Planet overview</a></li>
      <li class:selected={$currentRoute === "/Seite2"}><a href="#/Seite2">Comparison</a></li>
      <li class:selected={$currentRoute === "/table-view"}><a href="#/table-view">Table View</a></li> <!-- Add link to table view -->
    </ul>
  </nav>
  <main>
    <Router {routes} />
  </main>
</div>

<style>
  body, html {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
  }

  #wrapper {
    width: 100%;
    height: 100%;
    margin: auto;
    display: flex;
    flex-direction: column;
    padding: 0em;
    box-sizing: border-box;
  }

  nav {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  nav ul {
    list-style: none;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0;
  }

  nav ul li {
    margin: 0 1em;
    position: relative;
  }

  nav ul li a {
    color: white;
    text-decoration: none;
  }

  nav ul li.selected::after {
    content: "";
    display: block;
    width: 100%;
    height: 2px;
    background: white;
    position: absolute;
    bottom: -5px;
    left: 0;
  }

  main {
    flex: 1;
    display: flex;
    flex-direction: column;
  }
</style>
