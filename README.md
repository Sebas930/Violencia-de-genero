// === Escena ===
const scene = new THREE.Scene();
scene.fog = new THREE.FogExp2(0x000000, 0.15);

// === Cámara ===
const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 6;

// === Render ===
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setClearColor(0x000000);
document.body.appendChild(renderer.domElement);

// === Luz parpadeante ===
const light = new THREE.PointLight(0xffffff, 3, 15);
light.position.set(0, 3, 5);
scene.add(light);

// === Habitación ===
const geometry = new THREE.BoxGeometry(8, 5, 12);
const material = new THREE.MeshStandardMaterial({
  color: 0x222222,
  roughness: 0.9,
  metalness: 0.1
});
const room = new THREE.Mesh(geometry, material);
scene.add(room);

// === Sonido (opcional) ===
const listener = new THREE.AudioListener();
camera.add(listener);

const audio = new THREE.Audio(listener);
const loader = new THREE.AudioLoader();
loader.load('sonido.mp3', buffer => {
  audio.setBuffer(buffer);
  audio.setLoop(false);
  audio.setVolume(0.4);
  audio.play();
});

// === Animación ===
let start = Date.now();

function animate() {
  requestAnimationFrame(animate);

  const t = (Date.now() - start) / 1000;

  // Movimiento de respiración
  const scale = 1 + Math.sin(t * 2) * 0.03;
  room.scale.set(scale, scale, scale);

  // Luz parpadeante
  light.intensity = 3 + Math.sin(t * 20) * 0.6;

  // Cámara acercándose lentamente
  camera.position.z = 6 - t * 0.08;

  // Pequeño temblor de cámara
  camera.position.x = Math.sin(t * 8) * 0.03;
  camera.position.y = Math.sin(t * 6) * 0.02;

  // Final a los 30s (cámara muy cerca)
  if (t >= 30) return;

  renderer.render(scene, camera);
}

animate();
