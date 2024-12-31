<script>
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

  let container;

  onMount(() => {
    // Szene erstellen
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x111111); // Hintergrundfarbe (#111)

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
    renderer.shadowMap.enabled = true; // Schatten aktivieren
    container.appendChild(renderer.domElement);

    // Lichtquellen hinzufügen
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.3); // Weiches Umgebungslicht
    scene.add(ambientLight);

    const spotLight = new THREE.SpotLight(0xffffff, 1.2); // Fokuslicht von oben
    spotLight.position.set(0, 50, 30); // Über dem Tiger positioniert
    spotLight.castShadow = true; // Schatten aktivieren
    spotLight.shadow.mapSize.width = 1024; // Schattenqualität
    spotLight.shadow.mapSize.height = 1024;
    scene.add(spotLight);

    const pointLight = new THREE.PointLight(0xffaa00, 0.6, 100); // Warmes Licht für Highlights
    pointLight.position.set(-10, 20, 40); // Position leicht seitlich
    scene.add(pointLight);

    const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 0.4); // Neutraler Himmel und Boden
    hemiLight.position.set(0, 50, 0); // Licht von oben
    scene.add(hemiLight);

    // GLTFLoader verwenden, um das Modell zu laden
    const loader = new GLTFLoader();
    let mixer;

    loader.load("src/assets/models/tiger.glb", (gltf) => {
      const model = gltf.scene;
      model.scale.set(0.5, 0.5, 0.5);
      model.position.y = -10; // Modell auf dem Boden positionieren
      model.castShadow = true; // Schatten werfen
      model.receiveShadow = true; // Schatten empfangen
      scene.add(model);

      // Animation aktivieren
      if (gltf.animations && gltf.animations.length > 0) {
        mixer = new THREE.AnimationMixer(model);
        gltf.animations.forEach((clip) => {
          mixer.clipAction(clip).play();
        });
      }

      // Animation loop
      function animate() {
        requestAnimationFrame(animate);
        if (mixer) mixer.update(0.01);
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

<div bind:this={container}></div>

<style>
  div {
    width: 100vw;
    height: 100vh;
    display: block;
    overflow: hidden;
  }
</style>
