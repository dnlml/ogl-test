# OGL Test

Un progetto di esempio per testare la libreria OGL (Minimal WebGL framework) con SvelteKit.

## Descrizione

Questo progetto dimostra come utilizzare la libreria OGL per creare animazioni 3D in WebGL all'interno di un'applicazione SvelteKit. Include tre esempi:

1. **Cubo rotante** - Un semplice cubo 3D che ruota nello spazio
2. **Sfera con texture** - Una sfera 3D con una texture applicata
3. **Sistema di particelle** - Un sistema di particelle 3D animate

## Tecnologie utilizzate

- [SvelteKit](https://kit.svelte.dev/) - Framework per applicazioni web
- [Svelte 5](https://svelte.dev/) - Framework UI reattivo con runes
- [OGL](https://github.com/oframe/ogl) - Minimal WebGL framework
- [TypeScript](https://www.typescriptlang.org/) - Superset tipizzato di JavaScript

## Installazione

1. Clona il repository:
   ```bash
   git clone https://github.com/dnlml/ogl-test.git
   cd ogl-test
   ```

2. Installa le dipendenze con pnpm:
   ```bash
   pnpm install
   ```

3. Avvia il server di sviluppo:
   ```bash
   pnpm dev
   ```

4. Apri il browser all'indirizzo [http://localhost:5173](http://localhost:5173)

## Struttura del progetto

```
ogl-test/
├── src/
│   ├── lib/
│   │   └── components/
│   │       ├── OglScene.svelte     # Componente cubo
│   │       ├── OglSphere.svelte    # Componente sfera con texture
│   │       └── OglParticles.svelte # Componente sistema di particelle
│   ├── routes/
│   │   └── +page.svelte            # Pagina principale con selezione esempi
│   └── app.html                    # Template HTML principale
├── static/                         # File statici
├── package.json                    # Dipendenze e script
├── svelte.config.js                # Configurazione SvelteKit
├── tsconfig.json                   # Configurazione TypeScript
└── vite.config.ts                  # Configurazione Vite
```

## Utilizzo della libreria OGL

OGL è una libreria WebGL minimalista che offre un'API moderna e intuitiva per creare grafica 3D nel browser. Ecco alcuni concetti chiave:

### Renderer

Il `Renderer` è responsabile della creazione del contesto WebGL e del rendering della scena:

```typescript
const renderer = new Renderer({
  dpr: Math.min(window.devicePixelRatio, 2),
});
const gl = renderer.gl;
```

### Camera

La `Camera` definisce la prospettiva attraverso cui viene visualizzata la scena:

```typescript
const camera = new Camera(gl, {
  fov: 35,
  aspect: container.offsetWidth / container.offsetHeight,
});
camera.position.z = 5;
```

### Geometria

Le classi di geometria come `Box`, `Sphere` o `Geometry` definiscono la forma degli oggetti:

```typescript
const geometry = new Box(gl);
// oppure
const geometry = new Sphere(gl, { radius: 1 });
```

### Programma e Shader

Il `Program` contiene gli shader GLSL che definiscono l'aspetto visivo degli oggetti:

```typescript
const program = new Program(gl, {
  vertex: `/* GLSL vertex shader */`,
  fragment: `/* GLSL fragment shader */`,
  uniforms: {
    // Variabili uniformi passate agli shader
  }
});
```

### Mesh

La `Mesh` combina geometria e programma per creare un oggetto renderizzabile:

```typescript
const mesh = new Mesh(gl, { geometry, program });
mesh.setParent(scene);
```

## Risorse

- [Documentazione OGL](https://github.com/oframe/ogl)
- [Esempi OGL](https://oframe.github.io/ogl/examples)
- [Documentazione SvelteKit](https://kit.svelte.dev/docs)
- [Documentazione Svelte 5 Runes](https://svelte-5-preview.vercel.app/docs/runes)

## Licenza

MIT