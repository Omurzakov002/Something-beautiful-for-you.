<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title></title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      background-color: #050505;
      color: #ffffff;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;
    }
    .card {
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid rgba(255, 255, 255, 0.1);
      padding: 30px;
      border-radius: 20px;
      text-align: center;
      backdrop-filter: blur(10px);
      width: 280px;
      z-index: 10;
      transition: opacity 0.8s ease, transform 0.8s ease;
    }
    .card.hidden { opacity: 0; transform: scale(0.9); pointer-events: none; }
    .icon { font-size: 40px; margin-bottom: 15px; }
    .title { font-size: 22px; margin-bottom: 10px; font-weight: 600; }
    .status { font-size: 13px; color: #888; margin-bottom: 25px; }
    .btn {
      background: rgba(255, 255, 255, 0.1);
      border: 1px solid rgba(255, 255, 255, 0.2);
      color: #666;
      padding: 12px 24px;
      border-radius: 25px;
      font-size: 12px;
      font-weight: 600;
      letter-spacing: 1px;
      cursor: not-allowed;
      transition: all 0.3s ease;
    }
    .btn.active {
      background: #e63946;
      color: #fff;
      border-color: #e63946;
      cursor: pointer;
      box-shadow: 0 0 15px rgba(230, 57, 70, 0.4);
    }
    canvas { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; }
    .message {
      position: absolute;
      bottom: 80px;
      font-size: 20px;
      color: rgba(255, 255, 255, 0.9);
      opacity: 0;
      transform: translateY(20px);
      transition: opacity 1.5s ease, transform 1.5s ease;
      z-index: 10;
      letter-spacing: 0.5px;
    }
    .message.visible { opacity: 1; transform: translateY(0); }
  </style>
</head>
<body>
  <div class="card" id="card">
    <div class="icon">🌹</div>
    <h1 class="title">Для тебя</h1>
    <p class="status" id="status">Загрузка подарки для тебя...</p>
    <button class="btn" id="btn" disabled>Нажми чтобы увидеть</button>
  </div>

  <canvas id="canvas"></canvas>
  <div class="message" id="message">Что то красивое для тебя на сегодня 🌹</div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const card = document.getElementById('card');
      const status = document.getElementById('status');
      const btn = document.getElementById('btn');
      const canvas = document.getElementById('canvas');
      const message = document.getElementById('message');
      const ctx = canvas.getContext('2d');

      function resize() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
      }
      resize();
      window.addEventListener('resize', resize);

      const steps = [
        { text: "Growing digital petals...", delay: 1000 },
        { text: "Optimizing 3D rendering...", delay: 2200 },
        { text: "Ready to bloom!", delay: 3200 }
      ];

      steps.forEach(({ text, delay }) => {
        setTimeout(() => {
          status.textContent = text;
          if (text === "Ready to bloom!") {
            btn.classList.add('active');
            btn.disabled = false;
          }
        }, delay);
      });

      btn.addEventListener('click', () => {
        card.classList.add('hidden');
        setTimeout(startFlowerAnimation, 500);
      });

      function startFlowerAnimation() {
        const cx = canvas.width / 2;
        const cy = canvas.height / 2 + 100;
        let progress = 0;

        function animate() {
          ctx.clearRect(0, 0, canvas.width, canvas.height);
          progress += 0.006;

          const stemProgress = Math.min(progress * 2, 1);
          const stemHeight = 260 * stemProgress;
          
          ctx.beginPath();
          ctx.moveTo(cx, cy);
          ctx.quadraticCurveTo(cx - 10, cy - stemHeight / 2, cx, cy - stemHeight);
          ctx.strokeStyle = '#2d5a27';
          ctx.lineWidth = 4;
          ctx.lineCap = 'round';
          ctx.stroke();

          const flowerY = cy - stemHeight;

          if (progress > 0.3) {
            const leafProgress = Math.min((progress - 0.3) * 2.5, 1);
            
            ctx.save();
            ctx.translate(cx - 3, cy - 100);
            ctx.scale(leafProgress, leafProgress);
            ctx.beginPath();
            ctx.moveTo(0, 0);
            ctx.quadraticCurveTo(-40, -10, -50, -30);
            ctx.quadraticCurveTo(-20, 10, 0, 0);
            ctx.fillStyle = '#3a7d34';
            ctx.fill();
            ctx.restore();

            ctx.save();
            ctx.translate(cx + 3, cy - 140);
            ctx.scale(leafProgress, leafProgress);
            ctx.beginPath();
            ctx.moveTo(0, 0);
            ctx.quadraticCurveTo(40, -10, 50, -30);
            ctx.quadraticCurveTo(20, 10, 0, 0);
            ctx.fillStyle = '#3a7d34';
            ctx.fill();
            ctx.restore();
          }

          if (progress > 0.5) {
            const bloomProgress = Math.min((progress - 0.5) * 2, 1);
            const petalCount = 12;

            for (let i = 0; i < petalCount; i++) {
              const angle = (i * Math.PI * 2) / petalCount;
              const radius = 35 * bloomProgress;

              ctx.save();
              ctx.translate(cx, flowerY);
              ctx.rotate(angle + bloomProgress * 0.5);
              
              ctx.beginPath();
              ctx.moveTo(0, 0);
              ctx.quadraticCurveTo(radius, -radius * 1.5, 0, -radius * 2);
              ctx.quadraticCurveTo(-radius, -radius * 1.5, 0, 0);
              
              ctx.fillStyle = i % 2 === 0 ? 'rgba(230, 57, 70, 0.85)' : 'rgba(180, 30, 50, 0.85)';
              ctx.fill();
              ctx.restore();
            }

            ctx.beginPath();
            ctx.arc(cx, flowerY, 12 * bloomProgress, 0, Math.PI * 2);
            ctx.fillStyle = '#900c3f';
            ctx.fill();
          }

          if (progress < 1) {
            requestAnimationFrame(animate);
          } else {
            message.classList.add('visible');
          }
        }

        animate();
      }
    });
  </script>
</body>
</html>
