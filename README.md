<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>IP Checker Tool</title>
<link rel="stylesheet" href="style.css">
</head>
<body>
<div class="container">
<h1>🌐 Website IP Checker</h1>
<input type="text" id="domainInput" placeholder="Enter domain (e.g., google.com)">
<button onclick="getIP()">Get IP Info</button>
<div id="result"></div>
<label class="switch">
<input type="checkbox" onchange="toggleDarkMode()">
<span class="slider"></span>
</label>
<span class="mode-label">🌙 Dark Mode</span>
<p class="footer">Built with ❤️ using DNS & IP APIs</p>
</div>
async function getIP() {
const domain = document.getElementById("domainInput").value.trim();
const resultBox = document.getElementById("result");
if (!domain) {
resultBox.innerHTML = "❌ Please enter a valid domain.";
return;
}
resultBox.innerHTML = "⏳ Looking up IP address...";
const proxy = "https://cors-anywhere.herokuapp.com/";
try {
const dnsRes = await fetch(`${proxy}https://dns.google/resolve?name=${domain}&type=A`);
const dnsData = await dnsRes.json();
if (!dnsData.Answer || dnsData.Answer.length === 0) {
resultBox.innerHTML = "❌ No IP found. Check the domain name.";
return;
}
const ip = dnsData.Answer.find(a => a.type === 1).data;
const ipRes = await fetch(`${proxy}https://ipwho.is/${ip}`);
const ipData = await ipRes.json();
if (!ipData.success) {
resultBox.innerHTML = `✅ IP: ${ip} <br> ⚠️ No location info found.`;
return;
}
resultBox.innerHTML = `
✅ IP Address of <b>${domain}</b>: <b>${ip}</b><br><br>
🌍 Location: ${ipData.city}, ${ipData.region}, ${ipData.country}<br>
🛰️ ISP: ${ipData.connection.isp}<br>
🌐 IP Type: ${ipData.type}<br>
`;
} catch (err) {
resultBox.innerHTML = "⚠️ Error fetching info. Check browser console.";
console.error(err);
}
}
function toggleDarkMode() {
document.body.classList.toggle("dark");
}
