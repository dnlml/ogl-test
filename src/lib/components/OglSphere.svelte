<script lang="ts">
  // src/lib/components/OglSphere.svelte
  // Componente per la visualizzazione di una sfera 3D con texture
  
  import { onMount } from 'svelte';
  
  let container: HTMLElement;
  
  onMount(async () => {
    try {
      // Importiamo OGL dinamicamente per evitare problemi di SSR
      const { Renderer, Camera, Transform, Program, Mesh, Sphere, Texture } = await import('ogl');
      
      // Inizializza il renderer
      const renderer = new Renderer({
        dpr: Math.min(window.devicePixelRatio, 2),
        alpha: true,
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
      
      // Carica una texture
      const texture = new Texture(gl);
      
      // Utilizziamo un'immagine di esempio
      const img = new Image();
      img.crossOrigin = 'Anonymous';
      img.src = 'https://images.unsplash.com/photo-1589519160732-576f165b9aad?q=80&w=1000';
      
      // Crea una sfera
      const geometry = new Sphere(gl, {
        radius: 1,
        widthSegments: 64,
        heightSegments: 32,
      });
      
      const program = new Program(gl, {
        vertex: /* glsl */`
          attribute vec3 position;
          attribute vec3 normal;
          attribute vec2 uv;
          
          uniform mat4 modelViewMatrix;
          uniform mat4 projectionMatrix;
          
          varying vec2 vUv;
          varying vec3 vNormal;
          
          void main() {
            vUv = uv;
            vNormal = normal;
            gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
          }
        `,
        fragment: /* glsl */`
          precision highp float;
          
          uniform sampler2D tMap;
          
          varying vec2 vUv;
          varying vec3 vNormal;
          
          void main() {
            vec3 normal = normalize(vNormal);
            float lighting = dot(normal, normalize(vec3(0.5, 1.0, 0.3)));
            vec4 texture = texture2D(tMap, vUv);
            gl_FragColor = texture * lighting;
          }
        `,
        uniforms: {
          tMap: { value: texture },
        },
      });
      
      const mesh = new Mesh(gl, { geometry, program });
      mesh.setParent(scene);
      
      // Carica la texture quando l'immagine è pronta
      img.onload = () => {
        texture.image = img;
      };
      
      img.onerror = (err) => {
        console.error('Errore nel caricamento della texture:', err);
        // Fallback: usa un colore solido se la texture non può essere caricata
        program.uniforms.tMap.value = null;
      };
      
      // Funzione di animazione
      let animationId: number;
      function update() {
        animationId = requestAnimationFrame(update);
        
        // Ruota la sfera lentamente
        mesh.rotation.y += 0.005;
        
        renderer.render({ scene, camera });
      }
      
      // Gestisci il ridimensionamento
      const resize = () => {
        if (container) {
          renderer.setSize(container.offsetWidth, container.offsetHeight);
          camera.perspective({ aspect: container.offsetWidth / container.offsetHeight });
        }
      };
      
      window.addEventListener('resize', resize);
      
      update();
      
      return () => {
        window.removeEventListener('resize', resize);
        if (animationId) {
          cancelAnimationFrame(animationId);
        }
        if (gl.canvas && gl.canvas.parentNode) {
          gl.canvas.parentNode.removeChild(gl.canvas);
        }
      };
    } catch (error) {
      console.error('Errore nell\'inizializzazione della scena WebGL:', error);
    }
  });
</script>

<div class="canvas-container" bind:this={container}></div>

<style>
  .canvas-container {
    width: 100%;
    height: 100vh;
    overflow: hidden;
    background: radial-gradient(circle, #1a1a2e 0%, #16213e 100%);
  }
</style>