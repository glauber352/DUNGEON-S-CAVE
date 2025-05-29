<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DUNGEONS CAVE</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Fonts -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=MedievalSharp&display=swap" rel="stylesheet">

  <!-- Estilos Customizados -->
  <style>
    body {
      font-family: 'Inter', sans-serif;
      background-color: #1a1a2e;
      color: #e0e0e0;
      overscroll-behavior: none;
    }

    .medieval-title {
      font-family: 'MedievalSharp', cursive;
      color: #f9a826;
    }

    .game-container {
      max-width: 900px;
      margin: 20px auto;
      padding: 20px;
      background-color: #24243e;
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
      background-color: #f9a826;
      color: #1a1a2e;
    }
    .btn-primary:hover {
      background-color: #fbc02d;
    }

    .btn-secondary {
      background-color: #4a4a6a;
      color: #e0e0e0;
    }
    .btn-secondary:hover {
      background-color: #5f5f82;
    }

    .btn-danger {
      background-color: #c70039;
      color: white;
    }
    .btn-danger:hover {
      background-color: #e00040;
    }

    .message-log {
      background-color: #1e1e33;
      border: 1px solid #3a3a5a;
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
      border-bottom: 1px dashed #3a3a5a;
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
    .hp-bar { background-color: #c0392b; }
    .xp-bar { background-color: #2980b9; }

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
  <!-- Conteúdo aqui -->
</body>
</html>
                    enemies: [
                        { name: "Morcego Gigante", hp: 30, attack: 8, defense: 2, xp: 20, gold: 5 },
                        { name: "Rato das Sombras", hp: 25, attack: 6, defense: 1, xp: 15, gold: 3 },
                        { name: "Aranha Venenosa", hp: 35, attack: 9, defense: 3, xp: 25, gold: 7 }
                    ]
                },
                ruinasAntigas: {
                    id: "ruinasAntigas",
                    name: "Ruínas Antigas",
                    levelRange: "3-7",
                    description: "Estruturas esquecidas, lar de espíritos e guardiões mágicos.",
                    enemies: [
                        { name: "Esqueleto Guerreiro", hp: 50, attack: 12, defense: 6, xp: 40, gold: 15 },
                        { name: "Espírito Perdido", hp: 40, attack: 15, defense: 3, xp: 45, gold: 12 },
                        { name: "Golem de Pedra", hp: 70, attack: 10, defense: 12, xp: 60, gold: 20 }
                    ]
                }
            };

        // Funções auxiliares
        function logMessage(message) {
            const p = document.createElement('p');
            p.textContent = message;
            messageLog.appendChild(p);
            messageLog.scrollTop = messageLog.scrollHeight;
        }

        function updatePlayerUI() {
            if (!playerCharacter) return;
            playerNameDisplay.textContent = playerCharacter.name;
            playerClassDisplay.textContent = playerCharacter.class;
            playerLevelDisplay.textContent = playerCharacter.level;
            playerCurrentHPDisplay.textContent = playerCharacter.hp;
            playerMaxHPDisplay.textContent = playerCharacter.maxHp;
            playerHPBar.style.width = `${(playerCharacter.hp / playerCharacter.maxHp) * 100}%`;
            playerHPBar.textContent = `${playerCharacter.hp}/${playerCharacter.maxHp}`;
            playerCurrentXPDisplay.textContent = playerCharacter.xp;
            playerNextLevelXPDisplay.textContent = playerCharacter.nextLevelXp;
            playerXPBar.style.width = `${(playerCharacter.xp / playerCharacter.nextLevelXp) * 100}%`;
            playerXPBar.textContent = `${playerCharacter.xp}/${playerCharacter.nextLevelXp}`;
            playerGoldDisplay.textContent = playerCharacter.gold;

            inventoryList.innerHTML = '';
            if (playerCharacter.inventory.length === 0) {
                const li = document.createElement('li');
                li.textContent = "Inventário vazio.";
                inventoryList.appendChild(li);
            } else {
                playerCharacter.inventory.forEach(item => {
                    const li = document.createElement('li');
                    li.textContent = item;
                    inventoryList.appendChild(li);
                });
            }
        }

        function showScreen(screen) {
            loadingScreen.classList.add('hidden');
            characterCreationScreen.classList.add('hidden');
            mainGameScreen.classList.add('hidden');
            screen.classList.remove('hidden');
        }

        function randomEnemy(dungeon) {
            const enemies = dungeon.enemies;
            return JSON.parse(JSON.stringify(enemies[Math.floor(Math.random() * enemies.length)]));
        }

        function startBattle(enemy) {
            currentEnemy = enemy;
            enemyEncounterDiv.classList.remove('hidden');
            combatActionsDiv.classList.remove('hidden');
            exploreDungeonButton.classList.add('hidden');
            enemyNameDisplay.textContent = enemy.name;
            enemyCurrentHPDisplay.textContent = enemy.hp;
            enemyMaxHPDisplay.textContent = enemy.hp;
            enemyHPBar.style.width = '100%';
            logMessage(`Você encontrou um ${enemy.name}!`);
        }

        function endBattle() {
            currentEnemy = null;
            enemyEncounterDiv.classList.add('hidden');
            combatActionsDiv.classList.add('hidden');
            exploreDungeonButton.classList.remove('hidden');
        }

        function checkLevelUp() {
            if (playerCharacter.xp >= playerCharacter.nextLevelXp) {
                playerCharacter.level += 1;
                playerCharacter.xp -= playerCharacter.nextLevelXp;
                playerCharacter.nextLevelXp = Math.floor(playerCharacter.nextLevelXp * 1.5);
                const hpIncrease = 10;
                const attackIncrease = 2;
                const defenseIncrease = 2;
                playerCharacter.maxHp += hpIncrease;
                playerCharacter.hp = playerCharacter.maxHp;
                playerCharacter.attack += attackIncrease;
                playerCharacter.defense += defenseIncrease;
                statIncreasesList.innerHTML = `
                    <li>+${hpIncrease} HP</li>
                    <li>+${attackIncrease} Ataque</li>
                    <li>+${defenseIncrease} Defesa</li>
                `;
                levelUpPlayerName.textContent = playerCharacter.name;
                newPlayerLevel.textContent = playerCharacter.level;
                levelUpModal.classList.remove('hidden');
                logMessage(`Você subiu para o nível ${playerCharacter.level}!`);
                updatePlayerUI();
            }
        }

        function playerAttack() {
            if (!currentEnemy) return;
            const damage = Math.max(0, playerCharacter.attack - currentEnemy.defense);
            currentEnemy.hp -= damage;
            logMessage(`Você causou ${damage} de dano ao ${currentEnemy.name}.`);
            if (currentEnemy.hp <= 0) {
                logMessage(`Você derrotou ${currentEnemy.name}!`);
                playerCharacter.xp += currentEnemy.xp;
                playerCharacter.gold += currentEnemy.gold;
                checkLevelUp();
                endBattle();
                updatePlayerUI();
            } else {
                enemyTurn();
            }
            updateEnemyUI();
        }

        function useSpecialAbility() {
            if (specialAbilityCooldown) {
                logMessage("Sua habilidade especial ainda está recarregando.");
                return;
            }

            if (!currentEnemy) return;
            let damage = 0;
            if (playerCharacter.class === 'Ladino') {
                damage = Math.max(0, (playerCharacter.attack * 2) - currentEnemy.defense);
                logMessage("Você usa Ataque Furtivo!");
            } else if (playerCharacter.class === 'Mago') {
                damage = Math.max(0, (playerCharacter.attack + 10) - (currentEnemy.defense * 0.5));
                logMessage("Você lança Bola de Fogo!");
            } else if (playerCharacter.class === 'Cavaleiro') {
                damage = 0;
                playerCharacter.isShielded = true;
                logMessage("Você ativa Escudo Imbatível!");
            }

            if (damage > 0) {
                currentEnemy.hp -= damage;
                logMessage(`Habilidade causou ${damage} de dano ao ${currentEnemy.name}.`);
                if (currentEnemy.hp <= 0) {
                    logMessage(`Você derrotou ${currentEnemy.name}!`);
                    playerCharacter.xp += currentEnemy.xp;
                    playerCharacter.gold += currentEnemy.gold;
                    checkLevelUp();
                    endBattle();
                    updatePlayerUI();
                } else {
                    enemyTurn();
                }
                updateEnemyUI();
            }

            specialAbilityCooldown = true;
        }

        function enemyTurn() {
            if (!currentEnemy) return;
            let damage = Math.max(0, currentEnemy.attack - playerCharacter.defense);
            if (playerCharacter.isShielded) {
                damage = 0;
                playerCharacter.isShielded = false;
                logMessage(`${currentEnemy.name} ataca, mas seu Escudo Imbatível bloqueia todo o dano!`);
            } else {
                playerCharacter.hp -= damage;
                logMessage(`${currentEnemy.name} causa ${damage} de dano a você.`);
            }

            if (playerCharacter.hp <= 0) {
                playerCharacter.hp = 0;
                updatePlayerUI();
                gameOver();
            } else {
                updatePlayerUI();
            }
        }

        function updateEnemyUI() {
            if (!currentEnemy) return;
            enemyCurrentHPDisplay.textContent = currentEnemy.hp;
            enemyHPBar.style.width = `${(currentEnemy.hp / currentEnemy.maxHp) * 100}%`;
        }

        function gameOver() {
            gameOverModal.classList.remove('hidden');
            gameOverMessage.textContent = "Você foi derrotado... Mas sempre há uma próxima tentativa!";
            logMessage("Fim de jogo.");
        }

        restartGameButton.addEventListener('click', () => {
            location.reload();
        });

        closeLevelUpModalButton.addEventListener('click', () => {
            levelUpModal.classList.add('hidden');
        });

        attackButton.addEventListener('click', () => {
            playerAttack();
        });

        specialAbilityButton.addEventListener('click', () => {
            useSpecialAbility();
        });

        leaveDungeonButton.addEventListener('click', () => {
            dungeonView.classList.add('hidden');
            townView.classList.remove('hidden');
            currentDungeon = null;
            currentEnemy = null;
            specialAbilityCooldown = false;
            endBattle();
        });

        exploreDungeonButton.addEventListener('click', () => {
            if (currentEnemy) {
                logMessage("Você não pode explorar enquanto está em combate!");
                return;
            }
            const enemy = randomEnemy(currentDungeon);
            startBattle(enemy);
            specialAbilityCooldown = false;
        });

        goToDungeonSelectionButton.addEventListener('click', () => {
            townView.classList.add('hidden');
            dungeonSelectionView.classList.remove('hidden');
        });

        backToTownButtonDungeon.addEventListener('click', () => {
            dungeonSelectionView.classList.add('hidden');
            townView.classList.remove('hidden');
        });

        restButton.addEventListener('click', () => {
            playerCharacter.hp = playerCharacter.maxHp;
            logMessage("Você descansou e recuperou todo o seu HP.");
            updatePlayerUI();
        });

        // Continua: autenticação, carregamento de dados, criação de personagem, persistência no Firebase, etc.

    </script>
</body>
</html>
