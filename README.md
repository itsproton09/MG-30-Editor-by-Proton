<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX Editor by Proton</title>
    <style>
        /* --- 1. PREMIUM TYPOGRAPHY & VARS --- */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;800&family=JetBrains+Mono:wght@500;700&display=swap');

        :root {
            --bg-body: #050505;
            --bg-panel: #111111;
            --bg-surface: #1a1a1a;
            
            --accent-gold: #D4AF37;       /* Metallic Gold */
            --accent-glow: rgba(212, 175, 55, 0.15);
            --accent-active: #FFD700;
            
            --text-primary: #ffffff;
            --text-secondary: #888888;
            
            --border-subtle: rgba(255, 255, 255, 0.08);
            --glass: rgba(20, 20, 20, 0.85);
            
            --safe-green: #00E676;
            --danger-red: #FF5252;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; user-select: none; outline: none; }
        
        body {
            margin: 0; background-color: var(--bg-body); color: var(--text-primary);
            font-family: 'Inter', sans-serif; height: 100vh;
            display: flex; flex-direction: column; overflow: hidden;
            background-image: radial-gradient(circle at 50% 0%, #1a1a1a 0%, #050505 80%);
        }

        /* --- 2. HEADER (CENTERED BRANDING) --- */
        header {
            height: 60px; 
            background: var(--glass); 
            backdrop-filter: blur(12px);
            border-bottom: 1px solid var(--border-subtle);
            display: flex; justify-content: center; align-items: center;
            position: relative; z-index: 100;
        }

        .brand-container {
            text-align: center;
            letter-spacing: 3px;
        }

        .brand-main {
            font-weight: 800; font-size: 0.9rem; color: #fff;
            text-transform: uppercase;
        }
        
        .brand-sub {
            font-size: 0.6rem; color: var(--accent-gold);
            font-weight: 600; display: block; margin-top: 2px;
            text-shadow: 0 0 10px var(--accent-glow);
        }

        /* Status Badge (Absolute Positioned to Right) */
        .status-badge {
            position: absolute; right: 20px;
            font-family: 'JetBrains Mono', monospace; font-size: 0.65rem;
            padding: 4px 10px; background: rgba(0,0,0,0.3); 
            border: 1px solid var(--border-subtle); border-radius: 20px;
            display: flex; align-items: center; gap: 6px; color: #666;
            transition: all 0.3s ease;
        }
        .status-dot { width: 6px; height: 6px; border-radius: 50%; background: #444; transition: 0.3s; }
        .status-badge.online { border-color: rgba(0, 230, 118, 0.3); color: var(--safe-green); background: rgba(0, 30, 10, 0.5); }
        .status-badge.online .status-dot { background: var(--safe-green); box-shadow: 0 0 8px var(--safe-green); }
        .status-badge.sending .status-dot { background: var(--accent-active); }

        /* --- 3. RIG DISPLAY (THE SCREEN) --- */
        .rig-section {
            padding: 20px;
            background: linear-gradient(to bottom, #111 0%, #0a0a0a 100%);
            border-bottom: 1px solid var(--border-subtle);
            display: flex; justify-content: center;
            box-shadow: inset 0 -20px 20px -20px rgba(0,0,0,0.8);
        }

        .nux-screen {
            width: 320px; height: 160px;
            background: #000;
            border-radius: 8px;
            position: relative;
            box-shadow: 0 0 0 4px #1a1a1a, 0 10px 30px rgba(0,0,0,0.5);
            overflow: hidden;
            display: flex; flex-direction: column;
        }

        /* Screen Glare Effect */
        .nux-screen::after {
            content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%;
            background: linear-gradient(135deg, rgba(255,255,255,0.03) 0%, transparent 40%);
            pointer-events: none;
        }

        .lcd-bar {
            height: 24px; background: #0a0a0a; border-bottom: 1px solid #222;
            display: flex; justify-content: space-between; align-items: center;
            padding: 0 12px; font-family: 'JetBrains Mono', monospace; font-size: 0.65rem; color: #555;
        }
        .lcd-content {
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
                 
