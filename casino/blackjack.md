<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blackjack Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #2c3e50;
            color: #ecf0f1;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            max-width: 600px;
            text-align: center;
            background-color: #34495e;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.3);
        }
        h1 {
            font-size: 2rem;
            margin-bottom: 10px;
        }
        label, .result, .error {
            display: block;
            margin: 10px 0;
        }
        input, button {
            padding: 10px;
            font-size: 16px;
            margin-top: 10px;
        }
        .hand {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-top: 10px;
        }
        .card {
            width: 50px;
            height: 70px;
            border-radius: 5px;
            box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.2);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            color: #333;
            background-color: #fff;
            position: relative;
        }
        .card.red {
            color: #e74c3c;
        }
        .result, .error {
            margin-top: 15px;
            font-size: 1.1rem;
        }
        .error {
            color: #e74c3c;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Blackjack Game</h1>
        <label for="betAmount">Enter your bet:</label>
        <input type="number" id="betAmount" min="1" required>
        <button onclick="startGame()">Deal</button>
        <button onclick="hit()">Hit</button>
        <button onclick="stand()">Stand</button>
        <div id="balance" class="result"></div>
        <div>
            <h3>Player Hand</h3>
            <div id="playerHand" class="hand"></div>
        </div>
        <div>
            <h3>Dealer Hand</h3>
            <div id="dealerHand" class="hand"></div>
        </div>
        <div id="result" class="result"></div>
        <div id="error" class="error"></div>
    </div>
    <script>
        const apiUrl = 'http://localhost:8085/api/casino/blackjack'; 
        async function startGame() {
            const bet = document.getElementById("betAmount").value;
            document.getElementById("error").innerText = "";
            try {
                const response = await fetch(apiUrl + '/start', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        email: 'your_email@example.com',
                        password: 'your_password',
                        betAmount: parseInt(bet)
                    })
                });

                if (!response.ok) throw new Error("Failed to start the game");
                const game = await response.json();
                updateHands(game);
            } catch (error) {
                document.getElementById("error").innerText = "Error: " + error.message;
            }
        }

        async function hit() {
            try {
                const response = await fetch(apiUrl + '/hit', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        email: 'your_email@example.com'
                    })
                });

                if (!response.ok) throw new Error("Failed to hit");
                const game = await response.json();
                updateHands(game);
            } catch (error) {
                document.getElementById("error").innerText = "Error: " + error.message;
            }
        }

        async function stand() {
            try {
                const response = await fetch(apiUrl + '/stand', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        email: 'your_email@example.com'
                    })
                });

                if (!response.ok) throw new Error("Failed to stand");
                const game = await response.json();
                updateHands(game);
            } catch (error) {
                document.getElementById("error").innerText = "Error: " + error.message;
            }
        }

        function updateHands(game) {
            const playerCardsDiv = document.getElementById('playerHand');
            const dealerCardsDiv = document.getElementById('dealerHand');

            playerCardsDiv.innerHTML = '';
            dealerCardsDiv.innerHTML = '';

            game.gameState.playerHand.forEach(card => {
                const img = document.createElement('img');
                img.src = `/images/cards/${card.replace(' ', '_')}.png`; // Adjust path as necessary
                playerCardsDiv.appendChild(img);
            });

            game.gameState.dealerHand.forEach(card => {
                const img = document.createElement('img');
                img.src = `/images/cards/${card.replace(' ', '_')}.png`; // Adjust path as necessary
                dealerCardsDiv.appendChild(img);
            });
        }
    </script>
</body>
</html>