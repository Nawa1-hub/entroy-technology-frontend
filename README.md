<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ENTROY TECHNOLOGY COMPANY</title>

<!-- JS Libraries -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<!-- Google Fonts -->
<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
*{margin:0;padding:0;box-sizing:border-box;}
body{font-family:'Poppins',sans-serif;background:linear-gradient(135deg,#000,#001f66,#000);color:#fff;transition:0.3s;}
header{text-align:center;padding:15px;background:rgba(0,0,0,0.8);backdrop-filter:blur(10px);position:sticky;top:0;z-index:10;}
nav button{margin:5px;padding:8px 15px;border-radius:20px;border:1px solid #333;background:transparent;color:#fff;cursor:pointer;transition:0.3s;}
nav button:hover{background:orange;color:#000;}
.page{display:none;opacity:0;transform:translateX(50px);transition:0.5s;max-width:1000px;margin:100px auto;background:rgba(20,20,20,0.95);padding:25px;border-radius:15px;box-shadow:0 0 20px rgba(0,0,0,0.5);}
.page.active{display:block;opacity:1;transform:translateX(0);}
input,select,textarea{width:100%;padding:10px;margin:8px 0;background:#1a1a1a;border:1px solid #333;color:#fff;border-radius:5px;}
button.action{background:orange;border:none;padding:10px;border-radius:5px;cursor:pointer;font-weight:bold;}
.card{background:#111;padding:15px;margin:10px 0;border-radius:10px;border-left:4px solid orange;transition:0.3s;}
.card:hover{transform:scale(1.02);}
table{width:100%;margin-top:10px;border-collapse:collapse;}
th,td{padding:10px;text-align:center;}
th{background:#001f66;}
tr:nth-child(even){background:#1a1a1a;}
.flash{padding:10px;margin:5px 0;border-radius:5px;}
.success{background:green;}
.error{background:red;}
.progress-container{background:#222;border-radius:5px;overflow:hidden;margin:5px 0;}
.progress-bar{height:10px;background:orange;width:0%;transition:0.5s;}
@media(max-width:768px){header h2{font-size:20px;}nav button{padding:5px 10px;font-size:12px;} .page{margin:50px 10px;padding:15px;}}
</style>
</head>
<body>

<header>
<h2>ENTROY TECHNOLOGY COMPANY</h2>
<nav>
<button onclick="go('home')">Home</button>
<button onclick="go('builder')">AI Builder</button>
<button onclick="go('adminDashboard')">Admin</button>
<button onclick="go('clientDashboard')">Client</button>
<button onclick="go('analytics')">Analytics</button>
</nav>
</header>

<!-- ================= HOME ================= -->
<div class="page active" id="home">
<h3>Welcome to ENTROY TECHNOLOGY</h3>
<p>Where Ideation meets Creation by bringing your Imagination to Reality. Build your project automatically.</p>
<div class="card">
<h4>⚡ What You Can Build</h4>
<ul>
<li>🌐 Websites</li>
<li>📱 Mobile Apps</li>
<li>💻 Desktop Apps</li>
<li>☁️ Cloud Systems</li>
<li>⚙️ Analog System Softwares</li>
</ul>
</div>
</div>

<!-- ================= AI BUILDER ================= -->
<div class="page" id="builder">
<h3>🧠 AI Project Builder</h3>

<input id="clientId" placeholder="Client ID">
<input id="projectName" placeholder="Project Name">
<select id="projectType">
<option>Website</option>
<option>Mobile App</option>
<option>Desktop App</option>
<option>Cloud System</option>
<option>Analog System Software</option>
</select>
<textarea id="projectDesc" placeholder="Describe your project..."></textarea>
<button class="action" onclick="generateProject()">⚡ Generate Project</button>

<h4>Generated Projects</h4>
<div id="projects"></div>
</div>

<!-- ================= ADMIN ================= -->
<div class="page" id="adminDashboard">
<h3>Admin Dashboard</h3>
<input id="cid" placeholder="Client ID">
<input id="cname" placeholder="Client Name">
<input id="cemail" placeholder="Email">
<input id="cphone" placeholder="Phone (+260...)">
<input id="amount" placeholder="Amount">

<button class="action" onclick="addClient()">Add Client</button>
<table id="ctable"></table>
</div>

<!-- ================= CLIENT ================= -->
<div class="page" id="clientDashboard">
<h3>Client Dashboard</h3>
<h4>Your Projects</h4>
<div id="clientProjects"></div>
</div>

<!-- ================= ANALYTICS ================= -->
<div class="page" id="analytics">
<h3>Business Analytics</h3>
<div class="card">
<p>Total Revenue: <span id="revenue"></span></p>
<p>Total Clients: <span id="totalClients"></span></p>
<p>Total Projects: <span id="totalProjects"></span></p>
</div>
<canvas id="projectChart" style="margin-top:20px;"></canvas>
</div>

<script>
let token = ""; // store JWT here after login

// ================= PAGE NAVIGATION =================
function go(id){
document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
document.getElementById(id).classList.add('active');
if(id==='adminDashboard') loadClients();
if(id==='analytics') loadAnalytics();
if(id==='clientDashboard') loadClientProjects();
if(id==='builder') loadProjects();
}

// ================= AUTH =================
async function login(email, password){
const res = await fetch('/api/login',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({email,password})});
const data = await res.json();
if(data.token){token=data.token; alert("✅ Logged in");} else alert(data.error);
}

// ================= CLIENT =================
async function addClient(){
try{
const id=document.getElementById('cid').value;
const name=document.getElementById('cname').value;
const email=document.getElementById('cemail').value;
const phone=document.getElementById('cphone').value;
const amount=parseFloat(document.getElementById('amount').value)||0;
const res=await fetch('/api/clients',{
method:'POST',
headers:{'Content-Type':'application/json','Authorization':token},
body:JSON.stringify({id,name,email,phone,amount})
});
const data=await res.json();
if(data.success){alert('✅ Client added'); loadClients();}
else alert(data.error||'Failed');
}catch(e){console.error(e);}
}

async function loadClients(){
const res=await fetch('/api/clients',{headers:{'Authorization':token}});
const clients=await res.json();
let table=document.getElementById('ctable');
table.innerHTML='<tr><th>ID</th><th>Name</th><th>Amount</th><th>Email</th><th>Phone</th><th>Invoice</th></tr>';
clients.forEach(c=>{
let row=table.insertRow();
row.insertCell(0).innerText=c.id;
row.insertCell(1).innerText=c.name;
row.insertCell(2).innerText='K'+c.amount;
row.insertCell(3).innerText=c.email||'-';
row.insertCell(4).innerText=c.phone||'-';
row.insertCell(5).innerHTML=`<button class="action" onclick="invoice('${c.id}')">PDF</button>`;
});
}

// ================= PROJECT =================
async function generateProject(){
try{
const clientId=document.getElementById('clientId').value;
const name=document.getElementById('projectName').value;
const type=document.getElementById('projectType').value;
const desc=document.getElementById('projectDesc').value;
if(!clientId||!name||!desc){alert('Enter all fields'); return;}
const res=await fetch('/api/projects',{
method:'POST',
headers:{'Content-Type':'application/json','Authorization':token},
body:JSON.stringify({clientId,name,type,desc})
});
const data=await res.json();
if(data.success){alert('🚀 Project Generated!'); loadProjects(); loadAnalytics();}
else alert(data.error||'Payment required before building project');
}catch(e){console.error(e);}
}

async function loadProjects(){
const res=await fetch('/api/projects',{headers:{'Authorization':token}});
const projects=await res.json();
let container=document.getElementById('projects');
container.innerHTML='';
projects.forEach(p=>{
container.innerHTML+=`<div class="card">
<h4>${p.name}</h4>
<p>Type: ${p.type}</p>
<p>${p.desc}</p>
<p>Status: ${p.status}</p>
</div>`;
});
}

// ================= CLIENT DASHBOARD =================
async function loadClientProjects(){
const res=await fetch('/api/projects',{headers:{'Authorization':token}});
const projects=await res.json();
let container=document.getElementById('clientProjects');
container.innerHTML='';
projects.forEach(p=>{
container.innerHTML+=`<div class="card">
<h4>${p.name}</h4>
<p>Type: ${p.type}</p>
<p>Status: ${p.status}</p>
</div>`;
});
}

// ================= ANALYTICS =================
async function loadAnalytics(){
const res=await fetch('/api/analytics',{headers:{'Authorization':token}});
const data=await res.json();
document.getElementById('revenue').innerText='K'+data.totalRevenue;
document.getElementById('totalClients').innerText=data.totalClients;
document.getElementById('totalProjects').innerText=data.totalProjects;

// chart
let ctx=document.getElementById('projectChart').getContext('2d');
let counts={Website:0,'Mobile App':0,'Desktop App':0,'Cloud System':0,'Analog System Software':0};
const projectRes=await fetch('/api/projects',{headers:{'Authorization':token}});
const projects=await projectRes.json();
projects.forEach(p=>counts[p.type]++);
if(window.projectChart) window.projectChart.destroy();
window.projectChart=new Chart(ctx,{
type:'bar',
data:{labels:Object.keys(counts),datasets:[{label:'Projects Count',data:Object.values(counts),backgroundColor:'orange'}]},
options:{responsive:true,plugins:{legend:{display:false}}}
});
}

// ================= INVOICE =================
async function invoice(id){
const res=await fetch(`/api/invoice/${id}`,{headers:{'Authorization':token}});
const blob=await res.blob();
const url=window.URL.createObjectURL(blob);
const a=document.createElement('a');
a.href=url;
a.download=`invoice_${id}.pdf`;
a.click();
}
</script>

</body>
</html>
