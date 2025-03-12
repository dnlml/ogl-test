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

## Risoluzione dei problemi

Se riscontri problemi durante l'esecuzione del progetto, ecco alcune soluzioni comuni:

### Errori di WebGL

1. **WebGL non supportato**: Assicurati che il tuo browser supporti WebGL. Puoi verificarlo visitando [webglreport.com](https://webglreport.com/).

2. **Errori di contesto WebGL**: Se vedi errori come "WebGL context lost" o "Unable to initialize WebGL", prova a:
   - Aggiornare il browser
   - Abilitare l'accelerazione hardware
   - Aggiornare i driver della scheda grafica

3. **Problemi di performance**: Se l'animazione è lenta o scattosa:
   - Riduci il numero di particelle (nel componente OglParticles)
   - Diminuisci la complessità delle geometrie
   - Usa un valore più basso per `dpr` nel renderer

### Errori di Svelte/SvelteKit

1. **Problemi con le runes di Svelte 5**: Se vedi errori relativi a `$state` o altre runes:
   - Assicurati di usare una versione compatibile di Svelte 5 (almeno la next.58)
   - Verifica la sintassi delle runes nel file `+page.svelte`

2. **Errori di importazione**: Se vedi errori come "Cannot find module 'ogl'":
   - Esegui `pnpm install` per assicurarti che tutte le dipendenze siano installate
   - Verifica che il file package.json contenga la dipendenza `ogl`

3. **Errori di SSR**: Se vedi errori durante il rendering lato server:
   - Assicurati che l'importazione di OGL avvenga solo nel browser usando `onMount` e import dinamici

### Problemi di texture

Se la sfera non mostra la texture:
1. Verifica che l'URL dell'immagine sia accessibile
2. Controlla la console per errori CORS
3. Prova a usare un'immagine locale nella cartella `static`

## Risorse

- [Documentazione OGL](https://github.com/oframe/ogl)
- [Esempi OGL](https://oframe.github.io/ogl/examples)
- [Documentazione SvelteKit](https://kit.svelte.dev/docs)
- [Documentazione Svelte 5 Runes](https://svelte-5-preview.vercel.app/docs/runes)

## Licenza

MIT