<script>
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

  let container;

  onMount(() => {
    // Scene setup
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x0b0b0b);

    // Camera setup
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    camera.position.set(0, 60, 170);

    // Renderer setup
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    container.appendChild(renderer.domElement);

    // Ambient light
    const ambientLight = new THREE.AmbientLight(0x404040, 1);
    scene.add(ambientLight);

    // Main spotlight
    const spotLight = new THREE.SpotLight(0xffffff, 1.5);
    spotLight.position.set(0, 40, 80);
    spotLight.target.position.set(0, 0, 0);
    spotLight.castShadow = true;
    spotLight.shadow.mapSize.width = 2048;
    spotLight.shadow.mapSize.height = 2048;
    scene.add(spotLight);
    scene.add(spotLight.target);

    // Point light
    const pointLight = new THREE.PointLight(0xffffff, 0.6, 100);
    pointLight.position.set(0, 100, 160);
    pointLight.castShadow = true;
    pointLight.rotation.set(-Math.PI / 2, 0, 0);
    pointLight.intensity = 2000.0;
    scene.add(pointLight);

    // Hemisphere light
    const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 0.4);
    hemiLight.position.set(0, 0, 0);
    scene.add(hemiLight);

    // Top spotlight
    const topSpotLight = new THREE.SpotLight(0xffffff, 1.0);
    topSpotLight.position.set(0, 100, 0);
    topSpotLight.castShadow = true;
    topSpotLight.shadow.mapSize.width = 2048;
    topSpotLight.shadow.mapSize.height = 2048;
    topSpotLight.intensity = 100.0;
    scene.add(topSpotLight);

    // Hover spotlight
    const hoverSpotLight = new THREE.SpotLight(0xffffff, 2);
    hoverSpotLight.position.set(0, 100, 0);
    hoverSpotLight.castShadow = true;
    hoverSpotLight.shadow.mapSize.width = 1024;
    hoverSpotLight.shadow.mapSize.height = 1024;
    hoverSpotLight.intensity = 1000.0;
    hoverSpotLight.visible = false;
    scene.add(hoverSpotLight);

    const hoverSpotLightHelper = new THREE.SpotLightHelper(hoverSpotLight);
    scene.add(hoverSpotLightHelper);

    // Load model
    const loader = new GLTFLoader();
    let mixer;
    let model;

    loader.load("src/assets/models/tiger.glb", (gltf) => {
      model = gltf.scene;
      model.scale.set(1, 1, 1);
      model.position.y = -10;
      model.castShadow = true;
      model.receiveShadow = true;
      scene.add(model);

      if (gltf.animations && gltf.animations.length > 0) {
        mixer = new THREE.AnimationMixer(model);
        gltf.animations.forEach((clip) => {
          mixer.clipAction(clip).play();
        });
      }

      function animate() {
        requestAnimationFrame(animate);
        if (mixer) mixer.update(0.01);
        renderer.render(scene, camera);
      }
      animate();
    });

    container.addEventListener("mousemove", (event) => {
  if (!model) return;

  const rect = container.getBoundingClientRect();
  const mouseX = ((event.clientX - rect.left) / rect.width) * 2 - 1;
  const mouseY = -((event.clientY - rect.top) / rect.height) * 2 + 1;

  // Unproject mouse position to 3D space
  const vector = new THREE.Vector3(mouseX, mouseY, 0.5).unproject(camera);
  const dir = vector.sub(camera.position).normalize();

  // Calculate hover position with fixed Z range
  const distance = 100; // Fixed distance in front of the camera
  const hoverPosition = camera.position.clone().add(dir.multiplyScalar(distance));

  const minZ = model.position.z + 120; // Minimum Z (in front of model)
  const maxZ = model.position.z + 160; // Maximum Z (in front of model)
  hoverPosition.z = Math.max(minZ, Math.min(maxZ, hoverPosition.z));

  hoverSpotLight.position.set(
    hoverPosition.x,
    hoverPosition.y,
    hoverPosition.z
  );

  // Set the target position higher
  hoverSpotLight.target.position.set(model.position.x, model.position.y + 50, model.position.z);
  hoverSpotLight.target.updateMatrixWorld();

  hoverSpotLight.visible = true;
  hoverSpotLightHelper.update();
});


    // Handle window resize
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
