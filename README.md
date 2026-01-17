<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 Editor | PROTON</title>
    <style>
        /* --- 1. CORE THEME --- */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&family=JetBrains+Mono:wght@500;700&display=swap');

        :root {
            --bg-deep: #050505;
            --bg-panel: #111;
            --accent-gold: #D4AF37;
            --accent-dim: #443a1a;
            --text-main: #EAEAEA;
            --border: 1px solid rgba(255,255,255,0.1);
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; user-select: none; outline: none; }
        
        body {
            margin: 0; background-color: var(--bg-deep); color: var(--text-main);
            font-family: 'Inter', sans-serif; height: 100vh;
            display: flex; flex-direction: column; overflow: hidden;
        }

        /* --- 2. HEADER --- */
        header {
            height: 60px; background: #0f0f0f; border-bottom: var(--border);
            display: flex; justify-content: space-between; align-items: center; padding: 0 20px;
            z-index: 100; /* High Z-Index ensures clicks work */
        }
        
        .brand-area { display: flex; align-items: center; gap: 10px; }
        .logo-shape { 
            width: 24px; height: 24px; background: var(--accent-gold); 
            clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%); 
        }
        .brand-title { font-weight: 800; font-size: 14px; letter-spacing: 1px; color: #fff; }

        .header-controls { display: flex; align-items: center; gap: 15px; }
        
        .vol-strip { 
            display: flex; align-items: center; gap: 10px; background: #222; 
            padding: 5px 10px; border-radius: 20px; border: var(--border); 
        }
        .vol-label { font-size: 10px; font-weight: 700; color: #666; }
        input[type=range] { -webkit-appearance: none; width: 80px; height: 4px; background: #444; border-radius: 2px; }
        input[type=range]::-webkit-slider-thumb { 
            -webkit-appearance: none; width: 12px; height: 12px; border-radius: 50%; 
            background: var(--accent-gold); cursor: pointer; 
        }
        .status-dot { height: 8px; width: 8px; border-radius: 50%; background: #333; transition: 0.3s; }
        .status-dot.online { background: #00E676; box-shadow: 0 0 8px #00E676; }

        /* --- 3. LCD AREA --- */
        .rig-section {
            background: #121212; padding: 15px 0; border-bottom: var(--border);
            display: flex; flex-direction: column; align-items: center; gap: 10px; z-index: 10;
        }

        .lcd-frame {
            width: 300px; height: 140px; background: #000; border-radius: 6px;
            border: 1px solid #333; position: relative; overflow: hidden;
            display: flex; flex-direction: column;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }
        .lcd-meta { 
            height: 24px; background: #080808; border-bottom: 1px solid #222; 
            display: flex; justify-content: space-between; padding: 0 10px; align-items: center;
            font-family: 'JetBrains Mono'; font-size: 10px; color: #444; 
        }
        .lcd-main {
            flex: 1; display: flex; flex-direction: column; justify-content: center; align-items: center;
            background: radial-gradient(circle at 50% 120%, #151515 0%, #000 80%);
        }
        .patch-big { font-family: 'JetBrains Mono'; font-size: 3rem; color: var(--accent-gold); font-weight: 700; line-height: 1; }
        .patch-small { font-family: 'Inter'; font-size: 0.75rem; letter-spacing: 2px; color: #888; margin-top: 5px; text-transform: uppercase; }

        .nav-bar { display: flex; gap: 10px; width: 300px; justify-content: space-between; }
        .nav-btn {
            background: #222; border: 1px solid #333; color: #888; height: 32px; width: 45px;
            border-radius: 4px; cursor: pointer; display: grid; place-items: center; font-size: 16px;
            transition: 0.2s;
        }
        .nav-btn:hover { background: #333; color: #fff; border-color: #555; }
        .nav-btn:active { background: var(--accent-gold); color: #000; }

        /* --- 4. SIGNAL CHAIN --- */
        .chain-strip {
            height: 60px; background: #151515; border-bottom: var(--border);
            display: flex; align-items: center; padding: 0 15px; gap: 5px; overflow-x: auto;
            z-index: 20;
        }
        .chain-strip::-webkit-scrollbar { display: none; }

        .block {
            min-width: 55px; height: 36px; background: #222; border: 1px solid #333; border-radius: 3px;
            display: flex; justify-content: center; align-items: center;
            font-size: 10px; font-weight: 700; color: #666; cursor: pointer; transition: 0.1s;
        }
        .block:hover { background: #2a2a2a; color: #999; }
        .block.on { background: linear-gradient(180deg, #2a2510, #111); border-color: var(--accent-dim); color: var(--accent-gold); }
        .block.editing { border: 1px solid #fff; color: #fff; background: #333; box-shadow: 0 0 10px rgba(255,255,255,0.1); }
        .block.on.editing { background: var(--accent-gold); color: #000; border-color: var(--accent-gold); font-weight: 800; }

        /* --- 5. STAGE AREA --- */
        .model-bar {
            padding: 8px; background: var(--bg-deep); border-bottom: var(--border); display: flex; justify-content: center;
        }
        select {
            background: #111; color: var(--accent-gold); border: 1px solid #333; padding: 5px 15px;
            font-family: 'JetBrains Mono'; font-size: 11px; border-radius: 4px; cursor: pointer;
        }
        
        .stage-area { flex: 1; background: var(--bg-deep); position: relative; overflow-y: auto; padding: 30px 20px; }
        .knob-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(70px, 1fr)); 
            gap: 20px; max-width: 800px; margin: 0 auto;
        }
        
        /* Message Layer - Ensure it doesn't block clicks elsewhere */
        .offline-msg {
            position: absolute; inset: 0; display: flex; flex-direction: column; justify-content: center; align-items: center;
            color: #333; pointer-events: none; z-index: 5;
        }

        .k-wrap { display: flex; flex-direction: column; align-items: center; gap: 5px; }
        svg.k-svg { width: 70px; height: 70px; cursor: ns-resize; touch-action: none; }
        .k-val { font-family: 'JetBrains Mono'; color: var(--accent-gold); font-size: 12px; }
        .k-lbl { font-size: 10px; color: #666; text-transform: uppercase; font-weight: 700; }

        /* --- 6. FOOTER --- */
        footer {
            height: 60px; background: #111; border-top: var(--border);
            display: flex; gap: 10px; padding: 0 20px; align-items: center; z-index: 300;
        }
        .btn {
            flex: 1; height: 38px; background: #222; border: 1px solid #333; color: #888;
            font-size: 11px; font-weight: 700; border-radius: 4px; cursor: pointer;
            text-transform: uppercase; letter-spacing: 0.5px; transition: 0.2s;
        }
        .btn:hover { background: #333; color: #fff; border-color: #555; }
        .btn:active { transform: translateY(1px); }
        .btn-mgr { background: var(--accent-dim); color: var(--accent-gold); border-color: #554411; }
        .btn-con { background: rgba(0,50,0,0.3); border-color: rgba(0,100,0,0.5); color: #0f0; }

        /* --- 7. MODAL --- */
        .modal-bg {
            position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 9999;
            display: none; justify-content: center; align-items: center;
        }
        .modal {
            width: 600px; max-width: 90%; background: #111; border: 1px solid #333; 
            border-top: 2px solid var(--accent-gold); border-radius: 6px; 
            padding: 20px; max-height: 80vh; display: flex; flex-direction: column;
            box-shadow: 0 20px 60px rgba(0,0,0,0.7);
        }
        .modal-head { display: flex; justify-content: space-between; margin-bottom: 20px; border-bottom: 1px solid #222; padding-bottom: 10px; }
        .slot-grid {
            display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; overflow-y: auto; padding-right: 5px;
        }
        .slot {
            background: #1a1a1a; border: 1px solid #2a2a2a; padding: 10px; text-align: center;
            border-radius: 3px; font-family: 'JetBrains Mono'; font-size: 11px; color: #666; cursor: pointer;
        }
        .slot:hover { border-color: #666; color: #fff; background: #222; }
        .btn-close { background: transparent; border: 1px solid #333; color: #fff; padding: 5px 10px; cursor: pointer; }

    </style>
    
    <script>
        // --- SAFE DATABASE ---
        // Defined globally so all functions can see it immediately
        const DB = {
            'WAH': { 'Clyde':[{n:'Pos',cc:10}], 'Cry Wah':[{n:'Pos',cc:10}], 'V847':[{n:'Pos',cc:10}] },
            'CMP': { 'Rose Comp':[{n:'Sustain',cc:15},{n:'Level',cc:16}], 'Studio Comp':[{n:'Thresh',cc:15}] },
            'EFX': { 'Tube Drv':[{n:'Drive',cc:20}], 'Blues Drv':[{n:'Gain',cc:20}], 'Dist+':[{n:'Dist',cc:20}], 'Rat':[{n:'Dist',cc:20}] },
            'AMP': { 
                'Jazz Clean':[{n:'Gain',cc:30},{n:'Master',cc:31},{n:'Bass',cc:32},{n:'Mid',cc:33},{n:'Treb',cc:34}],
                'Deluxe Rvb':[{n:'Gain',cc:30},{n:'Master',cc:31},{n:'Bass',cc:32}],
                'Recto Dual':[{n:'Gain',cc:30},{n:'Master',cc:31},{n:'Bass',cc:32},{n:'Mid',cc:33},{n:'Treb',cc:34},{n:'Pres',cc:35}],
                'Diezel VH4':[{n:'Gain',cc:30},{n:'Master',cc:31},{n:'Bass',cc:32},{n:'Mid',cc:33},{n:'Treb',cc:34},{n:'Deep',cc:36}],
                'AC30 Top':[{n:'Gain',cc:30},{n:'Master',cc:31},{n:'Bass',cc:32},{n:'Cut',cc:33},{n:'Treb',cc:34}]
            },
            'NR':  { 'Noise Gate':[{n:'Thresh',cc:40},{n:'Decay',cc:41}] },
            'EQ':  { '10-Band':[{n:'1k',cc:50}] },
            'MOD': { 'Chorus':[{n:'Rate',cc:60}], 'Phaser':[{n:'Rate',cc:60}], 'Tremolo':[{n:'Rate',cc:60}] },
            'DLY': { 'Analog':[{n:'Time',cc:70}], 'Digital':[{n:'Time',cc:70}], 'Tape':[{n:'Time',cc:70}] },
            'RVB': { 'Room':[{n:'Decay',cc:80}], 'Hall':[{n:'Decay',cc:80}], 'Plate':[{n:'Decay',cc:80}], 'Spring':[{n:'Decay',cc:80}] },
            'IR':  { 'Guitar Cab':[{n:'LoCut',cc:91}], 'Bass Cab':[{n:'LoCut',cc:91}] }
        };

        // --- GLOBALS ---
        let midiOut = null;
        let currentPatch = 0;
        let editingBlock = 'AMP';
        let selectedSlot = 0;
        let exportWaiting = false;

        // --- MANAGER FUNCTIONS (Defined Top-Level) ---
        function openManager() {
            document.getElementById('managerModal').style.display = 'flex';
        }

        function closeManager() {
            document.getElementById('managerModal').style.display = 'none';
        }

        function generateSlotGrid() {
            const grid = document.getElementById('slotGrid');
            if(!grid) return;
            grid.innerHTML = '';
            for(let i=0; i<32; i++) {
                ['A','B','C','D'].forEach((s, x) => {
                    const idx = (i*4)+x;
                    const d = document.createElement('div');
                    d.className = 'slot';
                    d.innerText = (i+1).toString().padStart(2,'0') + s;
                    d.onclick = () => {
                        selectedSlot = idx;
                        document.getElementById('filePicker').click();
                    };
                    grid.appendChild(d);
                });
            }
        }

        function uploadPatch(input) {
            const f = input.files[0];
            if(!f) return;
            if(midiOut) {
                if(confirm(`Overwrite Slot ${selectedSlotToName(selectedSlot)} with ${f.name}?`)) {
                    midiOut.send([0xC0, selectedSlot]);
                    alert(`Installed to ${selectedSlotToName(selectedSlot)}`);
                    currentPatch = selectedSlot;
                    updateLCD(currentPatch);
                }
            } else {
                alert("Connect USB First");
            }
            closeManager();
            input.value = '';
        }

        function triggerExport() {
            if(!midiOut) return alert("Connect USB first");
            if(confirm("Download current patch?")) {
                exportWaiting = true;
                midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]); 
            }
        }

        function navPatch(dir) {
            currentPatch = Math.max(0, Math.min(127, currentPatch + dir));
            updateLCD(currentPatch);
            if(midiOut) {
                midiOut.send([0xC0, currentPatch]);
                setTimeout(() => midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]), 100);
            }
        }

        // --- BLOCK SELECTION ---
        function selectBlock(blk) {
            editingBlock = blk;
            document.querySelectorAll('.block').forEach(b => b.classList.remove('editing'));
            const el = document.getElementById(`blk-${blk}`);
            if(el) el.classList.add('editing');

            const list = document.getElementById('modelList');
            if(!list) return;
            list.innerHTML = '';
            
            const models = Object.keys(DB[blk] || {});
            if(models.length > 0) {
                models.forEach(m => list.appendChild(new Option(m, m)));
                list.disabled = false;
            } else {
                list.innerHTML = "<option>NO MODELS</option>";
                list.disabled = true;
            }
            renderKnobs();
        }

        function renderKnobs() {
            const grid = document.getElementById('knobGrid');
            if(!grid) return;
            grid.innerHTML = '';
            
            const list = document.getElementById('modelList');
            const model = list ? list.value : '';
            const params = DB[editingBlock][model] || [];

            params.forEach(x => {
                const div = document.createElement('div');
                div.className = 'k-wrap';
                div.innerHTML = `
                    <svg class="k-svg" viewBox="0 0 100 100" onmousedown="startDrag(event, this, ${x.cc})">
                        <circle cx="50" cy="50" r="40" stroke="#222" stroke-width="6" fill="none" />
                        <circle class="k-arc" cx="50" cy="50" r="40" stroke="#D4AF37" stroke-width="6" fill="none"
                            stroke-dasharray="200" stroke-dashoffset="100" transform="rotate(135 50 50)" />
                        <circle cx="50" cy="50" r="30" fill="#151515" />
                        <line class="k-ptr" x1="50" y1="50" x2="50" y2="10" stroke="#fff" stroke-width="4" stroke-linecap="round"
                            transform="rotate(0 50 50)" />
                    </svg>
                    <div class="k-val">50</div>
                    <div class="k-lbl">${x.n}</div>`;
                grid.appendChild(div);
            });
        }

        // --- MIDI ENGINE ---
        function initMIDI() {
            if(!navigator.requestMIDIAccess) return alert("Use Google Chrome");
            navigator.requestMIDIAccess({sysex: true}).then(acc => {
                const outputs = Array.from(acc.outputs.values());
                const inputs = Array.from(acc.inputs.values());
                midiOut = outputs.find(o => o.name.includes("NUX")) || outputs[0];
                const midiIn = inputs.find(i => i.name.includes("NUX")) || inputs[0];

                if(midiOut && midiIn) {
                    midiIn.onmidimessage = handleMIDI;
                    setOnlineMode();
                    midiOut.send([0xC0, currentPatch]);
                } else {
                    alert("NUX MG-30 Not Found");
                }
            });
        }

        function handleMIDI(e) {
            if(exportWaiting && e.data[0]===0xF0 && e.data.length>20) {
                const name = document.getElementById('lcdName').innerText.trim() || "PATCH";
                saveBlob(e.data, `${name}.mg30patch`);
                exportWaiting = false;
                return;
            }
            if((e.data[0] & 0xF0) === 0xC0) {
                currentPatch = e.data[1];
                updateLCD(currentPatch);
                midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]);
            }
            if(e.data[0]===0xF0 && e.data.length>10) {
                let name = "";
                for(let i=8; i<e.data.length-1; i++) {
                    if(e.data[i]>=32 && e.data[i]<=126) name += String.fromCharCode(e.data[i]);
                    else if(name.length>0) break;
                }
                if(name.length>0) document.getElementById('lcdName').innerText = name;
            }
        }

        function setOnlineMode() {
            const dot = document.getElementById('statusDot');
            if(dot) dot.classList.add('online');
            
            const state = document.getElementById('usbState');
            if(state) {
                state.innerText = "LINK";
                state.style.color = "#00E676";
            }
            
            const msg = document.getElementById('offlineMsg');
            if(msg) msg.style.display = "none";
            
            const grid = document.getElementById('knobGrid');
            if(grid) grid.style.display = "grid";

            selectBlock(editingBlock);
        }

        // --- UTILS ---
        function saveBlob(data, filename) {
            const blob = new Blob([data], {type: "application/octet-stream"});
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a'); a.href = url; a.download = filename;
            document.body.appendChild(a); a.click(); document.body.removeChild(a);
        }
        function updateLCD(n) {
            const b = Math.floor(n/4)+1;
            const s = ['A','B','C','D'][n%4];
            const num = document.getElementById('lcdNum');
            if(num) num.innerText = (b<10?'0'+b:b)+s;
        }
        function selectedSlotToName(idx) {
            const b = Math.floor(idx/4)+1;
            const s = ['A','B','C','D'][idx%4];
            return (b<10?'0'+b:b)+s;
        }
        function setMasterVol(v) { if(midiOut) midiOut.send([0xB0, 0x07, Math.floor((v/100)*127)]); }

        // --- KNOB DRAG ---
        let dragCC=-1, startY=0, curVal=64, activeUI=null;
        function startDrag(e, svg, cc) {
            dragCC=cc; startY=e.clientY;
            activeUI={ arc:svg.querySelector('.k-arc'), ptr:svg.querySelector('.k-ptr'), txt:svg.nextElementSibling };
            curVal=parseInt(activeUI.txt.innerText)||64;
            document.addEventListener('mousemove', onDrag);
            document.addEventListener('mouseup', stopDrag);
        }
        function onDrag(e) {
            curVal += (startY - e.clientY);
            curVal = Math.max(0, Math.min(127, curVal));
            const offset = 200 - ((curVal/127)*200);
            const angle = -135 + ((curVal/127)*270);
            activeUI.arc.style.strokeDashoffset = offset;
            activeUI.ptr.setAttribute('transform', `rotate(${angle} 50 50)`);
            activeUI.txt.innerText = curVal;
            startY = e.clientY;
            if(midiOut) midiOut.send([0xB0, dragCC, curVal]);
        }
        function stopDrag() { document.removeEventListener('mousemove', onDrag); document.removeEventListener('mouseup', stopDrag); }
        
        // --- BOOT ---
        window.addEventListener('DOMContentLoaded', () => {
            generateSlotGrid();
            selectBlock('AMP');
        });
    </script>
</head>
<body>

    <div id="managerModal" class="modal-bg">
        <div class="modal">
            <div class="modal-head">
                <span class="modal-title">PATCH MANAGER</span>
                <button class="btn-close" onclick="closeManager()">CLOSE</button>
            </div>
            <p style="color:#666; font-size:10px; margin-bottom:15px; text-transform:uppercase;">Select Slot to Install:</p>
            <div class="slot-grid" id="slotGrid"></div>
        </div>
    </div>

    <header>
        <div class="brand-area">
            <div class="logo-shape"></div>
            <span class="brand-title">NUX EDITOR</span>
        </div>
        <div class="header-controls">
            <div class="vol-strip">
                <span class="vol-label">OUT</span>
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
            <button class="nav-btn" onclick="navPatch(-1)">◀</button>
            <button class="nav-btn" onclick="navPatch(1)">▶</button>
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
        <select id="modelList" onchange="renderKnobs()"><option>SELECT BLOCK</option></select>
    </div>

    <div class="stage-area">
        <div id="offlineMsg" class="offline-msg" style="display:none;">
            <p>USB DISCONNECTED</p>
        </div>
        <div class="knob-grid" id="knobGrid"></div>
    </div>

    <footer>
        <button class="btn btn-mgr" onclick="openManager()">Patch Manager</button>
        <button class="btn" onclick="triggerExport()">Export</button>
        <button class="btn btn-con" onclick="initMIDI()">Connect USB</button>
    </footer>

    <input type="file" id="filePicker" hidden onchange="uploadPatch(this)" accept=".bin,.mg30patch">

</body>
</html>
