<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kalkulator Game</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1a1a2e, #16213e, #0f3460);
            color: #fff;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        .container {
            width: 100%;
            max-width: 400px;
            position: relative;
        }

        .calculator {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            transition: all 0.5s ease;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .calculator.hidden {
            opacity: 0;
            transform: scale(0.8);
            pointer-events: none;
        }

        .display {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            text-align: right;
            font-size: 2em;
            height: 60px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            position: relative;
            overflow: hidden;
        }

        .display::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 2px;
            background: linear-gradient(90deg, #00dbde, #fc00ff);
        }

        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
        }

        button {
            background: rgba(255, 255, 255, 0.1);
            border: none;
            border-radius: 10px;
            padding: 15px;
            font-size: 1.2em;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(5px);
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        button:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateY(-3px);
        }

        button:active {
            transform: translateY(1px);
        }

        .operator {
            background: rgba(0, 219, 222, 0.3);
        }

        .equals {
            background: rgba(252, 0, 255, 0.3);
            grid-column: span 2;
        }

        .clear {
            background: rgba(255, 71, 87, 0.3);
        }

        /* Game Styles */
        .game {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #1a1a2e, #16213e);
            opacity: 0;
            pointer-events: none;
            transition: all 0.5s ease;
            transform: scale(1.1);
            z-index: 10;
        }

        .game.active {
            opacity: 1;
            pointer-events: all;
            transform: scale(1);
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            grid-template-rows: repeat(3, 100px);
            gap: 10px;
            margin: 30px 0;
        }

        .cell {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 3em;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .cell:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05);
        }

        .cell.x::before, .cell.x::after {
            content: '';
            position: absolute;
            width: 80%;
            height: 8px;
            background: #00dbde;
            border-radius: 5px;
        }

        .cell.x::before {
            transform: rotate(45deg);
        }

        .cell.x::after {
            transform: rotate(-45deg);
        }

        .cell.o::before {
            content: '';
            position: absolute;
            width: 60%;
            height: 60%;
            border: 8px solid #fc00ff;
            border-radius: 50%;
        }

        .game-title {
            font-size: 2.5em;
            margin-bottom: 10px;
            background: linear-gradient(90deg, #00dbde, #fc00ff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
        }

        .game-status {
            font-size: 1.2em;
            margin-bottom: 20px;
            height: 24px;
        }

        .back-button {
            padding: 10px 20px;
            background: rgba(255, 71, 87, 0.3);
            border-radius: 50px;
            margin-top: 20px;
            font-size: 1em;
        }

        .particles {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
        }

        .particle {
            position: absolute;
            background: rgba(255, 255, 255, 0.5);
            border-radius: 50%;
            animation: float linear infinite;
        }

        @keyframes float {
            0% {
                transform: translateY(0) rotate(0deg);
            }
            100% {
                transform: translateY(-100vh) rotate(360deg);
            }
        }

        @media (max-width: 500px) {
            .container {
                max-width: 90%;
            }
            
            .game-board {
                grid-template-columns: repeat(3, 80px);
                grid-template-rows: repeat(3, 80px);
            }
        }
    </style>
</head>
<body>
    <div class="particles" id="particles"></div>
    
    <div class="container">
        <div class="calculator" id="calculator">
            <div class="display" id="display">0</div>
            <div class="buttons">
                <button class="clear" onclick="clearDisplay()">C</button>
                <button onclick="appendToDisplay('(')">(</button>
                <button onclick="appendToDisplay(')')">)</button>
                <button class="operator" onclick="appendToDisplay('/')">/</button>
                
                <button onclick="appendToDisplay('7')">7</button>
                <button onclick="appendToDisplay('8')">8</button>
                <button onclick="appendToDisplay('9')">9</button>
                <button class="operator" onclick="appendToDisplay('x')">x</button>
                
                <button onclick="appendToDisplay('4')">4</button>
                <button onclick="appendToDisplay('5')">5</button>
                <button onclick="appendToDisplay('6')">6</button>
                <button class="operator" onclick="appendToDisplay('-')">-</button>
                
                <button onclick="appendToDisplay('1')">1</button>
                <button onclick="appendToDisplay('2')">2</button>
                <button onclick="appendToDisplay('3')">3</button>
                <button class="operator" onclick="appendToDisplay('+')">+</button>
                
                <button onclick="appendToDisplay('0')">0</button>
                <button onclick="appendToDisplay('.')">.</button>
                <button class="equals" onclick="calculate()">=</button>
            </div>
        </div>
        
        <div class="game" id="game">
            <h1 class="game-title">Tic Tac Toe</h1>
            <div class="game-status" id="game-status">Player X's turn</div>
            <div class="game-board" id="game-board">
                <div class="cell" data-index="0"></div>
                <div class="cell" data-index="1"></div>
                <div class="cell" data-index="2"></div>
                <div class="cell" data-index="3"></div>
                <div class="cell" data-index="4"></div>
                <div class="cell" data-index="5"></div>
                <div class="cell" data-index="6"></div>
                <div class="cell" data-index="7"></div>
                <div class="cell" data-index="8"></div>
            </div>
            <button class="back-button" onclick="exitGame()">Back to Calculator</button>
        </div>
    </div>

    <script>
        // Calculator functionality
        let currentDisplay = '0';
        const display = document.getElementById('display');
        const calculator = document.getElementById('calculator');
        const game = document.getElementById('game');
        
        function updateDisplay() {
            display.textContent = currentDisplay;
        }
        
        function appendToDisplay(value) {
            if (currentDisplay === '0' && value !== '.') {
                currentDisplay = value;
            } else {
                currentDisplay += value;
            }
            
            // Check for 1x1 to open game
            if (currentDisplay === '1x1') {
                openGame();
                return;
            }
            
            updateDisplay();
        }
        
        function clearDisplay() {
            currentDisplay = '0';
            updateDisplay();
        }
        
        function calculate() {
            try {
                // Replace 'x' with '*' for calculation
                const expression = currentDisplay.replace(/x/g, '*');
                currentDisplay = eval(expression).toString();
                updateDisplay();
            } catch (error) {
                currentDisplay = 'Error';
                updateDisplay();
                setTimeout(clearDisplay, 1000);
            }
        }
        
        // Game functionality
        let currentPlayer = 'x';
        let gameBoard = ['', '', '', '', '', '', '', '', ''];
        let gameActive = true;
        const statusDisplay = document.getElementById('game-status');
        const cells = document.querySelectorAll('.cell');
        
        function openGame() {
            calculator.classList.add('hidden');
            game.classList.add('active');
            resetGame();
            createParticles();
        }
        
        function exitGame() {
            calculator.classList.remove('hidden');
            game.classList.remove('active');
            clearParticles();
            clearDisplay();
        }
        
        const winningConditions = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
            [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
            [0, 4, 8], [2, 4, 6]             // diagonals
        ];
        
        function handleCellClick(e) {
            const clickedCell = e.target;
            const clickedCellIndex = parseInt(clickedCell.getAttribute('data-index'));
            
            if (gameBoard[clickedCellIndex] !== '' || !gameActive) return;
            
            handleCellPlayed(clickedCell, clickedCellIndex);
            handleResultValidation();
        }
        
        function handleCellPlayed(clickedCell, clickedCellIndex) {
            gameBoard[clickedCellIndex] = currentPlayer;
            clickedCell.classList.add(currentPlayer);
        }
        
        function handleResultValidation() {
            let roundWon = false;
            
            for (let i = 0; i < winningConditions.length; i++) {
                const [a, b, c] = winningConditions[i];
                
                if (gameBoard[a] === '' || gameBoard[b] === '' || gameBoard[c] === '') continue;
                
                if (gameBoard[a] === gameBoard[b] && gameBoard[b] === gameBoard[c]) {
                    roundWon = true;
                    break;
                }
            }
            
            if (roundWon) {
                statusDisplay.textContent = `Player ${currentPlayer.toUpperCase()} wins!`;
                gameActive = false;
                celebrateWin();
                return;
            }
            
            if (!gameBoard.includes('')) {
                statusDisplay.textContent = "Game ended in a draw!";
                gameActive = false;
                return;
            }
            
            currentPlayer = currentPlayer === 'x' ? 'o' : 'x';
            statusDisplay.textContent = `Player ${currentPlayer.toUpperCase()}'s turn`;
        }
        
        function resetGame() {
            currentPlayer = 'x';
            gameBoard = ['', '', '', '', '', '', '', '', ''];
            gameActive = true;
            statusDisplay.textContent = `Player ${currentPlayer.toUpperCase()}'s turn`;
            
            cells.forEach(cell => {
                cell.classList.remove('x', 'o');
            });
        }
        
        function celebrateWin() {
            const colors = ['#00dbde', '#fc00ff', '#ff4747', '#47ff5e', '#f9ff47'];
            
            for (let i = 0; i < 50; i++) {
                const confetti = document.createElement('div');
                confetti.classList.add('particle');
                confetti.style.left = `${Math.random() * 100}%`;
                confetti.style.width = `${Math.random() * 10 + 5}px`;
                confetti.style.height = confetti.style.width;
                confetti.style.background = colors[Math.floor(Math.random() * colors.length)];
                confetti.style.animationDuration = `${Math.random() * 3 + 2}s`;
                confetti.style.animationDelay = `${Math.random() * 0.5}s`;
                document.getElementById('particles').appendChild(confetti);
                
                setTimeout(() => {
                    confetti.remove();
                }, 3000);
            }
        }
        
        function createParticles() {
            for (let i = 0; i < 20; i++) {
                const particle = document.createElement('div');
                particle.classList.add('particle');
                particle.style.left = `${Math.random() * 100}%`;
                particle.style.bottom = `${Math.random() * 20}px`;
                particle.style.width = `${Math.random() * 5 + 2}px`;
                particle.style.height = particle.style.width;
                particle.style.opacity = Math.random() * 0.5 + 0.1;
                particle.style.animationDuration = `${Math.random() * 10 + 5}s`;
                particle.style.animationDelay = `${Math.random() * 5}s`;
                document.getElementById('particles').appendChild(particle);
            }
        }
        
        function clearParticles() {
            document.getElementById('particles').innerHTML = '';
        }
        
        // Event listeners
        cells.forEach(cell => {
            cell.addEventListener('click', handleCellClick);
        });
        
        // Keyboard support for calculator
        document.addEventListener('keydown', (e) => {
            if (game.classList.contains('active')) return;
            
            if (/[0-9()+\-.]/.test(e.key)) {
                appendToDisplay(e.key);
            } else if (e.key === '*') {
                appendToDisplay('x');
            } else if (e.key === '/') {
                appendToDisplay('/');
            } else if (e.key === 'Enter') {
                calculate();
            } else if (e.key === 'Escape') {
                clearDisplay();
            } else if (e.key === 'Backspace') {
                currentDisplay = currentDisplay.slice(0, -1) || '0';
                updateDisplay();
            }
        });
    </script>
</body>
</html>
