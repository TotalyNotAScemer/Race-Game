<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>3D Racing Game Prototype</title>
  <style>
    body { margin: 0; overflow: hidden; }
    #ui-controls { position: absolute; top: 10px; left: 10px; color: white; z-index: 10; }
  </style>
</head>
<body>
  <div id="ui-controls">
    <select id="biomeSelector"></select>
    <select id="mapSelector"></select>
    <select id="carSelector"></select>
    <button id="startBtn">Start Game</button>
  </div>
  <script type="module">
    import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';
    // Optionally: import { World, RayCastVehicle } from 'https://cdn.jsdelivr.net/npm/cannon-es'; 

    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x87ceeb);

    const camera = new THREE.PerspectiveCamera(60, innerWidth / innerHeight, 0.1, 1000);
    camera.position.set(0, 10, 20);

    const ambient = new THREE.HemisphereLight(0xffffbb, 0x080820, 1);
    scene.add(ambient);

    const directional = new THREE.DirectionalLight(0xffffff, 0.8);
    directional.position.set(-5, 10, 5);
    scene.add(directional);

    // Placeholder ground
    const ground = new THREE.Mesh(
      new THREE.PlaneGeometry(200, 200),
      new THREE.MeshLambertMaterial({ color: 0x228822 })
    );
    ground.rotation.x = -Math.PI / 2;
    scene.add(ground);

    // Placeholder car
    const car = new THREE.Mesh(
      new THREE.BoxGeometry(1, 0.5, 2),
      new THREE.MeshStandardMaterial({ color: 0xff0000 })
    );
    car.position.set(0, 0.25, 0);
    scene.add(car);

    // Simple WASD controls
    const keys = {};
    window.addEventListener('keydown', (e) => (keys[e.key.toLowerCase()] = true));
    window.addEventListener('keyup', (e) => (keys[e.key.toLowerCase()] = false));

    function animate() {
      requestAnimationFrame(animate);
      if (keys.w) car.position.z -= 0.2;
      if (keys.s) car.position.z += 0.2;
      if (keys.a) car.position.x -= 0.2;
      if (keys.d) car.position.x += 0.2;
      camera.position.lerp(
        new THREE.Vector3(car.position.x, car.position.y + 5, car.position.z + 10),
        0.1
      );
      camera.lookAt(car.position);
      renderer.render(scene, camera);
    }
    animate();

    // Biome, map, car selection UI wiring (simplified)
    const biomes = ['Desert', 'Forest', 'Snow', /* ... */];
    const mapsPerBiome = 5;
    const cars = Array.from({ length: 25 }, (_, i) => `Car ${i + 1}`);

    const biomeSel = document.getElementById('biomeSelector');
    const mapSel = document.getElementById('mapSelector');
    const carSel = document.getElementById('carSelector');

    biomes.forEach(b => biomeSel.add(new Option(b, b)));
    cars.forEach(c => carSel.add(new Option(c, c)));
    biomeSel.onchange = () => {
      mapSel.innerHTML = '';
      for (let i = 1; i <= mapsPerBiome; i++) {
        mapSel.add(new Option(`Map ${i}`, i));
      }
    };
    biomeSel.onchange();

    document.getElementById('startBtn').onclick = () => {
      alert(`Starting biome: ${biomeSel.value}, map: ${mapSel.value}, car: ${carSel.value}`);
      // Load selected biome, map layout, and car model here
    };
  </script>
</body>
</html>
