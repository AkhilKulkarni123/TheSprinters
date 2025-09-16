---
layout: post
title: API Demo
permalink: /API/
---

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Interactive API Demo</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    input, button { padding: 8px; margin: 5px; }
    #result { margin-top: 20px; padding: 10px; border: 1px solid #ccc; }
  </style>
</head>
<body>
  <h1>API Demo – Fetch User Info</h1>
  <p>Enter a user ID (1–10):</p>
  
  <input type="number" id="userId" min="1" max="10" />
  <button onclick="fetchUser()">Get User</button>
  
  <div id="result">User info will appear here...</div>

  <script>
    async function fetchUser() {
      const userId = document.getElementById("userId").value;
      const resultDiv = document.getElementById("result");

      if (!userId) {
        resultDiv.innerHTML = "⚠️ Please enter a user ID.";
        return;
      }

      resultDiv.innerHTML = "⏳ Fetching user data...";

      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
        
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const user = await response.json();

        // Show data nicely
        resultDiv.innerHTML = `
          <h2>${user.name}</h2>
          <p><b>Username:</b> ${user.username}</p>
          <p><b>Email:</b> ${user.email}</p>
          <p><b>Phone:</b> ${user.phone}</p>
          <p><b>Company:</b> ${user.company.name}</p>
        `;
      } catch (err) {
        resultDiv.innerHTML = `❌ Error: ${err.message}`;
      }
    }
  </script>
</body>
</html>
