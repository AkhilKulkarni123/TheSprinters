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
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      color: #00ffe0;
      background: radial-gradient(circle at top, #0a0f1e, #000000);
      background-attachment: fixed;
      min-height: 100vh;
    }

    h1 {
      color: #00ffcc;
      text-shadow: 0 0 10px #00ffe0, 0 0 20px #00ffee;
    }

    p, label {
      color: #ccefff;
    }

    input, button {
      padding: 8px;
      margin: 5px;
      border: 1px solid #00ffe0;
      border-radius: 5px;
      background: #111;
      color: #00ffe0;
      font-weight: bold;
      box-shadow: 0 0 8px #00ffee;
    }

    button:hover {
      background: #00ffe0;
      color: #000;
      cursor: pointer;
      box-shadow: 0 0 15px #00ffee, 0 0 25px #00ffff;
    }

    .lab {
      margin-bottom: 50px;
      padding: 20px;
      border: 2px solid #00ffe0;
      border-radius: 10px;
      background: rgba(0, 20, 40, 0.85);
      box-shadow: 0 0 20px #00ffee;
    }

    #result, #postsResult, #combinedResult {
      margin-top: 20px;
      padding: 15px;
      border: 1px solid #00ffe0;
      border-radius: 5px;
      background: rgba(0, 10, 20, 0.85);
      color: #ccefff;
      box-shadow: inset 0 0 10px #00ffee;
    }
  </style>
</head>
<body>
  <!-- Lab 1 -->
  <div class="lab">
    <h1>Lab 1: Fetch User Info</h1>
    <p><i>This lab fetches basic details (name, email, phone, company) of a user by ID.</i></p>
    <p>Enter a user ID (1–10):</p>
    <input type="number" id="userId" min="1" max="10" />
    <button onclick="fetchUser()">Get User</button>
    <div id="result">User info will appear here...</div>
  </div>

  <!-- Lab 2 -->
  <div class="lab">
    <h1>Lab 2: Fetch User Posts</h1>
    <p><i>This lab fetches a list of posts written by the user, based on their ID.</i></p>
    <p>Enter a user ID (1–10):</p>
    <input type="number" id="postUserId" min="1" max="10" />
    <button onclick="fetchPosts()">Get Posts</button>
    <div id="postsResult">Posts will appear here...</div>
  </div>

  <!-- Lab 3 -->
  <div class="lab">
    <h1>Lab 3: Fetch User + Posts Together</h1>
    <p><i>This lab fetches both the user’s profile info and their posts in one go, using multiple API calls.</i></p>
    <p>Enter a user ID (1–10):</p>
    <input type="number" id="combinedUserId" min="1" max="10" />
    <button onclick="fetchUserAndPosts()">Get User & Posts</button>
    <div id="combinedResult">User + Posts will appear here...</div>
  </div>

  <script>
    // Lab 1: Fetch User
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
        if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

        const user = await response.json();
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

    // Lab 2: Fetch Posts
    async function fetchPosts() {
      const userId = document.getElementById("postUserId").value;
      const postsDiv = document.getElementById("postsResult");

      if (!userId) {
        postsDiv.innerHTML = "⚠️ Please enter a user ID.";
        return;
      }
      postsDiv.innerHTML = "⏳ Fetching posts...";

      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`);
        if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

        const posts = await response.json();
        if (posts.length === 0) {
          postsDiv.innerHTML = "No posts found for this user.";
          return;
        }
        postsDiv.innerHTML = posts
          .map(p => `<h3>${p.title}</h3><p>${p.body}</p><hr/>`)
          .join("");
      } catch (err) {
        postsDiv.innerHTML = `❌ Error: ${err.message}`;
      }
    }

    // Lab 3: Fetch User AND Posts
    async function fetchUserAndPosts() {
      const userId = document.getElementById("combinedUserId").value;
      const combinedDiv = document.getElementById("combinedResult");

      if (!userId) {
        combinedDiv.innerHTML = "⚠️ Please enter a user ID.";
        return;
      }
      combinedDiv.innerHTML = "⏳ Fetching user and posts...";

      try {
        // Fetch user & posts in parallel
        const [userRes, postsRes] = await Promise.all([
          fetch(`https://jsonplaceholder.typicode.com/users/${userId}`),
          fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`)
        ]);

        if (!userRes.ok || !postsRes.ok) {
          throw new Error("Failed to fetch user or posts.");
        }

        const user = await userRes.json();
        const posts = await postsRes.json();

        combinedDiv.innerHTML = `
          <h2>${user.name} (${user.username})</h2>
          <p><b>Email:</b> ${user.email}</p>
          <p><b>Phone:</b> ${user.phone}</p>
          <p><b>Company:</b> ${user.company.name}</p>
          <h3>Posts:</h3>
          ${posts.map(p => `<h4>${p.title}</h4><p>${p.body}</p>`).join("<hr/>")}
        `;
      } catch (err) {
        combinedDiv.innerHTML = `❌ Error: ${err.message}`;
      }
    }
  </script>
</body>
</html>
