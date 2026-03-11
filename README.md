[index.html](https://github.com/user-attachments/files/25892674/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Cyberpunk OSINT Dashboard</title>

<!-- Google Font -->
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">

<!-- jsPDF -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<!-- Particles.js -->
<script src="https://cdn.jsdelivr.net/npm/particles.js@2.0.0/particles.min.js"></script>

<style>
body{
margin:0; padding:0;
font-family:'Orbitron', monospace;
background:#0b0b0b;
color:#00ff9d;
overflow-x:hidden;
}

/* Particles */
#particles-js{
position:fixed; top:0; left:0;
width:100%; height:100%;
z-index:-1;
}

/* Titles */
h1,h2{
color:#00ffcc;
text-shadow:0 0 10px #00ffcc,0 0 20px #00ffcc;
text-align:center;
}

/* Container */
.container{
background: rgba(255,255,255,0.05);
backdrop-filter: blur(10px);
border-radius:15px;
border:1px solid rgba(0,255,150,0.3);
padding:20px;
max-width:1000px;
margin:auto;
box-shadow:0 0 20px #00ff9d33;
}

/* Collapsible windows */
.collapsible{
background:#000;
color:#00ff9d;
cursor:pointer;
padding:12px;
width:100%;
border:none;
text-align:left;
outline:none;
font-size:16px;
border-radius:5px;
margin-top:10px;
box-shadow:0 0 10px #00ff9d;
transition:0.3s;
}
.collapsible:hover{background:#0a0a0a; box-shadow:0 0 20px #00ffcc;}

.content{
padding:10px 20px;
display:none;
overflow:hidden;
background: rgba(0,0,0,0.4);
border-radius:10px;
margin-bottom:10px;
}

/* Terminal style */
.terminal{
background:#000000;
color:#00ff9d;
font-family:'Courier New', monospace;
padding:15px;
border-radius:10px;
height:200px;
overflow-y:auto;
box-shadow:0 0 15px #00ff9d55;
margin-bottom:15px;
}

/* Inputs & Buttons */
input, textarea{
margin:5px;
padding:8px;
width:70%;
background:#000;
border:1px solid #00ff9d;
color:#00ff9d;
border-radius:5px;
}
textarea{height:80px;}
button{
background:#00ff9d;
border:none;
font-weight:bold;
padding:8px 15px;
margin:5px;
cursor:pointer;
border-radius:5px;
box-shadow:0 0 10px #00ff9d;
transition:0.3s;
}
button:hover{
transform:scale(1.05);
box-shadow:0 0 20px #00ffcc;
}

a{
display:block;
color:#00ff9d;
margin:5px 0;
text-decoration:none;
transition:0.3s;
}
a:hover{color:#00cc7a; text-shadow:0 0 5px #00ff9d;}

.footer{
margin-top:20px; font-size:12px; color:#00ff9d; text-align:right; text-shadow:0 0 5px #00ff9d;
}
</style>
</head>

<body>

<div id="particles-js"></div>

<h1>🕶️ Cyberpunk OSINT Dashboard</h1>

<div class="container">

<!-- Terminal -->
<div class="terminal" id="terminalOutput">Welcome to Cyberpunk OSINT Terminal...</div>

<!-- Collapsible windows for each tool -->
<button class="collapsible">🔎 Username Search</button>
<div class="content">
<input id="username" placeholder="Enter username">
<button onclick="runUsernameScan()">Scan Username</button>
<div id="results"></div>
</div>

<button class="collapsible">📧 Email Lookup</button>
<div class="content">
<input id="email" placeholder="Enter email">
<button onclick="runEmailLookup()">Check Email</button>
<div id="emailResult"></div>
</div>

<button class="collapsible">📱 Phone Lookup</button>
<div class="content">
<input id="phone" placeholder="Enter phone number">
<button onclick="runPhoneLookup()">Check Phone</button>
<div id="phoneResult"></div>
</div>

<button class="collapsible">🖼️ Reverse Image Search</button>
<div class="content">
<input type="file" id="revImg">
<button onclick="runReverseImage()">Search Image</button>
<div id="revImgResult"></div>
</div>

<button class="collapsible">🌐 IP Lookup / Geolocation</button>
<div class="content">
<input id="ip" placeholder="Enter IP address">
<button onclick="runIPLookup()">Check IP</button>
<div id="ipResult"></div>
</div>

<button class="collapsible">🔍 Whois Lookup</button>
<div class="content">
<input id="domain" placeholder="Enter domain">
<button onclick="runWhois()">Check Domain</button>
<div id="whoisResult"></div>
</div>

<button class="collapsible">📝 Metadata Analyzer</button>
<div class="content">
<input type="file" id="fileInput">
<button onclick="runMetadataScan()">Scan File Metadata</button>
<div id="metaResult"></div>
</div>

<button class="collapsible">🖼️ Image Comparison</button>
<div class="content">
<input type="file" id="img1">
<input type="file" id="img2">
<button onclick="runImageScan()">Compare Images</button>
<p id="compareResult"></p>
</div>

<button class="collapsible">📝 Investigation Notes</button>
<div class="content">
<textarea id="notes" placeholder="Write investigation notes here..."></textarea>
<br>
<button onclick="exportNotes()">Export TXT</button>
<button onclick="generateReport()">Generate TXT Report</button>
<button onclick="generatePDF()">Export PDF</button>
</div>

<div class="footer">Made by MYRVAXEL MINI</div>
</div>

<script>
/* PARTICLES */
particlesJS("particles-js",{
"particles":{"number":{"value":80},"color":{"value":"#00ff9d"},"shape":{"type":"circle"},"opacity":{"value":0.5},"size":{"value":3},"line_linked":{"enable":true,"distance":150,"color":"#00ff9d","opacity":0.3,"width":1},"move":{"enable":true,"speed":2}},
"interactivity":{"events":{"onhover":{"enable":true,"mode":"grab"}}}
});

/* Collapsible panels */
var coll = document.getElementsByClassName("collapsible");
for (let i = 0; i < coll.length; i++){
  coll[i].addEventListener("click", function(){
    this.classList.toggle("active");
    let content = this.nextElementSibling;
    content.style.display = (content.style.display === "block") ? "none" : "block";
  });
}

/* Terminal typing effect */
function terminalPrint(text,delay=30){
let term=document.getElementById("terminalOutput");
let i=0;
return new Promise(resolve=>{
function type(){
if(i<text.length){
term.innerHTML+=text.charAt(i);
term.scrollTop=term.scrollHeight;
i++;
setTimeout(type,delay);
}else{term.innerHTML+='<br>'; resolve();}
} type();
});
}

/* Startup log */
window.addEventListener('load', async ()=>{
  await terminalPrint("> Cyberpunk OSINT Terminal loaded.");
  await terminalPrint("> Made by MYRVAXEL MINI.");
});

/* Username scan (simplified 300+ sites can be expanded) */
const sites=[["Google","https://www.google.com/search?q="],["Instagram","https://www.instagram.com/"],["Facebook","https://www.facebook.com/"],["Twitter/X","https://twitter.com/"],["GitHub","https://github.com/"],["Reddit","https://www.reddit.com/user/"],["TikTok","https://www.tiktok.com/@"],["Pinterest","https://www.pinterest.com/"],["Twitch","https://www.twitch.tv/"],["SoundCloud","https://soundcloud.com/"],["Medium","https://medium.com/@"],["YouTube","https://www.youtube.com/results?search_query="]];

async function runUsernameScan(){
let username=document.getElementById("username").value;
if(!username){alert("Enter username"); return;}
document.getElementById("results").innerHTML='';
await terminalPrint(`> Initiating username scan for ${username}...`);
for(let site of sites){
await terminalPrint(`[+] Checking ${site[0]}...`);
document.getElementById("results").innerHTML+=`<a href="${site[1]}${username}" target="_blank">${site[0]}</a>`;
}
await terminalPrint("> Username scan completed.");
}

/* Email Lookup */
async function runEmailLookup(){
let email=document.getElementById("email").value;
if(!email){alert("Enter email"); return;}
await terminalPrint(`> Checking email: ${email}...`);
document.getElementById("emailResult").innerHTML=`<a href="https://haveibeenpwned.com/unifiedsearch/${email}" target="_blank">Check ${email} for leaks</a>`;
await terminalPrint("> Email lookup completed.");
}

/* Phone Lookup */
async function runPhoneLookup(){
let phone=document.getElementById("phone").value;
if(!phone){alert("Enter phone"); return;}
await terminalPrint(`> Searching phone: ${phone}...`);
document.getElementById("phoneResult").innerHTML=`<a href="https://www.google.com/search?q=${phone}" target="_blank">Search phone ${phone}</a>`;
await terminalPrint("> Phone lookup completed.");
}

/* Reverse Image */
async function runReverseImage(){
let file=document.getElementById("revImg").files[0];
if(!file){alert("Upload image"); return;}
await terminalPrint("> Reverse image search initiated...");
document.getElementById("revImgResult").innerHTML=`<a href="https://www.google.com/searchbyimage?image_url=${URL.createObjectURL(file)}" target="_blank">Search image on Google</a>`;
await terminalPrint("> Reverse image search completed.");
}

/* IP Lookup */
async function runIPLookup(){
let ip=document.getElementById("ip").value;
if(!ip){alert("Enter IP"); return;}
await terminalPrint(`> Looking up IP: ${ip}...`);
document.getElementById("ipResult").innerHTML=`<a href="https://www.iplocation.net/ip-lookup?query=${ip}" target="_blank">Check IP ${ip}</a>`;
await terminalPrint("> IP lookup completed.");
}

/* Whois Lookup */
async function runWhois(){
let domain=document.getElementById("domain").value;
if(!domain){alert("Enter domain"); return;}
await terminalPrint(`> Checking domain: ${domain}...`);
document.getElementById("whoisResult").innerHTML=`<a href="https://whois.domaintools.com/${domain}" target="_blank">Check Whois ${domain}</a>`;
await terminalPrint("> Whois lookup completed.");
}

/* Metadata Scan */
async function runMetadataScan(){
let file=document.getElementById("fileInput").files[0];
if(!file){alert("Upload file"); return;}
await terminalPrint(`> Analyzing file: ${file.name}...`);
let info=`File Name: ${file.name}<br>Size: ${file.size} bytes<br>Type: ${file.type}<br>Last Modified: ${new Date(file.lastModified)}`;
document.getElementById("metaResult").innerHTML=info;
await terminalPrint("> Metadata analysis completed.");
}

/* Image Comparison */
async function runImageScan(){
let img1=document.getElementById("img1").files[0];
let img2=document.getElementById("img2").files[0];
if(!img1 || !img2){alert("Upload two images"); return;}
await terminalPrint(`> Comparing images: ${img1.name} & ${img2.name}...`);
if(img1.size===img2.size){
document.getElementById("compareResult").innerText="Images might be identical.";
await terminalPrint("> Images might be identical.");
}else{
document.getElementById("compareResult").innerText="Images appear different.";
await terminalPrint("> Images appear different.");
}
await terminalPrint("> Image scan completed.");
}

/* Notes Export */
function exportNotes(){
let text=document.getElementById("notes").value;
let blob=new Blob([text],{type:"text/plain"});
let a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="osint_notes.txt";
a.click();
}

/* TXT Report */
function generateReport(){
let username=document.getElementById("username").value;
let location=document.getElementById("ip").value;
let notes=document.getElementById("notes").value;
let date=new Date().toLocaleString();
let report=`
OSINT INVESTIGATION REPORT
----------------------------
Date: ${date}

Notes:
${notes}

Report generated using Cyberpunk OSINT Dashboard.
`;
let blob=new Blob([report],{type:"text/plain"});
let a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="investigation_report.txt";
a.click();
}

/* PDF Report */
async function generatePDF(){
const { jsPDF } = window.jspdf;
let notes=document.getElementById("notes").value;
let date=new Date().toLocaleString();
let doc=new jsPDF();
doc.setFont("Courier","Normal");
doc.setFontSize(16);
doc.text("OSINT INVESTIGATION REPORT",20,20);
doc.setFontSize(11);
doc.text("Date: "+date,20,35);
doc.text("Notes:",20,50);
let splitNotes=doc.splitTextToSize(notes||"No notes provided.",170);
doc.text(splitNotes,20,58);
doc.save("investigation_report.pdf");
}
</script>

</body>
</html>
