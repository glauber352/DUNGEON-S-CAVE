<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DUNGEONS CAVE</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=MedievalSharp&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a1a2e; /* Cor de fundo escura e temática */
            color: #e0e0e0; /* Cor de texto clara */
            overscroll-behavior: none; /* Prevenir "pull-to-refresh" */
        }
        .medieval-title {
            font-family: 'MedievalSharp', cursive;
            color: #f9a826; /* Dourado para títulos */
        }
        .game-container {
            max-width: 900px;
            margin: 20px auto;
            padding: 20px;
            background-color: #24243e; /* Fundo do container um pouco mais claro */
            border-radius: 12px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.3);
        }
        .btn {
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            transition: all 0.3s ease;
            cursor: pointer;
            border: none;
        }
        .btn-primary {
            background-color: #f9a826; /* Dourado */
            color: #1a1a2e;
        }
        .btn-primary:hover {
            background-color: #fbc02d; /* Dourado mais claro */
        }
        .btn-secondary {
            background-color: #4a4a6a; /* Cinza azulado escuro */
            color: #e0e0e0;
        }
        .btn-secondary:hover {
            background-color: #5f5f82; /* Cinza azulado mais claro */
        }
        .btn-danger {
            background-color: #c70039;
            color: #fff;
        }
        .btn-danger:hover {
            background-color: #e00040;
        }
        .message-log {
            background-color: #1e1e33; /* Fundo do log */
            border: 1px solid #3a3a5a; /* Borda sutil */
            padding: 15px;
            margin-top: 20px;
            border-radius: 8px;
            height: 200px;
            overflow-y: auto;
            font-size: 0.9em;
            line-height: 1.6;
        }
        .message-log p {
            margin-bottom: 8px;
            border-bottom: 1px dashed #3a3a5a; /* Separador de mensagens */
            padding-bottom: 8px;
        }
        .message-log p:last-child {
            border-bottom: none;
            margin-bottom: 0;
        }
        .stat-bar-container {
            width: 100%;
            background-color: #3a3a5a;
            border-radius: 6px;
            overflow: hidden;
            height: 20px;
            margin-bottom: 5px;
        }
        .stat-bar {
            height: 100%;
            text-align: center;
            line-height: 20px;
            color: white;
            font-size: 0.8em;
            transition: width 0.5s ease-in-out;
        }
        .hp-bar { background-color: #c0392b; } /* Vermelho para HP */
        .xp-bar { background-color: #2980b9; } /* Azul para XP */

        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .modal-content {
            background-color: #24243e;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            text-align: center;
            max-width: 90%;
            width: 400px;
        }
        .hidden { display: none; }
    </style>
</head>
<body>
    <div id="gameContainer" class="game-container">
        <header class="text-center mb-8">
            <h1 class="text-5xl medieval-title">DUNGEONS CAVE</h1>
            <p class="text-lg text-gray-400">"Aventure-se nas profundezas... se tiver coragem."</p>
            <p class="text-sm text-gray-500 mt-2">User ID: <span id="userIdDisplay">Carregando...</span></p>
        </header>

        <div id="loadingScreen" class="text-center p-8">
            <p class="text-xl">Carregando dados do jogo...</p>
            <div class="mt-4 w-16 h-16 border-4 border-dashed rounded-full animate-spin border-f9a826 mx-auto"></div>
        </div>
        
        <div id="characterCreationScreen" class="hidden">
            <h2 class="text-3xl medieval-title mb-6 text-center">Crie seu Herói</h2>
            <div class="mb-4">
                <label for="characterName" class="block text-sm font-medium text-gray-300 mb-1">Nome do Personagem:</label>
                <input type="text" id="characterName" class="w-full p-2 bg-gray-700 border border-gray-600 rounded-md focus:ring-f9a826 focus:border-f9a826" placeholder="Ex: Argon">
            </div>
            <div class="mb-6">
                <label class="block text-sm font-medium text-gray-300 mb-1">Escolha sua Classe:</label>
                <div id="classSelection" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    </div>
            </div>
            <button id="createCharacterButton" class="btn btn-primary w-full">Iniciar Aventura</button>
        </div>

        <div id="mainGameScreen" class="hidden">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
                <div class="md:col-span-1 p-4 bg-gray-800 rounded-lg shadow">
                    <h3 class="text-xl medieval-title mb-3">Herói: <span id="playerName"></span></h3>
                    <p class="text-sm">Classe: <span id="playerClass"></span></p>
                    <p class="text-sm">Nível: <span id="playerLevel"></span></p>
                    
                    <div class="mt-2">
                        <label class="text-xs text-gray-400">HP (<span id="playerCurrentHP"></span>/<span id="playerMaxHP"></span>):</label>
                        <div class="stat-bar-container">
                            <div id="playerHPBar" class="stat-bar hp-bar"></div>
                        </div>
                    </div>
                    <div class="mt-2">
                        <label class="text-xs text-gray-400">XP (<span id="playerCurrentXP"></span>/<span id="playerNextLevelXP"></span>):</label>
                        <div class="stat-bar-container">
                            <div id="playerXPBar" class="stat-bar xp-bar"></div>
                        </div>
                    </div>
                    <p class="text-sm mt-2">Ouro: <span id="playerGold">0</span></p>
                    <div id="inventoryDisplay" class="mt-3">
                        <h4 class="text-md medieval-title mb-1">Inventário:</h4>
                        <ul id="inventoryList" class="list-disc list-inside text-xs text-gray-300 max-h-24 overflow-y-auto">
                            </ul>
                    </div>
                </div>

                <div class="md:col-span-2 p-4 bg-gray-800 rounded-lg shadow">
                    <div id="townView" class="text-center">
                        <h3 class="text-2xl medieval-title mb-4">Cidade de Refúgio</h3>
                        <p class="mb-4 text-gray-300">Você está seguro na cidade. O que deseja fazer?</p>
                        <button id="goToDungeonSelectionButton" class="btn btn-primary mr-2 mb-2">Explorar Dungeons</button>
                        <button id="restButton" class="btn btn-secondary mr-2 mb-2">Descansar (Curar HP)</button>
                        <p class="mt-3 text-sm text-gray-400">NPCs (em breve): Maelis (Loja), Sif (Ferreiro), Eldric (Guilda)</p>
                    </div>

                    <div id="dungeonSelectionView" class="hidden text-center">
                        <h3 class="text-2xl medieval-title mb-4">Escolha uma Dungeon</h3>
                        <div id="dungeonList" class="grid grid-cols-1 gap-3">
                            </div>
                        <button id="backToTownButtonDungeon" class="btn btn-secondary mt-4">Voltar para Cidade</button>
                    </div>
                    
                    <div id="dungeonView" class="hidden">
                        <h3 class="text-2xl medieval-title mb-2">Explorando: <span id="currentDungeonName"></span></h3>
                        <p id="dungeonDescription" class="text-sm text-gray-400 mb-4"></p>
                        <div id="enemyEncounter" class="hidden mb-4 p-3 bg-gray-700 rounded">
                            <h4 class="text-lg medieval-title text-red-400">Inimigo: <span id="enemyName"></span></h4>
                            <p class="text-sm">HP: <span id="enemyCurrentHP"></span>/<span id="enemyMaxHP"></span></p>
                            <div class="stat-bar-container mt-1">
                                <div id="enemyHPBar" class="stat-bar hp-bar bg-red-600"></div>
                            </div>
                        </div>
                        <div id="combatActions" class="hidden space-x-2 mb-4">
                            <button id="attackButton" class="btn btn-danger">Atacar</button>
                            <button id="specialAbilityButton" class="btn btn-primary">Habilidade Especial</button>
                        </div>
                        <button id="exploreDungeonButton" class="btn btn-primary">Explorar Mais Fundo</button>
                        <button id="leaveDungeonButton" class="btn btn-secondary mt-2">Sair da Dungeon</button>
                    </div>
                </div>
            </div>
            
            <div>
                <h3 class="text-xl medieval-title mb-2">Diário da Aventura</h3>
                <div id="messageLog" class="message-log">
                    <p>Bem-vindo à Dungeons Cave!</p>
                </div>
            </div>
        </div>

        <div id="gameOverModal" class="modal hidden">
            <div class="modal-content">
                <h2 class="text-3xl medieval-title text-red-500 mb-4">Fim de Jogo!</h2>
                <p id="gameOverMessage" class="text-gray-300 mb-6">Você foi derrotado...</p>
                <button id="restartGameButton" class="btn btn-primary">Recomeçar Aventura</button>
            </div>
        </div>

        <div id="levelUpModal" class="modal hidden">
            <div class="modal-content">
                <h2 class="text-3xl medieval-title text-yellow-400 mb-4">Nível Aumentado!</h2>
                <p class="text-gray-300 mb-2">Parabéns, <span id="levelUpPlayerName"></span>!</p>
                <p class="text-gray-300 mb-2">Você alcançou o Nível <span id="newPlayerLevel"></span>!</p>
                <p class="text-gray-300 mb-4">Seus atributos aumentaram!</p>
                <ul id="statIncreases" class="text-gray-300 list-disc list-inside mb-6"></ul>
                <button id="closeLevelUpModalButton" class="btn btn-primary">Continuar</button>
            </div>
        </div>
    </div>

    <script type="module">
        // Importações do Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged, setPersistence, browserLocalPersistence } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, updateDoc, collection, runTransaction, setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Configuração do Firebase (será preenchida pelo ambiente)
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : { apiKey: "...", authDomain: "...", projectId: "..."};
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'dungeons-cave-dev';

        // Inicializa Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        setLogLevel('debug'); // 'debug' para desenvolvimento, 'silent' para produção

        // Elementos da UI
        const loadingScreen = document.getElementById('loadingScreen');
        const characterCreationScreen = document.getElementById('characterCreationScreen');
        const mainGameScreen = document.getElementById('mainGameScreen');
        const userIdDisplay = document.getElementById('userIdDisplay');
        const characterNameInput = document.getElementById('characterName');
        const classSelectionDiv = document.getElementById('classSelection');
        const createCharacterButton = document.getElementById('createCharacterButton');
        
        const playerNameDisplay = document.getElementById('playerName');
        const playerClassDisplay = document.getElementById('playerClass');
        const playerLevelDisplay = document.getElementById('playerLevel');
        const playerCurrentHPDisplay = document.getElementById('playerCurrentHP');
        const playerMaxHPDisplay = document.getElementById('playerMaxHP');
        const playerHPBar = document.getElementById('playerHPBar');
        const playerCurrentXPDisplay = document.getElementById('playerCurrentXP');
        const playerNextLevelXPDisplay = document.getElementById('playerNextLevelXP');
        const playerXPBar = document.getElementById('playerXPBar');
        const playerGoldDisplay = document.getElementById('playerGold');
        const inventoryList = document.getElementById('inventoryList');

        const messageLog = document.getElementById('messageLog');
        
        const townView = document.getElementById('townView');
        const dungeonSelectionView = document.getElementById('dungeonSelectionView');
        const dungeonView = document.getElementById('dungeonView');
        const goToDungeonSelectionButton = document.getElementById('goToDungeonSelectionButton');
        const restButton = document.getElementById('restButton');
        const dungeonListDiv = document.getElementById('dungeonList');
        const backToTownButtonDungeon = document.getElementById('backToTownButtonDungeon');

        const currentDungeonNameDisplay = document.getElementById('currentDungeonName');
        const dungeonDescriptionDisplay = document.getElementById('dungeonDescription');
        const exploreDungeonButton = document.getElementById('exploreDungeonButton');
        const leaveDungeonButton = document.getElementById('leaveDungeonButton');
        
        const enemyEncounterDiv = document.getElementById('enemyEncounter');
        const enemyNameDisplay = document.getElementById('enemyName');
        const enemyCurrentHPDisplay = document.getElementById('enemyCurrentHP');
        const enemyMaxHPDisplay = document.getElementById('enemyMaxHP');
        const enemyHPBar = document.getElementById('enemyHPBar');
        const combatActionsDiv = document.getElementById('combatActions');
        const attackButton = document.getElementById('attackButton');
        const specialAbilityButton = document.getElementById('specialAbilityButton');

        const gameOverModal = document.getElementById('gameOverModal');
        const gameOverMessage = document.getElementById('gameOverMessage');
        const restartGameButton = document.getElementById('restartGameButton');
        const levelUpModal = document.getElementById('levelUpModal');
        const levelUpPlayerName = document.getElementById('levelUpPlayerName');
        const newPlayerLevel = document.getElementById('newPlayerLevel');
        const statIncreasesList = document.getElementById('statIncreases');
        const closeLevelUpModalButton = document.getElementById('closeLevelUpModalButton');

        // Estado do Jogo
        let currentUser = null;
        let playerCharacter = null;
        let currentEnemy = null;
        let currentDungeon = null;
        let specialAbilityCooldown = false;

        // Definições do Jogo (simplificado)
        const classes = {
            Cavaleiro: { 
                name: "Cavaleiro", 
                description: "Tanque, defesa alta, espada e escudo.", 
                baseStats: { hp: 120, attack: 10, defense: 15 }, 
                specialAbility: "Escudo Imbatível", 
                specialAbilityDesc: "Bloqueia 100% do dano do próximo ataque inimigo.",
                img: "https://placehold.co/150x100/A9A9A9/FFFFFF?text=Cavaleiro"
            },
            Ladino: { 
                name: "Ladino", 
                description: "Ágil, furtivo, dano crítico alto.", 
                baseStats: { hp: 80, attack: 15, defense: 8 }, 
                specialAbility: "Ataque Furtivo", 
                specialAbilityDesc: "Causa 200% de dano no próximo ataque.",
                img: "https://placehold.co/150x100/696969/FFFFFF?text=Ladino"
            },
            Mago: { 
                name: "Mago", 
                description: "Mestre dos elementos, dano mágico.", 
                baseStats: { hp: 70, attack: 18, defense: 5 }, 
                specialAbility: "Bola de Fogo", 
                specialAbilityDesc: "Causa dano mágico moderado ignorando parte da defesa.",
                img: "https://placehold.co/150x100/ADD8E6/000000?text=Mago"
            }
        };

        const dungeons = {
            cavernaSombria: {
                id: "cavernaSombria",
                name: "Caverna Sombria",
                levelRange: "1-5",
                description: "Uma caverna escura e úmida, lar de criaturas noturnas. Cuidado onde pisa!",
                enemies: [
                    { name: "Morcego Gigante", hp: 30, attack: 8, defense: 2, xp: 10, gold: 5, lootTable: [{itemId: "asaDeMorcego", chance: 0.5}] },
                    { name: "Aranha Pequena", hp: 20, attack: 6, defense: 1, xp: 7, gold: 3, lootTable: [{itemId: "presaDeAranha", chance: 0.3}] }
                ],
                boss: { name: "Gorak, O Devorador (Simplificado)", hp: 150, attack: 15, defense: 5, xp: 100, gold: 50, lootTable: [{itemId: "olhoDeGorak", chance: 1.0}] }
            },
            // Mais dungeons podem ser adicionadas aqui
        };
        
        const items = { // Simples definição de itens
            asaDeMorcego: { name: "Asa de Morcego", type: "material", value: 2 },
            presaDeAranha: { name: "Presa de Aranha", type: "material", value: 1 },
            olhoDeGorak: { name: "Olho de Gorak", type: "trophy", value: 25 },
            pocaoVidaPequena: { name: "Poção de Vida Pequena", type: "consumable", effect: { heal: 20 }, value: 10 }
        };


        // Funções do Jogo
        function logMessage(message, type = "normal") {
            const p = document.createElement('p');
            p.textContent = message;
            if (type === "combat") p.classList.add("text-yellow-400");
            if (type === "loot") p.classList.add("text-green-400");
            if (type === "error") p.classList.add("text-red-400");
            if (type === "info") p.classList.add("text-blue-400");
            if (type === "system") p.classList.add("text-purple-400", "italic");

            messageLog.appendChild(p);
            messageLog.scrollTop = messageLog.scrollHeight; // Auto-scroll
        }

        async function authenticateUser() {
            try {
                await setPersistence(auth, browserLocalPersistence); // Tenta persistir localmente
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    logMessage("Autenticando com token...", "system");
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    logMessage("Autenticando anonimamente...", "system");
                    await signInAnonymously(auth);
                }
            } catch (error) {
                console.error("Erro na autenticação:", error);
                logMessage(`Erro de autenticação: ${error.message}`, "error");
