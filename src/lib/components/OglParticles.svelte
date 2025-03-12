<script lang="ts">
  // src/lib/components/OglParticles.svelte
  // Componente per la visualizzazione di un sistema di particelle 3D
  
  import { onMount } from 'svelte';
  
  let container: HTMLElement;
  
  onMount(async () => {
    try {
      // Importiamo OGL dinamicamente per evitare problemi di SSR
      const { Renderer, Camera, Transform, Program, Mesh, Geometry } = await import('ogl');
      
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
      camera.position.z = 15;
      
      const scene = new Transform();
      
      // Numero di particelle
      const count = 5000;
      
      // Crea la geometria per le particelle
      const geometry = new Geometry(gl, {
        position: { size: 3, data: new Float32Array(count * 3) },
        uv: { size: 2, data: new Float32Array(count * 2) },
        id: { size: 1, data: new Float32Array(count) },
      });
      
      // Riempi gli array con posizioni casuali
      const positions = geometry.attributes.position.data;
      const uvs = geometry.attributes.uv.data;
      const ids = geometry.attributes.id.data;
      
      for (let i = 0; i < count; i++) {
        // Posizione casuale in una sfera
        const phi = Math.random() * Math.PI * 2;
        const costheta = Math.random() * 2 - 1;
        const theta = Math.acos(costheta);
        const r = 5 + Math.random() * 3;
        
        const x = r * Math.sin(theta) * Math.cos(phi);
        const y = r * Math.sin(theta) * Math.sin(phi);
        const z = r * Math.cos(theta);
        
        positions[i * 3] = x;
        positions[i * 3 + 1] = y;
        positions[i * 3 + 2] = z;
        
        // UV per animazione
        uvs[i * 2] = Math.random();
        uvs[i * 2 + 1] = Math.random();
        
        // ID per variazione
        ids[i] = i / count;
      }
      
      // Crea il programma shader
      const program = new Program(gl, {
        vertex: /* glsl */`
          attribute vec3 position;
          attribute vec2 uv;
          attribute float id;
          
          uniform mat4 modelViewMatrix;
          uniform mat4 projectionMatrix;
          uniform float uTime;
          
          varying vec2 vUv;
          varying float vId;
          
          void main() {
            vUv = uv;
            vId = id;
            
            // Animazione delle particelle
            vec3 pos = position;
            float noise = sin(uTime * 0.5 + id * 6.28) * 0.5 + 0.5;
            pos += position * noise * 0.2;
            
            gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
            
            // Dimensione delle particelle
            gl_PointSize = 4.0 * (1.0 - noise * 0.5);
          }
        `,
        fragment: /* glsl */`
          precision highp float;
          
          varying vec2 vUv;
          varying float vId;
          uniform float uTime;
          
          void main() {
            // Forma circolare per le particelle
            float d = length(gl_PointCoord - 0.5) * 2.0;
            if (d > 1.0) discard;
            
            // Colore basato su ID e tempo
            vec3 color = 0.5 + 0.5 * cos(vId * 6.28 + uTime * 0.5 + vec3(0, 2, 4));
            
            // Opacità che diminuisce verso i bordi
            float alpha = 1.0 - d * d;
            
            gl_FragColor = vec4(color, alpha);
          }
        `,
        uniforms: {
          uTime: { value: 0 },
        },
        transparent: true,
        depthWrite: false,
      });
      
      // Crea la mesh con le particelle
      const mesh = new Mesh(gl, { 
        geometry, 
        program,
        mode: gl.POINTS,
      });
      mesh.setParent(scene);
      
      // Tempo iniziale
      let startTime = Date.now();
      
      // Funzione di animazione
      let animationId: number;
      function update() {
        animationId = requestAnimationFrame(update);
        
        // Aggiorna il tempo per l'animazione
        const time = (Date.now() - startTime) * 0.001;
        program.uniforms.uTime.value = time;
        
        // Ruota lentamente la scena
        mesh.rotation.y += 0.002;
        mesh.rotation.x += 0.001;
        
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
    background: linear-gradient(135deg, #000000 0%, #1a1a2e 100%);
  }
</style>