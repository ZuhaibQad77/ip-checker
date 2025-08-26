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

  <script src="script.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  background: #f2f2f2;
  color: #222;
  text-align: center;
  padding: 50px;
  transition: background 0.3s, color 0.3s;
}

.dark {
  background: #121212;
  color: #fff;
}

.container {
  max-width: 600px;
  margin: auto;
}

input[type="text"] {
  padding: 10px;
  width: 80%;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-top: 20px;
  font-size: 1em;
}

button {
  margin-top: 15px;
  padding: 10px 20px;
  font-size: 1em;
  background-color: #00bcd4;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #0097a7;
}

#result {
  margin-top: 30px;
  font-size: 1.1em;
}

.footer {
  margin-top: 60px;
  font-size: 0.9em;
  opacity: 0.6;
}

/* Dark Mode Toggle Switch */
.switch {
  position: relative;
  display: inline-block;
  width: 50px;
  height: 24px;
  margin-top: 30px;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0; left: 0; right: 0; bottom: 0;
  background-color: #ccc;
  transition: .4s;
  border-radius: 24px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 18px; width: 18px;
  left: 3px;
  bottom: 3px;
  background-color: white;
  transition: .4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: #2196F3;
}

input:checked + .slider:before {
  transform: translateX(26px);
}

.mode-label {
  display: block;
  margin-top: 10px;
  font-size: 0.9em;
}
async function getIP() {
  const domain = document.getElementById("domainInput").value.trim();
  const resultBox = document.getElementById("result");

  if (!domain) {
    resultBox.innerHTML = "❌ Please enter a valid domain.";
    return;
  }

  resultBox.innerHTML = "⏳ Looking up IP address...";

  try {
    const dnsRes = await fetch(`https://dns.google/resolve?name=${domain}&type=A`);
    const dnsData = await dnsRes.json();

    if (!dnsData.Answer || dnsData.Answer.length === 0) {
      resultBox.innerHTML = "❌ No IP found. Check the domain name.";
      return;
    }

    const ip = dnsData.Answer.find(a => a.type === 1).data;

    // Now get location info from ipwho.is
    const ipRes = await fetch(`https://ipwho.is/${ip}`);
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
    resultBox.innerHTML = "⚠️ Error fetching info.";
    console.error(err);
  }
}

function toggleDarkMode() {
  document.body.classList.toggle("dark");
}

