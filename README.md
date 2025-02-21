# Terminator
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Highlander Elimination</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f4;
            margin: 50px;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            display: inline-block;
        }
        .roll-button {
            background-color: blue;
            color: white;
            font-size: 20px;
            padding: 15px 30px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
        }
        .result {
            font-size: 24px;
            margin-top: 20px;
        }
        .sad-animation {
            color: red;
            font-size: 30px;
            animation: shake 0.5s;
        }
        .confetti {
            color: green;
            font-size: 30px;
            animation: confetti 1s ease-out;
        }
        table {
            margin-top: 20px;
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 10px;
        }
        @keyframes shake {
            0% { transform: translateX(0); }
            25% { transform: translateX(-5px); }
            50% { transform: translateX(5px); }
            75% { transform: translateX(-5px); }
            100% { transform: translateX(0); }
        }
        @keyframes confetti {
            0% { transform: scale(1); }
            50% { transform: scale(1.2); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Highlander Elimination</h1>
        <input type="email" id="email" placeholder="Enter email">
        <button onclick="registerUser()">Sign In</button>
        <button class="roll-button" onclick="rollDice()">Roll the Dice</button>
        <div id="result" class="result"></div>
    </div>
    
    <h2>Previous Week's Winner</h2>
    <table>
        <thead>
            <tr>
                <th>Week</th>
                <th>Winner</th>
            </tr>
        </thead>
        <tbody id="winnersTable">
        </tbody>
    </table>

    <script>
        let users = {};
        let eliminatedUsers = new Set();
        let today = new Date().toDateString();
        let startOfWeek = new Date();
        startOfWeek.setDate(startOfWeek.getDate() - startOfWeek.getDay());
        let weekStart = startOfWeek.toDateString();
        let previousWinners = [];

        function registerUser() {
            let email = document.getElementById("email").value;
            if (!email) {
                alert("Please enter a valid email address.");
                return;
            }
            if (!users[email]) {
                users[email] = { rolledToday: false };
            }
            alert(`Welcome, ${email}! You can roll once today.`);
        }

        function rollDice() {
            let email = document.getElementById("email").value;
            if (!email || !users[email]) {
                alert("Please sign in first.");
                return;
            }
            if (users[email].rolledToday === today) {
                alert("You have already rolled today.");
                return;
            }
            if (eliminatedUsers.has(email)) {
                alert("You have already been eliminated.");
                return;
            }

            let roll = Math.floor(Math.random() * 20) + 1;
            let resultDiv = document.getElementById("result");
            resultDiv.innerHTML = `You rolled a ${roll}!`;
            resultDiv.className = "result";
            users[email].rolledToday = today;

            if (roll === 1) {
                resultDiv.innerHTML += " <span class='sad-animation'>&#128546; You Lose! Eliminated.</span>";
                eliminatedUsers.add(email);
            } else if (roll === 20) {
                resultDiv.innerHTML += " <span class='confetti'>&#127881; Critical Win!</span>";
            } else if (roll > 15) {
                let goodPhrases = ["Nice Roll", "You're safe today", "That's Clutch!", "Dang, that's solid bro..."];
                resultDiv.innerHTML += ` <span>${goodPhrases[Math.floor(Math.random() * goodPhrases.length)]}</span>`;
            } else if (roll < 5) {
                let badPhrases = ["Uh-Oh", "Not the best roll...", "Hopefully someone rolls a 1."];
                resultDiv.innerHTML += ` <span>${badPhrases[Math.floor(Math.random() * badPhrases.length)]}</span>`;
            }
        }

        function resetGameIfNeeded() {
            let currentDate = new Date().toDateString();
            if (currentDate !== weekStart) {
                if (eliminatedUsers.size < Object.keys(users).length) {
                    let winner = Object.keys(users).find(u => !eliminatedUsers.has(u));
                    if (winner) {
                        previousWinners.push({ week: weekStart, winner });
                        updateWinnersTable();
                    }
                }
                eliminatedUsers.clear();
                weekStart = currentDate;
            }
        }
        function updateWinnersTable() {
            let table = document.getElementById("winnersTable");
            table.innerHTML = previousWinners.map(w => `<tr><td>${w.week}</td><td>${w.winner}</td></tr>`).join("");
        }
        setInterval(resetGameIfNeeded, 60000);
    </script>
</body>
</html>
