<!-- ===================== TOP SECTION - SPACE ANIMATION ===================== -->

<style>
  * {
    box-sizing: border-box;
  }
  
  #universe-container {
    width: 100%;
    height: 600px;
    background: #000814;
    position: relative;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 30px;
    border-radius: 10px;
  }
  
  canvas {
    display: block;
    width: 100%;
    height: 100%;
  }
  
  #name-popup {
    position: absolute;
    font-size: 48px;
    font-weight: bold;
    color: #00f7ff;
    text-shadow: 0 0 20px #00f7ff, 0 0 40px #0099ff, 0 0 60px #00f7ff;
    font-family: "Press Start 2P", cursive;
    opacity: 0;
    transform: scale(0);
    z-index: 10;
    animation: namePopup 1s ease-out forwards;
  }
  
  @keyframes namePopup {
    0% {
      opacity: 0;
      transform: scale(0) rotateY(180deg);
    }
    50% {
      opacity: 1;
    }
    100% {
      opacity: 1;
      transform: scale(1) rotateY(0deg);
    }
  }
  
  #content-reveal {
    opacity: 0;
    animation: fadeIn 1.5s ease-in-out forwards;
    animation-delay: 5s;
  }
  
  @keyframes fadeIn {
    0% {
      opacity: 0;
      transform: translateY(30px);
    }
    100% {
      opacity: 1;
      transform: translateY(0);
    }
  }
  
  .subtitle-animation {
    animation: slideIn 0.8s ease-out forwards;
    animation-delay: 5.5s;
    opacity: 0;
  }
  
  @keyframes slideIn {
    0% {
      opacity: 0;
      transform: translateX(-20px);
    }
    100% {
      opacity: 1;
      transform: translateX(0);
    }
  }
</style>

<div id="universe-container">
  <canvas id="spaceCanvas"></canvas>
  <div id="name-popup">AAYUSH</div>
</div>

<script>
  const canvas = document.getElementById('spaceCanvas');
  const ctx = canvas.getContext('2d');
  
  // Set canvas size
  function resizeCanvas() {
    canvas.width = canvas.offsetWidth;
    canvas.height = canvas.offsetHeight;
  }
  resizeCanvas();
  window.addEventListener('resize', resizeCanvas);
  
  // Particle/star system
  const particles = [];
  const planets = [];
  
  class Star {
    constructor() {
      this.x = Math.random() * canvas.width;
      this.y = Math.random() * canvas.height;
      this.z = Math.random() * 1000;
      this.size = Math.random() * 2;
      this.opacity = Math.random() * 0.7 + 0.3;
      this.twinkle = Math.random() * 0.02;
      this.twinkleDirection = Math.random() > 0.5 ? 1 : -1;
    }
    
    update(cameraZ) {
      this.z -= 8;
      if (this.z < 0) {
        this.z = 1000;
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
      }
      
      this.opacity += this.twinkle * this.twinkleDirection;
      if (this.opacity > 0.9 || this.opacity < 0.3) {
        this.twinkleDirection *= -1;
      }
    }
    
    draw() {
      const perspective = this.z / 1000;
      const scale = (1 - perspective) * 2 + 0.5;
      
      ctx.fillStyle = `rgba(0, 247, 255, ${this.opacity})`;
      ctx.shadowBlur = 10;
      ctx.shadowColor = '#00f7ff';
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size * scale, 0, Math.PI * 2);
      ctx.fill();
    }
  }
  
  class Planet {
    constructor(x, y, size, color) {
      this.x = x;
      this.y = y;
      this.z = Math.random() * 500 + 200;
      this.size = size;
      this.color = color;
      this.rotation = Math.random() * Math.PI * 2;
      this.rotationSpeed = Math.random() * 0.01;
    }
    
    update() {
      this.z -= 12;
      this.rotation += this.rotationSpeed;
      
      if (this.z < -300) {
        this.z = 600;
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
      }
    }
    
    draw() {
      const perspective = this.z / 600;
      const scale = (1 - perspective) * 1.5 + 0.5;
      
      ctx.fillStyle = this.color;
      ctx.shadowBlur = 15;
      ctx.shadowColor = this.color;
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size * scale, 0, Math.PI * 2);
      ctx.fill();
      
      // Planet details
      ctx.strokeStyle = `rgba(255, 255, 255, ${0.3 * scale})`;
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size * scale * 0.7, 0, Math.PI * 2);
      ctx.stroke();
    }
  }
  
  // Initialize
  for (let i = 0; i < 150; i++) {
    particles.push(new Star());
  }
  
  planets.push(new Planet(canvas.width * 0.3, canvas.height * 0.3, 15, 'rgba(255, 100, 150, 0.8)'));
  planets.push(new Planet(canvas.width * 0.7, canvas.height * 0.5, 20, 'rgba(100, 200, 255, 0.7)'));
  planets.push(new Planet(canvas.width * 0.5, canvas.height * 0.7, 12, 'rgba(255, 200, 100, 0.8)'));
  planets.push(new Planet(canvas.width * 0.2, canvas.height * 0.6, 18, 'rgba(150, 100, 255, 0.7)'));
  
  let animationTime = 0;
  const maxAnimationTime = 6000;
  
  function drawGalaxy() {
    const centerX = canvas.width / 2;
    const centerY = canvas.height / 2;
    
    // Galaxy gradient background
    const grd = ctx.createRadialGradient(centerX, centerY, 0, centerX, centerY, Math.max(canvas.width, canvas.height));
    grd.addColorStop(0, 'rgba(0, 150, 255, 0.1)');
    grd.addColorStop(0.5, 'rgba(0, 50, 150, 0.05)');
    grd.addColorStop(1, 'rgba(0, 10, 40, 0)');
    
    ctx.fillStyle = grd;
    ctx.fillRect(0, 0, canvas.width, canvas.height);
  }
  
  function animate() {
    // Clear canvas with fade trail
    ctx.fillStyle = 'rgba(0, 8, 20, 0.1)';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    drawGalaxy();
    
    // Update and draw stars
    particles.forEach(star => {
      star.update();
      star.draw();
    });
    
    // Update and draw planets
    planets.forEach(planet => {
      planet.update();
      planet.draw();
    });
    
    animationTime += 16;
    
    // Trigger name popup at the right time
    if (animationTime === 3000) {
      document.getElementById('name-popup').style.display = 'block';
    }
    
    // Reveal rest of content after animation
    if (animationTime >= maxAnimationTime) {
      document.getElementById('content-reveal').style.display = 'block';
      return;
    }
    
    requestAnimationFrame(animate);
  }
  
  animate();
</script>

<!-- Content reveal section -->
<div id="content-reveal" style="display: none;">

<!-- Pixel / Retro Typing Name -->
<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?font=Press+Start+2P&size=32&duration=2500&pause=800&color=00F7FF&center=true&vCenter=true&width=700&lines=AAYUSH" />
</p>

<h3 align="center" class="subtitle-animation">
  Problem Solver • Backend Developer • Equity Market Enthusiast
</h3>

<p align="center">
  🔭 Currently working on <strong>DSA & Real-World Projects</strong><br/>
  🌱 Exploring <strong>Backend Development & Equity Markets</strong><br/>
  💬 Ask me about <strong>Python • Data Structures • Markets</strong><br/>
  📫 Open to collaboration & learning opportunities
</p>

---

## 🛠️ Skills

<p align="center">

<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
<img src="https://img.shields.io/badge/SQL-003B57?style=for-the-badge&logo=postgresql&logoColor=white" />
<img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white" />
<img src="https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=white" />
<img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" />
<img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" />
<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" />
<img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" />
<img src="https://img.shields.io/badge/C-00599C?style=for-the-badge&logo=c&logoColor=white" />
<img src="https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" />
<img src="https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visual-studio-code&logoColor=white" />
<img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" />
<img src="https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />
<img src="https://img.shields.io/badge/Equity_Market-1E8449?style=for-the-badge&logo=chartdotjs&logoColor=white" />

</p>

---

## 🔧 Frameworks & 💻 Tools
<p align="center">
  
  <img alt="Django" src="https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=white" />
  <img alt="MySQL" src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white" />
  <img alt="GitHub" src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" />
  <img alt="VS Code" src="https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visual%20studio%20code&logoColor=white" />
  <img alt="Vercel" src="https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />
  <img alt="Dev C++" src="https://img.shields.io/badge/Dev--C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" />
  <img alt="AutoCAD" src="https://img.shields.io/badge/AutoCAD-E51050?style=for-the-badge&logo=autodesk&logoColor=white" />
</p>

---

## 📊 GitHub Stats

<p align="center">
  <img 
    src="https://github-readme-stats.vercel.app/api?username=gumnaam4&show_icons=true&theme=radical&count_private=true&include_all_commits=true&cache_seconds=1800" 
    width="49%" 
  />
  
  <img 
    src="https://github-readme-stats.vercel.app/api/top-langs/?username=gumnaam4&layout=compact&theme=radical&count_private=true&cache_seconds=1800" 
    width="49%" 
  />
</p>

<p align="center">
  <img 
    src="https://streak-stats.demolab.com?user=gumnaam4&theme=radical&hide_border=true" 
    width="60%" 
  />
</p>

---

<p align="center">
  <img 
    src="https://github-readme-activity-graph.vercel.app/graph?username=gumnaam4&theme=react-dark&hide_border=true" 
    width="95%" 
  />
</p>

---

## 🔗 Connect With Me
 <p align="center">
  <a href="https://instagram.com/gumnaam.41">
    <img alt="Instagram" src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/>
  </a>
  
  <a href="https://yourwebsite.com">
    <img alt="Website" src="https://img.shields.io/badge/Portfolio-000000?style=for-the-badge&logo=vercel&logoColor=white"/>
  </a>
  
  <a href="mailto:ad5909@srmist.edu.in">
    <img alt="Email" src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white"/>
  </a>
</p>

---

## ✨ Fun Facts

- 💡 I enjoy building practical, real-world solutions  
- ♟️ Chess sharpens my strategic thinking  
- 📈 Passionate about Equity & Financial Markets  
- 🚀 Committed to continuous growth  

---

<p align="center">
  <i>Thanks for visiting — let’s build something amazing! 🚀</i>
</p>
</div> <!-- end content-reveal -->