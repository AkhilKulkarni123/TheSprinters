---
layout: post
title: Sprint 2 - Introduction to Python Interactive
description:  Lesson for python basics thru a sim
breadcrumbs: True
permalink: /InteractiveDemo
---


<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Learn About Random Module & RNG</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background: #f9f9f9;
      color: #333;
    }
    h1 {
      text-align: center;
      color: #444;
    }
    .section {
      background: #fff;
      padding: 15px;
      margin: 15px 0;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    button {
      padding: 8px 15px;
      margin-top: 8px;
      border: none;
      border-radius: 4px;
      background: #0078d7;
      color: #fff;
      cursor: pointer;
    }
    button:hover {
      background: #005fa3;
    }
    input {
      padding: 5px;
      width: 70px;
    }
    #output {
      font-weight: bold;
      color: #0078d7;
      margin-top: 10px;
    }
    code {
      background: #eee;
      padding: 2px 4px;
      border-radius: 4px;
    }
  </style>
</head>
<body>

  <h1>Learn the Random Module & Try RNGs</h1>

  <div class="section">
    <h2>About Python’s <code>random</code> Module</h2>
    <p>The <code>random</code> module in Python is used to generate random numbers. Some key functions include:</p>
    <ul>
      <li><code>random()</code>: Returns a float between <code>0.0</code> and <code>1.0</code>.</li>
      <li><code>randint(a, b)</code>: Returns an integer between <code>a</code> and <code>b</code> (inclusive).</li>
      <li><code>uniform(a, b)</code>: Returns a float between <code>a</code> and <code>b</code>.</li>
      <li><code>choice(seq)</code>: Returns a random element from a sequence.</li>
    </ul>
    <p>Below, you can try out RNGs (Random Number Generators) in your browser! These mimic the behavior of Python’s functions using JavaScript.</p>
  </div>

  <div class="section">
    <h2>Generate Random Float (0.0 – 1.0)</h2>
    <button onclick="generateRandom()">Generate</button>
    <div id="output-random"></div>
  </div>

  <div class="section">
    <h2>Generate Random Integer (like <code>randint(a, b)</code>)</h2>
    <label>Min: <input type="number" id="min" value="1"></label>
    <label>Max: <input type="number" id="max" value="10"></label>
    <br>
    <button onclick="generateRandInt()">Generate</button>
    <div id="output-randint"></div>
  </div>

  <div class="section">
    <h2>Generate Random Float in Range (like <code>uniform(a, b)</code>)</h2>
    <label>Min: <input type="number" id="minf" value="0"></label>
    <label>Max: <input type="number" id="maxf" value="1"></label>
    <br>
    <button onclick="generateUniform()">Generate</button>
    <div id="output-uniform"></div>
  </div>

  <script>
    function generateRandom() {
      const num = Math.random();
      document.getElementById("output-random").innerText = "Random float: " + num;
    }

    function generateRandInt() {
      const min = parseInt(document.getElementById("min").value);
      const max = parseInt(document.getElementById("max").value);
      if (min > max) {
        document.getElementById("output-randint").innerText = "Error: Min should be ≤ Max";
        return;
      }
      const num = Math.floor(Math.random() * (max - min + 1)) + min;
      document.getElementById("output-randint").innerText = "Random integer: " + num;
    }

    function generateUniform() {
      const min = parseFloat(document.getElementById("minf").value);
      const max = parseFloat(document.getElementById("maxf").value);
      if (min > max) {
        document.getElementById("output-uniform").innerText = "Error: Min should be ≤ Max";
        return;
      }
      const num = Math.random() * (max - min) + min;
      document.getElementById("output-uniform").innerText = "Random float in range: " + num;
    }
  </script>

</body>
</html>
