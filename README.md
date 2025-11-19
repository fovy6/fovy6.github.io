<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gym Tracker Pro</title>
    <style>
        :root {
            --primary: #3b82f6;
            --bg: #0f172a;
            --card: #1e293b;
            --card-highlight: #2a3855;
            --text: #f8fafc;
            --text-muted: #94a3b8;
            --danger: #ef4444;
            --success: #10b981;
            --warning: #f59e0b;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }

        /* Navigation Tabs */
        .nav-tabs {
            display: flex;
            background-color: var(--card);
            border-top: 1px solid #334155;
            padding-bottom: env(safe-area-inset-bottom);
        }

        .nav-item {
            flex: 1;
            text-align: center;
            padding: 15px;
            color: var(--text-muted);
            cursor: pointer;
            font-weight: bold;
            transition: 0.2s;
        }

        .nav-item.active {
            color: var(--primary);
            border-top: 3px solid var(--primary);
        }

        /* Main Scrollable Area */
        .content-area {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            padding-bottom: 80px; /* Space for bottom nav */
        }

        .tab-content { display: none; }
        .tab-content.active { display: block; }

        h1, h2 { margin: 0 0 15px 0; }
        h2 { font-size: 1.2rem; border-bottom: 1px solid #334155; padding-bottom: 8px; margin-top: 25px;}
        h3 { margin: 0; }

        /* Input Section */
        .input-card {
            background-color: var(--card);
            padding: 20px;
            border-radius: 16px;
            margin-bottom: 20px;
        }

        .mic-button {
            background-color: var(--primary);
            color: white;
            border: none;
            width: 100%;
            padding: 20px;
            font-size: 1.2rem;
            border-radius: 12px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-bottom: 15px;
        }

        .mic-button.listening { background-color: var(--danger); animation: pulse 1.5s infinite; }

        .form-group { margin-bottom: 12px; }
        label { display: block; color: var(--text-muted); font-size: 0.85rem; margin-bottom: 4px; }
        
        input, select {
            width: 100%;
            box-sizing: border-box;
            padding: 12px;
            background: #334155;
            border: 1px solid #475569;
            color: white;
            border-radius: 8px;
            font-size: 1rem;
        }

        .row { display: flex; gap: 10px; }

        .btn-add {
            background-color: var(--card-highlight);
            color: var(--primary);
            border: 1px solid var(--primary);
            width: 100%;
            padding: 12px;
            font-size: 1rem;
            border-radius: 8px;
            margin-top: 5px;
            font-weight: bold;
        }

        /* Current Session Styles */
        .current-session-box {
            border: 1px dashed #475569;
            border-radius: 12px;
            padding: 15px;
            margin-top: 20px;
            display: none; /* Hidden by default */
        }
        .current-session-box.active { display: block; }

        .session-item {
            display: flex;
            justify-content: space-between;
            padding: 8px 0;
            border-bottom: 1px solid #334155;
        }
        .session-item:last-child { border-bottom: none; }

        .btn-finish {
            background-color: var(--success);
            color: white;
            border: none;
            width: 100%;
            padding: 15px;
            font-size: 1.1rem;
            border-radius: 8px;
            margin-top: 15px;
            font-weight: bold;
        }

        /* History Cards */
        .workout-card {
            background-color: var(--card);
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 15px;
        }
        .workout-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            padding-bottom: 8px;
            border-bottom: 1px solid #334155;
        }
        .workout-date { color: var(--primary); font-weight: bold; font-size: 0.9rem; }
        
        .exercise-row {
            display: flex;
            justify-content: space-between;
            padding: 4px 0;
            font-size: 0.95rem;
        }
        .ex-name { font-weight: 500; }
        .ex-stats { color: var(--text-muted); }

        .btn-delete-workout {
            color: var(--danger);
            background: none;
            border: none;
            font-size: 0.8rem;
            padding: 5px;
        }

        /* Progress Styles */
        .progress-chart-item {
            background: var(--card);
            margin-bottom: 8px;
            padding: 12px;
            border-radius: 8px;
        }
        .bar-container {
            background: #334155;
            height: 8px;
            border-radius: 4px;
            margin-top: 8px;
            overflow: hidden;
        }
        .bar-fill {
            background: var(--primary);
            height: 100%;
            width: 0%;
            transition: width 0.5s ease;
        }

        /* Backup Section */
        .backup-section {
            margin-top: 40px;
            border-top: 1px solid #334155;
            padding-top: 20px;
            text-align: center;
        }
        .btn-export {
            background-color: var(--card-highlight);
            color: white;
            border: 1px solid #475569;
            padding: 10px 20px;
            border-radius: 8px;
            margin-right: 10px;
            font-size: 0.9rem;
        }
        .btn-import {
            background-color: var(--card-highlight);
            color: white;
            border: 1px solid #475569;
            padding: 10px 20px;
            border-radius: 8px;
            font-size: 0.9rem;
        }
        #fileInput { display: none; }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.02); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>

<div class="content-area">
    
    <!-- === LOG TAB === -->
    <div id="tab-log" class="tab-content active">
        <h1>Log Workout 🏋️‍♂️</h1>

        <!-- Input Card -->
        <div class="input-card">
            <button id="micBtn" class="mic-button">🎤 Tap to Speak</button>
            <p id="status" style="text-align: center; color: var(--text-muted); font-size: 0.85rem; min-height: 1.2em;">
                "Bench press 80kg for 5 reps"
            </p>

            <div class="form-group">
                <label>Exercise Name</label>
                <input type="text" id="exerciseInput" placeholder="e.g. Squat">
            </div>

            <div class="row">
                <div class="form-group" style="flex: 1;">
                    <label>Weight</label>
                    <input type="number" id="weightInput" placeholder="0">
                </div>
                <div class="form-group" style="flex: 1;">
                    <label>Reps</label>
                    <input type="number" id="repsInput" placeholder="0">
                </div>
            </div>

            <button class="btn-add" onclick="addToCurrentSession()">+ Add to Current Workout</button>
        </div>

        <!-- Current Session Staging Area -->
        <div id="currentSessionBox" class="current-session-box">
            <h3 style="margin:0 0 10px 0; font-size: 1rem; color: var(--text-muted);">Current Session</h3>
            <div id="sessionList"></div>
            <button class="btn-finish" onclick="finishWorkout()">✅ Finish & Save Workout</button>
        </div>

        <!-- Workout History -->
        <h2>Past Workouts</h2>
        <div id="historyList"></div>

        <!-- Backup Section -->
        <div class="backup-section">
            <h3 style="font-size: 1rem; margin-bottom: 10px; color: var(--text-muted);">Data Management</h3>
            <button class="btn-export" onclick="exportCSV()">⬇ Export CSV</button>
            <button class="btn-import" onclick="document.getElementById('fileInput').click()">⬆ Import CSV</button>
            <input type="file" id="fileInput" accept=".csv" onchange="importCSV(this)">
            <p style="font-size: 0.75rem; color: var(--text-muted); margin-top:10px;">Save your data before switching phones.</p>
        </div>
    </div>

    <!-- === PROGRESS TAB === -->
    <div id="tab-progress" class="tab-content">
        <h1>Progress 📈</h1>
        <div class="form-group">
            <label>Select Exercise</label>
            <select id="progressSelect" onchange="renderProgressChart()">
                <option value="">Select an exercise...</option>
            </select>
        </div>
        <div id="progressResults"></div>
    </div>

</div>

<!-- Navigation Bottom Bar -->
<div class="nav-tabs">
    <div class="nav-item active" onclick="switchTab('log')">Log Workout</div>
    <div class="nav-item" onclick="switchTab('progress')">Progress</div>
</div>

<script>
    // === STATE MANAGEMENT ===
    let currentSession = [];
    
    // --- Voice Recognition Setup ---
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    const micBtn = document.getElementById('micBtn');
    const statusText = document.getElementById('status');
    let recognition;

    if (SpeechRecognition) {
        recognition = new SpeechRecognition();
        recognition.continuous = false;
        recognition.lang = 'en-US';
        
        micBtn.addEventListener('click', toggleMic);

        recognition.onstart = () => {
            micBtn.classList.add('listening');
            micBtn.innerHTML = '🛑 Listening...';
        };

        recognition.onend = () => {
            micBtn.classList.remove('listening');
            micBtn.innerHTML = '🎤 Tap to Speak';
        };

        recognition.onresult = (event) => {
            const transcript = event.results[0][0].transcript;
            parseAndFill(transcript);
        };
    } else {
        micBtn.style.display = 'none';
        statusText.innerText = "Voice not supported. Type manually.";
    }

    function toggleMic() {
        if (micBtn.classList.contains('listening')) recognition.stop();
        else recognition.start();
    }

    // === PARSING LOGIC ===
    function parseAndFill(text) {
        let cleanText = text.toLowerCase();
        let weight = null;
        let reps = null;
        
        const repsMatch = cleanText.match(/(\d+)\s*(reps|repetitions)/);
        if (repsMatch) {
            reps = repsMatch[1];
            cleanText = cleanText.replace(repsMatch[0], ''); 
        }

        const weightMatch = cleanText.match(/(\d+)\s*(kg|kgs|lbs|pounds|kilos)/);
        if (weightMatch) {
            weight = weightMatch[1];
            cleanText = cleanText.replace(weightMatch[0], '');
        }

        const remainingNumbers = cleanText.match(/(\d+(\.\d+)?)/g);
        if (remainingNumbers) {
            if (!weight && !reps && remainingNumbers.length >= 2) {
                const n1 = parseFloat(remainingNumbers[0]);
                const n2 = parseFloat(remainingNumbers[1]);
                weight = Math.max(n1, n2);
                reps = Math.min(n1, n2);
                cleanText = cleanText.replace(remainingNumbers[0], '').replace(remainingNumbers[1], '');
            } 
            else if (!weight && remainingNumbers.length > 0) {
                weight = remainingNumbers[0];
                cleanText = cleanText.replace(weight, '');
            }
            else if (!reps && remainingNumbers.length > 0) {
                reps = remainingNumbers[0];
                cleanText = cleanText.replace(reps, '');
            }
        }

        let exerciseName = cleanText;
        const badWords = [/\bfor\b/g, /\bwith\b/g, /\bat\b/g, /\bx\b/g, /\bby\b/g];
        badWords.forEach(regex => { exerciseName = exerciseName.replace(regex, ''); });
        exerciseName = exerciseName.replace(/[^a-zA-Z\s]/g, '').trim();
        if(exerciseName.length > 0) {
            exerciseName = exerciseName.charAt(0).toUpperCase() + exerciseName.slice(1);
        }

        if (exerciseName) document.getElementById('exerciseInput').value = exerciseName;
        if (weight) document.getElementById('weightInput').value = weight;
        if (reps) document.getElementById('repsInput').value = reps;
        
        statusText.innerText = `Heard: "${text}"`;
    }

    // === WORKOUT LOGIC ===
    function addToCurrentSession() {
        const exInput = document.getElementById('exerciseInput');
        const wInput = document.getElementById('weightInput');
        const rInput = document.getElementById('repsInput');

        const name = exInput.value.trim();
        if (!name) return alert("Enter an exercise name");

        const exercise = {
            id: Date.now() + Math.random(), // Ensure unique ID
            name: name,
            weight: wInput.value || 0,
            reps: rInput.value || 0
        };

        currentSession.push(exercise);
        
        exInput.value = ''; wInput.value = ''; rInput.value = '';
        exInput.focus();

        renderSession();
    }

    function removeSessionItem(id) {
        currentSession = currentSession.filter(item => item.id !== id);
        renderSession();
    }

    function renderSession() {
        const container = document.getElementById('currentSessionBox');
        const list = document.getElementById('sessionList');
        
        if (currentSession.length === 0) {
            container.classList.remove('active');
            return;
        }
        
        container.classList.add('active');
        list.innerHTML = currentSession.map(item => `
            <div class="session-item">
                <span style="color:white;">${item.name}</span>
                <span style="color:var(--text-muted);">${item.weight}kg x ${item.reps} <span onclick="removeSessionItem(${item.id})" style="color:var(--danger); margin-left:10px; cursor:pointer;">✕</span></span>
            </div>
        `).join('');
    }

    function finishWorkout() {
        if (currentSession.length === 0) return;
        if (!confirm("Finish and save this workout?")) return;

        const workout = {
            id: Date.now(),
            date: new Date().toISOString(),
            exercises: currentSession
        };

        let allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        allWorkouts.unshift(workout);
        localStorage.setItem('gymWorkouts', JSON.stringify(allWorkouts));

        currentSession = [];
        renderSession();
        renderHistory();
        updateProgressDropdown();
    }

    function deleteWorkout(id) {
        if(!confirm("Delete this entire workout log?")) return;
        let allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        allWorkouts = allWorkouts.filter(w => w.id !== id);
        localStorage.setItem('gymWorkouts', JSON.stringify(allWorkouts));
        renderHistory();
        updateProgressDropdown();
    }

    function renderHistory() {
        const historyList = document.getElementById('historyList');
        const allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        
        if (allWorkouts.length === 0) {
            historyList.innerHTML = '<p style="text-align:center; color: var(--text-muted);">No history found.</p>';
            return;
        }

        historyList.innerHTML = allWorkouts.map(workout => {
            const dateObj = new Date(workout.date);
            const dateStr = dateObj.toLocaleDateString() + " " + dateObj.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            
            const exercisesHtml = workout.exercises.map(ex => `
                <div class="exercise-row">
                    <span class="ex-name">${ex.name}</span>
                    <span class="ex-stats">${ex.weight}kg x ${ex.reps}</span>
                </div>
            `).join('');

            return `
                <div class="workout-card">
                    <div class="workout-header">
                        <span class="workout-date">📅 ${dateStr}</span>
                        <button class="btn-delete-workout" onclick="deleteWorkout(${workout.id})">Delete</button>
                    </div>
                    ${exercisesHtml}
                </div>
            `;
        }).join('');
    }

    // === EXPORT / IMPORT LOGIC ===
    
    function exportCSV() {
        const allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        if (allWorkouts.length === 0) {
            alert("No data to export.");
            return;
        }

        // CSV Header
        let csvContent = "data:text/csv;charset=utf-8,";
        csvContent += "WorkoutID,Date,Exercise Name,Weight,Reps\n";

        // CSV Rows
        allWorkouts.forEach(w => {
            w.exercises.forEach(e => {
                // Wrap strings in quotes to handle commas in names
                const row = `${w.id},"${w.date}","${e.name}",${e.weight},${e.reps}`;
                csvContent += row + "\n";
            });
        });

        // Download Link
        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", "gym_history.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }

    function importCSV(input) {
        const file = input.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(e) {
            const text = e.target.result;
            const rows = text.split("\n");
            
            // Skip header
            const dataRows = rows.slice(1);
            
            const newWorkoutsMap = {};

            dataRows.forEach(row => {
                if (!row.trim()) return;

                // Regex to split by comma, ignoring commas inside quotes
                // Explanation: Match a comma only if it's followed by an even number of quotes
                const cols = row.split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/).map(c => c.replace(/^"|"$/g, '').trim());
                
                if (cols.length < 5) return;

                const wId = parseInt(cols[0]);
                const wDate = cols[1];
                const exName = cols[2];
                const exWeight = cols[3];
                const exReps = cols[4];

                if (!newWorkoutsMap[wId]) {
                    newWorkoutsMap[wId] = {
                        id: wId,
                        date: wDate,
                        exercises: []
                    };
                }

                newWorkoutsMap[wId].exercises.push({
                    id: Date.now() + Math.random(), // Generate new internal ID for the exercise
                    name: exName,
                    weight: exWeight,
                    reps: exReps
                });
            });

            // Convert map to array
            const importedWorkouts = Object.values(newWorkoutsMap);

            if (importedWorkouts.length === 0) {
                alert("No valid data found in CSV.");
                return;
            }

            // Merge logic: Add only if ID doesn't exist
            let currentWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
            const currentIds = new Set(currentWorkouts.map(w => w.id));
            
            let addedCount = 0;
            importedWorkouts.forEach(w => {
                if (!currentIds.has(w.id)) {
                    currentWorkouts.push(w);
                    addedCount++;
                }
            });

            // Sort by date (newest first)
            currentWorkouts.sort((a, b) => new Date(b.date) - new Date(a.date));

            localStorage.setItem('gymWorkouts', JSON.stringify(currentWorkouts));
            
            alert(`Import successful! Added ${addedCount} new workouts.`);
            renderHistory();
            updateProgressDropdown();
        };
        
        reader.readAsText(file);
        // Reset input so same file can be selected again if needed
        input.value = ''; 
    }

    // === PROGRESS LOGIC ===
    function updateProgressDropdown() {
        const allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        const select = document.getElementById('progressSelect');
        const names = new Set();
        allWorkouts.forEach(w => w.exercises.forEach(e => names.add(e.name.trim())));
        const currentVal = select.value;

        select.innerHTML = '<option value="">Select an exercise...</option>';
        Array.from(names).sort().forEach(name => {
            const option = document.createElement('option');
            option.value = name;
            option.innerText = name;
            select.appendChild(option);
        });

        if (names.has(currentVal)) select.value = currentVal;
    }

    function renderProgressChart() {
        const name = document.getElementById('progressSelect').value;
        const resultsDiv = document.getElementById('progressResults');
        const allWorkouts = JSON.parse(localStorage.getItem('gymWorkouts') || '[]');
        
        if (!name) {
            resultsDiv.innerHTML = '';
            return;
        }

        let dataPoints = [];
        allWorkouts.forEach(w => {
            w.exercises.forEach(e => {
                if (e.name.trim() === name) {
                    dataPoints.push({ date: new Date(w.date), weight: parseFloat(e.weight), reps: e.reps });
                }
            });
        });

        dataPoints.sort((a, b) => a.date - b.date);

        if (dataPoints.length === 0) {
            resultsDiv.innerHTML = '<p>No data found.</p>';
            return;
        }

        const maxWeight = Math.max(...dataPoints.map(d => d.weight)) || 1;

        resultsDiv.innerHTML = dataPoints.map(d => {
            const dateStr = d.date.toLocaleDateString();
            const widthPercentage = (d.weight / maxWeight) * 100;
            return `
                <div class="progress-chart-item">
                    <div style="display:flex; justify-content:space-between;">
                        <span style="font-size:0.85rem; color:var(--text-muted);">${dateStr}</span>
                        <span style="font-weight:bold;">${d.weight}kg <span style="font-weight:normal; font-size:0.85rem;">x${d.reps}</span></span>
                    </div>
                    <div class="bar-container">
                        <div class="bar-fill" style="width: ${widthPercentage}%;"></div>
                    </div>
                </div>
            `;
        }).reverse().join('');
    }

    // === TABS ===
    function switchTab(tabName) {
        document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
        document.getElementById('tab-' + tabName).classList.add('active');
        const navIndex = tabName === 'log' ? 0 : 1;
        document.querySelectorAll('.nav-item')[navIndex].classList.add('active');
        if (tabName === 'progress') updateProgressDropdown();
    }

    // Initial Load
    renderHistory();
    updateProgressDropdown();

</script>

</body>
</html>