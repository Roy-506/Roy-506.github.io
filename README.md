# Roy-506.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Epstein Rigged Rock Paper Scissors</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }
        body {
            background: #1a1a1a;
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            gap: 20px;
        }
        .game-container {
            background: #2d2d2d;
            padding: 40px 20px;
            border-radius: 20px;
            box-shadow: 0 0 30px rgba(0,0,0,0.7);
            text-align: center;
            max-width: 500px;
            width: 100%;
        }
        h1 {
            color: #ff4757;
            margin-bottom: 25px;
            font-size: 28px;
        }
        .score-board {
            display: flex;
            justify-content: space-around;
            margin-bottom: 25px;
            font-size: 18px;
        }
        .score-board div {
            background: #3d3d3d;
            padding: 10px 20px;
            border-radius: 10px;
        }
        .choices {
            display: flex;
            justify-content: space-around;
            margin: 30px 0;
            gap: 10px;
            flex-wrap: wrap;
        }
        .choice-btn {
            background: #3d3d3d;
            color: white;
            border: none;
            padding: 15px 25px;
            border-radius: 10px;
            font-size: 18px;
            cursor: pointer;
            transition: all 0.3s ease;
            flex: 1;
            min-width: 100px;
        }
        .choice-btn:hover {
            background: #ff4757;
            transform: scale(1.05);
        }
        .result {
            font-size: 22px;
            margin: 20px 0;
            min-height: 60px;
            color: #f1f1f1;
        }
        .control-btns {
            display: flex;
            gap: 15px;
            justify-content: center;
        }
        .quit-btn, .reset-btn {
            background: #ff4757;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 8px;
            font-size: 16px;
            cursor: pointer;
        }
        .reset-btn {
            background: #27ae60;
        }
        /* 惩罚图片弹窗：强制全屏、无延迟显示 */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: rgba(0,0,0,0.95);
            z-index: 9999; /* 最高层级，确保不被遮挡 */
            align-items: center;
            justify-content: center;
            overflow: hidden;
            animation: fadeIn 0.2s ease-in-out; /* 淡入动画，无卡顿 */
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        .modal-content {
            max-width: 90%;
            max-height: 90%;
            object-fit: contain; /* 保证图片完整显示，不拉伸 */
        }
        .close-btn {
            position: absolute;
            top: 20px;
            right: 30px;
            color: #ff4757;
            font-size: 40px;
            cursor: pointer;
            transition: all 0.2s ease;
        }
        .close-btn:hover {
            color: #fff;
            transform: scale(1.1);
        }
        /* 响应式适配手机 */
        @media (max-width: 480px) {
            h1 {
                font-size: 22px;
            }
            .choice-btn {
                padding: 12px 20px;
                font-size: 16px;
            }
            .result {
                font-size: 18px;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Epstein Rigged RPS (You Can't Win!)</h1>
        <!-- 新增计分板，更完整 -->
        <div class="score-board">
            <div>Win: <span id="winCount">0</span></div>
            <div>Lose: <span id="loseCount">0</span></div>
            <div>Tie: <span id="tieCount">0</span></div>
        </div>
        <div class="choices">
            <button class="choice-btn" data-choice="rock">Rock</button>
            <button class="choice-btn" data-choice="scissors">Scissors</button>
            <button class="choice-btn" data-choice="paper">Paper</button>
        </div>
        <div class="result">Choose Rock/Scissors/Paper to play!</div>
        <div class="control-btns">
            <button class="quit-btn" id="quitBtn">Quit Game</button>
            <button class="reset-btn" id="resetBtn">Reset Game</button>
        </div>
    </div>

    <!-- 惩罚图片弹窗 -->
    <div class="modal" id="punishmentModal">
        <span class="close-btn" id="closeModal">&times;</span>
        <img class="modal-content" id="punishmentImage" alt="Punishment Image">
    </div>

    <script>
        // 游戏核心元素
        const choiceBtns = document.querySelectorAll('.choice-btn');
        const resultDiv = document.querySelector('.result');
        const quitBtn = document.getElementById('quitBtn');
        const resetBtn = document.getElementById('resetBtn');
        const modal = document.getElementById('punishmentModal');
        const closeModal = document.getElementById('closeModal');
        const punishmentImage = document.getElementById('punishmentImage');
        // 计分元素
        const winCountEl = document.getElementById('winCount');
        const loseCountEl = document.getElementById('loseCount');
        const tieCountEl = document.getElementById('tieCount');

        // 初始化计分
        let scores = { win: 0, lose: 0, tie: 0 };

        // 核心：作弊逻辑（电脑永远赢玩家）
        function getRiggedComputerChoice(playerChoice) {
            switch(playerChoice) {
                case 'rock': return 'paper'; // 纸克石头
                case 'scissors': return 'rock'; // 石头克剪刀
                case 'paper': return 'scissors'; // 剪刀克纸
                default: return 'rock';
            }
        }

        // 显示惩罚图片（无延迟、强制弹出）
        function showPunishmentModal() {
            // 替换为你的GitHub Raw图片链接（必须上传图片后替换！）
            const imageUrl = "https://raw.githubusercontent.com/Roy-506/Roy-506.github.io/main/es.webp";
            punishmentImage.src = imageUrl;
            
            // 强制显示弹窗，防止异步加载问题
            modal.style.display = 'flex';
            // 图片加载失败时提示
            punishmentImage.onerror = () => {
                resultDiv.textContent = "Punishment image failed to load!";
                modal.style.display = 'none';
            };
        }

        // 更新计分板
        function updateScores(type) {
            scores[type]++;
            winCountEl.textContent = scores.win;
            loseCountEl.textContent = scores.lose;
            tieCountEl.textContent = scores.tie;
        }

        // 关闭弹窗
        closeModal.addEventListener('click', () => {
            modal.style.display = 'none';
        });
        // 点击弹窗外区域也能关闭
        window.addEventListener('click', (e) => {
            if (e.target === modal) modal.style.display = 'none';
        });

        // 退出游戏
        quitBtn.addEventListener('click', () => {
            resultDiv.textContent = `Game ended! Final Score - Win: ${scores.win}, Lose: ${scores.lose}, Tie: ${scores.tie}`;
            choiceBtns.forEach(btn => btn.disabled = true);
        });

        // 重置游戏（新增，更完整）
        resetBtn.addEventListener('click', () => {
            scores = { win: 0, lose: 0, tie: 0 };
            updateScores('win'); // 触发更新（值为0）
            updateScores('lose');
            updateScores('tie');
            resultDiv.textContent = "Game reset! Choose Rock/Scissors/Paper to play!";
            choiceBtns.forEach(btn => btn.disabled = false);
        });

        // 游戏核心逻辑（输了直接弹图片）
        choiceBtns.forEach(btn => {
            btn.addEventListener('click', () => {
                const playerChoice = btn.dataset.choice;
                const computerChoice = getRiggedComputerChoice(playerChoice);

                // 判定结果（玩家永远赢不了）
                if (playerChoice === computerChoice) {
                    resultDiv.textContent = `You chose ${playerChoice}, Computer chose ${computerChoice} → TIE! Play again.`;
                    updateScores('tie');
                } else {
                    resultDiv.textContent = `You chose ${playerChoice}, Computer chose ${computerChoice} → YOU LOST!`;
                    updateScores('lose');
                    // 输了立即弹图片，无延迟
                    showPunishmentModal();
                }
            });
        });
    </script>
</body>
</html>