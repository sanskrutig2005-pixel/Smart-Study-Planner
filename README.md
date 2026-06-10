<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Smart Study Planner</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body{
    background:#f0f2f5;
    padding:20px;
    color:#333;
}

.planner-container{
    max-width:800px;
    margin:auto;
    background:#fff;
    padding:30px;
    border-radius:12px;
    box-shadow:0 4px 15px rgba(0,0,0,0.1);
}

h1{
    text-align:center;
    color:#2c3e50;
    margin-bottom:25px;
}

h2{
    margin-bottom:15px;
    color:#34495e;
}

/* Dashboard */
.dashboard{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:15px;
    margin-bottom:30px;
}

.stat-card{
    background:#f8f9fa;
    padding:15px;
    border-radius:8px;
    text-align:center;
    border:1px solid #e9ecef;
}

.stat-card h3{
    color:#7f8c8d;
    margin-bottom:5px;
}

.stat-card p{
    font-size:1.5rem;
    font-weight:bold;
}

.score-good{
    color:#2ecc71;
}

.score-warning{
    color:#f39c12;
}

.score-bad{
    color:#e74c3c;
}

/* Form */
.form-section{
    background:#f8f9fa;
    padding:20px;
    border-radius:8px;
    margin-bottom:30px;
}

.input-group{
    display:flex;
    flex-wrap:wrap;
    gap:10px;
}

input{
    flex:1;
    min-width:120px;
    padding:10px;
    border:1px solid #ccc;
    border-radius:6px;
}

button{
    background:#3498db;
    color:white;
    border:none;
    padding:10px 20px;
    border-radius:6px;
    cursor:pointer;
    font-weight:bold;
}

button:hover{
    background:#2980b9;
}

/* Schedule List */
ul{
    list-style:none;
}

li{
    background:#fff;
    border:1px solid #e0e0e0;
    padding:15px;
    border-radius:6px;
    margin-bottom:10px;
    display:flex;
    justify-content:space-between;
    align-items:center;
}

.session-info{
    display:flex;
    flex-direction:column;
}

.session-title{
    font-weight:bold;
    color:#2c3e50;
}

.session-meta{
    font-size:0.9rem;
    color:#7f8c8d;
}

.delete-btn{
    background:#e74c3c;
}

.delete-btn:hover{
    background:#c0392b;
}

@media(max-width:600px){
    .dashboard{
        grid-template-columns:1fr;
    }

    .input-group{
        flex-direction:column;
    }
}
</style>
</head>

<body>

<div class="planner-container">

    <h1>📚 Smart Study Planner</h1>

    <!-- Dashboard -->
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

    <!-- Form -->
    <div class="form-section">

        <h2>Add New Session</h2>

        <div class="input-group">
            <input type="text" id="topic-input" placeholder="Subject Name">
            <input type="number" id="study-input" placeholder="Study Hours" min="0" step="0.5">
            <input type="number" id="break-input" placeholder="Break Hours" min="0" step="0.5">
            <button id="add-btn">Add Session</button>
        </div>

    </div>

    <!-- Schedule -->
    <div class="schedule-section">

        <h2>Your Study Schedule</h2>

        <ul id="session-list"></ul>

    </div>

</div>

<script>
// DOM Elements
const topicInput = document.getElementById('topic-input');
const studyInput = document.getElementById('study-input');
const breakInput = document.getElementById('break-input');
const addBtn = document.getElementById('add-btn');
const sessionList = document.getElementById('session-list');

const totalStudyEl = document.getElementById('total-study');
const totalBreakEl = document.getElementById('total-break');
const balanceScoreEl = document.getElementById('balance-score');

// Data Storage
let sessions = [];

// Add Session Event
addBtn.addEventListener('click', addSession);

function addSession(){

    const topic = topicInput.value.trim();
    const studyHours = parseFloat(studyInput.value);
    const breakHours = parseFloat(breakInput.value);

    if(!topic || isNaN(studyHours) || isNaN(breakHours)){
        alert("Please fill all fields correctly!");
        return;
    }

    const session = {
        id: Date.now(),
        topic,
        studyHours,
        breakHours
    };

    sessions.push(session);

    clearInputs();
    updateUI();
}

function deleteSession(id){
    sessions = sessions.filter(
        session => session.id !== id
    );

    updateUI();
}

function clearInputs(){
    topicInput.value = "";
    studyInput.value = "";
    breakInput.value = "";
}

function updateUI(){

    sessionList.innerHTML = "";

    let totalStudy = 0;
    let totalBreak = 0;

    sessions.forEach(session => {

        totalStudy += session.studyHours;
        totalBreak += session.breakHours;

        const li = document.createElement("li");

        li.innerHTML = `
            <div class="session-info">
                <span class="session-title">${session.topic}</span>
                <span class="session-meta">
                    Study: ${session.studyHours} hrs |
                    Break: ${session.breakHours} hrs
                </span>
            </div>

            <button class="delete-btn"
            onclick="deleteSession(${session.id})">
            Remove
            </button>
        `;

        sessionList.appendChild(li);
    });

    totalStudyEl.innerText = totalStudy + " hrs";
    totalBreakEl.innerText = totalBreak + " hrs";

    if(totalStudy === 0){
        balanceScoreEl.innerText = "100%";
        balanceScoreEl.className = "score-good";
        return;
    }

    const actualRatio = totalBreak / totalStudy;
    const targetRatio = 0.25;

    const variance = Math.abs(actualRatio - targetRatio);

    const score = Math.max(
        0,
        100 - Math.round(variance * 100)
    );

    balanceScoreEl.innerText = score + "%";

    if(score >= 80){
        balanceScoreEl.className = "score-good";
    }
    else if(score >= 50){
        balanceScoreEl.className = "score-warning";
    }
    else{
        balanceScoreEl.className = "score-bad";
    }
}
</script>

</body>
</html>
