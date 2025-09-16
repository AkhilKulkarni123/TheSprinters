---
layout: post
title: API Demo
permalink: /API/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>API Learning Project</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #2196F3, #21CBF3);
            color: white;
            padding: 40px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            font-weight: 700;
        }

        .header p {
            font-size: 1.2em;
            opacity: 0.9;
        }

        .content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            padding: 40px;
        }

        .theory-section {
            background: #f8f9ff;
            padding: 30px;
            border-radius: 15px;
            border-left: 5px solid #2196F3;
        }

        .practical-section {
            background: #fff;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
        }

        .section-title {
            font-size: 1.5em;
            font-weight: 600;
            margin-bottom: 20px;
            color: #333;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .section-title::before {
            content: '';
            width: 6px;
            height: 30px;
            background: linear-gradient(45deg, #2196F3, #21CBF3);
            border-radius: 3px;
        }

        .api-card {
            background: white;
            border: 2px solid #e0e6ff;
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 20px;
            transition: all 0.3s ease;
        }

        .api-card:hover {
            border-color: #2196F3;
            transform: translateY(-2px);
            box-shadow: 0 10px 25px rgba(33, 150, 243, 0.1);
        }

        .api-card h3 {
            color: #2196F3;
            margin-bottom: 15px;
            font-size: 1.2em;
        }

        .api-form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .input-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        .input-group label {
            font-weight: 600;
            color: #555;
            font-size: 0.9em;
        }

        .input-group input, .input-group select {
            padding: 12px;
            border: 2px solid #e0e6ff;
            border-radius: 8px;
            font-size: 1em;
            transition: border-color 0.3s ease;
        }

        .input-group input:focus, .input-group select:focus {
            outline: none;
            border-color: #2196F3;
        }

        .btn {
            background: linear-gradient(135deg, #2196F3, #21CBF3);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 8px;
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 10px;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(33, 150, 243, 0.3);
        }

        .btn:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .response-container {
            margin-top: 20px;
            padding: 20px;
            background: #f5f7fa;
            border-radius: 8px;
            border-left: 4px solid #2196F3;
            min-height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .response-content {
            width: 100%;
        }

        .loading {
            display: flex;
            align-items: center;
            gap: 10px;
            color: #666;
        }

        .spinner {
            width: 20px;
            height: 20px;
            border: 2px solid #e0e6ff;
            border-top: 2px solid #2196F3;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .json-output {
            background: #1e1e1e;
            color: #f8f8f2;
            padding: 15px;
            border-radius: 8px;
            font-family: 'Monaco', 'Menlo', monospace;
            font-size: 0.9em;
            overflow-x: auto;
            white-space: pre-wrap;
            word-break: break-all;
        }

        .status-badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.8em;
            font-weight: 600;
            margin-bottom: 10px;
        }

        .status-200 { background: #d4edda; color: #155724; }
        .status-400 { background: #f8d7da; color: #721c24; }
        .status-500 { background: #f5c6cb; color: #721c24; }

        .concept-box {
            background: white;
            border: 1px solid #e0e6ff;
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 15px;
        }

        .concept-box h4 {
            color: #2196F3;
            margin-bottom: 10px;
        }

        .example-code {
            background: #f8f9fa;
            border: 1px solid #e9ecef;
            border-radius: 6px;
            padding: 15px;
            font-family: monospace;
            font-size: 0.9em;
            overflow-x: auto;
            margin: 10px 0;
        }

        @media (max-width: 768px) {
            .content {
                grid-template-columns: 1fr;
                padding: 20px;
            }
            
            .header {
                padding: 30px 20px;
            }
            
            .header h1 {
                font-size: 2em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🚀 API Learning Project</h1>
            <p>Learn APIs through interactive examples and real-world practice</p>
        </div>

        <div class="content">
            <div class="theory-section">
                <h2 class="section-title">📚 API Concepts</h2>
                
                <div class="concept-box">
                    <h4>What is an API?</h4>
                    <p>An API (Application Programming Interface) is a set of rules and protocols that allows different software applications to communicate with each other. Think of it as a waiter in a restaurant - you tell the waiter what you want, they communicate with the kitchen, and bring back your order.</p>
                </div>

                <div class="concept-box">
                    <h4>HTTP Methods</h4>
                    <ul style="margin: 10px 0; padding-left: 20px;">
                        <li><strong>GET:</strong> Retrieve data</li>
                        <li><strong>POST:</strong> Create new data</li>
                        <li><strong>PUT:</strong> Update existing data</li>
                        <li><strong>DELETE:</strong> Remove data</li>
                    </ul>
                </div>

                <div class="concept-box">
                    <h4>Status Codes</h4>
                    <ul style="margin: 10px 0; padding-left: 20px;">
                        <li><strong>200:</strong> Success</li>
                        <li><strong>404:</strong> Not Found</li>
                        <li><strong>500:</strong> Server Error</li>
                    </ul>
                </div>

                <div class="concept-box">
                    <h4>Example API Request</h4>
                    <div class="example-code">
fetch('https://api.example.com/users')
  .then(response => response.json())
  .then(data => console.log(data));</div>
                </div>
            </div>

            <div class="practical-section">
                <h2 class="section-title">🛠️ Try It Yourself</h2>
                
                <div class="api-card">
                    <h3>🌐 JSONPlaceholder API - Get Posts</h3>
                    <p>Fetch sample blog posts from a test API</p>
                    <div class="api-form">
                        <div class="input-group">
                            <label for="postId">Post ID (leave empty for all posts):</label>
                            <input type="number" id="postId" placeholder="Enter post ID (1-100)">
                        </div>
                        <button class="btn" onclick="fetchPosts()">📋 Fetch Posts</button>
                    </div>
                </div>

                <div class="api-card">
                    <h3>🎲 Random Quote API</h3>
                    <p>Get inspirational quotes from different categories</p>
                    <div class="api-form">
                        <div class="input-group">
                            <label for="category">Category:</label>
                            <select id="category">
                                <option value="">Random</option>
                                <option value="inspirational">Inspirational</option>
                                <option value="motivational">Motivational</option>
                                <option value="success">Success</option>
                                <option value="wisdom">Wisdom</option>
                            </select>
                        </div>
                        <button class="btn" onclick="fetchQuote()">💭 Get Quote</button>
                    </div>
                </div>

                <div class="api-card">
                    <h3>🐱 Cat Facts API</h3>
                    <p>Learn interesting facts about cats</p>
                    <div class="api-form">
                        <button class="btn" onclick="fetchCatFact()">🐾 Get Cat Fact</button>
                    </div>
                </div>

                <div id="response" class="response-container">
                    <div style="text-align: center; color: #666;">
                        <p>👆 Click any button above to make an API request!</p>
                        <p style="font-size: 0.9em; margin-top: 5px;">Watch the magic happen in real-time</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const responseContainer = document.getElementById('response');

        function showLoading(message = 'Making API request...') {
            responseContainer.innerHTML = `
                <div class="loading">
                    <div class="spinner"></div>
                    <span>${message}</span>
                </div>
            `;
        }

        function showResponse(data, status = 200, url = '') {
            const statusClass = status >= 400 ? (status >= 500 ? 'status-500' : 'status-400') : 'status-200';
            
            responseContainer.innerHTML = `
                <div class="response-content">
                    <div class="status-badge ${statusClass}">Status: ${status}</div>
                    ${url ? `<p style="font-size: 0.9em; color: #666; margin-bottom: 15px;"><strong>URL:</strong> ${url}</p>` : ''}
                    <div class="json-output">${JSON.stringify(data, null, 2)}</div>
                </div>
            `;
        }

        function showError(error, url = '') {
            responseContainer.innerHTML = `
                <div class="response-content">
                    <div class="status-badge status-400">Error</div>
                    ${url ? `<p style="font-size: 0.9em; color: #666; margin-bottom: 15px;"><strong>URL:</strong> ${url}</p>` : ''}
                    <div style="color: #d63384; padding: 15px; background: #f8d7da; border-radius: 8px;">
                        <strong>Error:</strong> ${error.message || error}
                    </div>
                </div>
            `;
        }

        async function fetchPosts() {
            const postId = document.getElementById('postId').value;
            const url = postId 
                ? `https://jsonplaceholder.typicode.com/posts/${postId}`
                : 'https://jsonplaceholder.typicode.com/posts?_limit=5';

            showLoading('Fetching posts from JSONPlaceholder...');

            try {
                const response = await fetch(url);
                const data = await response.json();
                
                if (!response.ok) {
                    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                }
                
                showResponse(data, response.status, url);
            } catch (error) {
                showError(error, url);
            }
        }

        async function fetchQuote() {
            const category = document.getElementById('category').value;
            const url = 'https://api.quotable.io/random' + (category ? `?tags=${category}` : '');

            showLoading('Fetching inspirational quote...');

            try {
                const response = await fetch(url);
                const data = await response.json();
                
                if (!response.ok) {
                    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                }
                
                showResponse(data, response.status, url);
            } catch (error) {
                showError(error, url);
            }
        }

        async function fetchCatFact() {
            const url = 'https://catfact.ninja/fact';

            showLoading('Fetching amazing cat fact...');

            try {
                const response = await fetch(url);
                const data = await response.json();
                
                if (!response.ok) {
                    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                }
                
                showResponse(data, response.status, url);
            } catch (error) {
                showError(error, url);
            }
        }

        // Add some interactivity
        document.addEventListener('DOMContentLoaded', function() {
            // Add hover effects to cards
            const cards = document.querySelectorAll('.api-card');
            cards.forEach(card => {
                card.addEventListener('mouseenter', function() {
                    this.style.transform = 'translateY(-5px)';
                });
                
                card.addEventListener('mouseleave', function() {
                    this.style.transform = 'translateY(0)';
                });
            });

            // Add enter key support for inputs
            document.getElementById('postId').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    fetchPosts();
                }
            });
        });
    </script>
</body>
</html>