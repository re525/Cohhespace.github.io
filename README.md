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
import React, { useState } from 'react';
import axios from 'axios';

function App() {
    const [token, setToken] = useState('');
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');
    const [question, setQuestion] = useState('');
    const [aiResponse, setAiResponse] = useState('');
    const [file, setFile] = useState(null);

    const register = async () => {
        await axios.post('/register', { username, password });
        alert('User registered!');
    };

    const login = async () => {
        const res = await axios.post('/login', { username, password });
        setToken(res.data.token);
        alert('Logged in successfully!');
    };

    const askAI = async () => {
        const res = await axios.post('/ask-ai', { question }, {
            headers: { Authorization: token }
        });
        setAiResponse(res.data.answer);
    };

    const uploadFile = async () => {
        const formData = new FormData();
        formData.append('file', file);
        await axios.post('/upload', formData, {
            headers: {
                Authorization: token,
                'Content-Type': 'multipart/form-data'
            }
        });
        alert('File uploaded!');
    };

    return (
        <div>
            <h1>Social AI Platform</h1>
            <div>
                <h2>Register/Login</h2>
                <input placeholder="Username" onChange={e => setUsername(e.target.value)} />
                <input type="password" placeholder="Password" onChange={e => setPassword(e.target.value)} />
                <button onClick={register}>Register</button>
                <button onClick={login}>Login</button>
            </div>
            <div>
                <h2>AI Interaction</h2>
                <textarea placeholder="Ask a question..." onChange={e => setQuestion(e.target.value)} />
                <button onClick={askAI}>Ask AI</button>
                <p>AI says: {aiResponse}</p>
            </div>
            <div>
                <h2>File Upload</h2>
                <input type="file" onChange={e => setFile(e.target.files[0])} />
                <button onClick={uploadFile}>Upload</button>
            </div>
            <div>
                <h2>Games</h2>
                <ul>
                    <li>Game 1</li>
                    <li>Game 2</li>
                    <li>Game 3</li>
                    <li>Game 4</li>
                    <li>Game 5</li>
                </ul>
            </div>
        </div>
    );
}

export default App;
const express = require('express');
const multer = require('multer');
const bodyParser = require('body-parser');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');
const app = express();
const port = 3000;

app.use(bodyParser.json());

// Mock database
let users = [];
let posts = [];

// JWT secret key
const JWT_SECRET = "your_jwt_secret";

// Middleware for authentication
function authenticateToken(req, res, next) {
    const token = req.headers['authorization'];
    if (!token) return res.sendStatus(401);

    jwt.verify(token, JWT_SECRET, (err, user) => {
        if (err) return res.sendStatus(403);
        req.user = user;
        next();
    });
}

// User Registration
app.post('/register', async (req, res) => {
    const { username, password } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    users.push({ username, password: hashedPassword });
    res.status(201).send({ message: "User registered successfully" });
});

// User Login
app.post('/login', async (req, res) => {
    const { username, password } = req.body;
    const user = users.find(u => u.username === username);
    if (!user || !(await bcrypt.compare(password, user.password))) {
        return res.status(401).send({ error: "Invalid credentials" });
    }
    const token = jwt.sign({ username }, JWT_SECRET, { expiresIn: '1h' });
    res.json({ token });
});

// AI Interaction
app.post('/ask-ai', authenticateToken, (req, res) => {
    const { question } = req.body;
    // Simple AI placeholder
    const answer = `AI Response to your question: "${question}"`;
    res.json({ answer });
});

// File Upload
const upload = multer({ dest: 'uploads/' });
app.post('/upload', authenticateToken, upload.single('file'), (req, res) => {
    res.send({ message: "File uploaded successfully", file: req.file });
});

// Placeholder for games
app.get('/games', (req, res) => {
    res.json({ games: ["Game 1", "Game 2", "Game 3", "Game 4", "Game 5"] });
});

app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
<!-- Signup Form -->
<input type="email" id="signup-email" placeholder="Email">
<input type="password" id="signup-password" placeholder="Password">
<button onclick="signUp(document.getElementById('signup-email').value, document.getElementById('signup-password').value)">Sign Up</button>

<!-- Login Form -->
<input type="email" id="login-email" placeholder="Email">
<input type="password" id="login-password" placeholder="Password">
<button onclick="signIn(document.getElementById('login-email').value, document.getElementById('login-password').value)">Log In</button>
<!-- Add this to your HTML file, inside <head> -->
<script type="module">
  // Import the functions you need from the SDKs
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.11.0/firebase-app.js";
  import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/10.11.0/firebase-auth.js";

  const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
  };

  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);

  // Example: sign up user
  function signUp(email, password) {
    createUserWithEmailAndPassword(auth, email, password)
      .then(userCredential => {
        alert("Signup successful!");
      })
      .catch(error => {
        alert(error.message);
      });
  }

  // Example: sign in user
  function signIn(email, password) {
    signInWithEmailAndPassword(auth, email, password)
      .then(userCredential => {
        alert("Login successful!");
      })
      .catch(error => {
        alert(error.message);
      });
  }

  // Hook these functions to your login/signup forms
</script>
