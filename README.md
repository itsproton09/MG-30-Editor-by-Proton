<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 Editor | PROTON</title>
    <style>
        /* --- 1. PREMIUM THEME SYSTEM --- */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&family=JetBrains+Mono:wght@500;700&display=swap');

        :root {
            --bg-deep: #050505;
            --bg-surface: #121212;
            --bg-panel: #181818;
            --accent-gold: #D4AF37;
            --accent-gold-dim: rgba(212, 175, 55, 0.1);
            --accent-glow: 0 0 15px rgba(212, 175, 55, 0.2);
            --text-main: #EAEAEA;
            --text-muted: #666;
            --border: 1px solid rgba(255,255,255,0.08);
            --glass: rgba(18, 18, 18, 0.85);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; user-select: none; outline: none; }
        
        body {
            margin: 0; background-color: var(--bg-deep); color: var(--text-main);
            font-family: 'Inter', sans-serif; height: 100vh;
            display: flex; flex-direction: column; overflow: hidden;
            background-image: radial-gradient(circle at 50% 10%, #1a1a1a 0%, #050505 60%);
        }

        /* --- 2. HEADER & LOGO --- */
        header {
            height: 65px; background: var(--glass); border-bottom: var(--border);
            backdrop-filter: blur(12px); display: flex; justify-content: space-between; 
            align-items: center; padding: 0 25px; z-index: 100;
        }
        
        .logo-area { display: flex; align-items: center; gap: 12px; }
        .logo-svg { width: 32px; height: 32px; fill: var(--accent-gold); filter: drop-shadow(0 0 5px rgba(212,175,55,0.4)); }
        .brand-text { display: flex; flex-direction: column; line-height: 1.1; }
        .brand-title { font-weight: 800; font-size: 14px; letter-spacing: 1px; color: #fff; }
        .brand-sub { font-size: 10px; color: var(--accent-gold); font-weight: 600; letter-spacing: 2px; text-transform: uppercase; }

        .header-controls { display: flex; align-items: center; gap: 20px; }
        
        /* Pro Volume Slider */
        .vol-strip { 
            display: flex; align-items: center; gap: 10px; background: rgba(255,255,255,0.03); 
            padding: 6px 14px; border-radius: 30px; border: var(--border); 
        }
        .vol-label { font-size: 10px; font-weight: 700; color: #555; letter-spacing: 1px; }
        input[type=range] { -webkit-appearance: none; width: 100px; height: 4px; background: #333; border-radius: 2px; }
        input[type=range]::-webkit-slider-thumb { 
            -webkit-appearance: none; width: 12px; height: 12px; border-radius: 50%; 
            background: var(--accent-gold); cursor: pointer; box-shadow: 0 0 8px rgba(212,175,55,0.5); 
        }

        .status-dot {
            height: 8px; width: 8px; border-radius: 50%; background: #333;
            box-shadow: 0 0 0 2px #222; transition: 0.3s;
        }
        .status-dot.online { background: #00E676; box-shadow: 0 0 10px #00E676; }

        /* --- 3. LCD DISPLAY (CINEMA LOOK) --- */
        .rig-section {
            background: var(--bg-surface); padding: 25px 0; border-bottom: var(--border);
            display: flex; flex-direction: column; align-items: center; gap: 20px;
        }

        .lcd-frame {
            width: 340px; height: 160px; background: #000; border-radius: 8px;
            border: 1px solid #333; position: relative; overflow: hidden;
            display: flex; flex-direction: column;
            box-shadow: inset 0 0 40px rgba(0,0,0,0.8), 0 10px 30px rgba(0,0,0,0.3);
        }
        /* Glossy Reflection */
        .lcd-frame::after {
            content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%;
            background: linear-gradient(135deg, rgba(255,255,255,0.04) 0%, transparent 40%); pointer-events: none;
        }

        .lcd-meta { 
            height: 28px; background: #080808; border-bottom: 1px solid #1a1a1a; 
            display: flex; justify-content: space-between; padding: 0 12px; align-items: center;
            font-family: 'JetBrains Mono'; font-size: 10px; color: #444; 
        }
        .lcd-main {
            flex: 1; display: flex; flex-direction: column; justify-content: center; align-items: center;
            background: radial-gradient(circle at 50% 120%, #151515 0%, #000 80%);
        }
        .patch-big { font-family: 'JetBrains Mono'; font-size: 3.5rem; color: var(--accent-gold); font-weight: 700; text-shadow: 0 0 20px rgba(212,175,55,0.15); line-height: 1; }
        .patch-small { font-family: 'Inter'; font-size: 0.85rem; letter-spacing: 3px; color: #888; margin-top: 8px; text-transform: uppercase; font-weight: 500; }

        .nav-bar { display: flex; gap: 12px; width: 340px; justify-content: space-between; }
        .nav-btn {
            background: var(--bg-panel); border: var(--border); color: #666; height: 32px; width: 48px;
            border-radius: 4px; cursor: pointer; transition: 0.2s; font-size: 16px; display: grid; place-items: center;
        }
        .nav-btn:hover { border-color: #555; color: #fff; background: #222; }
        .nav-btn:active { background: var(--accent-gold); color: #000; }

        /* --- 4. SIGNAL CHAIN (PREMIUM) --- */
        .chain-strip {
            height: 80px; background: var(--bg-surface); border-bottom: var(--border);
            display: flex; align-items: center; padding: 0 25px; gap: 8px; overflow-x: auto;
        }
        .chain-strip::-webkit-scrollbar { display: none; }

        .block {
            min-width: 65px; height: 44px; background: #161616; border: 1px solid #252525; border-radius: 4px;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            font-size: 10px; font-weight: 700; color: #444; letter-spacing: 0.5px;
            cursor: pointer; transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1); position: relative;
        }
        /* Active (ON) State */
        .block.on { 
            background: linear-gradient(160deg, #221f10, #111);
            border-color: rgba(212, 175, 55, 0.4); 
            color: var(--accent-gold); 
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }
        /* Selected for Editing */
        .block.editing { border-color: #fff; color: #fff; transform: translateY(-2px); box-shadow: 0 5px 15px rgba(0,0,0,0.5); }
        /* Editing + ON */
        .block.on.editing { background: var(--accent-gold); color: #000; border-color: var(--accent-gold); font-weight: 800; box-shadow: 0 0 15px rgba(212,175,55,0.4); }

        /* --- 5. STAGE (KNOBS) --- */
        .model-bar {
            padding: 15px; background: var(--bg-deep); border-bottom: var(--border); display: flex; justify-content: center;
        }
        select {
            background: #111; color: var(--accent-gold); border: 1px solid #333; padding: 8px 20px;
            font-family: 'JetBrains Mono'; font-size: 12px; border-radius: 6px; outline: none; cursor: pointer;
            transition: border 0.2s;
        }
        select:hover { border-color: #555; }
        
        .stage-area { flex: 1; background: var(--bg-deep); position: relative; overflow-y: auto; padding: 50px 20px; }
        .knob-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(75px, 1fr)); 
            gap: 30px; max-width: 800px; margin: 0 auto; display: none;
        }
        .offline-message {
            position: absolute; inset: 0; display: flex; flex-direction: column; justify-content: center; align-items: center;
            color: #222; pointer-events: none; gap: 10px;
        }
        .offline-message span { font-size: 40px; opacity: 0.1; }
        .offline-message p { font-size: 12px; font-weight: 700; letter-spacing: 2px; color: #333; }

        /* Knob SVG */
        .k-wrap { display: flex; flex-direction: column; align-items: center; gap: 8px; }
        svg.k-svg { width: 75px; height: 75px; cursor: ns-resize; filter: drop-shadow(0 4px 8px rgba(0,0,0,0.4)); }
        .k-val { font-family: 'JetBrains Mono'; color: var(--accent-gold); font-size: 13px; font-weight: 500; }
        .k-lbl { font-size: 10px; color: #666; text-transform: uppercase; font-weight: 800; letter-spacing: 1px; }

        /* --- 6. FOOTER --- */
        footer {
            height: 70px; background: var(--glass); border-top: var(--border);
            backdrop-filter: blur(12px); display: flex; gap: 15px; padding: 0 30px; align-items: center;
        }
        .btn {
            flex: 1; height: 40px; background: rgba(255,255,255,0.03); border: var(--border); color: #999;
            font-size: 11px; font-weight: 700; border-radius: 6px; cursor: pointer;
            text-transform: uppercase; letter-spacing: 1px; transition: all 0.2s;
        }
        .btn:hover { background: rgba(255,255,255,0.08); color: #fff; border-color: #555; }
        .btn:active { transform: scale(0.98); }
        
        .btn-mgr { background: var(--accent-gold-dim); border-color: rgba(212,175,55,0.3); color: var(--accent-gold); }
        .btn-mgr:hover { background: var(--accent-gold); color: #000; border-color: var(--accent-gold); }
        
        .btn-con { background: rgba(0,230,118,0.05); border-color: rgba(0,230,118,0.2); color: #00E676; }
        .btn-con:hover { background: rgba(0,230,118,0.15); box-shadow: 0 0 15px rgba(0,230,118,0.1); }

        /* --- 7. MODALS --- */
        .modal-bg {
            position: fixed; inset: 0; background: rgba(0,0,0,0.9); z-index: 9999;
            display: none; justify-content: center; align-items: center; backdrop-filter: blur(5px);
        }
        .modal {
            width: 700px; max-width: 90%; background: #121212; border: 1px solid #333; 
            border-top: 3px solid var(--accent-gold); border-radius: 8px; 
            padding: 30px; max-height: 85vh; display: flex; flex-direction: column;
            box-shadow: 0 25px 60px rgba(0,0,0,0.6);
        }
        .modal-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; border-bottom: 1px solid #222; padding-bottom: 15px; }
        .modal-title { font-size: 14px; font-weight: 800; color: #fff; letter-spacing: 1px; }
        
        .slot-grid {
            display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; overflow-y: auto; padding-right: 5px;
        }
        .slot {
            background: #1a1a1a; border: 1px solid #2a2a2a; padding: 12px; text-align: center;
            border-radius: 4px; font-family: 'JetBrains Mono'; font-size: 12px; color: #666; cursor: pointer; transition: 0.2s;
        }
        .slot:hover { border-color: #666; color: #fff; background: #222; }
        
        .btn-close { background: none; border: none; color: #555; font-weight: 700; cursor: pointer; font-size: 12px; }
        .btn-close:hover { color: #fff; }

    </style>
</head>
<body>

    <div id="managerModal" class="modal-bg">
        <div class="modal">
            <div class="modal-head">
                <span class="modal-title">PATCH MANAGER</span>
                <button class="btn-close" onclick="closeManager()">CLOSE</button>
            </div>
            <p style="color:#666; font-size:11px; margin-bottom:15px; text-transform:uppercase; letter-spacing:1px;">Select a target slot to install file:</p>
            <div class="slot-grid" id="slotGrid"></div>
        </div>
    </div>

    <header>
        <div class="logo-area">
            <svg class="logo-svg" viewBox="0 0 100 100">
                <path d="M50 5 L90 25 L90 75 L50 95 L10 75 L10 25 Z" fill="none" stroke="currentColor" stroke-width="4"/>
                <path d="M30 50 L45 65 L70 35" fill="none" stroke="currentColor" stroke-width="6" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <div class="brand-text">
                <span class="brand-title">NUX EDITOR</span>
                <span class="brand-sub">PROTON STUDIO</span>
            </div>
        </div>
        
        <div class="header-controls">
            <div class="vol-strip">
                <span class="vol-label">OUT VOL</span>
                <input type="range" min="0" max="100" value="100" oninput="setMasterVol(this.value)">
            </div>
            <div class="status-dot" id="statusDot"></div>
        </div>
    </header>

    <div class="rig-section">
        <div class="lcd-frame">
            <div class="lcd-meta">
                <span>USB: <span id="usbState" style="color:#666;">--</span></span>
                <span>BPM 120</span>
            </div>
            <div class="lcd-main">
                <div class="patch-big" id="lcdNum">--</div>
                <div class="patch-small" id="lcdName">STANDBY</div>
            </div>
        </div>
        <div class="nav-bar">
            <div class="nav-btn" onclick="navPatch(-1)">◀</div>
            <div class="nav-btn" onclick="navPatch(1)">▶</div>
        </div>
    </div>

    <div class="chain-strip">
        <div class="block" id="blk-WAH" onclick="selectBlock('WAH')">WAH</div>
        <div class="block" id="blk-CMP" onclick="selectBlock('CMP')">CMP</div>
        <div class="block" id="blk-EFX" onclick="selectBlock('EFX')">EFX</div>
        <div class="block" id="blk-AMP" onclick="selectBlock('AMP')">AMP</div>
        <div class="block" id="blk-NR"  onclick="selectBlock('NR')">NR</div>
        <div class="block" id="blk-EQ"  onclick="selectBlock('EQ')">EQ</div>
        <div class="block" id="blk-MOD" onclick="selectBlock('MOD')">MOD</div>
        <div class="block" id="blk-DLY" onclick="selectBlock('DLY')">DLY</div>
        <div class="block" id="blk-RVB" onclick="selectBlock('RVB')">RVB</div>
        <div class="block" id="blk-IR"  onclick="selectBlock('IR')">IR</div>
    </div>

    <div class="model-bar">
        <select id="modelList" onchange="renderKnobs()" disabled><option>CONNECT DEVICE</option></select>
    </div>

    <div class="stage-area">
        <div id="offlineMsg" class="offline-message">
            <span>⚡</span>
            <p>CONNECT USB TO EDIT</p>
        </div>
        <div class="knob-grid" id="knobGrid"></div>
    </div>

    <footer>
        <button class="btn btn-mgr" onclick="openManager()">Patch Manager</button>
        <button class="btn" onclick="triggerExport()">Export File</button>
        <button class="btn btn-con" onclick="initMIDI()">Connect USB</button>
    </footer>

    <input type="file" id="filePicker" hidden onchange="uploadPatch(this)" accept=".bin,.mg30patch">

<script>
    // ===============================================
    // 1. EXTENDED DATABASE (FULL MODELS)
    // ===============================================
    const DB = {
        'WAH': { 
            'Clyde': [{n:'Pos',cc:10}, {n:'Min',cc:11}, {n:'Max',cc:12}], 
            'Cry Wah': [{n:'Pos',cc:10}, {n:'Min',cc:11}, {n:'Max',cc:12}],
            'V847': [{n:'Pos',cc:10}, {n:'Min',cc:11}, {n:'Max',cc:12}],
            'Horse': [{n:'Pos',cc:10}, {n:'Min',cc:11}, {n:'Max',cc:12}]
        },
        'CMP': { 
            'Rose Comp': [{n:'Sustain',cc:15}, {n:'Level',cc:16}], 
            'K Comp': [{n:'Sustain',cc:15}, {n:'Level',cc:16}, {n:'Clip',cc:17}],
            'Studio Comp': [{n:'Thresh',cc:15}, {n:'Ratio',cc:16}, {n:'Gain',cc:17}, {n:'Attack',cc:18}]
        },
        'EFX': { 
            'Tube Drv': [{n:'Drive',cc:20}, {n:'Tone',cc:21}, {n:'Level',cc:22}], 
            'Blues Drv': [{n:'Gain',cc:20}, {n:'Tone',cc:21}, {n:'Level',cc:22}], 
            'Dist+': [{n:'Dist',cc:20}, {n:'Output',cc:22}], 
            'Rat Dist': [{n:'Dist',cc:20}, {n:'Filter',cc:21}, {n:'Level',cc:22}], 
            'Muff Fuzz': [{n:'Sustain',cc:20}, {n:'Tone',cc:21}, {n:'Level',cc:22}],
            'Morning Drv': [{n:'Vol',cc:20}, {n:'Drive',cc:21}, {n:'Tone',cc:22}],
            'Red Dirt': [{n:'Drive',cc:20}, {n:'Tone',cc:21}, {n:'Level',cc:22}]
        },
        'AMP': { 
            'Jazz Clean': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}], 
            'Deluxe Rvb': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}],
            'Bassman': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}],
            'AC30 Top': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Cut',cc:33}, {n:'Treb',cc:34}],
            'Plexi 100': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}],
            'Brit 800': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}],
            'Recto Dual': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}],
            'Diezel VH4': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}, {n:'Deep',cc:36}],
            'Soldano': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}],
            'Friedman': [{n:'Gain',cc:30}, {n:'Master',cc:31}, {n:'Bass',cc:32}, {n:'Mid',cc:33}, {n:'Treb',cc:34}, {n:'Pres',cc:35}]
        },
        'NR': { 
            'Noise Gate': [{n:'Thresh',cc:40}, {n:'Decay',cc:41}] 
        },
        'EQ': { 
            '6-Band': [{n:'100',cc:45}, {n:'200',cc:46}, {n:'400',cc:47}, {n:'800',cc:48}, {n:'1.6k',cc:49}, {n:'3.2k',cc:50}], 
            '10-Band': [{n:'31',cc:45}, {n:'62',cc:46}, {n:'125',cc:47}, {n:'250',cc:48}, {n:'500',cc:49}, {n:'1k',cc:50}, {n:'2k',cc:51}, {n:'4k',cc:52}, {n:'8k',cc:53}, {n:'16k',cc:54}] 
        },
        'MOD': { 
            'Chorus': [{n:'Rate',cc:60}, {n:'Depth',cc:61}, {n:'Level',cc:62}], 
            'Stereo Cho': [{n:'Rate',cc:60}, {n:'Depth',cc:61}, {n:'Level',cc:62}],
            'Flanger': [{n:'Rate',cc:60}, {n:'Depth',cc:61}, {n:'Fdbk',cc:62}, {n:'Mix',cc:63}],
            'Phaser': [{n:'Rate',cc:60}, {n:'Depth',cc:61}, {n:'Fdbk',cc:62}], 
            'Tremolo': [{n:'Rate',cc:60}, {n:'Depth',cc:61}],
            'Vibe': [{n:'Rate',cc:60}, {n:'Depth',cc:61}, {n:'Mode',cc:62}],
            'Detune': [{n:'Mix',cc:60}, {n:'Pitch',cc:61}, {n:'Level',cc:62}]
        },
        'DLY': { 
            'Analog': [{n:'Time',cc:70}, {n:'Fdbk',cc:71}, {n:'Mix',cc:72}], 
            'Digital': [{n:'Time',cc:70}, {n:'Fdbk',cc:71}, {n:'Mix',cc:72}, {n:'Tone',cc:73}],
            'Tape Echo': [{n:'Time',cc:70}, {n:'Fdbk',cc:71}, {n:'Mix',cc:72}, {n:'Wow',cc:73}],
            'Reverse': [{n:'Time',cc:70}, {n:'Fdbk',cc:71}, {n:'Mix',cc:72}]
        },
        'RVB': { 
            'Room': [{n:'Decay',cc:80}, {n:'Tone',cc:81}, {n:'Mix',cc:82}], 
            'Hall': [{n:'Decay',cc:80}, {n:'Tone',cc:81}, {n:'Mix',cc:82}], 
            'Plate': [{n:'Decay',cc:80}, {n:'Tone',cc:81}, {n:'Mix',cc:82}],
            'Spring': [{n:'Decay',cc:80}, {n:'Tone',cc:81}, {n:'Mix',cc:82}],
            'Shimmer': [{n:'Decay',cc:80}, {n:'Tone',cc:81}, {n:'Mix',cc:82}, {n:'Pitch',cc:83}]
        },
        'IR': { 
            'Guitar Cab': [{n:'Level',cc:90}, {n:'LoCut',cc:91}, {n:'HiCut',cc:92}],
            'Bass Cab': [{n:'Level',cc:90}, {n:'LoCut',cc:91}, {n:'HiCut',cc:92}],
            'Acoustic': [{n:'Level',cc:90}, {n:'LoCut',cc:91}, {n:'HiCut',cc:92}]
        }
    };

    let midiOut=null, midiIn=null, currentPatch=0, editingBlock='AMP', selectedSlot=0;
    let exportWaiting = false;

    // --- INITIALIZATION ---
    window.addEventListener('DOMContentLoaded', () => {
        setOfflineMode();
        generateSlotGrid();
        selectBlock('AMP');
    });

    // --- MIDI ENGINE ---
    function initMIDI() {
        if(!navigator.requestMIDIAccess) return alert("Please use Google Chrome.");
        navigator.requestMIDIAcces        .lcd-content {
            flex: 1; display: flex; flex-direction: column; justify-content: center; align-items: center;
            background: radial-gradient(circle at 50% 120%, #151515 0%, #000 70%);
        }
        .patch-id { 
            font-family: 'JetBrains Mono', monospace; font-size: 3.5rem; 
            color: var(--accent-gold); line-height: 1; font-weight: 700;
            text-shadow: 0 0 20px rgba(212, 175, 55, 0.2);
        }
        .patch-name { 
            font-size: 0.85rem; color: #eee; letter-spacing: 2px; 
            margin-top: 8px; text-transform: uppercase; font-weight: 500;
            opacity: 0.9;
        }

        /* --- 4. SIGNAL CHAIN (MODULES) --- */
        .chain-nav {
            height: 70px; /* Reduced height slightly since icons are gone */
            background: var(--bg-panel);
            display: flex; align-items: center; overflow-x: auto;
            padding: 0 20px; gap: 8px; border-bottom: 1px solid var(--border-subtle);
        }
        /* Hide Scrollbar */
        .chain-nav::-webkit-scrollbar { display: none; }

        .block {
            min-width: 65px; height: 45px; /* More compact professional look */
            background: linear-gradient(145deg, #222, #1a1a1a);
            border: 1px solid #333; border-radius: 4px; /* Sharper corners */
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            font-size: 0.7rem; font-weight: 700; color: #666;
            cursor: pointer; transition: all 0.2s ease;
            letter-spacing: 1px;
        }
        .block:hover { border-color: #555; color: #aaa; }
        .block.active {
            background: linear-gradient(145deg, #2a2510, #1f1c0d);
            border-color: var(--accent-gold); color: var(--accent-gold);
            box-shadow: 0 2px 8px rgba(212, 175, 55, 0.1);
            transform: translateY(0);
        }
        /* Icon class removed/hidden */
        
        /* --- 5. MODEL SELECTOR --- */
        .model-area {
            padding: 12px; background: var(--bg-body); 
            border-bottom: 1px solid var(--border-subtle);
            display: flex; justify-content: center;
        }
        .model-dropdown {
            background: #0a0a0a; color: var(--accent-gold);
            border: 1px solid #333; border-radius: 6px;
            padding: 8px 12px; font-family: 'JetBrains Mono', monospace;
            font-size: 0.85rem; width: 100%; max-width: 400px;
            cursor: pointer; text-align: center;
            transition: border-color 0.2s;
        }
        .model-dropdown:hover { border-color: #555; }
        .model-dropdown:focus { border-color: var(--accent-gold); }

        /* --- 6. MAIN STAGE (KNOBS) --- */
        .stage {
            flex: 1; overflow-y: auto; padding: 40px 20px;
            background: radial-gradient(circle at center, #111, #050505);
        }
        .knob-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(80px, 1fr)); 
            gap: 30px 15px; max-width: 700px; margin: 0 auto;
        }
        .k-wrapper { display: flex; flex-direction: column; align-items: center; }

        /* SVG Knob Styles */
        svg.knob-svg { 
            width: 80px; height: 80px; cursor: ns-resize; touch-action: none;
            filter: drop-shadow(0 5px 10px rgba(0,0,0,0.5));
        }
        .track { fill: none; stroke: #1a1a1a; stroke-width: 6; stroke-linecap: round; }
        .arc { 
            fill: none; stroke: var(--accent-gold); stroke-width: 6; stroke-linecap: round; 
            transform-origin: 50px 50px; transform: rotate(135deg);
            stroke-dasharray: 200; stroke-dashoffset: 200;
            filter: drop-shadow(0 0 2px var(--accent-gold));
            transition: stroke-dashoffset 0.1s linear; 
        }

        .k-val { 
            font-family: 'JetBrains Mono', monospace; color: var(--accent-gold); 
            font-size: 0.9rem; margin-top: 8px; text-shadow: 0 0 10px var(--accent-glow);
        }
        .k-lbl { 
            font-size: 0.65rem; color: #666; font-weight: 700; 
            text-transform: uppercase; letter-spacing: 1px; margin-top: 4px; 
        }

        /* --- 7. FOOTER & ACTIONS --- */
        footer {
            height: 70px; background: var(--glass);
            backdrop-filter: blur(10px);
            border-top: 1px solid var(--border-subtle);
            display: flex; gap: 12px; padding: 0 20px; align-items: center;
        }

        .btn {
            flex: 1; height: 44px;
            background: #1a1a1a; color: #ccc;
            border: 1px solid #333; border-radius: 6px;
            font-weight: 600; font-size: 0.75rem; letter-spacing: 0.5px;
            cursor: pointer; transition: all 0.2s ease;
            text-transform: uppercase;
        }
        .btn:hover { background: #222; border-color: #555; color: #fff; }
        .btn:active { transform: scale(0.98); }
        
        .btn-nav { max-width: 60px; font-size: 1.2rem; font-family: 'Inter', sans-serif;}
        .btn-primary { background: linear-gradient(180deg, #1f1f1f, #111); }
        .btn-connect { 
            background: rgba(0, 230, 118, 0.05); 
            border-color: rgba(0, 230, 118, 0.2); 
            color: var(--safe-green); 
        }

        /* --- 8. SAFETY MODAL --- */
        #safetyOverlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.95); z-index: 9999;
            display: flex; justify-content: center; align-items: center;
        }
        .modal-card {
            background: #111; border: 1px solid #333; border-top: 2px solid var(--accent-gold);
            padding: 40px; width: 90%; max-width: 450px; border-radius: 8px;
            text-align: center; box-shadow: 0 20px 50px rgba(0,0,0,0.5);
        }
        .modal-card h2 { color: #fff; font-size: 1.2rem; letter-spacing: 2px; margin-bottom: 20px; text-transform: uppercase;}
        .modal-info { text-align: left; background: #1a1a1a; padding: 20px; border-radius: 6px; margin-bottom: 25px; border: 1px solid #222;}
        .modal-info p { color: #aaa; font-size: 0.85rem; line-height: 1.6; margin: 0 0 10px 0; }
        .modal-info strong { color: var(--accent-gold); }
        .btn-agree {
            background: var(--accent-gold); color: #000; width: 100%; padding: 15px;
            border: none; border-radius: 4px; font-weight: 800; cursor: pointer;
            text-transform: uppercase; letter-spacing: 1px;
        }
        .btn-agree:hover { background: #ffe066; }

    </style>
</head>
<body>

    <div id="safetyOverlay">
        <div class="modal-card">
            <h2>System Check</h2>
            <div class="modal-info">
                <p><strong>1. Connection Required:</strong> Ensure your NUX MG-30 is connected via USB before proceeding.</p>
                <p><strong>2. Safety Protocol:</strong> This editor uses a 40ms buffer queue to prevent data flooding.</p>
                <p><strong>3. Stability:</strong> Do not disconnect the cable during data sync.</p>
            </div>
            <button class="btn-agree" onclick="document.getElementById('safetyOverlay').style.display='none'">Initialize System</button>
        </div>
    </div>

    <header>
        <div class="brand-container">
            <div class="brand-main">NUX EDITOR</div>
            <div class="brand-sub">BY PROTON</div>
        </div>
        <div class="status-badge" id="statusBadge">
            <div class="status-dot" id="statusDot"></div>
            <span id="statusText">OFFLINE</span>
        </div>
    </header>

    <section class="rig-section">
        <div class="nux-screen">
            <div class="lcd-bar">
                <span>BPM 120</span>
                <span>USB</span>
                <span>VOL 99</span>
            </div>
            <div class="lcd-content">
                <div class="patch-id" id="screenNum">--</div>
                <div class="patch-name" id="screenName">CONNECT USB</div>
            </div>
        </div>
    </section>

    <nav class="chain-nav">
        <div class="block" onclick="setBlock('WAH')">WAH</div>
        <div class="block" onclick="setBlock('CMP')">CMP</div>
        <div class="block" onclick="setBlock('EFX')">EFX</div>
        <div class="block active" onclick="setBlock('AMP')">AMP</div>
        <div class="block" onclick="setBlock('EQ')">EQ</div>
        <div class="block" onclick="setBlock('MOD')">MOD</div>
        <div class="block" onclick="setBlock('DLY')">DLY</div>
        <div class="block" onclick="setBlock('RVB')">RVB</div>
        <div class="block" onclick="setBlock('IR')">IR</div>
    </nav>

    <div class="model-area">
        <select class="model-dropdown" id="modelSelector" onchange="loadModelKnobs()">
        </select>
    </div>

    <main class="stage">
        <div class="knob-grid" id="knobContainer"></div>
    </main>

    <footer>
        <button class="btn btn-nav" onclick="prevPatch()">◀</button>
        <button class="btn btn-nav" onclick="nextPatch()">▶</button>
        <button class="btn btn-primary" onclick="document.getElementById('fileInput').click()">Load BIN</button>
        <button class="btn btn-primary" onclick="exportPatch()">Save Patch</button>
        <button class="btn btn-connect" onclick="connectMIDI()">Connect USB</button>
    </footer>

    <input type="file" id="fileInput" hidden onchange="importPatch(this)" accept="*">

<script>
    // ===============================================
    // 1. DATA: MG-30 DATABASE
    // ===============================================
    const MG30_DB = {
        'WAH': { 'Clyde Wah': ['Pos', 'Min', 'Max'], 'Cry Wah': ['Pos', 'Min', 'Max'], 'V847 Wah': ['Pos', 'Min', 'Max'] },
        'CMP': { 'Rose Comp': ['Sustain', 'Level'], 'K Comp': ['Sustain', 'Level', 'Clip'], 'Studio Comp': ['Thresh', 'Ratio', 'Gain'] },
        'EFX': { 'Tube Screamer': ['Level', 'Tone', 'Drive'], 'Blues Driver': ['Level', 'Tone', 'Gain'], 'Distortion+': ['Dist', 'Output'] },
        'AMP': { 
            'Jazz Clean': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Bright', 'Level'],
            'Recto Dual': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Bias', 'Level'],
            'AC30 Top': ['Gain', 'Master', 'Bass', 'Treble', 'Cut', 'Level'],
            'Plexi 100': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Diezel VH4': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Deep', 'Pres', 'Level']
        },
        'EQ': { '6-Band': ['100', '200', '400', '800', '1.6k', '3.2k', 'Level'] },
        'MOD': { 'CE-1': ['Intensity', 'Depth', 'Level'], 'Chorus': ['Rate', 'Depth', 'Level'], 'Phaser': ['Speed', 'Depth'] },
        'DLY': { 'Analog': ['Time', 'Repeat', 'Mix'], 'Digital': ['Time', 'Repeat', 'Mix', 'Tone'] },
        'RVB': { 'Room': ['Decay', 'Tone', 'Mix'], 'Hall': ['Decay', 'Tone', 'Mix'], 'Plate': ['Decay', 'Tone', 'Mix'] },
        'IR': { 'Guitar Cab': ['Level', 'LoCut', 'HiCut'] }
    };

    // Global State
    let currentBlock = 'AMP';
    let currentPatch = 0; // 0-127 (internally)
    let midiAccess = null;
    let midiOutput = null;
    let midiInput = null;
    let isConnected = false;

    // Initialize UI
    window.addEventListener('DOMContentLoaded', () => {
        setBlock('AMP');
        // Do NOT updateScreen here to avoid overwriting 01A before sync
    });

    // ===============================================
    // 2. MIDI ENGINE
    // ===============================================
    
    function connectMIDI() {
        if (!navigator.requestMIDIAccess) { alert("Web MIDI not supported. Use Chrome."); return; }
        
        navigator.requestMIDIAccess({ sysex: true }).then(access => {
            midiAccess = access;
            const outputs = Array.from(midiAccess.outputs.values());
            const inputs = Array.from(midiAccess.inputs.values());
            
            midiOutput = outputs.find(o => o.name.includes("NUX") || o.name.includes("MG-30")) || outputs[0];
            midiInput = inputs.find(i => i.name.includes("NUX") || i.name.includes("MG-30")) || inputs[0];

            if (midiOutput && midiInput) {
                document.getElementById('statusText').innerText = "LINKED";
                document.getElementById('statusBadge').classList.add('online');
                isConnected = true;
                
                midiInput.onmidimessage = handleMidiMessage;

                // Sync immediately on connect
                updateScreen(); 
                alert(`Connected to ${midiOutput.name}! Syncing...`);
            } else {
                alert("NUX MG-30 not found. Check USB cable.");
            }
        }, () => alert("MIDI Access Denied."));
    }

    function handleMidiMessage(event) {
        const [status, data1, data2] = event.data;
        
        // Program Change (Patch Switch on Pedal)
        if ((status & 0xF0) === 0xC0) {
            currentPatch = data1;
            updateUIFromPatchIndex(currentPatch);
        }
        
        // Control Change (Knob turned on Pedal)
        if ((status & 0xF0) === 0xB0) {
            // Placeholder: Logic to update specific knob if data1 matches param ID
        }
    }

    function sendParameter(ccNumber, value) {
        if (!midiOutput) return;
        const ccMsg = [0xB0, 0x10, value]; // Generic CC for demo
        midiOutput.send(ccMsg);
        
        const badge = document.getElementById('statusBadge');
        badge.classList.add('sending');
        setTimeout(() => badge.classList.remove('sending'), 100);
    }

    function sendMidi(msg) {
        if (midiOutput) midiOutput.send(msg);
    }

    // ===============================================
    // 3. UI LOGIC
    // ===============================================

    function updateScreen() {
        updateUIFromPatchIndex(currentPatch);
        if (midiOutput) sendMidi([0xC0, currentPatch]); 
    }

    function updateUIFromPatchIndex(idx) {
        const bank = Math.floor(idx / 4) + 1;
        const sub = ['A','B','C','D'][idx % 4];
        document.getElementById('screenNum').innerText = (bank < 10 ? '0'+bank : bank) + sub;
    }

    function prevPatch() { if(currentPatch>0) { currentPatch--; updateScreen(); } }
    function nextPatch() { if(currentPatch<127) { currentPatch++; updateScreen(); } }

    function setBlock(blockName) {
        currentBlock = blockName;
        document.querySelectorAll('.block').forEach(b => b.classList.remove('active'));
        event.currentTarget.classList.add('active');
        
        const selector = document.getElementById('modelSelector');
        selector.innerHTML = '';
        const models = Object.keys(MG30_DB[blockName] || {});
        models.forEach(m => {
            let opt = document.createElement('option');
            opt.value = m; opt.innerText = m;
            selector.appendChild(opt);
        });
        
        loadModelKnobs();
    }

    function loadModelKnobs() {
        const modelName = document.getElementById('modelSelector').value;
        const params = MG30_DB[currentBlock][modelName] || [];
        const container = document.getElementById('knobContainer');
        container.innerHTML = '';

        params.forEach((param, index) => {
            const wrapper = document.createElement('div');
            wrapper.className = 'k-wrapper';
            
            wrapper.innerHTML = `
                <svg class="knob-svg" viewBox="0 0 100 100" onmousedown="startDrag(event, this, ${index})">
                 
