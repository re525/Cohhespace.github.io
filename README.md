# Cohhespace.github.io
Cohhespace is great 
<!-- CohheSpace - Social Network Frontend Template -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>CohheSpace - Connect Freely</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background-color: #0d0d0d;
      color: #fff;
      background-image: url('https://www.transparenttextures.com/patterns/dark-fish-skin.png'), url('https://cdn.pixabay.com/photo/2017/06/29/05/12/roses-2456634_1280.jpg');
      background-blend-mode: overlay;
      background-size: cover;
    }
    header {
      background-color: #1a1a1a;
      padding: 20px;
      text-align: center;
      border-bottom: 1px solid #444;
    }
    nav {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 20px;
      margin: 15px 0;
    }
    nav a {
      color: #ff4c4c;
      text-decoration: none;
      font-weight: bold;
    }
    .content {
      padding: 20px;
      max-width: 900px;
      margin: auto;
    }
    .section {
      background-color: #1c1c1c;
      padding: 20px;
      margin: 20px 0;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(255, 76, 76, 0.2);
    }
    footer {
      background-color: #111;
      text-align: center;
      padding: 15px;
      font-size: 14px;
      color: #aaa;
    }
    .security {
      color: #0f0;
      font-weight: bold;
    }
    iframe {
      border: none;
      width: 100%;
      height: 400px;
      margin-top: 10px;
    }
    canvas {
      border: 1px solid #444;
      background-color: #fff;
      display: block;
      margin: 10px auto;
    }
    .controls {
      text-align: center;
      margin: 10px 0;
    }
    .controls button, .controls input {
      margin: 5px;
      padding: 10px;
      font-size: 16px;
    }
  </style>
</head>
<body>
  <header>
    <h1>CohheSpace</h1>
    <p>Connect with Friends | Share | Explore</p>
  </header>

  <nav>
    <a href="#">Home</a>
    <a href="#">Posts</a>
    <a href="#">Photos</a>
    <a href="#">Reels</a>
    <a href="#">Messages</a>
    <a href="#">Games</a>
    <a href="#">Explore</a>
    <a href="#">Profile</a>
    <a href="#">Owner Panel</a>
  </nav>

  <div class="content">
    <div class="section">
      <h2>Welcome to CohheSpace</h2>
      <p>Post updates, share pictures, chat with friends, and explore the community.</p>
    </div>

    <div class="section">
      <h2>Color & Draw</h2>
      <p>Unleash your creativity! Use the canvas below to draw and color freely.</p>
      <div class="controls">
        <label for="colorPicker">Pick a color: </label>
        <input type="color" id="colorPicker" value="#000000">
        <label for="brushSize">Brush size: </label>
        <input type="number" id="brushSize" min="1" max="50" value="5">
        <button id="clearCanvas">Clear Canvas</button>
      </div>
      <canvas id="drawingCanvas" width="800" height="400"></canvas>
    </div>

    <div class="section">
      <h2>AI Assistant in Chat</h2>
      <p>Get instant answers and help from our built-in AI chatbot directly in your messages. Just ask questions, and it replies instantly!</p>
    </div>

    <div class="section">
      <h2>Explore Gallery</h2>
      <p>Discover user-uploaded images from all over the platform, sorted by popularity, tags, and date.</p>
      <iframe src="https://unsplash.com/embed"></iframe>
    </div>

    <footer>
      &copy; 2025 CohheSpace. All rights reserved.
    </footer>
  </div>

  <script>
    const canvas = document.getElementById('drawingCanvas');
    const ctx = canvas.getContext('2d');
    const colorPicker = document.getElementById('colorPicker');
    const brushSize = document.getElementById('brushSize');
    const clearCanvasButton = document.getElementById('clearCanvas');

    let painting = false;

    function startPosition(e) {
      painting = true;
      draw(e);
    }

    function endPosition() {
      painting = false;
      ctx.beginPath();
    }

    function draw(e) {
      if (!painting) return;

      ctx.lineWidth = brushSize.value;
      ctx.lineCap = 'round';
      ctx.strokeStyle = colorPicker.value;

      ctx.lineTo(e.clientX - canvas.offsetLeft, e.clientY - canvas.offsetTop);
      ctx.stroke();
      ctx.beginPath();
      ctx.moveTo(e.clientX - canvas.offsetLeft, e.clientY - canvas.offsetTop);
    }

    canvas.addEventListener('mousedown', startPosition);
    canvas.addEventListener('mouseup', endPosition);
    canvas.addEventListener('mousemove', draw);

    clearCanvasButton.addEventListener('click', () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    });
  </script>
</body>
</html>
const express = require('express');
const bodyParser = require('body-parser');
const session = require('express-session');

const app = express();
app.use(bodyParser.urlencoded({ extended: true }));
app.use(session({ secret: 'secret-key', resave: false, saveUninitialized: true }));

const users = {}; // In-memory user storage (use a database in production)

// Serve static files
app.use(express.static('public'));

// Signup route
app.post('/signup', (req, res) => {
  const { username, password } = req.body;
  if (users[username]) {
    return res.status(400).send('Username already exists!');
  }
  users[username] = { password, theme: 'space' }; // Default theme
  res.send('Signup successful!');
});

// Login route
app.post('/login', (req, res) => {
  const { username, password } = req.body;
  const user = users[username];
  if (!user || user.password !== password) {
    return res.status(400).send('Invalid credentials!');
  }
  req.session.user = username;
  res.send('Login successful!');
});

// Update theme route
app.post('/theme', (req, res) => {
  const { theme } = req.body;
  const user = users[req.session.user];
  if (user) {
    user.theme = theme;
    return res.send('Theme updated!');
  }
  res.status(401).send('Unauthorized!');
});

// Get user data
app.get('/me', (req, res) => {
  const user = users[req.session.user];
  if (user) {
    return res.json(user);
  }
  res.status(401).send('Unauthorized!');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
body.space
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  text-align: center;
  max-width: 600px;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

body.space {
  background: url('space.jpg') no-repeat center center / cover;
  color: white;
}

body.gothic {
  background: black;
  color: white;
}

body.dark {
  background: #121212;
  color: grey;
}

body.blue-ciel {
  background: linear-gradient(to bottom, #87CEEB, #4682B4);
  color: white;
}
