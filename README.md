# Memory-and-reflex-test
​A web-based cognitive lab measuring age-related memory capacity, reaction speed, and metabolic factors (caffeine/health) with real-time analytics and line chart visualization
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TÜBİTAK 2204-A Cognitive Performance Lab</title>
    <!-- Chart.js Line Chart Library -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * { box-sizing: border-box; user-select: none; }
        body {
            margin: 0;
            padding: 15px;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: #0f172a;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .box {
            width: 100%;
            max-width: 520px;
            background: #1e293b;
            border-radius: 16px;
            padding: 20px;
            text-align: center;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        h2 { margin-top: 0; color: #38bdf8; font-size: 20px; }
        .form-group {
            margin-bottom: 10px;
            text-align: left;
            font-size: 13px;
        }
        label { display: block; margin-bottom: 3px; color: #cbd5e1; font-weight: bold; }
        input, select {
            width: 100%;
            padding: 8px;
            font-size: 14px;
            border-radius: 6px;
            border: 1px solid #475569;
            background: #0f172a;
            color: #fff;
        }
        button {
            width: 100%;
            padding: 10px;
            font-size: 15px;
            font-weight: bold;
            border: none;
            border-radius: 6px;
            background: #10b981;
            color: #fff;
            cursor: pointer;
            margin-top: 10px;
        }
        button:active { transform: scale(0.98); }
        .info {
            display: flex;
            justify-content: space-between;
            background: #0f172a;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 12px;
            font-size: 13px;
            font-weight: bold;
        }
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(75px, 1fr));
            gap: 8px;
            margin-bottom: 12px;
        }
        .card {
            height: 75px;
            border-radius: 10px;
            opacity: 0.35;
            cursor: pointer;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 18px;
            font-weight: bold;
            color: #fff;
            text-shadow: 0 0 4px rgba(0,0,0,0.8);
            transition: opacity 0.15s, transform 0.1s;
        }
        .card.active {
            opacity: 1;
            transform: scale(1.05);
            box-shadow: 0 0 15px currentColor;
        }
        #status { font-size: 14px; font-weight: bold; color: #38bdf8; margin: 8px 0; min-height: 20px; }
        .panel { display: none; }
        .results {
            background: #0f172a;
            padding: 12px;
            border-radius: 8px;
            text-align: left;
            font-size: 13px;
            line-height: 1.5;
            max-height: 400px;
            overflow-y: auto;
        }
        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin: 10px 0;
            font-size: 12px;
        }
        .data-table th, .data-table td {
            border: 1px solid #334155;
            padding: 8px;
            text-align: center;
        }
        .data-table th {
            background-color: #1e293b;
            color: #38bdf8;
        }
        .data-table td.highlight {
            font-weight: bold;
            color: #10b981;
        }
        .chart-box {
            background: #0f172a;
            padding: 10px;
            border-radius: 8px;
            margin-top: 10px;
        }
        .leaderboard {
            margin-top: 10px;
            background: #0f172a;
            padding: 10px;
            border-radius: 8px;
            text-align: left;
            font-size: 12px;
        }
    </style>
</head>
<body>

<div class="box">
    <h2>TÜBİTAK Research Lab</h2>

    <!-- SETUP PANEL -->
    <div id="setupPanel" class="panel" style="display: block;">
        <p style="font-size: 13px; color: #94a3b8;">Participant Data Collection Form:</p>
        <div class="form-group">
            <label>Full Name / Nickname:</label>
            <input type="text" id="userName" value="Çağatay">
        </div>
        <div class="form-group">
            <label>Age:</label>
            <input type="number" id="userAge" value="15" min="5" max="100">
        </div>
        <div class="form-group">
            <label>Gender:</label>
            <select id="userGender">
                <option value="Male">Male</option>
                <option value="Female">Female</option>
                <option value="Other">Other / Prefer not to say</option>
            </select>
        </div>
        <div class="form-group">
            <label>Health Status / Medical Condition:</label>
            <input type="text" id="userDisease" placeholder="None or specify...">
        </div>
        <div class="form-group">
            <label>Substance / Medication / Caffeine Intake:</label>
            <input type="text" id="userSubstance" placeholder="None or specify (e.g., Coffee)...">
        </div>
        <button id="startBtn">Start Experiment</button>
    </div>

    <!-- GAME PANEL -->
    <div id="gamePanel" class="panel">
        <div class="info">
            <span>Score: <span id="scoreText">0</span></span>
            <span>Cards: <span id="cardCountText">4</span></span>
            <span>Speed: <span id="speedLevelText">1x</span></span>
        </div>

        <div class="grid" id="cardContainer"></div>

        <div id="status">Get Ready...</div>
    </div>

    <!-- BREAK PANEL (Level 20) -->
    <div id="breakPanel" class="panel">
        <h3 style="color: #f59e0b;">Level 20 Cognitive Break & Focus Test</h3>
        <p style="font-size: 13px; color: #cbd5e1;">Short mental resting phase. Continue when ready!</p>
        <button id="continueBtn">Continue Experiment</button>
    </div>

    <!-- RESULT PANEL -->
    <div id="resultPanel" class="panel">
        <h3>Scientific Analysis Report</h3>
        <div class="results" id="resBox"></div>

        <!-- Line Chart Box -->
        <div class="chart-box">
            <canvas id="reactionChart"></canvas>
        </div>

        <div class="leaderboard">
            <strong>Project Leaderboard:</strong>
            <div id="leaderboardList">No records yet.</div>
        </div>
        <button onclick="location.reload()">New Experiment</button>
    </div>
</div>

<script>
    const basePalette = ["#ef4444", "#3b82f6", "#10b981", "#f59e0b", "#8b5cf6", "#ec4899", "#06b6d4", "#eab308"];
    const extraSymbols = ["A", "B", "C", "D", "E", "F", "1", "2", "3", "4", "5", "6", "7", "8", "9", "X", "Y", "Z"];

    let sequence = [];
    let playerSeq = [];
    let score = 0;
    let userData = {};
    let times = [];
    let roundReactionTimes = [];
    let lastTime = 0;
    let canClick = false;
    let activeCards = [];
    let myChart = null;

    document.getElementById('startBtn').onclick = function() {
        userData.name = document.getElementById('userName').value || "Participant";
        userData.age = parseInt(document.getElementById('userAge').value) || 15;
        userData.gender = document.getElementById('userGender').value;
        userData.disease = document.getElementById('userDisease').value || "None";
        userData.substance = document.getElementById('userSubstance').value || "None";

        document.getElementById('setupPanel').style.display = 'none';
        document.getElementById('gamePanel').style.display = 'block';
        
        sequence = [];
        score = 0;
        times = [];
        roundReactionTimes = [];
        startNextRound();
    };

    document.getElementById('continueBtn').onclick = function() {
        document.getElementById('breakPanel').style.display = 'none';
        document.getElementById('gamePanel').style.display = 'block';
        startNextRound();
    };

    function renderCards() {
        let targetCardCount = 4 + Math.floor(score / 5);
        document.getElementById('cardCountText').innerText = targetCardCount;

        while (activeCards.length < targetCardCount) {
            let index = activeCards.length;
            let item = {};
            if (index < basePalette.length) {
                item = { id: index, bg: basePalette[index], label: "" };
            } else {
                let symIndex = index - basePalette.length;
                let sym = extraSymbols[symIndex % extraSymbols.length];
                item = { id: index, bg: "#334155", label: sym };
            }
            activeCards.push(item);
        }

        const container = document.getElementById('cardContainer');
        container.innerHTML = '';
        activeCards.forEach((c, idx) => {
            const card = document.createElement('div');
            card.className = 'card';
            card.style.backgroundColor = c.bg;
            if (c.label) card.innerText = c.label;
            card.onclick = () => handleCardClick(idx);
            container.appendChild(card);
        });
    }

    function getSpeedInterval() {
        let speedMultiplier = Math.floor(score / 10);
        document.getElementById('speedLevelText').innerText = (1 + speedMultiplier * 0.2).toFixed(1) + "x";
        return Math.max(180, 650 - (speedMultiplier * 80));
    }

    function startNextRound() {
        if (score > 0 && score % 20 === 0) {
            document.getElementById('gamePanel').style.display = 'none';
            document.getElementById('breakPanel').style.display = 'block';
            return;
        }

        playerSeq = [];
        document.getElementById('scoreText').innerText = score;
        renderCards();
        sequence.push(Math.floor(Math.random() * activeCards.length));
        playSequence();
    }

    function playSequence() {
        canClick = false;
        document.getElementById('status').innerText = "Showing sequence...";
        let i = 0;
        const cardElements = document.getElementById('cardContainer').children;
        let currentSpeed = getSpeedInterval();

        const timer = setInterval(() => {
            flashCard(cardElements[sequence[i]], Math.round(currentSpeed * 0.5));
            i++;
            if (i >= sequence.length) {
                clearInterval(timer);
                setTimeout(() => {
                    canClick = true;
                    document.getElementById('status').innerText = "Your turn!";
                    lastTime = performance.now();
                }, 200);
            }
        }, currentSpeed);
    }

    function flashCard(element, duration) {
        if (!element) return;
        element.classList.add('active');
        setTimeout(() => element.classList.remove('active'), duration);
    }

    function handleCardClick(idx) {
        if (!canClick) return;

        let now = performance.now();
        let diff = now - lastTime;
        times.push(diff);
        lastTime = now;

        const cardElements = document.getElementById('cardContainer').children;
        flashCard(cardElements[idx], 220);
        playerSeq.push(idx);

        let pos = playerSeq.length - 1;
        if (playerSeq[pos] !== sequence[pos]) {
            endGame();
            return;
        }

        if (playerSeq.length === sequence.length) {
            let turnAvg = Math.round(times.slice(-sequence.length).reduce((a, b) => a + b, 0) / sequence.length);
            roundReactionTimes.push(turnAvg);

            score++;
            canClick = false;
            document.getElementById('status').innerText = "Correct! Next level...";
            setTimeout(startNextRound, 700);
        }
    }

    function endGame() {
        canClick = false;
        document.getElementById('gamePanel').style.display = 'none';
        document.getElementById('resultPanel').style.display = 'block';

        let avgSpd = times.length > 0 ? Math.round(times.reduce((a, b) => a + b, 0) / times.length) : 0;
        
        let targetScore = userData.age <= 12 ? 6 : (userData.age <= 18 ? 10 : 12);
        let targetSpeed = userData.age <= 12 ? 650 : (userData.age <= 18 ? 420 : 380);

        let scoreDiff = score >= targetScore ? "Above Average" : "Below Average";
        let speedDiff = avgSpd <= targetSpeed ? "Above Average (Faster)" : "Above Average (Slower)";

        let scientificContext = "";
        if (userData.substance.toLowerCase().includes("coffee") || userData.substance.toLowerCase().includes("caffeine") || userData.substance.toLowerCase().includes("kafein") || userData.substance.toLowerCase().includes("energy")) {
            scientificContext = "<b>Caffeine Impact Analysis:</b> Ingested stimulants/caffeine may have blocked adenosine receptors, enhancing central nervous system arousal and optimizing motor reaction time.";
        } else if (userData.disease.toLowerCase() !== "none" && userData.disease.toLowerCase() !== "yok" && userData.disease.trim() !== "") {
            scientificContext = `<b>Clinical Assessment Note:</b> Reported condition (${userData.disease}) might have indirectly influenced cognitive processing speed and fatigue threshold.`;
        } else {
            scientificContext = "<b>Metabolic Status:</b> The test was completed under standard baseline physiological conditions.";
        }

        let reportHTML = `
            <b>Participant:</b> ${userData.name} (${userData.age} yrs, ${userData.gender})<br>
            <b>Substance / Medical Note:</b> ${userData.substance} / ${userData.disease}
            
            <table class="data-table">
                <thead>
                    <tr>
                        <th>Metric</th>
                        <th>Age Group Avg</th>
                        <th>Your Value</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td><b>Memory (Score)</b></td>
                        <td>${targetScore}</td>
                        <td class="highlight">${score}</td>
                        <td>${scoreDiff}</td>
                    </tr>
                    <tr>
                        <td><b>Reflex (ms)</b></td>
                        <td>${targetSpeed} ms</td>
                        <td class="highlight">${avgSpd} ms</td>
                        <td>${speedDiff}</td>
                    </tr>
                </tbody>
            </table>

            <b>Scientific Interpretation:</b><br>${scientificContext}
        `;

        document.getElementById('resBox').innerHTML = reportHTML;

        renderLineChart(roundReactionTimes);

        let leaderboard = JSON.parse(localStorage.getItem('tubitak_leaderboard_en') || '[]');
        leaderboard.push({ name: userData.name, score: score, speed: avgSpd });
        leaderboard.sort((a, b) => b.score - a.score || a.speed - b.speed);
        localStorage.setItem('tubitak_leaderboard_en', JSON.stringify(leaderboard.slice(0, 5)));

        let boardHTML = "";
        leaderboard.slice(0, 5).forEach((item, index) => {
            boardHTML += `<div>${index + 1}. <b>${item.name}</b> - Score: ${item.score} | Reflex: ${item.speed}ms</div>`;
        });
        document.getElementById('leaderboardList').innerHTML = boardHTML;
    }

    function renderLineChart(dataPoints) {
        const ctx = document.getElementById('reactionChart').getContext('2d');
        if (myChart) myChart.destroy();

        const labels = dataPoints.map((_, i) => `Level ${i + 1}`);

        myChart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: labels.length > 0 ? labels : ['Level 1'],
                datasets: [{
                    label: 'Reaction Speed (ms)',
                    data: dataPoints.length > 0 ? dataPoints : [0],
                    borderColor: '#38bdf8',
                    backgroundColor: 'rgba(56, 189, 248, 0.2)',
                    borderWidth: 2,
                    fill: true,
                    tension: 0.3
                }]
            },
            options: {
                responsive: true,
                plugins: {
                    title: {
                        display: true,
                        text: 'Reaction Speed Progression by Level',
                        color: '#cbd5e1'
                    },
                    legend: {
                        labels: { color: '#cbd5e1' }
                    }
                },
                scales: {
                    x: {
                        ticks: { color: '#94a3b8' },
                        grid: { color: '#334155' }
                    },
                    y: {
                        ticks: { color: '#94a3b8' },
                        grid: { color: '#334155' }
                    }
                }
            }
        });
    }
</script>

</body>
</html>
