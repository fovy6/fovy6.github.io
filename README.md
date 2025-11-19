<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Voice Gym Log</title>
    <style>
        :root {
            --primary: #3b82f6;
            --bg: #0f172a;
            --card: #1e293b;
            --text: #f8fafc;
            --text-muted: #94a3b8;
            --danger: #ef4444;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
        }

        h1 { margin-bottom: 20px; font-size: 1.5rem; }

        .container {
            width: 100%;
            max-width: 500px;
        }

        /* Input Section */
        .input-card {
            background-color: var(--card);
            padding: 20px;
            border-radius: 16px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
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
            transition: background 0.2s;
        }

        .mic-button.listening {
            background-color: var(--danger);
            animation: pulse 1.5s infinite;
        }

        .form-group {
            margin-bottom: 12px;
        }

        label {
            display: block;
            color: var(--text-muted);
            font-size: 0.85rem;
            margin-bottom: 4px;
        }

        input {
            width: 100%;
            box-sizing: border-box;
            padding: 12px;
            background: #334155;
            border: 1px solid #475569;
            color: white;
            border-radius: 8px;
            font-size: 1rem;
        }

        .row {
            display: flex;
            gap: 10px;
        }

        .btn-save {
            background-color: #10b981;
            color: white;
            border: none;
            width: 100%;
            padding: 15px;
            font-size: 1rem;
            border-radius: 8px;
            margin-top: 10px;
            font-weight: bold;
        }

        /* History Section */
        .history-title {
            margin-top: 20px;
            margin-bottom: 10px;
            font-size: 1.2rem;
            border-bottom: 1px solid #334155;
            padding-bottom: 10px;
        }

        .log-item {
            background-color: var(--card);
            padding: 15px;
            border-radius: 12px;
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .log-info h3 { margin: 0 0 4px 0; font-size: 1.1rem; }
        .log-details { color: var(--text-muted); font-size: 0.9rem; }
        .log-date { font-size: 0.75rem; color: #64748b; margin-top: 4px;}

        .btn-delete {
            background: transparent;
            border: 1px solid #475569;
            color: var(--danger);
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 0.8rem;
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.02); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Gym Log 🏋️‍♂️</h1>

    <!-- Input Area -->
    <div class="input-card">
        <button id="micBtn" class="mic-button">
            🎤 Tap to Speak
        </button>
        <p id="status" style="text-align: center; color: var(--text-muted); font-size: 0.9rem; min-height: 1.2em;">
            Try: "Bench press 80 kilos for 5 reps"
        </p>

        <div class="form-group">
            <label>Exercise Name</label>
            <input type="text" id="exerciseInput" placeholder="e.g. Squat">
        </div>

        <div class="row">
            <div class="form-group" style="flex: 1;">
                <label>Weight (kg/lbs)</label>
                <input type="number" id="weightInput" placeholder="0">
            </div>
            <div class="form-group" style="flex: 1;">
                <label>Reps</label>
                <input type="number" id="repsInput" placeholder="0">
            </div>
        </div>

        <button class="btn-save" onclick="saveWorkout()">Save Log</button>
    </div>

    <!-- History Area -->
    <h2 class="history-title">History</h2>
    <div id="historyList"></div>
</div>

<script>
    // --- Voice Recognition Setup ---
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    const micBtn = document.getElementById('micBtn');
    const statusText = document.getElementById('status');
    
    let recognition;

    if (SpeechRecognition) {
        recognition = new SpeechRecognition();
        recognition.continuous = false;
        recognition.lang = 'en-US';
        recognition.interimResults = false;

        micBtn.addEventListener('click', toggleMic);

        recognition.onstart = () => {
            micBtn.classList.add('listening');
            micBtn.innerHTML = '🛑 Listening...';
            statusText.innerText = "Say: [Exercise] [Weight] [Reps]";
        };

        recognition.onend = () => {
            micBtn.classList.remove('listening');
            micBtn.innerHTML = '🎤 Tap to Speak';
        };

        recognition.onresult = (event) => {
            const transcript = event.results[0][0].transcript;
            statusText.innerText = `Heard: "${transcript}"`;
            parseAndFill(transcript);
        };

        recognition.onerror = (event) => {
            statusText.innerText = "Error detected: " + event.error;
            micBtn.classList.remove('listening');
            micBtn.innerHTML = '🎤 Tap to Speak';
        };
    } else {
        micBtn.style.display = 'none';
        statusText.innerText = "Voice not supported on this browser. Please type manually.";
    }

    function toggleMic() {
        if (micBtn.classList.contains('listening')) {
            recognition.stop();
        } else {
            recognition.start();
        }
    }

    // --- Parsing Logic ---
    function parseAndFill(text) {
        text = text.toLowerCase();
        
        // Logic: Look for numbers. 
        // Common patterns: "Bench press 100 for 5", "5 reps of 100 bench press"
        
        let weight = null;
        let reps = null;
        let exercise = text;

        // Regex to find numbers
        const numbers = text.match(/(\d+(\.\d+)?)/g);

        if (numbers) {
            // Heuristic: If the word 'reps' is near a number, that's the rep count.
            // Usually weight > reps, but not always.
            
            // 1. Try to find explicit "reps" keyword
            const repsMatch = text.match(/(\d+)\s*(reps|repetitions)/);
            if (repsMatch) {
                reps = repsMatch[1];
                exercise = exercise.replace(repsMatch[0], ''); // Remove from name
            }

            // 2. Try to find explicit weight keywords (kg, lbs, pounds)
            const weightMatch = text.match(/(\d+)\s*(kg|kgs|lbs|pounds|kilos)/);
            if (weightMatch) {
                weight = weightMatch[1];
                exercise = exercise.replace(weightMatch[0], ''); // Remove from name
            }

            // 3. Fallback if we have numbers but no keywords
            // If we haven't found reps/weight yet but have numbers left
            const remainingNumbers = exercise.match(/(\d+)/g);
            
            if (remainingNumbers) {
                if (!weight && !reps && remainingNumbers.length >= 2) {
                    // Assume larger number is weight, smaller is reps (risky but effective)
                    const n1 = parseFloat(remainingNumbers[0]);
                    const n2 = parseFloat(remainingNumbers[1]);
                    weight = Math.max(n1, n2);
                    reps = Math.min(n1, n2);
                } else if (!weight && remainingNumbers.length > 0) {
                    weight = remainingNumbers[0];
                } else if (!reps && remainingNumbers.length > 0) {
                    reps = remainingNumbers[0];
                }
            }

            // Clean up the exercise name (remove loose numbers and "for")
            exercise = exercise.replace(/[0-9]/g, '').replace(/\bfor\b/g, '').replace(/\bwith\b/g, '').trim();
            // Capitalize first letter
            exercise = exercise.charAt(0).toUpperCase() + exercise.slice(1);
        }

        // Fill Inputs
        document.getElementById('exerciseInput').value = exercise;
        if (weight) document.getElementById('weightInput').value = weight;
        if (reps) document.getElementById('repsInput').value = reps;
    }

    // --- Data Management (Local Storage) ---
    
    function saveWorkout() {
        const exercise = document.getElementById('exerciseInput').value;
        const weight = document.getElementById('weightInput').value;
        const reps = document.getElementById('repsInput').value;

        if (!exercise) {
            alert("Please enter an exercise name");
            return;
        }

        const workout = {
            id: Date.now(),
            date: new Date().toLocaleDateString() + " " + new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}),
            exercise,
            weight,
            reps
        };

        let history = JSON.parse(localStorage.getItem('gymLog') || '[]');
        history.unshift(workout); // Add to top
        localStorage.setItem('gymLog', JSON.stringify(history));

        // Clear inputs
        document.getElementById('exerciseInput').value = '';
        document.getElementById('weightInput').value = '';
        document.getElementById('repsInput').value = '';
        
        renderHistory();
    }

    function deleteLog(id) {
        if(!confirm("Delete this entry?")) return;
        
        let history = JSON.parse(localStorage.getItem('gymLog') || '[]');
        history = history.filter(item => item.id !== id);
        localStorage.setItem('gymLog', JSON.stringify(history));
        renderHistory();
    }

    function renderHistory() {
        const historyList = document.getElementById('historyList');
        const history = JSON.parse(localStorage.getItem('gymLog') || '[]');
        
        historyList.innerHTML = '';

        if (history.length === 0) {
            historyList.innerHTML = '<p style="text-align:center; color: #64748b;">No workouts logged yet.</p>';
            return;
        }

        history.forEach(item => {
            const div = document.createElement('div');
            div.className = 'log-item';
            div.innerHTML = `
                <div class="log-info">
                    <h3>${item.exercise}</h3>
                    <div class="log-details">
                        ${item.weight ? item.weight + ' kg/lbs' : 'Bodyweight'} x ${item.reps} reps
                    </div>
                    <div class="log-date">${item.date}</div>
                </div>
                <button class="btn-delete" onclick="deleteLog(${item.id})">✕</button>
            `;
            historyList.appendChild(div);
        });
    }

    // Initial load
    renderHistory();

</script>

</body>
</html>
