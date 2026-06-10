<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Study Planner</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="planner-container">
        <h1>Smart Study Planner</h1>
        
        <!-- Dashboard Section -->
        <div class="dashboard">
            <div class="stat-card">
                <h3>Study Hours</h3>
                <p id="total-study">0 hrs</p>
            </div>
            <div class="stat-card">
                <h3>Break Hours</h3>
                <p id="total-break">0 hrs</p>
            </div>
            <div class="stat-card">
                <h3>Balance Score</h3>
                <p id="balance-score" class="score-good">100%</p>
            </div>
        </div>

        <!-- Input Form Section -->
        <div class="form-section">
            <h2>Add New Session</h2>
            <div class="input-group">
                <input type="text" id="topic-input" placeholder="Topic / Subject Name">
                <input type="number" id="study-input" placeholder="Study (Hours)" min="0" step="0.5">
                <input type="number" id="break-input" placeholder="Break (Hours)" min="0" step="0.5">
                <button id="add-btn">Add Session</button>
            </div>
        </div>

        <!-- Schedule List Section -->
        <div class="schedule-section">
            <h2>Your Study Schedule</h2>
            <ul id="session-list">
                <!-- JavaScript will add tasks here dynamically -->
            </ul>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
    background-color: #f0f2f5;
    color: #333;
    padding: 20px;
}

.planner-container {
    max-width: 800px;
    margin: 0 auto;
    background: #ffffff;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

h1 {
    text-align: center;
    color: #2c3e50;
    margin-bottom: 25px;
}

h2 {
    color: #34495e;
    margin-bottom: 15px;
    font-size: 1.2rem;
}

/* Dashboard Styles */
.dashboard {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 15px;
    margin-bottom: 30px;
}

.stat-card {
    background: #f8f9fa;
    padding: 15px;
    border-radius: 8px;
    text-align: center;
    border: 1px solid #e9ecef;
}

.stat-card h3 {
    font-size: 0.9rem;
    color: #7f8c8d;
    margin-bottom: 5px;
}

.stat-card p {
    font-size: 1.5rem;
    font-weight: bold;
}

.score-good { color: #2ecc71; }
.score-warning { color: #e67e22; }
.score-bad { color: #e74c3c; }

/* Input Form Styles */
.form-section {
    background: #f8f9fa;
    padding: 20px;
    border-radius: 8px;
    margin-bottom: 30px;
}

.input-group {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
}

input {
    flex: 1;
    min-width: 120px;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 0.95rem;
}

button {
    background: #3498db;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-weight: bold;
    transition: background 0.2s;
}

button:hover {
    background: #2980b9;
}

/* Schedule List Styles */
.schedule-section {
    margin-top: 20px;
}

ul {
    list-style: none;
}

li {
    background: #fff;
    border: 1px solid #e0e0e0;
    padding: 15px;
    border-radius: 6px;
    margin-bottom: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.session-info {
    display: flex;
    flex-direction: column;
}

.session-title {
    font-weight: bold;
    color: #2c3e50;
}

.session-meta {
    font-size: 0.85rem;
    color: #7f8c8d;
    margin-top: 4px;
}

.delete-btn {
    background: #e74c3c;
    padding: 6px 12px;
    font-size: 0.85rem;
}

.delete-btn:hover {
    background: #c0392b;
}
// Grab DOM elements
const topicInput = document.getElementById('topic-input');
const studyInput = document.getElementById('study-input');
const breakInput = document.getElementById('break-input');
const addBtn = document.getElementById('add-btn');
const sessionList = document.getElementById('session-list');

const totalStudyEl = document.getElementById('total-study');
const totalBreakEl = document.getElementById('total-break');
const balanceScoreEl = document.getElementById('balance-score');

// App state data storage
let sessions = [];

// Event listeners
addBtn.addEventListener('click', addSession);

// Core Functions
function addSession() {
    const topic = topicInput.value.trim();
    const studyHours = parseFloat(studyInput.value);
    const breakHours = parseFloat(breakInput.value);

    // Basic Validation check
    if (!topic || isNaN(studyHours) || isNaN(breakHours)) {
        alert("Please fill in all inputs with valid numbers!");
        return;
    }

    // Create session object
    const session = {
        id: Date.now(),
        topic: topic,
        studyHours: studyHours,
        breakHours: breakHours
    };

    // Update state and clear inputs
    sessions.push(session);
    clearInputs();
    updateUI();
}

function deleteSession(id) {
    sessions = sessions.filter(session => session.id !== id);
    updateUI();
}

function clearInputs() {
    topicInput.value = '';
    studyInput.value = '';
    breakInput.value = '';
}

function updateUI() {
    // 1. Clear old DOM elements inside list
    sessionList.innerHTML = '';

    let totalStudy = 0;
    let totalBreak = 0;

    // 2. Render each item from array to page
    sessions.forEach(session => {
        totalStudy += session.studyHours;
        totalBreak += session.breakHours;

        const li = document.createElement('li');
        li.innerHTML = `
            <div class="session-info">
                <span class="session-title">${session.topic}</span>
                <span class="session-meta">Study: ${session.studyHours} hrs | Break: ${session.breakHours} hrs</span>
            </div>
            <button class="delete-btn" onclick="deleteSession(${session.id})">Remove</button>
        `;
        sessionList.appendChild(li);
    });

    // 3. Update Text on Top Statistics Cards
    totalStudyEl.innerText = `${totalStudy} hrs`;
    totalBreakEl.innerText = `${totalBreak} hrs`;

    // 4. Calculate Smart Balance Score 
    // Healthy ideal ratio is 1 hour of break for every 4 hours of study (25% break ratio)
    if (totalStudy === 0) {
        balanceScoreEl.innerText = "100%";
        balanceScoreEl.className = "score-good";
        return;
    }

    const actualRatio = totalBreak / totalStudy;
    const targetRatio = 0.25; // 15 mins break per 1 hour study
    
    // Calculate how close the user is to the healthy balance target
    let variance = Math.abs(actualRatio - targetRatio);
    let score = Math.max(0, 100 - Math.round(variance * 100));

    balanceScoreEl.innerText = `${score}%`;

    // Visual feedback color styling
    if (score >= 80) {
        balanceScoreEl.className = "score-good";
    } else if (score >= 50) {
        balanceScoreEl.className = "score-warning";
    } else {
        balanceScoreEl.className = "score-bad";
    }
}
