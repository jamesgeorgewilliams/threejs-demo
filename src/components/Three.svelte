<script>
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  import gsap from "gsap";
  import * as dat from "dat.gui";

  onMount(() => {
    // Canvas
    const canvas = document.querySelector("canvas.webgl");

    // Debug Gui
    const gui = new dat.GUI({ width: 500 });

    const parameters = {
      color: 0x989898,
      count: 500,
      rotateY: () => {
        gsap.to(mesh.rotation, {
          duration: 1,
          y: mesh.rotation.y + 2,
          ease: "power2.out",
        });
      },
    };

    // Scene
    const scene = new THREE.Scene();

    // Object
    const geometry = new THREE.BufferGeometry();
    const positionsArray = new Float32Array(parameters.count * 3 * 3);
    for (let i = 0; i < parameters.count * 3 * 3; i++) {
      positionsArray[i] = (Math.random() - 0.5) * 2;
    }
    const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3);
    geometry.setAttribute("position", positionsAttribute);

    const material = new THREE.MeshBasicMaterial({
      color: parameters.color,
      wireframe: true,
    });
    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);

    // Debug Gui Controls
    gui.addColor(parameters, "color").onChange(() => {
      material.color.set(parameters.color);
    });

    gui.add(parameters, "rotateY");

    gui.add(mesh, "visible");

    gui.add(material, "wireframe");

    // Sizes

    const sizes = {
      width: window.innerWidth,
      height: window.innerHeight,
    };

    window.addEventListener("resize", () => {
      // Update sizes
      sizes.width = window.innerWidth;
      sizes.height = window.innerHeight;
      // Update camera
      camera.aspect = sizes.width / sizes.height;
      camera.updateProjectionMatrix();
      // Update renderer
      renderer.setSize(sizes.width, sizes.height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    });

    // Camera
    const camera = new THREE.PerspectiveCamera(
      75,
      sizes.width / sizes.height,
      0.1,
      100
    );
    camera.position.z = 3;
    scene.add(camera);

    // Controls
    const controls = new OrbitControls(camera, canvas);
    controls.enableDamping = true;

    //  Renderer

    const renderer = new THREE.WebGLRenderer({
      canvas: canvas,
    });
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

    // Animate

    const clock = new THREE.Clock();

    const tick = () => {
      const elapsedTime = clock.getElapsedTime();

      // Update controls
      controls.update();

      // Render
      renderer.render(scene, camera);

      // Call tick again on the next frame
      window.requestAnimationFrame(tick);
    };

    tick();
  });
</script>

<canvas class="webgl" />

<style>
  .webgl {
    position: fixed;
    top: 0;
    left: 0;
    outline: none;
  }
</style>
