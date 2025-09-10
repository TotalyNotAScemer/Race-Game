<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>3D Racing Game Prototype</title>
  <style>
    body { margin: 0; overflow: hidden; }
    #info {
      position: absolute; top: 10px; left: 10px;
      background: rgba(255,255,255,0.8);
      padding: 10px; font-family: Arial;
      max-width: 300px;
    }
    #upgrades {
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <div id="info">
    <div>
      Select Biome:
      <select id="biomeSelect"></select>
    </div>
    <div>
      Select Car:
      <select id="carSelect"></select>
    </div>
    <div id="upgrades">
      <button id="speedUpgrade">Speed Upgrade (+10)</button>
    </div>
    <div>
      Speed: <span id="speedDisplay">50</span>
    </div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/three@0.150.1/build/three.min.js"></script>
  <script>
    // Basic Three.js setup
    let scene, camera, renderer;
    let car;
    let speed = 50; // Base speed
    const maxSpeed = 150;
    let biomeIndex = 0;
    let carIndex = 0;

    // Biomes (simplified with different ground colors)
    const biomes = [
      { name: "Desert", color: 0xdeb887 },
      { name: "Forest", color: 0x228B22 },
      { name: "Snow", color: 0xffffff },
      { name: "Volcano", color: 0x8b0000 },
      { name: "Beach", color: 0xf4a460 },
      { name: "City", color: 0x808080 },
      { name: "Swamp", color: 0x556b2f },
      { name: "Mountain", color: 0xa9a9a9 },
      { name: "Jungle", color: 0x006400 },
      { name: "Tundra", color: 0xdcdcdc }
    ];

    // Cars (simplified as colored boxes)
    const cars = [];
    for(let i=0; i<100; i++) {
      cars.push({
        name: `Car ${i+1}`,
        color: new THREE.Color(Math.random(), Math.random(), Math.random())
      });
    }

    function init() {
      scene = new THREE.Scene();
      camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
      renderer = new THREE.WebGLRenderer({antialias:true});
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // Ground plane
      const groundGeometry = new THREE.PlaneGeometry(200, 200);
      const groundMaterial = new THREE.MeshPhongMaterial({color: biomes[biomeIndex].color});
      const ground = new THREE.Mesh(groundGeometry, groundMaterial);
      ground.rotation.x = -Math.PI/2;
      ground.receiveShadow = true;
      ground.name = "ground";
      scene.add(ground);

      // Car (box)
      const carGeometry = new THREE.BoxGeometry(4, 2, 8);
      const carMaterial = new THREE.MeshPhongMaterial({color: cars[carIndex].color});
      car = new THREE.Mesh(carGeometry, carMaterial);
      car.position.y = 1;
      scene.add(car);

      // Lighting
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
      scene.add(ambientLight);
      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
      directionalLight.position.set(10, 20, 10);
      scene.add(directionalLight);

      camera.position.set(0, 10, 20);
      camera.lookAt(car.position);

      setupUI();
      animate();
    }

    function setupUI() {
      const biomeSelect = document.getElementById('biomeSelect');
      biomes.forEach((b, i) => {
        let option = document.createElement('option');
        option.value = i;
        option.textContent = b.name;
        biomeSelect.appendChild(option);
      });
      biomeSelect.value = biomeIndex;
      biomeSelect.addEventListener('change', e => {
        biomeIndex = parseInt(e.target.value);
        updateBiome();
      });

      const carSelect = document.getElementById('carSelect');
      cars.forEach((c, i) => {
        let option = document.createElement('option');
        option.value = i;
        option.textContent = c.name;
        carSelect.appendChild(option);
      });
      carSelect.value = carIndex;
      carSelect.addEventListener('change', e => {
        carIndex = parseInt(e.target.value);
        updateCar();
      });

      const speedUpgradeBtn = document.getElementById('speedUpgrade');
      speedUpgradeBtn.addEventListener('click', () => {
        if(speed < maxSpeed){
          speed += 10;
          document.getElementById('speedDisplay').textContent = speed;
        }
      });

      document.getElementById('speedDisplay').textContent = speed;
    }

    function updateBiome() {
      const ground = scene.getObjectByName("ground");
      ground.material.color.set(biomes[biomeIndex].color);
    }

    function updateCar() {
      car.material.color.set(cars[carIndex].color);
    }

    function animate() {
      requestAnimationFrame(animate);

      // Simple forward car movement based on speed
      car.position.z -= speed * 0.01;

      // Follow car with camera
      camera.position.z = car.position.z + 20;
      camera.position.x = car.position.x;
      camera.lookAt(car.position);

      renderer.render(scene, camera);
    }

    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth/window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    init();
  </script>
</body>
</html>
