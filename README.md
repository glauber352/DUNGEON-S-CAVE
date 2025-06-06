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
            border: 1px solid
