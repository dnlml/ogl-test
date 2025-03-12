<script lang="ts">
  // src/routes/+page.svelte
  // Pagina principale che permette di selezionare i diversi esempi OGL

  import { onMount } from "svelte";

  let selectedDemo = $state("cube");
  let OglScene = $state<any>(null);
  let OglSphere = $state<any>(null);
  let OglParticles = $state<any>(null);

  // Carica i componenti dinamicamente per evitare problemi di SSR
  onMount(async () => {
    try {
      const sceneModule = await import("$lib/components/OglScene.svelte");
      const sphereModule = await import("$lib/components/OglSphere.svelte");
      const particlesModule = await import(
        "$lib/components/OglParticles.svelte"
      );

      OglScene = sceneModule.default;
      OglSphere = sphereModule.default;
      OglParticles = particlesModule.default;
    } catch (error) {
      console.error("Errore nel caricamento dei componenti:", error);
    }
  });

  // Cambia demo
  function selectDemo(demo: string) {
    selectedDemo = demo;
  }
</script>

<svelte:head>
  <title>OGL Test - Esempi di WebGL con OGL e SvelteKit</title>
</svelte:head>

<div class="app">
  <header>
    <h1>OGL Test</h1>
    <p>Esempi di WebGL con OGL e SvelteKit</p>

    <nav>
      <button
        class:active={selectedDemo === "cube"}
        onclick={() => selectDemo("cube")}
      >
        Cubo
      </button>
      <button
        class:active={selectedDemo === "sphere"}
        onclick={() => selectDemo("sphere")}
      >
        Sfera con Texture
      </button>
      <button
        class:active={selectedDemo === "particles"}
        onclick={() => selectDemo("particles")}
      >
        Sistema di Particelle
      </button>
    </nav>
  </header>

  <main>
    {#if selectedDemo === "cube" && OglScene}
      <OglScene />
    {:else if selectedDemo === "sphere" && OglSphere}
      <OglSphere />
    {:else if selectedDemo === "particles" && OglParticles}
      <OglParticles />
    {:else}
      <div class="loading">
        <p>Caricamento...</p>
      </div>
    {/if}
  </main>

  <footer>
    <p>
      Creato con <a
        href="https://github.com/oframe/ogl"
        target="_blank"
        rel="noopener noreferrer">OGL</a
      >,
      <a href="https://svelte.dev" target="_blank" rel="noopener noreferrer"
        >Svelte 5</a
      >
      e
      <a href="https://kit.svelte.dev" target="_blank" rel="noopener noreferrer"
        >SvelteKit</a
      >
    </p>
  </footer>
</div>

<style>
  .app {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 10;
    background-color: rgba(0, 0, 0, 0.7);
    backdrop-filter: blur(10px);
    padding: 1rem;
    color: white;
    text-align: center;
  }

  h1 {
    margin: 0;
    font-size: 2rem;
  }

  p {
    margin: 0.5rem 0;
    opacity: 0.8;
  }

  nav {
    display: flex;
    justify-content: center;
    gap: 1rem;
    margin-top: 1rem;
  }

  button {
    background-color: rgba(255, 255, 255, 0.1);
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  button:hover {
    background-color: rgba(255, 255, 255, 0.2);
  }

  button.active {
    background-color: rgba(255, 255, 255, 0.3);
  }

  main {
    flex: 1;
    position: relative;
  }

  .loading {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    color: white;
    background-color: #000;
  }

  footer {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.7);
    backdrop-filter: blur(10px);
    padding: 1rem;
    color: white;
    text-align: center;
  }

  a {
    color: #4fc3f7;
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
  }

  :global(body) {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
    overflow: hidden;
  }
</style>
