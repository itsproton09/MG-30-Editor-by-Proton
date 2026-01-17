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
            height: 85px; background: var(--bg-panel);
            display: flex; align-items: center; overflow-x: auto;
            padding: 0 20px; gap: 8px; border-bottom: 1px solid var(--border-subtle);
        }
        /* Hide Scrollbar */
        .chain-nav::-webkit-scrollbar { display: none; }

        .block {
            min-width: 65px; height: 55px;
            background: linear-gradient(145deg, #222, #1a1a1a);
            border: 1px solid #333; border-radius: 6px;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            font-size: 0.65rem; font-weight: 700; color: #666;
            cursor: pointer; transition: all 0.2s ease;
        }
        .block:hover { border-color: #555; color: #aaa; }
        .block.active {
            background: linear-gradient(145deg, #2a2510, #1f1c0d);
            border-color: var(--accent-gold); color: var(--accent-gold);
            box-shadow: 0 4px 12px var(--accent-glow);
            transform: translateY(-1px);
        }
        .b-icon { font-size: 1.2rem; margin-bottom: 3px; }

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
        }
        .knob-cap { fill: #111; } /* Center cap if needed */

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
        
        /* Specific Button Styles */
        .btn-nav { max-width: 60px; font-size: 1.2rem; }
        .btn-primary { background: linear-gradient(180deg, #1f1f1f, #111); }
        .btn-connect { 
            background: rgba(0, 230, 118, 0.05); 
            border-color: rgba(0, 230, 118, 0.2); 
            color: var(--safe-green); 
        }
        .btn-connect:hover { 
            background: rgba(0, 230, 118, 0.1); 
            box-shadow: 0 0 15px rgba(0, 230, 118, 0.1);
        }

        /* --- 8. SAFETY MODAL (Professional Style) --- */
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
                <p><strong>2. Safety Protocol:</strong> This editor uses a 40ms buffer queue to prevent data flooding and hardware freezing.</p>
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
                <div class="patch-id" id="screenNum">01A</div>
                <div class="patch-name" id="screenName">RECTO LEAD</div>
            </div>
        </div>
    </section>

    <nav class="chain-nav">
        <div class="block" onclick="setBlock('WAH')"><div class="b-icon">🦶</div>WAH</div>
        <div class="block" onclick="setBlock('CMP')"><div class="b-icon">🗜️</div>CMP</div>
        <div class="block" onclick="setBlock('EFX')"><div class="b-icon">⚡</div>EFX</div>
        <div class="block active" onclick="setBlock('AMP')"><div class="b-icon">🎸</div>AMP</div>
        <div class="block" onclick="setBlock('EQ')"><div class="b-icon">🎚️</div>EQ</div>
        <div class="block" onclick="setBlock('MOD')"><div class="b-icon">🌀</div>MOD</div>
        <div class="block" onclick="setBlock('DLY')"><div class="b-icon">🕰️</div>DLY</div>
        <div class="block" onclick="setBlock('RVB')"><div class="b-icon">🌌</div>RVB</div>
        <div class="block" onclick="setBlock('IR')"><div class="b-icon">📢</div>IR</div>
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
        
        <button class="btn btn-primary" onclick="document.getElementById('fileInput').click()">Load Preset</button>
        <button class="btn btn-primary" onclick="exportPatch()">Save Preset</button>
        
        <button class="btn btn-connect" onclick="connectMIDI()">Connect USB</button>
    </footer>

    <input type="file" id="fileInput" hidden onchange="importPatch(this)" accept=".mg30patch,.json">

<script>
    // ===============================================
    // 1. DATA: MG-30 DATABASE (FULL)
    // ===============================================
    const MG30_DB = {
        'WAH': { 
            'Clyde Wah': ['Pos', 'Min', 'Max'], 'Cry Wah': ['Pos', 'Min', 'Max'], 
            'V847 Wah': ['Pos', 'Min', 'Max'], 'Horse Wah': ['Pos', 'Min', 'Max'],
            'Octave Shift': ['Pitch', 'Mix', 'Level'], 'Touch Wah': ['Sens', 'Res', 'Decay', 'Level']
        },
        'CMP': { 
            'Rose Comp': ['Sustain', 'Level'], 'K Comp': ['Sustain', 'Level', 'Clip'],
            'Studio Comp': ['Thresh', 'Ratio', 'Gain', 'Attack'], 'Red Comp': ['Level', 'Sustain', 'Attack']
        },
        'EFX': { 
            'Tube Screamer': ['Level', 'Tone', 'Drive'], 'Blues Driver': ['Level', 'Tone', 'Gain'], 
            'Morning Glory': ['Vol', 'Drive', 'Tone', 'Gain'], 'Distortion+': ['Dist', 'Output'], 
            'Rat Dist': ['Dist', 'Filter', 'Volume'], 'Red Dirt': ['Level', 'Tone', 'Drive'], 
            'Crunch Box': ['Vol', 'Tone', 'Gain', 'Pres'], 'Muff Fuzz': ['Vol', 'Tone', 'Sustain'], 
            'Katana Boost': ['Boost', 'Vol'], 'RC Boost': ['Gain', 'Vol', 'Bass', 'Treble'],
            'AC Boost': ['Gain', 'Vol', 'Bass', 'Treble'], 'Zen Drive': ['Vol', 'Gain', 'Tone', 'Voice']
        },
        'AMP': { 
            'Jazz Clean': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Bright', 'Level'],
            'Deluxe Rvb': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Level'],
            'Bassman': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Twin Rvb': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Bright', 'Level'],
            'Hiwire': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'AC30 Top': ['Gain', 'Master', 'Bass', 'Treble', 'Cut', 'Level'],
            'Plexi 100': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Brit 800': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Brit 2000': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'SLO 100': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Recto Dual': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Bias', 'Level'],
            'Diezel VH4': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Deep', 'Pres', 'Level'],
            'Fireman': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Uber': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'Vibro King': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Fat', 'Level'],
            'Budda': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Cut', 'Level'],
            'Matchless': ['Gain', 'Master', 'Bass', 'Treble', 'Cut', 'Level'],
            'VIVO': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'SOLDA': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Pres', 'Level'],
            'AGL (Bass)': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Deep', 'Level'],
            'MLD (Bass)': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Level'],
            'Stageman': ['Gain', 'Master', 'Bass', 'Mid', 'Treble', 'Level']
        },
        'EQ': { '6-Band': ['100', '200', '400', '800', '1.6k', '3.2k', 'Level'], '10-Band': ['31', '62', '125', '250', '500', '1k', '2k', '4k', '8k', '16k', 'Vol'] },
        'MOD': { 
            'CE-1': ['Intensity', 'Depth', 'Level'], 'CE-2': ['Rate', 'Depth'], 'Stereo Cho': ['Rate', 'Depth', 'Level'], 
            'Vibrato': ['Rate', 'Depth', 'B.Rise'], 'Detune': ['Mix', 'Pitch', 'Level'], 'Flanger': ['Rate', 'Depth', 'Res', 'Manual'], 
            'Phase 90': ['Speed'], 'Phase 100': ['Speed', 'Intensity'], 'Tremolo': ['Rate', 'Depth'], 
            'Rotary': ['Speed', 'Balance', 'Level'], 'Uni-Vibe': ['Rate', 'Depth', 'Vol'], 'SCF': ['Speed', 'Width', 'Mode'],
            'Clone Cho': ['Rate', 'Depth'], 'Pitch Shift': ['Pitch', 'Mix', 'Level'], 'Harmonist': ['Key', 'Interval', 'Mix']
        },
        'DLY': { 
            'Analog': ['Time', 'Repeat', 'Mix'], 'Digital': ['Time', 'Repeat', 'Mix', 'Tone'], 
            'Tape Echo': ['Time', 'Repeat', 'Mix', 'Wow', 'Flutter'], 'Reverse': ['Time', 'Feedback', 'Mix'], 
            'Pan Delay': ['Time', 'Feedback', 'Mix', 'Pan'], 'Duotime': ['Time 1', 'Time 2', 'Repeat', 'Mix'], 
            'Space': ['Time', 'Feedback', 'Mix', 'Reverb']
        },
        'RVB': { 
            'Room': ['Decay', 'Tone', 'Mix', 'PreDly'], 'Hall': ['Decay', 'Tone', 'Mix', 'PreDly'], 
            'Plate': ['Decay', 'Tone', 'Mix', 'Damp'], 'Spring': ['Decay', 'Tone', 'Mix', 'Dwell'], 
            'Shimmer': ['Decay', 'Mix', 'Pitch', 'Blend']
        },
        'IR': { 'Guitar Cab': ['Level', 'LoCut', 'HiCut'], 'Bass Cab': ['Level', 'LoCut', 'HiCut'], 'Acoustic': ['Level', 'LoCut', 'HiCut'] }
    };

    // State Variables
    let currentBlock = 'AMP';
    let currentPatch = 0;
    let midiOutput = null;
    let commandQueue = [];
    let isSending = false;

    // ===============================================
    // 2. INITIALIZATION
    // ===============================================
    window.addEventListener('DOMContentLoaded', () => {
        setBlock('AMP');
        updateScreen();
    });

    function updateScreen() {
        const bank = Math.floor(currentPatch / 4) + 1;
        const sub = ['A','B','C','D'][currentPatch % 4];
        document.getElementById('screenNum').innerText = (bank < 10 ? '0'+bank : bank) + sub;
        if(midiOutput) sendMidi([0xC0, currentPatch]);
    }

    function prevPatch() { if(currentPatch>0) { currentPatch--; updateScreen(); } }
    function nextPatch() { if(currentPatch<127) { currentPatch++; updateScreen(); } }

    function setBlock(type) {
        currentBlock = type;
        document.querySelectorAll('.block').forEach(b => b.classList.remove('active'));
        const blocks = Array.from(document.querySelectorAll('.block'));
        const target = blocks.find(b => b.innerText.includes(type));
        if(target) target.classList.add('active');

        const sel = document.getElementById('modelSelector');
        sel.innerHTML = '';
        const models = MG30_DB[type];
        if(models) {
            Object.keys(models).forEach(m => {
                const opt = document.createElement('option');
                opt.value = m; opt.innerText = m; sel.appendChild(opt);
            });
            loadModelKnobs();
        }
    }

    function loadModelKnobs() {
        const model = document.getElementById('modelSelector').value;
        const labels = MG30_DB[currentBlock][model];
        const container = document.getElementById('knobContainer');
        container.innerHTML = '';
        if(labels) labels.forEach((label, i) => createKnob(container, label, i));
    }

    // ===============================================
    // 3. KNOB CREATION & PHYSICS
    // ===============================================
    function createKnob(container, label, idx) {
        const div = document.createElement('div');
        div.className = 'k-wrapper';
        div.innerHTML = `
            <svg class="knob-svg" viewBox="0 0 100 100" data-idx="${idx}" data-val="50">
                <circle cx="50" cy="50" r="10" fill="#222" /> <path d="M 20 80 A 40 40 0 1 1 80 80" class="track" />
                <path class="arc" d="M 20 80 A 40 40 0 1 1 80 80" stroke-dashoffset="200" />
            </svg>
            <div class="k-val">50</div>
            <div class="k-lbl">${label}</div>
        `;
        container.appendChild(div);
        
        const svg = div.querySelector('svg');
        initKnobLogic(svg);
        updateKnob(svg, 50);
    }

    function updateKnob(svg, val) {
        val = Math.max(0, Math.min(100, val));
        const offset = 200 - ((val/100)*200);
        svg.querySelector('.arc').style.strokeDashoffset = offset;
        svg.parentElement.querySelector('.k-val').innerText = Math.round(val);
        svg.dataset.val = val;
    }

    function initKnobLogic(svg) {
        let startY=0, startVal=0;
        const move = (e) => {
            let y = e.touches ? e.touches[0].clientY : e.clientY;
            let d = (startY - y) * 1.5;
            let v = startVal + d;
            updateKnob(svg, v);
        };
        const start = (e) => {
            e.preventDefault();
            startY = e.touches ? e.touches[0].clientY : e.clientY;
            startVal = parseFloat(svg.dataset.val);
            document.addEventListener('mousemove', move);
            document.addEventListener('touchmove', move, {passive:false});
            document.addEventListener('mouseup', end);
            document.addEventListener('touchend', end);
        };
        const end = () => {
            document.removeEventListener('mousemove', move);
            document.removeEventListener('touchmove', move);
            document.removeEventListener('mouseup', end);
            document.removeEventListener('touchend', end);
            queueMidiCC(svg.dataset.idx, svg.dataset.val); // SAFE SEND
        };
        svg.addEventListener('mousedown', start);
        svg.addEventListener('touchstart', start, {passive:false});
    }

    // ===============================================
    // 4. MIDI ENGINE (SAFE QUEUE)
    // ===============================================
    async function connectMIDI() {
        if(!navigator.requestMIDIAccess) return alert("Web MIDI Not Supported (Try Chrome)");
        try {
            const access = await navigator.requestMIDIAccess();
            if(access.outputs.size > 0) {
                midiOutput = access.outputs.values().next().value;
                document.getElementById('statusBadge').classList.add('online');
                document.getElementById('statusText').innerText = "ONLINE";
            } else alert("No NUX Device Found");
        } catch(e) { alert("Connection Failed"); }
    }

    function queueMidiCC(idx, val) {
        if(!midiOutput) return;
        const cc = 20 + parseInt(idx); 
        const midiVal = Math.floor(val * 1.27); 
        commandQueue.push([0xB0, cc, midiVal]);
        processQueue();
    }

    function sendMidi(msg) {
        commandQueue.push(msg);
        processQueue();
    }

    async function processQueue() {
        if(isSending || commandQueue.length === 0) return;
        isSending = true;
        document.getElementById('statusBadge').classList.add('sending');
        
        while(commandQueue.length > 0) {
            if(!midiOutput) { commandQueue=[]; break; }
            const msg = commandQueue.shift();
            try { midiOutput.send(msg); } catch(e){}
            // SAFETY DELAY: 40ms wait between messages
            await new Promise(r => setTimeout(r, 40));
        }
        document.getElementById('statusBadge').classList.remove('sending');
        isSending = false;
    }

    // ===============================================
    // 5. FILE IO (IMPORT/EXPORT)
    // ===============================================
    function exportPatch() {
        const data = {
            block: currentBlock,
            model: document.getElementById('modelSelector').value,
            params: {}
        };
        document.querySelectorAll('.k-wrapper').forEach(w => {
            data.params[w.querySelector('.k-lbl').innerText] = w.querySelector('svg').dataset.val;
        });
        const blob = new Blob([JSON.stringify(data)], {type:'application/json'});
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = 'Proton_NUX.mg30patch';
        a.click();
    }

    function importPatch(input) {
        const f = input.files[0];
        if(!f) return;
        const r = new FileReader();
        r.onload = (e) => {
            try {
                const d = JSON.parse(e.target.result);
                if(d.block) setBlock(d.block);
                if(d.model) {
                    document.getElementById('modelSelector').value = d.model;
                    loadModelKnobs();
                }
                setTimeout(() => {
                    for(const [key, val] of Object.entries(d.params)) {
                        const ls = Array.from(document.querySelectorAll('.k-lbl'));
                        const t = ls.find(l => l.innerText === key);
                        if(t) {
                            const svg = t.parentElement.querySelector('svg');
                            const v = parseFloat(val);
                            updateKnob(svg, v);
                            queueMidiCC(svg.dataset.idx, v);
                        }
                    }
                }, 50);
            } catch(x){ alert("Invalid File"); }
        };
        r.readAsText(f);
        input.value = '';
    }
</script>
</body>
</html>
