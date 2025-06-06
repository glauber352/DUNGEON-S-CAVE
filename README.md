<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>DUNGEONS CAVE: Aventura D&D</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet" />
    <style>
        /* Estilos personalizados para o jogo */
        body {
            font-family: 'Press Start 2P', cursive;
            background-color: #2c3e50;
            color: #ecf0f1;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            box-sizing: border-box;
            margin: 0;
        }

        .game-container {
            background-color: #34495e;
            border: 4px solid #1a242f;
            border-radius: 8px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
            padding: 1.5rem;
            max-width: 900px;
            width: 100%;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
            position: relative;
        }

        h1, h2, h3 {
            color: #f1c40f;
            text-align: center;
            margin-bottom: 1rem;
            text-shadow: 2px 2px #000;
        }

        .pixel-button {
            background-color: #e74c3c;
            color: white;
            padding: 0.75rem 1.5rem;
            border: 3px solid #c0392b;
            border-radius: 5px;
            cursor: pointer;
            font-family: 'Press Start 2P', cursive;
            font-size: 0.9rem;
            transition: background-color 0.1s, transform 0.1s;
            box-shadow: 3px 3px #a52a22;
            display: inline-block;
            text-align: center;
            image-rendering: pixelated;
        }

        .pixel-button:hover {
            background-color: #c0392b;
            transform: translateY(1px);
            box-shadow: 2px 2px #a52a22;
        }

        .pixel-button:active {
            transform: translateY(3px);
            box-shadow: 0 0 #a52a22;
        }

        .pixel-button:disabled {
            background-color: #95a5a6;
            border-color: #7f8c8d;
            color: #bdc3c7;
            cursor: not-allowed;
            box-shadow: 3px 3px #525e5e;
        }

        .panel {
            background-color: #2c3e50;
            border: 2px solid #1a242f;
            border-radius: 5px;
            padding: 1rem;
            box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.3);
        }

        .game-log {
            height: 150px;
            overflow-y: auto;
            background-color: #1a242f;
            border: 2px solid #000;
            padding: 0.75rem;
            font-size: 0.8rem;
            line-height: 1.4;
            color: #95a5a6;
            border-radius: 5px;
        }

        .game-log p { margin-bottom: 0.25rem; }
        .game-log p.text-yellow-400 { color: #f1c40f; }
        .game-log p.text-red-400 { color: #e74c3c; }
        .game-log p.text-orange-400 { color: #f39c12; }
        .game-log p.text-green-400 { color: #2ecc71; }
        .game-log p.text-blue-300 { color: #85c1e9; }

        .modal {
            display: none;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: #34495e;
            border: 4px solid #1a242f;
            border-radius: 8px;
            padding: 1.5rem 2rem;
            z-index: 100;
            box-shadow: 0 0 30px rgba(0, 0, 0, 0.7);
            text-align: center;
            width: 90%;
            max-width: 600px;
        }
        
        #start-screen, #class-selection-modal, #game-screen {
            display: flex;
            flex-direction: column;
            gap: 1rem;
        }

        #start-screen {
            align-items: center;
        }

        #game-screen { display: none; }
        
        /* --- LÓGICA DE VISIBILIDADE DAS TELAS --- */
        #default-view, #combat-scene {
            display: none; /* Ambos começam escondidos */
            flex-direction: column;
            gap: 1rem;
        }
        
        /* Quando não está em combate, mostra a visão padrão */
        #game-screen:not(.in-combat) #default-view {
            display: flex;
        }

        /* Quando está em combate, mostra a cena de combate */
        #game-screen.in-combat #combat-scene {
            display: flex;
        }
        
        /* --- ESTILOS DA VISÃO PADRÃO --- */
        .stats-area, .actions-inventory-area {
            display: flex;
            flex-direction: column;
            gap: 1rem;
        }

        @media (min-width: 768px) {
            .stats-area, .actions-inventory-area { flex-direction: row; }
        }

        .player-panel, .enemy-panel, .inventory-panel { flex: 1; }
        .actions-panel { flex: 2; }

        .player-stats, .enemy-info {
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem 1rem;
            justify-content: center;
            font-size: 0.85rem;
        }

        .player-stats span, .enemy-info span {
            background-color: #1a242f;
            padding: 0.4rem 0.8rem;
            border-radius: 4px;
            border: 1px solid #555;
            white-space: nowrap;
        }
        
        .inventory {
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
            max-height: 150px;
            overflow-y: auto;
            background-color: #1a242f;
            border: 1px solid #555;
            border-radius: 4px;
            padding: 0.5rem;
        }

        .inventory-item {
            background-color: #2c3e50;
            padding: 0.4rem 0.8rem;
            border-radius: 4px;
            border: 1px solid #555; /* Fixed border style */
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div id="start-screen">
            <h1>DUNGEONS CAVE</h1>
            <p>Uma Aventura D&D</p>
            <button id="start-game-button" class="pixel-button">COMEÇAR AVENTURA</button>
        </div>

        <div id="class-selection-modal" class="modal">
            <h2>Escolha sua Classe</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mt-4">
                <button class="pixel-button class-button" data-class="Warrior">Guerreiro</button>
                <button class="pixel-button class-button" data-class="Mage">Mago</button>
                <button class="pixel-button class-button" data-class="Rogue">Ladino</button>
            </div>
        </div>

        <div id="game-screen">
            <h2 id="game-title">DUNGEONS CAVE</h2>

            <div id="default-view">
                <div class="stats-area">
                    <div class="player-panel panel">
                        <h3>Herói</h3>
                        <div class="player-stats">
                            <span id="player-class">Classe: Guerreiro</span>
                            <span id="player-hp">HP: 100/100</span>
                            <span id="player-atk">ATK: 10</span>
                            <span id="player-def">DEF: 5</span>
                            <span id="player-gold">Ouro: 0</span>
                        </div>
                    </div>
                </div>

                <div class="actions-inventory-area">
                    <div class="actions-panel panel">
                        <h3>Ações</h3>
                        <div class="grid grid-cols-2 gap-4 mt-4">
                            <button id="explore-button" class="pixel-button">EXPLORAR</button>
                            <button id="rest-button" class="pixel-button">DESCANSAR</button>
                            <button id="shop-button" class="pixel-button" disabled>LOJA (Em Breve)</button>
                            <button id="inventory-button" class="pixel-button" disabled>INVENTÁRIO (Em Breve)</button>
                        </div>
                    </div>

                    <div class="inventory-panel panel">
                        <h3>Inventário</h3>
                        <div class="inventory" id="player-inventory">
                            <p class="text-gray-400 text-center">Nenhum item ainda...</p>
                        </div>
                    </div>
                </div>
            </div>

            <div id="combat-scene">
                <div class="stats-area">
                    <div class="player-panel panel">
                        <h3>Herói</h3>
                        <div class="player-stats">
                            <span id="combat-player-class">Classe: Guerreiro</span>
                            <span id="combat-player-hp">HP: 100/100</span>
                            <span id="combat-player-atk">ATK: 10</span>
                            <span id="combat-player-def">DEF: 5</span>
                        </div>
                    </div>
                    <div class="enemy-panel panel">
                        <h3 id="enemy-name">Monstro</h3>
                        <div class="enemy-info">
                            <span id="enemy-hp">HP: 50/50</span>
                            <span id="enemy-atk">ATK: 8</span>
                            <span id="enemy-def">DEF: 3</span>
                        </div>
                    </div>
                </div>

                <div class="actions-panel panel">
                    <h3>Ações de Combate</h3>
                    <div class="grid grid-cols-2 gap-4 mt-4">
                        <button id="attack-button" class="pixel-button">ATACAR</button>
                        <button id="flee-button" class="pixel-button">FUGIR</button>
                        </div>
                </div>
            </div>

            <div class="game-log-panel panel">
                <h3>Registro de Ações</h3>
                <div class="game-log" id="game-log">
                    <p class="text-yellow-400">Bem-vindo à DUNGEONS CAVE! Prepare-se para sua aventura.</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Game State
        const game = {
            player: {},
            enemy: null,
            inCombat: false,
            // Add other game state variables as needed
        };

        // DOM Elements
        const startScreen = document.getElementById('start-screen');
        const classSelectionModal = document.getElementById('class-selection-modal');
        const gameScreen = document.getElementById('game-screen');
        const startGameButton = document.getElementById('start-game-button');
        const classButtons = document.querySelectorAll('.class-button');
        const gameLog = document.getElementById('game-log');
        const playerClassSpan = document.getElementById('player-class');
        const playerHpSpan = document.getElementById('player-hp');
        const playerAtkSpan = document.getElementById('player-atk');
        const playerDefSpan = document.getElementById('player-def');
        const playerGoldSpan = document.getElementById('player-gold');
        const exploreButton = document.getElementById('explore-button');
        const restButton = document.getElementById('rest-button');
        const attackButton = document.getElementById('attack-button');
        const fleeButton = document.getElementById('flee-button');
        const enemyNameSpan = document.getElementById('enemy-name');
        const enemyHpSpan = document.getElementById('enemy-hp');
        const enemyAtkSpan = document.getElementById('enemy-atk');
        const enemyDefSpan = document.getElementById('enemy-def');
        const defaultView = document.getElementById('default-view');
        const combatScene = document.getElementById('combat-scene');

        // Game Logic Functions
        function logMessage(message, type = '') {
            const p = document.createElement('p');
            p.textContent = message;
            if (type) {
                p.classList.add(`text-${type}-400`);
            }
            gameLog.appendChild(p);
            gameLog.scrollTop = gameLog.scrollHeight; // Auto-scroll to bottom
        }

        function updatePlayerStats() {
            playerClassSpan.textContent = `Classe: ${game.player.class}`;
            playerHpSpan.textContent = `HP: ${game.player.currentHp}/${game.player.maxHp}`;
            playerAtkSpan.textContent = `ATK: ${game.player.attack}`;
            playerDefSpan.textContent = `DEF: ${game.player.defense}`;
            playerGoldSpan.textContent = `Ouro: ${game.player.gold}`;

            // Also update combat scene stats if applicable (they share IDs in your HTML)
            document.getElementById('combat-player-class').textContent = `Classe: ${game.player.class}`;
            document.getElementById('combat-player-hp').textContent = `HP: ${game.player.currentHp}/${game.player.maxHp}`;
            document.getElementById('combat-player-atk').textContent = `ATK: ${game.player.attack}`;
            document.getElementById('combat-player-def').textContent = `DEF: ${game.player.defense}`;
        }

        function updateEnemyStats() {
            if (game.enemy) {
                enemyNameSpan.textContent = game.enemy.name;
                enemyHpSpan.textContent = `HP: ${game.enemy.currentHp}/${game.enemy.maxHp}`;
                enemyAtkSpan.textContent = `ATK: ${game.enemy.attack}`;
                enemyDefSpan.textContent = `DEF: ${game.enemy.defense}`;
            } else {
                enemyNameSpan.textContent = "Nenhum Inimigo";
                enemyHpSpan.textContent = "HP: -";
                enemyAtkSpan.textContent = "ATK: -";
                enemyDefSpan.textContent = "DEF: -";
            }
        }

        function enterCombat(enemy) {
            game.enemy = { ...enemy, currentHp: enemy.maxHp }; // Copy enemy and set current HP
            game.inCombat = true;
            gameScreen.classList.add('in-combat'); // Add class to show combat scene
            updateEnemyStats();
            logMessage(`Um ${game.enemy.name} aparece! Prepare-se para a batalha!`, 'red');
        }

        function exitCombat() {
            game.inCombat = false;
            game.enemy = null;
            gameScreen.classList.remove('in-combat'); // Remove class to show default view
            logMessage("Você saiu do combate.", 'blue');
        }

        function playerAttack() {
            if (!game.inCombat || !game.enemy) return;

            let playerDamage = Math.max(0, game.player.attack - game.enemy.defense);
            game.enemy.currentHp -= playerDamage;
            logMessage(`Você atacou o ${game.enemy.name} e causou ${playerDamage} de dano.`, 'green');
            updateEnemyStats();

            if (game.enemy.currentHp <= 0) {
                logMessage(`${game.enemy.name} foi derrotado! Você ganhou ${game.enemy.goldDrop} ouro.`, 'yellow');
                game.player.gold += game.enemy.goldDrop;
                updatePlayerStats();
                exitCombat();
                return;
            }

            // Enemy's turn
            enemyAttack();
        }

        function enemyAttack() {
            if (!game.inCombat || !game.enemy) return;

            let enemyDamage = Math.max(0, game.enemy.attack - game.player.defense);
            game.player.currentHp -= enemyDamage;
            logMessage(`${game.enemy.name} atacou você e causou ${enemyDamage} de dano.`, 'red');
            updatePlayerStats();

            if (game.player.currentHp <= 0) {
                logMessage("Você foi derrotado! Fim de jogo.", 'orange');
                // Implement game over logic (e.g., reset game, show game over screen)
                alert("Game Over! Você foi derrotado.");
                resetGame();
            }
        }

        function resetGame() {
            game.player = {};
            game.enemy = null;
            game.inCombat = false;
            startScreen.style.display = 'flex';
            gameScreen.style.display = 'none';
            classSelectionModal.style.display = 'none';
            gameScreen.classList.remove('in-combat');
            gameLog.innerHTML = '<p class="text-yellow-400">Bem-vindo à DUNGEONS CAVE! Prepare-se para sua aventura.</p>';
        }

        // Event Listeners
        startGameButton.addEventListener('click', () => {
            startScreen.style.display = 'none';
            classSelectionModal.style.display = 'block'; // Show modal
        });

        classButtons.forEach(button => {
            button.addEventListener('click', (event) => {
                const selectedClass = event.target.dataset.class;
                switch (selectedClass) {
                    case 'Warrior':
                        game.player = {
                            class: 'Guerreiro',
                            maxHp: 120,
                            currentHp: 120,
                            attack: 12,
                            defense: 8,
                            gold: 0,
                            inventory: []
                        };
                        break;
                    case 'Mage':
                        game.player = {
                            class: 'Mago',
                            maxHp: 80,
                            currentHp: 80,
                            attack: 15,
                            defense: 3,
                            gold: 0,
                            inventory: []
                        };
                        break;
                    case 'Rogue':
                        game.player = {
                            class: 'Ladino',
                            maxHp: 100,
                            currentHp: 100,
                            attack: 10,
                            defense: 5,
                            gold: 0,
                            inventory: []
                        };
                        break;
                    default:
                        break;
                }
                updatePlayerStats();
                classSelectionModal.style.display = 'none'; // Hide modal
                gameScreen.style.display = 'flex'; // Show game screen
                logMessage(`Você escolheu ser um ${game.player.class}. Sua aventura começa agora!`, 'blue');
            });
        });

        exploreButton.addEventListener('click', () => {
            logMessage("Você explora a caverna...", 'blue');
            const randomEncounter = Math.random();
            if (randomEncounter < 0.5) { // 50% chance of encounter
                const enemies = [
                    { name: 'Goblin', maxHp: 30, attack: 7, defense: 2, goldDrop: 10 },
                    { name: 'Esqueleto', maxHp: 40, attack: 9, defense: 4, goldDrop: 15 },
                    { name: 'Morcego Gigante', maxHp: 25, attack: 6, defense: 1, goldDrop: 8 }
                ];
                const chosenEnemy = enemies[Math.floor(Math.random() * enemies.length)];
                enterCombat(chosenEnemy);
            } else {
                logMessage("Você não encontrou nada interessante.", 'gray');
                if (Math.random() < 0.3) { // 30% chance to find gold
                    const foundGold = Math.floor(Math.random() * 10) + 5;
                    game.player.gold += foundGold;
                    logMessage(`Você encontrou ${foundGold} ouro!`, 'yellow');
                    updatePlayerStats();
                }
            }
        });

        restButton.addEventListener('click', () => {
            if (game.player.currentHp < game.player.maxHp) {
                const healAmount = Math.floor(game.player.maxHp * 0.2); // Heal 20% of max HP
                game.player.currentHp = Math.min(game.player.maxHp, game.player.currentHp + healAmount);
                logMessage(`Você descansou e recuperou ${healAmount} HP.`, 'green');
                updatePlayerStats();
            } else {
                logMessage("Você já está com HP máximo.", 'gray');
            }
        });

        attackButton.addEventListener('click', playerAttack);

        fleeButton.addEventListener('click', () => {
            const fleeChance = Math.random();
            if (fleeChance > 0.6) { // 40% chance to flee
                logMessage("Você conseguiu fugir do combate!", 'blue');
                exitCombat();
            } else {
                logMessage("Você falhou em fugir!", 'orange');
                enemyAttack(); // Enemy gets a free attack if you fail to flee
            }
        });

        // Initial setup
        // Hide game screen and modal initially
        gameScreen.style.display = 'none';
        classSelectionModal.style.display = 'none';
    </script>
</body>
</html>
