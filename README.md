<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Mucize: Paris'i Kurtar - Hikaye Modu</title>
    <style>
        body {
            background-color: #000;
            color: #fff;
            font-family: 'Courier New', Courier, monospace;
            text-align: center;
            margin: 0;
            padding: 0;
            overflow: hidden;
            user-select: none;
            -webkit-user-select: none;
        }
        #game-container {
            position: relative;
            width: 100vw;
            height: calc(100vh - 120px);
            background-color: #111;
        }
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
        #story-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(10, 5, 20, 0.98);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            padding: 20px;
            box-sizing: border-box;
        }
        #story-screen h2 { color: #d32f2f; margin-bottom: 10px; font-size: 20px; text-shadow: 0 0 10px #b71c1c; }
        #story-screen p { color: #ddd; font-size: 12px; max-width: 440px; line-height: 1.5; margin-bottom: 20px; text-align: left; }
        .action-btn {
            padding: 12px 30px;
            background-color: #d32f2f;
            color: white;
            font-size: 15px;
            font-weight: bold;
            border: 2px solid #fff;
            border-radius: 8px;
            cursor: pointer;
            box-shadow: 0 0 15px rgba(211,47,47,0.8);
        }
        .ui {
            font-size: 9px;
            font-weight: bold;
            padding: 4px;
            background: #111;
            border-top: 2px solid #d32f2f;
            color: #ffeb3b;
            display: flex;
            justify-content: space-around;
            align-items: center;
            height: 50px;
            box-sizing: border-box;
        }
        .inventory-box {
            display: flex;
            gap: 3px;
            align-items: center;
        }
        .inv-slot {
            width: 24px;
            height: 24px;
            border: 1px solid #d32f2f;
            background: #222;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 11px;
        }
        #joystick-zone {
            position: absolute;
            bottom: 20px;
            left: 20px;
            width: 130px;
            height: 130px;
            background: rgba(255, 255, 255, 0.08);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            z-index: 10;
        }
        #joystick-knob {
            position: absolute;
            top: 40px;
            left: 40px;
            width: 50px;
            height: 50px;
            background: rgba(211, 47, 47, 0.85);
            border: 2px solid #fff;
            border-radius: 50%;
            pointer-events: none;
        }
        .control-btns {
            position: absolute;
            bottom: 25px;
            right: 25px;
            display: flex;
            gap: 10px;
            z-index: 10;
        }
        .circle-btn {
            width: 75px;
            height: 75px;
            border: 3px solid #fff;
            border-radius: 50%;
            color: white;
            font-weight: bold;
            font-size: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            box-shadow: 0 0 12px rgba(0,0,0,0.8);
            text-align: center;
        }
        #action-btn { background: rgba(255, 152, 0, 0.9); }
        #attack-btn { background: rgba(244, 67, 54, 0.9); display: none; }
        
        #map-modal {
            display: none;
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9);
            z-index: 50;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .map-btn {
            width: 220px;
            padding: 10px;
            margin: 6px;
            background: #333;
            color: #fff;
            border: 2px solid #555;
            border-radius: 6px;
            font-family: 'Courier New', monospace;
            font-weight: bold;
            cursor: pointer;
        }
        .map-btn.unlocked { background: #d32f2f; border-color: #ffeb3b; }
        .map-btn.locked { background: #222; color: #777; border-color: #444; cursor: not-allowed; }

        #map-toggle-btn {
            display: none;
            position: absolute;
            top: 10px;
            right: 15px;
            background: #ffeb3b;
            color: #000;
            border: 2px solid #d32f2f;
            padding: 6px 12px;
            font-weight: bold;
            border-radius: 5px;
            cursor: pointer;
            z-index: 20;
            font-size: 10px;
        }

        #restart-btn {
            display: none;
            position: absolute;
            top: 65%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 10px 20px;
            background-color: #d32f2f;
            color: white;
            font-size: 14px;
            font-weight: bold;
            border: 2px solid #fff;
            border-radius: 8px;
            z-index: 60;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <div id="story-screen">
            <h2>MUCİZE: PARİS'İ KURTAR</h2>
            <p>
                • Ses altyapısı ve otomatik oynatma optimize edildi.<br>
                • Düşman ve Gölge Güve sahneleri görünür hale getirildi!<br>
                • Bisikleti alarak HARİTA'nın kilidini aç ve bölgeler arası seyahat et.
            </p>
            <button class="action-btn" onclick="startGame()">MACERAYA BAŞLA</button>
        </div>

        <button id="map-toggle-btn" onclick="toggleMapModal()">🗺️ HARİTA</button>

        <div style="position:absolute; top:8px; left:10px; font-size:9px; color:#ffeb3b; z-index:5; text-align:left; pointer-events:none;">
            🐞 <b style="color:#ff4081;">Lore-Uyumlu Görevler</b>
        </div>

        <canvas id="gameCanvas"></canvas>
        
        <div class="control-btns">
            <button id="action-btn" class="circle-btn" onclick="triggerAction()">EYLEM / GİZLEN</button>
            <button id="attack-btn" class="circle-btn" onclick="triggerAttack()">SALDIR</button>
        </div>

        <div id="map-modal">
            <h3 style="color:#ffeb3b; margin-bottom:15px;">PARİS HARİTASI (Fast Travel)</h3>
            <div id="map-buttons-container"></div>
            <button class="map-btn" style="background:#555; margin-top:15px;" onclick="toggleMapModal()">Kapat</button>
        </div>

        <button id="restart-btn" onclick="resetGame()">Yeniden Başla</button>
    </div>

    <div class="ui">
        <div><span id="loc-name">Usta Fu Dükkanı</span></div>
        <div class="inventory-box">
            Env:
            <div class="inv-slot" id="slot-0">-</div>
            <div class="inv-slot" id="slot-1">-</div>
            <div class="inv-slot" id="slot-2">-</div>
        </div>
        <div>Görev (<span id="quest-num">1</span>/30): <span id="quest-status" style="color:#ff80ab;">Usta Fu'nun Gramofonu</span></div>
    </div>

    <div id="joystick-zone">
        <div id="joystick-knob"></div>
    </div>

<script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    function resize() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight - 120;
    }
    window.addEventListener('resize', resize);
    resize();

    // --- WEB AUDIO API SES SİSTEMİ ---
    let audioCtx = null;
    let musicInterval = null;

    function initAudio() {
        if (!audioCtx) {
            audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }
        if (audioCtx.state === 'suspended') {
            audioCtx.resume();
        }
    }

    const roomMelodies = [
        [220, 277, 329, 440], // 0: Usta Fu Dükkanı
        [150, 190, 220, 300], // 1: Paris Sokakları
        [261, 329, 392, 523], // 2: Dupain-Cheng Evi
        [330, 392, 493, 659], // 3: Marinette'in Odası
        [293, 349, 440, 587], // 4: Okul
        [440, 554, 659, 880], // 5: Eyfel Kulesi
        [110, 115, 130, 140]  // 6: Gölge Güve Sığınağı
    ];

    const chaseMelody = [100, 140, 120, 90];

    function playTone(freq, duration, type = 'sine') {
        if (!audioCtx) return;
        try {
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            osc.type = type;
            osc.frequency.value = freq;
            
            gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);

            osc.connect(gain);
            gain.connect(audioCtx.destination);

            osc.start();
            osc.stop(audioCtx.currentTime + duration);
        } catch(e) {}
    }

    function startMusicLoop() {
        if (musicInterval) clearInterval(musicInterval);
        let noteIndex = 0;

        musicInterval = setInterval(() => {
            if (gameOver) return;
            
            let melody = roomMelodies[currentRoom];
            let waveType = 'sine';
            let speed = 350;

            if (enemy.room === currentRoom && enemy.state === "chase" && !gameWon) {
                melody = chaseMelody;
                waveType = 'sawtooth';
                speed = 180;
            } else if (currentRoom === 6) {
                waveType = 'triangle';
            }

            let freq = melody[noteIndex % melody.length];
            playTone(freq, speed / 1000 * 0.8, waveType);
            noteIndex++;
        }, 350);
    }

    let player = { x: 2.0, y: 8.0, angle: 0, fov: Math.PI / 3, inventory: [], hidden: false };
    let currentRoom = 0;
    const roomNames = [
        "Usta Fu Dükkanı", "Paris Sokakları", "Dupain-Cheng Evi", 
        "Marinette'in Odası", "François Dupont Okulu", "Eyfel Kulesi Zirvesi", "Gölge Güve Sığınağı"
    ];

    let unlockedRooms = [true, false, false, false, false, false, false];
    let questStep = 0;
    let bossHP = 5;
    let endingAnimationStep = 0;

    const quests = [
        "Usta Fu'nun Gramofonu", "Mucize Kutusu'nu Al", "Bisikletle Hızlı İntikal", 
        "Tikki'nin Kurabiyesi", "Plagg'in Peyniri", "Telsiz Mesajı", 
        "Marinette'in Evi", "Balkon Kapısı", "Günlük (Gizli Sırlar)", 
        "Mucizevi Küpeler", "Kutuyu Odada Sakla", "Alya ile Buluşma", 
        "Alya'nın Akıllı Telefonu", "François Dupont Okulu", "Chloé'nin Dolabı", 
        "Kayıp Tarih Kitabı", "Kwami İzi", "Eyfel Kulesi'ne Varış", 
        "Asansör Kartı", "Zirve Anahtarı", "Akuma Tuzağı", 
        "Usta Fu'nun Kehaneti", "Kelebek İzi", "Karanlık Sokak Şifresi", 
        "Karanlık Sığınak", "Sığınak Şifresi", "Mucizevi Ajan Üretimi", 
        "Özel Silah (Yoyo / Baton)", "Güve'nin Tuzakları", "Gölge Güve ile Son Savaş"
    ];

    let maps = [
        [[1,1,1,1,1,2,1,1,1,1],[1,0,0,3,0,0,0,3,0,1],[1,0,1,1,0,1,1,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,0,3,1,1,0,3,1,1],[1,0,0,0,0,0,0,0,0,1],[1,0,1,1,0,1,1,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,1,1,1,1,1],[1,0,0,3,0,0,0,3,0,1],[1,0,0,0,0,0,0,0,0,2],[1,0,3,1,0,0,1,3,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,2],[1,0,3,1,0,0,1,3,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,2,1,1,1,1],[1,0,0,0,0,0,0,0,0,1],[1,0,1,1,1,1,1,1,0,1],[1,0,1,0,0,0,0,1,0,1],[1,0,1,0,3,3,0,1,0,1],[1,0,1,0,0,0,0,1,0,1],[1,0,1,1,2,1,1,1,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,1,1,1,1,1],[1,0,0,0,0,0,0,0,0,1],[1,0,3,3,0,0,3,3,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,2,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,2,1,1,1,1],[1,0,0,3,0,0,0,3,0,1],[1,0,1,1,0,0,1,1,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,3,0,0,0,0,3,0,1],[1,0,0,0,0,0,0,0,2],[1,0,1,1,0,0,1,1,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,2,1,1,1,1],[1,0,0,3,0,0,0,3,0,1],[1,0,1,1,0,0,1,1,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,3,1,0,0,1,3,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]],
        [[1,1,1,1,1,1,1,1,1,1],[1,0,0,0,0,0,0,0,0,1],[1,0,1,1,0,0,1,1,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,3,0,0,0,0,3,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,0,0,0,0,0,0,0,0,1],[1,1,1,1,1,1,1,1,1,1]]
    ];

    let worldObjects = [
        { room: 0, x: 2.0, y: 2.0, name: "Usta Fu Gramofonu", icon: "📻", type: "fu_gramophone", step: 0, collected: false },
        { room: 0, x: 8.0, y: 2.0, name: "Mucize Kutusu", icon: "📦", type: "miraculous_box", step: 1, collected: false },
        { room: 1, x: 5.0, y: 4.0, name: "Bisiklet", icon: "🚲", type: "bike", step: 2, collected: false },
        { room: 1, x: 2.0, y: 7.0, name: "Tikki'nin Kurabiyesi", icon: "🍪", type: "cookie", step: 3, collected: false },
        { room: 1, x: 8.0, y: 4.0, name: "Plagg'in Peyniri", icon: "🧀", type: "cheese", step: 4, collected: false },
        { room: 1, x: 8.0, y: 8.0, name: "Chat Noir Telsizi", icon: "🎙️", type: "radio", step: 5, collected: false },
        { room: 2, x: 3.5, y: 3.5, name: "Dupain-Cheng Evi", icon: "🏠", type: "house_enter", step: 6, collected: false },
        { room: 2, x: 4.0, y: 6.0, name: "Balkon / Üst Kat", icon: "🚪", type: "up_stairs", step: 7, collected: false },
        { room: 3, x: 3.0, y: 3.0, name: "Marinette'in Günlüğü", icon: "📔", type: "diary", step: 8, collected: false },
        { room: 3, x: 7.0, y: 3.0, name: "Uğur Böceği Küpeleri", icon: "🐞", type: "earrings", step: 9, collected: false },
        { room: 3, x: 5.0, y: 5.0, name: "Kutuyu Sakla", icon: "📦", type: "place_box", step: 10, collected: false },
        { room: 1, x: 2.0, y: 4.0, name: "Alya Buluşması", icon: "👥", type: "alya", step: 11, collected: false },
        { room: 1, x: 6.0, y: 7.0, name: "Alya'nın Telefonu", icon: "📱", type: "phone", step: 12, collected: false },
        { room: 4, x: 5.0, y: 8.0, name: "Okul Girişi", icon: "🏫", type: "school_in", step: 13, collected: false },
        { room: 4, x: 3.0, y: 5.0, name: "Chloé'nin Dolabı", icon: "🗄️", type: "chloe", step: 14, collected: false },
        { room: 4, x: 7.0, y: 5.0, name: "Kayıp Tarih Kitabı", icon: "📖", type: "book", step: 15, collected: false },
        { room: 4, x: 5.0, y: 2.0, name: "Kwami İzi", icon: "✨", type: "kwami", step: 16, collected: false },
        { room: 5, x: 5.0, y: 5.0, name: "Eyfel Kulesi", icon: "🗼", type: "eyfel", step: 17, collected: false },
        { room: 5, x: 3.0, y: 5.0, name: "Asansör Kartı", icon: "💳", type: "card", step: 18, collected: false },
        { room: 5, x: 7.0, y: 5.0, name: "Zirve Anahtarı", icon: "🗝️", type: "z_key", step: 19, collected: false },
        { room: 5, x: 5.0, y: 8.0, name: "Akuma Tuzağı", icon: "🦋", type: "trap", step: 20, collected: false },
        { room: 0, x: 5.0, y: 5.0, name: "Usta Fu Kehaneti", icon: "📜", type: "fu_info", step: 21, collected: false },
        { room: 1, x: 6.0, y: 4.0, name: "Kelebek İzi", icon: "🦋", type: "akuma", step: 22, collected: false },
        { room: 1, x: 4.0, y: 2.0, name: "Gizli Sokak Şifresi", icon: "🔢", type: "code", step: 23, collected: false },
        { room: 6, x: 2.0, y: 2.0, name: "Karanlık Sığınak", icon: "👁️", type: "moth_trace", step: 24, collected: false },
        { room: 6, x: 8.0, y: 2.0, name: "Sığınak Şifresi", icon: "🔐", type: "pass", step: 25, collected: false },
        { room: 6, x: 5.0, y: 3.0, name: "Tılsım Üreticisi", icon: "⚙️", type: "maker", step: 26, collected: false },
        { room: 6, x: 3.0, y: 7.0, name: "Uğur Böceği Yoyo'su", icon: "🪀", type: "weapon", step: 27, collected: false },
        { room: 6, x: 5.0, y: 4.0, name: "Güve Tuzakları", icon: "⚡", type: "drones", step: 28, collected: false },
        { room: 6, x: 5.0, y: 6.0, name: "Gölge Güve Boss", icon: "🦹", type: "boss", step: 29, collected: false }
    ];

    let enemy = { room: 1, x: 8.0, y: 8.0, patrolTarget: { x: 2.0, y: 2.0 }, speed: 0.008, state: "patrol" };
    let gameOver = false;
    let gameWon = false;

    let joystickZone = document.getElementById("joystick-zone");
    let joystickKnob = document.getElementById("joystick-knob");
    let touchVector = { x: 0, y: 0 };
    let joystickTouchId = null;
    let lookTouchId = null;
    let lastLookX = 0;

    window.addEventListener("touchstart", (e) => {
        initAudio();
        for (let i = 0; i < e.changedTouches.length; i++) {
            let t = e.changedTouches[i];
            if (t.clientX < window.innerWidth / 2 && joystickTouchId === null) joystickTouchId = t.identifier;
            else if (t.clientX >= window.innerWidth / 2 && lookTouchId === null) { lookTouchId = t.identifier; lastLookX = t.clientX; }
        }
    }, { passive: true });

    window.addEventListener("touchmove", (e) => {
        for (let i = 0; i < e.changedTouches.length; i++) {
            let t = e.changedTouches[i];
            if (t.identifier === joystickTouchId) {
                let rect = joystickZone.getBoundingClientRect();
                let cx = rect.left + rect.width / 2, cy = rect.top + rect.height / 2;
                let x = t.clientX - cx, y = t.clientY - cy, dist = Math.hypot(x, y), max = 45;
                if (dist > max) { x = (x / dist) * max; y = (y / dist) * max; }
                joystickKnob.style.transform = `translate(${x}px, ${y}px)`;
                touchVector = { x: x / max, y: y / max };
            } else if (t.identifier === lookTouchId) {
                player.angle += (t.clientX - lastLookX) * 0.004;
                lastLookX = t.clientX;
            }
        }
    }, { passive: true });

    window.addEventListener("touchend", (e) => {
        for (let i = 0; i < e.changedTouches.length; i++) {
            let t = e.changedTouches[i];
            if (t.identifier === joystickTouchId) { joystickTouchId = null; touchVector = { x: 0, y: 0 }; joystickKnob.style.transform = `translate(0px, 0px)`; }
            if (t.identifier === lookTouchId) lookTouchId = null;
        }
    }, { passive: true });

    function startGame() { 
        initAudio();
        document.getElementById("story-screen").style.display = "none";
        startMusicLoop();
    }

    function toggleMapModal() {
        let modal = document.getElementById("map-modal");
        if (modal.style.display === "flex") modal.style.display = "none";
        else { buildMapButtons(); modal.style.display = "flex"; }
    }

    function buildMapButtons() {
        let container = document.getElementById("map-buttons-container");
        container.innerHTML = "";
        roomNames.forEach((name, idx) => {
            let btn = document.createElement("button");
            btn.className = "map-btn " + (unlockedRooms[idx] ? "unlocked" : "locked");
            btn.innerText = name + (unlockedRooms[idx] ? "" : " 🔒");
            btn.onclick = () => {
                if (unlockedRooms[idx]) {
                    currentRoom = idx; player.x = 5.0; player.y = 8.5;
                    document.getElementById("loc-name").innerText = roomNames[currentRoom];
                    toggleMapModal();
                } else alert("⚠️ Bu bölge henüz kilitli!");
            };
            container.appendChild(btn);
        });
    }

    function updateInventoryUI() {
        document.getElementById("slot-0").innerText = player.inventory[0] ? player.inventory[0].icon : "-";
        document.getElementById("slot-1").innerText = player.inventory[1] ? player.inventory[1].icon : "-";
        document.getElementById("slot-2").innerText = player.inventory[2] ? player.inventory[2].icon : "-";
        document.getElementById("quest-num").innerText = questStep + 1;
        document.getElementById("quest-status").innerText = quests[questStep] || "Görevi Tamamla";
        if (questStep === 29) document.getElementById("attack-btn").style.display = "flex";
    }

    function advanceQuest() {
        questStep++;
        if (questStep >= quests.length) {
            triggerEndingSequence();
            return;
        }
        updateInventoryUI();
    }

    function triggerEndingSequence() {
        gameWon = true;
        if (musicInterval) clearInterval(musicInterval);
        endingAnimationStep = 1;
        setTimeout(() => { endingAnimationStep = 2; }, 2500);
        setTimeout(() => {
            endingAnimationStep = 3;
            document.getElementById("restart-btn").style.display = "block";
        }, 5000);
    }

    function triggerAction() {
        initAudio();
        if (gameOver || gameWon) return;
        let px = Math.floor(player.x), py = Math.floor(player.y);
        let nearLocker = false;
        [[0,1],[0,-1],[1,0],[-1,0]].forEach(c => {
            let cx = px + c[0], cy = py + c[1];
            if (cx >= 0 && cx < 10 && cy >= 0 && cy < 10 && maps[currentRoom][cy][cx] === 3) nearLocker = true;
        });

        if (maps[currentRoom][py][px] === 3 || nearLocker) {
            player.hidden = !player.hidden;
            alert(player.hidden ? "🪞 Kabine girdin!" : "🪞 Kabinden çıktın.");
            return;
        }

        worldObjects.filter(o => o.room === currentRoom).forEach(obj => {
            if (Math.hypot(player.x - obj.x, player.y - obj.y) < 2.2 && obj.step === questStep && !obj.collected) {
                obj.collected = true;
                player.inventory.push(obj);
                alert(`✅ ${obj.name} alındı!`);
                
                if (obj.type === "miraculous_box") { currentRoom = 1; unlockedRooms[1] = true; player.x = 5.0; player.y = 8.5; document.getElementById("loc-name").innerText = roomNames[currentRoom]; enemy.room = 1; enemy.x = 8.0; enemy.y = 8.0; }
                else if (obj.type === "bike") { document.getElementById("map-toggle-btn").style.display = "block"; unlockedRooms = [true, true, true, true, true, true, true]; alert("🗺️ Harita Özelliği Açıldı!"); }
                else if (obj.type === "house_enter" || obj.type === "up_stairs") { currentRoom = (obj.type === "house_enter") ? 2 : 3; unlockedRooms[currentRoom] = true; player.x = 5.0; player.y = 8.5; document.getElementById("loc-name").innerText = roomNames[currentRoom]; }
                else if (obj.type === "school_in") { currentRoom = 4; unlockedRooms[4] = true; player.x = 5.0; player.y = 8.5; document.getElementById("loc-name").innerText = roomNames[currentRoom]; }
                else if (obj.type === "eyfel") { currentRoom = 5; unlockedRooms[5] = true; player.x = 5.0; player.y = 8.5; document.getElementById("loc-name").innerText = roomNames[currentRoom]; }
                else if (obj.type === "moth_trace") { currentRoom = 6; unlockedRooms[6] = true; player.x = 5.0; player.y = 8.5; document.getElementById("loc-name").innerText = roomNames[currentRoom]; enemy.room = 6; enemy.x = 5.0; enemy.y = 2.0; enemy.state = "chase"; }

                advanceQuest();
                updateInventoryUI();
            }
        });
    }

    function triggerAttack() {
        if (questStep !== 29 || gameOver || gameWon) return;
        if (Math.hypot(player.x - enemy.x, player.y - enemy.y) < 5.0) {
            bossHP--;
            alert(`💥 Yoyo İsabeti! Can: ${bossHP}/5`);
            if (bossHP <= 0) triggerEndingSequence();
        } else alert("Gölge Güve çok uzakta!");
    }

    function update() {
        if (gameOver || gameWon) return;
        if (touchVector.x !== 0 || touchVector.y !== 0) {
            if (player.hidden) player.hidden = false;
            let speed = player.inventory.some(i => i.type === "bike") ? 0.045 : 0.026;
            let dx = (-touchVector.y * Math.cos(player.angle) - touchVector.x * Math.sin(player.angle)) * speed;
            let dy = (-touchVector.y * Math.sin(player.angle) + touchVector.x * Math.cos(player.angle)) * speed;
            let cmap = maps[currentRoom];

            if (cmap[Math.floor(player.y)][Math.floor(player.x + dx)] !== 1 && cmap[Math.floor(player.y)][Math.floor(player.x + dx)] !== 3) player.x += dx;
            if (cmap[Math.floor(player.y + dy)][Math.floor(player.x)] !== 1 && cmap[Math.floor(player.y + dy)][Math.floor(player.x)] !== 3) player.y += dy;
        }

        // Son odada (Gölge Güve Sığınağı) düşman direkt oyuncuyu takip eder
        if (currentRoom === 6) {
            enemy.room = 6;
            enemy.state = "chase";
        }

        if (enemy.room === currentRoom && !gameWon) {
            let dist = Math.hypot(player.x - enemy.x, player.y - enemy.y);
            
            if (player.hidden) {
                enemy.state = "patrol";
            } else if (dist < 4.5 || enemy.state === "chase") {
                enemy.state = "chase";
                let ang = Math.atan2(player.y - enemy.y, player.x - enemy.x);
                enemy.x += Math.cos(ang) * (enemy.speed * (currentRoom === 6 ? 0.7 : 1.0)); 
                enemy.y += Math.sin(ang) * (enemy.speed * (currentRoom === 6 ? 0.7 : 1.0));
                
                if (dist < 0.6) { 
                    gameOver = true; 
                    if (musicInterval) clearInterval(musicInterval);
                    document.getElementById("restart-btn").style.display = "block"; 
                }
            }

            if (enemy.state === "patrol" && currentRoom !== 6) {
                if (Math.hypot(enemy.patrolTarget.x - enemy.x, enemy.patrolTarget.y - enemy.y) < 0.5) {
                    enemy.patrolTarget.x = 2.0 + Math.random() * 6.0; enemy.patrolTarget.y = 2.0 + Math.random() * 6.0;
                }
                let ang = Math.atan2(enemy.patrolTarget.y - enemy.y, enemy.patrolTarget.x - enemy.x);
                enemy.x += Math.cos(ang) * (enemy.speed * 0.4); enemy.y += Math.sin(ang) * (enemy.speed * 0.4);
            }
        }
    }

    function draw() {
        ctx.fillStyle = "#1a0033"; ctx.fillRect(0, 0, canvas.width, canvas.height / 2);
        ctx.fillStyle = "#111111"; ctx.fillRect(0, canvas.height / 2, canvas.width, canvas.height / 2);

        let rays = canvas.width, fov = player.fov, cmap = maps[currentRoom];
        for (let i = 0; i < rays; i += 2) {
            let ra = player.angle - fov / 2 + (i / rays) * fov;
            let dist = 0, hit = false, ex = Math.cos(ra), ey = Math.sin(ra);
            while (!hit && dist < 15) {
                dist += 0.05;
                let tx = Math.floor(player.x + ex * dist), ty = Math.floor(player.y + ey * dist);
                if (tx < 0 || tx >= 10 || ty < 0 || ty >= 10) { hit = true; dist = 15; }
                else if (cmap[ty][tx] === 1 || cmap[ty][tx] === 3) hit = true;
            }
            dist *= Math.cos(ra - player.angle);
            let h = Math.min(canvas.height, canvas.height / dist);
            ctx.fillStyle = cmap[Math.floor(player.y + ey * dist)]?.[Math.floor(player.x + ex * dist)] === 3 ? "#ff4081" : "#880e4f";
            ctx.fillRect(i, (canvas.height - h) / 2, 2, h);
        }

        // Nesneleri ve Düşmanı (Gölge Güve) ekrana çizdirme
        let renderItems = [];

        worldObjects.filter(o => o.room === currentRoom && !o.collected && o.step === questStep).forEach(obj => {
            renderItems.push({ x: obj.x, y: obj.y, icon: obj.icon, type: "object" });
        });

        if (enemy.room === currentRoom && !gameWon) {
            renderItems.push({ x: enemy.x, y: enemy.y, icon: currentRoom === 6 ? "🦹" : "🦋", type: "enemy" });
        }

        // Görüş açısına göre sıralayıp çizme
        renderItems.forEach(item => {
            let dx = item.x - player.x, dy = item.y - player.y;
            let dist = Math.hypot(dx, dy);
            let ang = Math.atan2(dy, dx) - player.angle;
            while (ang < -Math.PI) ang += Math.PI * 2;
            while (ang > Math.PI) ang -= Math.PI * 2;
            if (Math.abs(ang) < player.fov && dist > 0.2) {
                let screenX = (canvas.width / 2) + Math.tan(ang) * (canvas.width / 2);
                let size = Math.min(150, canvas.height / dist);
                ctx.font = `${Math.max(18, size)}px sans-serif`;
                ctx.textAlign = "center";
                ctx.fillText(item.icon, screenX, (canvas.height / 2) + (size / 4));
            }
        });

        if (gameOver) {
            ctx.fillStyle = "rgba(0,0,0,0.95)"; ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "#ff1744"; ctx.font = "bold 20px 'Courier New'"; ctx.textAlign = "center";
            ctx.fillText("AKUMA SENİ YAKALADI!", canvas.width / 2, canvas.height / 2 - 15);
            ctx.fillStyle = "#ffffff"; ctx.font = "13px 'Courier New'";
            ctx.fillText("Gölge Güve seni akumalandırdı...", canvas.width / 2, canvas.height / 2 + 15);
        } else if (gameWon) {
            ctx.fillStyle = "rgba(0,0,0,0.95)"; ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.textAlign = "center";
            
            if (endingAnimationStep === 1) {
                ctx.fillStyle = "#ffeb3b"; ctx.font = "bold 16px 'Courier New'";
                ctx.fillText("Gölge Güve son saldırısını yapıyor...", canvas.width / 2, canvas.height / 2);
            } else if (endingAnimationStep === 2) {
                ctx.fillStyle = "#ff4081"; ctx.font = "bold 16px 'Courier New'";
                ctx.fillText("Maskesi düşüyor... Güçleri tükeniyor...", canvas.width / 2, canvas.height / 2 - 15);
                ctx.fillStyle = "#ffffff"; ctx.font = "14px 'Courier New'";
                ctx.fillText("O Gabriel Agreste!", canvas.width / 2, canvas.height / 2 + 15);
            } else if (endingAnimationStep >= 3) {
                ctx.fillStyle = "#00e676"; ctx.font = "bold 20px 'Courier New'";
                ctx.fillText("GABRIEL AGRESTE YENİLDİ!", canvas.width / 2, canvas.height / 2 - 25);
                ctx.fillStyle = "#ffeb3b"; ctx.font = "bold 16px 'Courier New'";
                ctx.fillText("Bu bir son mu? Yoksa başka bir şey mi var...?", canvas.width / 2, canvas.height / 2 + 15);
            }
        }
    }

    function resetGame() {
        player.x = 2.0; player.y = 8.0; player.inventory = []; player.hidden = false;
        currentRoom = 0; questStep = 0; bossHP = 5; endingAnimationStep = 0;
        unlockedRooms = [true, false, false, false, false, false, false];
        enemy.room = 1; enemy.x = 8.0; enemy.y = 8.0; enemy.state = "patrol";
        worldObjects.forEach(o => o.collected = false);
        gameOver = false; gameWon = false;
        document.getElementById("restart-btn").style.display = "none";
        document.getElementById("attack-btn").style.display = "none";
        document.getElementById("map-toggle-btn").style.display = "none";
        startMusicLoop();
        updateInventoryUI();
        document.getElementById("loc-name").innerText = roomNames[currentRoom];
    }

    function loop() { update(); draw(); requestAnimationFrame(loop); }
    loop();
</script>

</body>
</html>

