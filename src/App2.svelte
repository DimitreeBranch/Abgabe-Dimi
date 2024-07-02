<script>
  import Router from "svelte-spa-router";
  import Header from "./components/Header.svelte";
  import Seite1 from "./pages/Seite1.svelte";
  import Seite2 from "./pages/Seite2.svelte";
  import Planet from "./components/Planet.svelte";

  const routes = {
    "/": Seite1,
    "/planet/:id": Planet,
    "/Seite2": Seite2,
  };
</script>

<div id="wrapper">
  <Header />
  <nav>
    <ul>
      <li><a href="#/">Planetenübersicht</a></li>
      <li><a href="#/Seite2">Seite2</a></li>
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
    background-color: #222;
    padding: 1em;
    box-sizing: border-box;
  }

  nav {
    width: 100%;
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
  }

  nav ul li a {
    color: white;
    text-decoration: none;
  }

  main {
    flex: 1;
    display: flex;
    flex-direction: column;
  }
</style>
