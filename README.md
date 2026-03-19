# Beat-target-panic
<!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Archery Focus Tool</title>
    <style>
        body { font-family: sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        .lang-switch { margin-bottom: 20px; display: flex; gap: 10px; }
        .lang-btn { background: #333; color: white; border: 1px solid #555; padding: 8px 15px; border-radius: 5px; cursor: pointer; }
        .lang-btn.active { background: gold; color: black; font-weight: bold; }
        .header { width: 100%; max-width: 400px; display: flex; justify-content: space-between; margin-bottom: 20px; }
        .stat-box { background: #1e1e1e; padding: 10px; border-radius: 10px; border: 1px solid #333; flex: 1; margin: 0 5px; }
        .label { display: block; font-size: 0.7rem; color: #888; text-transform: uppercase; }
        .value { font-size: 1.8rem; font-weight: bold; }
        #coin { width: 130px; height: 130px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 1.2rem; font-weight: bold; border: 6px solid #444; background: #222; margin: 20px 0; transition: transform 0.2s; }
        .instruction-card { background: #1e1e1e; padding: 20px; border-radius: 15px; min-height: 80px; width: 90%; max-width: 400px; margin-bottom: 25px; border-left: 5px solid gold; font-size: 1.1rem; display: flex; align-items: center; justify-content: center; }
        .roll-btn { background: gold; color: black; border: none; padding: 18px 35px; font-size: 1.2rem; font-weight: bold; border-radius: 50px; margin-bottom: 15px; cursor: pointer; width: 80%; max-width: 300px; -webkit-tap-highlight-color: transparent; }
        .reset-btn { background: transparent; color: #666; border: 1px solid #444; padding: 8px 20px; border-radius: 5px; font-size: 0.9rem; cursor: pointer; margin-bottom: 30px; }
        .controls { display: flex; gap: 50px; }
        .btn { width: 80px; height: 80px; border-radius: 50%; border: none; font-size: 2.5rem; color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; -webkit-tap-highlight-color: transparent; }
        .plus { background: #2e7d32; }
        .minus { background: #c62828; }
        .btn:active, .roll-btn:active { opacity: 0.7; transform: scale(0.95); }
        .color-head { border-color: #4caf50 !important; color: #4caf50; box-shadow: 0 0 15px rgba(76, 175, 80, 0.3); }
        .color-tail { border-color: #2196f3 !important; color: #2196f3; box-shadow: 0 0 15px rgba(33, 150, 243, 0.3); }
    </style>
</head>
<body>

    <div class="lang-switch">
        <button id="btn-sk" class="lang-btn active" onclick="setLanguage('sk')">SK</button>
        <button id="btn-en" class="lang-btn" onclick="setLanguage('en')">EN</button>
    </div>

    <div class="header">
        <div class="stat-box"><span class="label" id="label-rolls">Hody</span><span id="rolls" class="value">0</span></div>
        <div class="stat-box"><span class="label" id="label-score">Skóre</span><span id="score" class="value">0</span></div>
    </div>

    <div id="coin">?</div>
    <div id="instruction" class="instruction-card">Priprav sa na čiaru...</div>

    <button id="roll-btn-text" class="roll-btn" onclick="tossCoin()">HODIŤ MINCOU</button>
    <button id="reset-btn-text" class="reset-btn" onclick="resetGame()">RESETOVAŤ</button>

    <div class="controls">
        <button class="btn minus" onclick="updateScore(-1)">−</button>
        <button class="btn plus" onclick="updateScore(1)">+</button>
    </div>

    <script>
        // Premenné musia byť globálne, aby ich funkcie videli
        var score = 0;
        var rollsCount = 0;
        var currentLang = 'sk';

        var translations = {
            sk: { rolls: "Hody", score: "Skóre", ready: "Priprav sa na čiaru...", rollBtn: "HODIŤ MINCOU", resetBtn: "RESETOVAŤ", head: "HLAVA", tail: "OREL", 
                  shot: "<strong>VÝSTREL:</strong> Perfektný proces. Mantra 'Ťahám...', nechaj sa prekvapiť.", 
                  down: "<strong>LET DOWN:</strong> Mier na stred 6s s prstom na spúšti, potom zlož.", askReset: "Naozaj resetovať?" },
            en: { rolls: "Rolls", score: "Score", ready: "Get ready...", rollBtn: "TOSS COIN", resetBtn: "RESET", head: "HEADS", tail: "TAILS", 
                  shot: "<strong>SHOT:</strong> Perfect process. Mantra 'Pulling...', surprise release.", 
                  down: "<strong>LET DOWN:</strong> Aim for 6s with finger on trigger, then let down.", askReset: "Really reset?" }
        };

        function setLanguage(lang) {
            currentLang = lang;
            document.getElementById('btn-sk').className = (lang === 'sk' ? 'lang-btn active' : 'lang-btn');
            document.getElementById('btn-en').className = (lang === 'en' ? 'lang-btn active' : 'lang-btn');
            document.getElementById('label-rolls').innerText = translations[lang].rolls;
            document.getElementById('label-score').innerText = translations[lang].score;
            document.getElementById('roll-btn-text').innerText = translations[lang].rollBtn;
            document.getElementById('reset-btn-text').innerText = translations[lang].resetBtn;
            if (rollsCount === 0) document.getElementById('instruction').innerHTML = translations[lang].ready;
        }

        function tossCoin() {
            var isHead = Math.random() < 0.5;
            rollsCount++;
            document.getElementById('rolls').innerText = rollsCount;
            
            var coinEl = document.getElementById('coin');
            coinEl.style.transform = "scale(0.8)";
            coinEl.innerText = "...";

            setTimeout(function() {
                coinEl.style.transform = "scale(1)";
                if (isHead) {
                    coinEl.innerText = translations[currentLang].head;
                    coinEl.className = "color-head";
                    document.getElementById('instruction').innerHTML = translations[currentLang].shot;
                } else {
                    coinEl.innerText = translations[currentLang].tail;
                    coinEl.className = "color-tail";
                    document.getElementById('instruction').innerHTML = translations[currentLang].down;
                }
            }, 200);
        }

        function updateScore(val) {
            score += val;
            document.getElementById('score').innerText = score;
        }

        function resetGame() {
            if (confirm(translations[currentLang].askReset)) {
                score = 0;
                rollsCount = 0;
                document.getElementById('score').innerText = "0";
                document.getElementById('rolls').innerText = "0";
                document.getElementById('coin').innerText = "?";
                document.getElementById('coin').className = "";
                document.getElementById('instruction').innerHTML = translations[currentLang].ready;
            }
        }
    </script>
</body>
</html>

