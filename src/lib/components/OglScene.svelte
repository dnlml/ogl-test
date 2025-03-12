<script lang="ts">
  // src/lib/components/OglScene.svelte
  // Componente per la visualizzazione di una scena 3D con OGL
  
  import { onMount } from 'svelte';
  
  // Importiamo le classi necessarie da OGL
  let container: HTMLElement;
  
  onMount(async () => {
    // Importiamo OGL dinamicamente per evitare problemi di SSR
    const { Renderer, Camera, Transform, Program, Mesh, Box } = await import('ogl');
    
    // Inizializza il renderer
    const renderer = new Renderer({
      dpr: Math.min(window.devicePixelRatio, 2),
    });
    const gl = renderer.gl;
    container.appendChild(gl.canvas);
    
    // Imposta le dimensioni
    renderer.setSize(container.offsetWidth, container.offsetHeight);
    
    // Crea camera e scena
    const camera = new Camera(gl, {
      fov: 35,
      aspect: container.offsetWidth / container.offsetHeight,
    });
    camera.position.z = 5;
    
    const scene = new Transform();
    
    // Crea un cubo
    const geometry = new Box(gl);
    const program = new Program(gl, {
      vertex: /* glsl */`
        attribute vec3 position;
        attribute vec3 normal;
        
        uniform mat4 modelViewMatrix;
        uniform mat4 projectionMatrix;
        
        varying vec3 vNormal;
        
        void main() {
          vNormal = normal;
          gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
      `,
      fragment: /* glsl */`
        precision highp float;
        
        varying vec3 vNormal;
        
        void main() {
          vec3 normal = normalize(vNormal);
          float lighting = dot(normal, normalize(vec3(0.5, 1.0, 0.3)));
          gl_FragColor = vec4(vec3(0.2, 0.8, 1.0) * lighting, 1.0);
        }
      `,
    });
    
    const mesh = new Mesh(gl, { geometry, program });
    mesh.setParent(scene);
    
    // Funzione di animazione
    function update() {
      const id = requestAnimationFrame(update);
      
      mesh.rotation.x += 0.01;
      mesh.rotation.y += 0.02;
      
      renderer.render({ scene, camera });
      
      return () => cancelAnimationFrame(id);
    }
    
    // Gestisci il ridimensionamento
    const resize = () => {
      renderer.setSize(container.offsetWidth, container.offsetHeight);
      camera.perspective({ aspect: container.offsetWidth / container.offsetHeight });
    };
    
    window.addEventListener('resize', resize);
    
    const animation = update();
    
    return () => {
      window.removeEventListener('resize', resize);
      animation();
      if (gl.canvas.parentNode) {
        gl.canvas.parentNode.removeChild(gl.canvas);
      }
    };
  });
</script>

<div class="canvas-container" bind:this={container}></div>

<style>
  .canvas-container {
    width: 100%;
    height: 100vh;
    overflow: hidden;
    background-color: #000;
  }
</style>