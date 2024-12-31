<script>
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

  let container; // Container für das 3D-Modell

  onMount(() => {
    // Szene erstellen
    const scene = new THREE.Scene();

    // Kamera erstellen
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    camera.position.set(0, 20, 100);

    // Renderer erstellen
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    container.appendChild(renderer.domElement);

    // Beleuchtung hinzufügen
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
    scene.add(ambientLight);

    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
    directionalLight.position.set(5, 10, 7.5);
    scene.add(directionalLight);

    // GLTFLoader verwenden, um das Modell und die Animationen zu laden
    const loader = new GLTFLoader();
    let mixer; // AnimationMixer

    loader.load("src/assets/models/tiger.glb", (gltf) => {
      const model = gltf.scene;
      model.scale.set(0.5, 0.5, 0.5);
      scene.add(model);

      // Animationen laden und Mixer initialisieren
      if (gltf.animations && gltf.animations.length > 0) {
        mixer = new THREE.AnimationMixer(model);
        gltf.animations.forEach((clip) => {
          mixer.clipAction(clip).play(); // Alle Animationen abspielen
        });
      }

      // Animation loop
      function animate() {
        requestAnimationFrame(animate);

        // Mixer aktualisieren
        if (mixer) mixer.update(0.01); // Geschwindigkeit der Animation
        renderer.render(scene, camera);
      }
      animate();
    });

    // Fenstergröße ändern
    window.addEventListener("resize", () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });
  });
</script>

<!-- Container für die 3D-Canvas -->
<div bind:this={container}></div>

<style>
  div {
    width: 100vw;
    height: 100vh;
    display: block;
    overflow: hidden;
  }
</style>
