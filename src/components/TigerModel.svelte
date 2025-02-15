<script>
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
  import { gsap } from "gsap";

  let container;
  let model;
  let mixer;
  let hoverSpotLight, hoverSpotLightHelper;
  let targetAngle = null;
  let camera;
  let spotLight;

  // Function to rotate the model with gsap
  function rotateTo(angle) {
    if (model && targetAngle !== angle) {
      targetAngle = angle;
      gsap.to(model.rotation, { y: angle, duration: 1, ease: "power2.inOut" });
    }
  }

  function rotateToFrontView() {
    rotateTo(0); // Rotate to front view
    if (model) {
      gsap.to(model.scale, { x: 1, y: 1, z: 1, duration: 1, ease: "power2.inOut" });
      gsap.to(model.position, { x: 0, y: -10, duration: 1, ease: "power2.inOut" }); // Reset position
      gsap.to(spotLight.position, { z: 80, duration: 1, ease: "power2.inOut" }); // Reset light position
    }
  }

  function rotateToSideView() {
    rotateTo(Math.PI / 2); // Rotate to side view
    if (model) {
      gsap.to(model.scale, { x: 2, y: 2, z: 2, duration: 1, ease: "power2.inOut" });
      gsap.to(model.position, { x: -150, y: -93, duration: 1, ease: "power2.inOut" }); // Move to left and down
      gsap.to(spotLight.position, { z: 80, duration: 1, ease: "power2.inOut" }); // Move light position
    }
  }

  function moveCameraTo(y) {
    if (camera) {
      gsap.to(camera.position, { y: y, duration: 1, ease: "power2.inOut" });
    }
  }

  onMount(() => {
    // Scene setup
    const scene = new THREE.Scene();
    // scene.background = new THREE.Color(0x090909);

    // Camera setup
    camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    camera.position.set(0, 75, 155);

    // Renderer setup
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    container.appendChild(renderer.domElement);

    // Lights
    const ambientLight = new THREE.AmbientLight(0x404040, 1);
    scene.add(ambientLight);

    spotLight = new THREE.SpotLight(0xffffff, 1.5);
    spotLight.position.set(0, 40, 80);
    spotLight.target.position.set(0, 0, 0);
    spotLight.castShadow = true;
    scene.add(spotLight);
    scene.add(spotLight.target);

    const pointLight = new THREE.PointLight(0xffffff, 0.6, 100);
    pointLight.position.set(0, 100, 160);
    pointLight.castShadow = true;
    pointLight.intensity = 300.0;
    scene.add(pointLight);

    const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444, 0.2);
    scene.add(hemiLight);

    const topSpotLight = new THREE.SpotLight(0xffffff, 1.0);
    topSpotLight.position.set(0, 100, 0);
    topSpotLight.castShadow = true;
    topSpotLight.intensity = 10.0;
    scene.add(topSpotLight);

    // Hover spotlight
    hoverSpotLight = new THREE.SpotLight(0xffffff, 2);
    hoverSpotLight.position.set(0, 100, 0);
    hoverSpotLight.castShadow = true;
    hoverSpotLight.shadow.mapSize.width = 1024;
    hoverSpotLight.shadow.mapSize.height = 1024;
    hoverSpotLight.intensity = 2000.0;
    hoverSpotLight.visible = false;
    scene.add(hoverSpotLight);

    // hoverSpotLightHelper = new THREE.SpotLightHelper(hoverSpotLight);
    // scene.add(hoverSpotLightHelper);

    // Load model
    const loader = new GLTFLoader();
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

    // Mouse move handler for hover spotlight
    container.addEventListener("mousemove", (event) => {
      if (!model) return;

      const rect = container.getBoundingClientRect();
      const mouseX = ((event.clientX - rect.left) / rect.width) * 2 - 1;
      const mouseY = -((event.clientY - rect.top) / rect.height) * 2 + 1;

      const vector = new THREE.Vector3(mouseX, mouseY, 0.5).unproject(camera);
      const dir = vector.sub(camera.position).normalize();

      const distance = 100;
      const hoverPosition = camera.position.clone().add(dir.multiplyScalar(distance));
      const minZ = model.position.z + 120;
      const maxZ = model.position.z + 160;
      hoverPosition.z = Math.max(minZ, Math.min(maxZ, hoverPosition.z));

      hoverSpotLight.position.set(
        hoverPosition.x,
        hoverPosition.y,
        hoverPosition.z
      );

      hoverSpotLight.target.position.set(model.position.x, model.position.y + 50, model.position.z);
      hoverSpotLight.target.updateMatrixWorld();

      hoverSpotLight.visible = true;
      // hoverSpotLightHelper.update();
      
    });

    // Window resize handler
    window.addEventListener("resize", () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // Listen for external rotation events
    document.addEventListener("rotateToFrontView", rotateToFrontView);
    document.addEventListener("rotateToSideView", rotateToSideView);
    document.addEventListener("moveCameraTo", (event) => moveCameraTo(event.detail.y));
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