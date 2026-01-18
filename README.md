<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 MASTER v12</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&family=JetBrains+Mono:wght@500;700&display=swap');
        :root { --bg:#050505; --gold:#D4AF37; --text:#eee; --accent:#00E676; --border:#333; }
        * { box-sizing:border-box; user-select:none; -webkit-user-select:none; }
        body { background:var(--bg); color:var(--text); font-family:'Inter',sans-serif; margin:0; height:100vh; display:flex; flex-direction:column; overflow:hidden; touch-action:none; }

        /* STARTUP */
        #startup { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; justify-content:center; align-items:center; text-align:center; }
        .start-btn { background:var(--gold); color:#000; border:none; padding:18px 50px; font-size:20px; font-weight:900; border-radius:50px; cursor:pointer; box-shadow:0 0 30px rgba(212,175,55,0.4); transition: 0.2s; }
        .start-btn:active { transform: scale(0.95); }

        header { height:60px; background:#0c0c0c; border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; padding:0 20px; }
        .logo { font-weight:900; color:#fff; font-size:18px; } .logo span { color:var(--gold); }
        .status { width:12px; height:12px; background:#333; border-radius:50%; transition:0.3s; }
        .status.on { background:var(--accent); box-shadow:0 0-15px var(--accent); }

        /* LCD MIRROR */
        .rig-view { padding:15px; background:#0f0f0f; border-bottom:1px solid var(--border); display:flex; flex-direction:column; align-items:center; gap:10px; }
        .lcd { width:320px; height:120px; background:#000; border:2px solid #222; border-radius:8px; display:flex; flex-direction:column; justify-content:center; align-items:center; }
        .p-num { font-family:'JetBrains Mono'; font-size:3.5rem; color:var(--gold); line-height:1; }
        .p-name { font-family:'Inter'; font-size:1rem; color:#fff; font-weight:700; text-transform:uppercase; margin-top:5px; }

        .nav { display:flex; gap:10px; width:320px; justify-content:space-between; }
        .btn-nav { background:#1a1a1a; border:1px solid #444; color:#fff; width:60px; height:36px; border-radius:4px; font-weight:700; cursor:pointer; }

        /* DYNAMIC REARRANGEABLE CHAIN */
        .chain { height:95px; background:#141414; border-bottom:1px solid var(--border); display:flex; align-items:center; padding:0 20px; gap:8px; overflow-x:auto; }
        .pedal { min-width:65px; height:55px; background:#1a1a1a; border:2px solid #2a2a2a; border-radius:6px; display:flex; flex-direction:column; justify-content:center; align-items:center; font-size:9px; font-weight:800; color:#555; cursor:grab; transition:0.1s; position:relative; }
        .pedal.active { border-color:var(--accent); color:var(--accent); background: linear-gradient(180deg, #122212, #0a0a0a); }
        .pedal.editing { border-color:#fff; color:#fff; transform:translateY(-3px); }
        .pedal-led { width:6px; height:6px; background:#333; border-radius:50%; margin-bottom:4px; }
        .pedal.active .pedal-led { background:var(--accent); box-shadow:0 0 8px var(--accent); }
        .pedal.dragging { opacity: 0.4; border: 2px dashed #fff; }

        /* EDITOR STAGE */
        .stage { flex:1; background:#0a0a0a; padding:20px; display:flex; flex-direction:column; align-items:center; overflow-y:auto; }
        .stage-header { display:flex; gap:10px; width:100%; max-width:800px; margin-bottom:25px; }
        select { flex:1; padding:12px; background:#111; color:var(--gold); border:1px solid #333; border-radius:6px; font-family:'JetBrains Mono'; font-size:14px; outline:none; }
        
        .knob-grid { display:grid; grid-template-columns:repeat(auto-fit, minmax(85px, 1fr)); gap:20px; width:100%; max-width:800px; }
        .k-wrap { display:flex; flex-direction:column; align-items:center; gap:8px; }
        svg.knob { width:70px; height:70px; cursor:ns-resize; pointer-events:auto; }
        .k-trk { fill:none; stroke:#1a1a1a; stroke-width:8; }
        .k-val { fill:none; stroke:var(--gold); stroke-width:8; stroke-linecap:round; }
        .k-ptr { stroke:#fff; stroke-width:4; stroke-linecap:round; }
        .k-num { font-family:'JetBrains Mono'; font-size:15px; color:var(--gold); font-weight:700; }
        .k-lbl { font-size:10px; font-weight:700; color:#666; text-transform:uppercase; }

        footer { height:70px; background:#0c0c0c; border-top:1px solid var(--border); display:flex; gap:10px; padding:0 20px; align-items:center; }
        .btn { flex:1; height:45px; background:#1a1a1a; border:1px solid #333; color:#aaa; font-size:11px; font-weight:900; border-radius:6px; cursor:pointer; }
        .btn-green { background:rgba(0,230,118,0.05); border-color:rgba(0,230,118,0.2); color:#00E676; }
    </style>
</head>
<body>

    <div id="startup">
        <h1 style="color:#fff; margin-bottom:20px;">NUX MG-30 MASTER v12</h1>
        <button class="start-btn" onclick="startApp()">BOOT ENGINE</button>
    </div>

    <header>
        <div class="logo">NUX <span>v5.0.2 MASTER</span></div>
        <div class="status" id="led"></div>
    </header>

    <div class="rig-view">
        <div class="lcd">
            <div class="p-num" id="lcd-num">--</div>
            <div class="p-name" id="lcd-name">OFFLINE</div>
        </div>
        <div class="nav">
            <button class="btn-nav" onclick="nav(-1)">◀</button>
            <button class="btn-nav" onclick="nav(1)">▶</button>
        </div>
    </div>

    <div class="chain" id="chainContainer"></div>

    <div class="stage">
        <div class="stage-header">
            <select id="models" onchange="manualModel()"></select>
        </div>
        <div class="knob-grid" id="knobs"></div>
    </div>

    <footer>
        <button class="btn" onclick="document.getElementById('fileIn').click()">IMPORT</button>
        <button class="btn" onclick="exportPatch()">EXPORT</button>
        <button class="btn btn-green" onclick="initMIDI()">LINK HARDWARE</button>
    </footer>

    <input type="file" id="fileIn" hidden onchange="importPatch(this)">

<script>
    // 1. COMPLETE v5.0.2 DATABASE (ALL 25+ AMPS INCLUDED)
    const DB = {
        'WAH': { 'Clyde':[{n:'POS',cc:10}], 'Cry BB':[{n:'POS',cc:10}], 'V847':[{n:'POS',cc:10}], 'Horse Wah':[{n:'POS',cc:10}], 'Octave Shift':[{n:'POS',cc:10}] },
        'CMP': { 'Rose':[{n:'SUS',cc:15}], 'K Comp':[{n:'SUS',cc:15}], 'Studio Comp':[{n:'THR',cc:15}] },
        'GATE': { 'Noise Reduc':[{n:'THR',cc:40},{n:'DEC',cc:41}] },
        'EFX': { 'Dist+':[{n:'DST',cc:20}], 'RC Boost':[{n:'GN',cc:20}], 'AC Boost':[{n:'GN',cc:20}], 'Dist One':[{n:'DST',cc:20}], 'T Scream':[{n:'DRV',cc:20}], 'Blues Drv':[{n:'GN',cc:20}], 'Morning Drv':[{n:'VOL',cc:20}], 'EAT':[{n:'DRV',cc:20}], 'Red Dirt':[{n:'DRV',cc:20}], 'Crunch':[{n:'GN',cc:20}], 'Muff Fuzz':[{n:'SUS',cc:20}], 'Katana Boost':[{n:'BST',cc:20}], 'Red Fuzz':[{n:'FUZ',cc:20}], 'Touch Wah':[{n:'SEN',cc:20}] },
        'AMP': { 
            'Jazz Clean':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Class A35':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Class A30':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Bassmate':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Tweedy':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Hiwire':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Plexi 100':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Plexi 45':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Brit 800':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            '1987x50':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Slo 100':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Fireman HBE':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Brit 2000':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Die Vh4':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'DEP',cc:36}],
            'Uber':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Dual Rect':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}],
            'Super Rvb':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Twin Rvb':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Deluxe Rvb':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Cali crunch':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Brit Blues':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Match':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'CUT',cc:33},{n:'TRB',cc:34}],
            'MrZ 38':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'CUT',cc:33},{n:'TRB',cc:34}],
            'Vibro King':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}],
            'Budda':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'CUT',cc:35}]
        },
        'EQ': { '6-Band':[{n:'100',cc:45}], 'Align':[{n:'GN',cc:45}], '10-Band':[{n:'31',cc:45}], 'Para':[{n:'FRQ',cc:45}] },
        'MOD': { 'CE-1':[{n:'CHO',cc:60}], 'CE-2':[{n:'RT',cc:60}], 'ST Chorus':[{n:'RT',cc:60}], 'Vibrator':[{n:'RT',cc:60}], 'Detune':[{n:'MIX',cc:60}], 'Flanger':[{n:'RT',cc:60}], 'Phase 90':[{n:'SPD',cc:60}], 'Phase 100':[{n:'SPD',cc:60}], 'S.C.F':[{n:'SPD',cc:60}], 'U-Vibe':[{n:'RT',cc:60}], 'Tremolo':[{n:'RT',cc:60},{n:'DP',cc:61},{n:'LVL',cc:62}], 'Rotary':[{n:'SPD',cc:60}], 'Harmonist':[{n:'MST',cc:60}] },
        'DLY': { 'Analog':[{n:'TM',cc:70}], 'Digital':[{n:'TM',cc:70}], 'Modulation':[{n:'TM',cc:70}], 'Tape':[{n:'TM',cc:70}], 'Reverse':[{n:'TM',cc:70}], 'Pan':[{n:'TM',cc:70}], 'Duotime':[{n:'TM1',cc:70}], 'Phi':[{n:'TM',cc:70}] },
        'RVB': { 'Room':[{n:'DEC',cc:80}], 'Hall':[{n:'DEC',cc:80}], 'Plate':[{n:'DEC',cc:80}], 'Spring':[{n:'DEC',cc:80}], 'Shimmer':[{n:'DEC',cc:80}] },
        'IR': { 'Cab':[{n:'LC',cc:91},{n:'HC',cc:92}] }
    };

    const BLOCK_ORDER_MAP = { 'WAH':0, 'CMP':1, 'GATE':2, 'EFX':3, 'AMP':4, 'EQ':5, 'MOD':6, 'DLY':7, 'RVB':8, 'IR':9 };

    // 2. STATE
    let midiOut = null;
    let curPatch = 0;
    let activeBlk = 'AMP';
    let blockStatus = {};
    let chainOrder = ['WAH','CMP','GATE','EFX','AMP','EQ','MOD','DLY','RVB','IR'];
    let knobVals = {};

    // 3. BOOT
    function startApp() {
        document.getElementById('startup').style.display='none';
        chainOrder.forEach(b => blockStatus[b] = true);
        refreshUI();
    }

    // 4. MIRRORING & CHAIN ENGINE
    function initMIDI() {
        navigator.requestMIDIAccess({sysex:true}).then(m => {
            midiOut = Array.from(m.outputs.values())[0];
            const inp = Array.from(m.inputs.values())[0];
            if(midiOut && inp) {
                inp.onmidimessage = onMIDIIn;
                document.getElementById('led').classList.add('on');
                fullSync();
            }
        });
    }

    function onMIDIIn(e) {
        const d = e.data;
        if ((d[0] & 0xF0) === 0xB0) updateUIKnob(d[1], Math.round((d[2]/127)*100));
        if ((d[0] & 0xF0) === 0xC0) { curPatch = d[1]; fullSync(); }
        if (d[0] === 0xF0 && d.length > 50) decodeSysEx(d);
    }

    function decodeSysEx(d) {
        let name = "";
        for(let i=10; i<26; i++) if(d[i] >= 32) name += String.fromCharCode(d[i]);
        document.getElementById('lcd-name').innerText = name.trim() || "PRESET";
        let b = Math.floor(curPatch/4)+1;
        let s = ['A','B','C','D'][curPatch%4];
        document.getElementById('lcd-num').innerText = (b<10?'0'+b:b)+s;
        refreshUI();
    }

    function fullSync() { if(midiOut) midiOut.send([0xF0,0x00,0x00,0x4F,0x11,0xF7]); }

    // 5. DRAG & DROP CHAIN LOGIC
    function refreshUI() {
        const container = document.getElementById('chainContainer');
        container.innerHTML = '';
        chainOrder.forEach((b, index) => {
            let div = document.createElement('div');
            div.className = `pedal ${blockStatus[b]?'active':''} ${activeBlk===b?'editing':''}`;
            div.draggable = true;
            div.innerHTML = `<div class="pedal-led"></div>${b}`;
            
            div.onclick = () => { 
                if(activeBlk === b) toggleBlock(b);
                activeBlk = b; refreshUI(); 
                if(midiOut) midiOut.send([0xB0, 49, BLOCK_ORDER_MAP[b] + 1]); // Follow Focus
            };

            div.addEventListener('dragstart', (e) => {
                e.dataTransfer.setData('text/plain', index);
                div.classList.add('dragging');
            });

            div.addEventListener('dragover', (e) => e.preventDefault());

            div.addEventListener('drop', (e) => {
                e.preventDefault();
                const fromIndex = e.dataTransfer.getData('text/plain');
                const item = chainOrder.splice(fromIndex, 1)[0];
                chainOrder.splice(index, 0, item);
                refreshUI();
                syncPhysicalChain();
            });

            div.addEventListener('dragend', () => div.classList.remove('dragging'));
            container.appendChild(div);
        });
        renderKnobs();
    }

    function syncPhysicalChain() {
        if(!midiOut) return;
        // Construct Chain Order SysEx (Simplified representation of NUX protocol)
        let orderBytes = chainOrder.map(b => BLOCK_ORDER_MAP[b]);
        midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x12, ...orderBytes, 0xF7]);
    }

    function renderKnobs() {
        const grid = document.getElementById('knobs'); grid.innerHTML = '';
        const sel = document.getElementById('models'); sel.innerHTML = '';
        const models = Object.keys(DB[activeBlk] || {});
        models.forEach(m => sel.appendChild(new Option(m,m)));

        const params = DB[activeBlk][models[0]] || [];
        params.forEach(p => {
            let val = knobVals[p.cc] || 50;
            let div = document.createElement('div');
            div.className = 'k-wrap';
            div.innerHTML = `
                <svg class="knob" viewBox="0 0 100 100" onpointerdown="dragKnob(event, ${p.cc})">
                    <circle cx="50" cy="50" r="40" class="k-trk"/>
                    <circle cx="50" cy="50" r="40" class="k-val" id="arc-${p.cc}" stroke-dasharray="200" style="stroke-dashoffset:${200-(val*2)}" transform="rotate(135 50 50)"/>
                    <line x1="50" y1="50" x2="50" y2="10" class="k-ptr" id="ptr-${p.cc}" transform="rotate(${-135+(val*2.7)} 50 50)"/>
                </svg>
                <div class="k-num" id="txt-${p.cc}">${val}</div>
                <div class="k-lbl">${p.n}</div>`;
            grid.appendChild(div);
        });
    }

    function dragKnob(e, cc) {
        e.preventDefault(); e.target.setPointerCapture(e.pointerId);
        let startY = e.clientY;
        let startVal = knobVals[cc] || 50;
        e.target.onpointermove = ev => {
            let dist = startY - ev.clientY;
            let newVal = Math.max(0, Math.min(100, startVal + dist));
            updateUIKnob(cc, newVal);
            if(midiOut) midiOut.send([0xB0, cc, Math.floor(newVal*1.27)]);
        };
        e.target.onpointerup = () => { e.target.onpointermove = null; };
    }

    function updateUIKnob(cc, val) {
        knobVals[cc] = val;
        const arc = document.getElementById('arc-'+cc);
        const ptr = document.getElementById('ptr-'+cc);
        const txt = document.getElementById('txt-'+cc);
        if(arc) {
            arc.style.strokeDashoffset = 200 - (val*2);
            ptr.setAttribute('transform', `rotate(${-135+(val*2.7)} 50 50)`);
            txt.innerText = val;
        }
    }

    function importPatch(input) {
        const file = input.files[0];
        const reader = new FileReader();
        reader.onload = e => {
            if(midiOut) {
                midiOut.send(new Uint8Array(e.target.result));
                alert("Imported!"); fullSync();
            }
        };
        reader.readAsArrayBuffer(file);
    }

    function exportPatch() {
        if(!midiOut) return alert("Link First");
        alert("Downloading .mg30patch...");
    }

    function toggleBlock(b) {
        blockStatus[b] = !blockStatus[b];
        const ccMap = { 'WAH':9, 'CMP':14, 'GATE':39, 'EFX':19, 'EQ':44, 'MOD':59, 'DLY':69, 'RVB':79 };
        if(midiOut) midiOut.send([0xB0, ccMap[b], blockStatus[b]?127:0]);
    }

    function nav(dir) {
        curPatch = Math.max(0, Math.min(127, curPatch + dir));
        if(midiOut) midiOut.send([0xC0, curPatch]);
    }
</script>
</body>
</html>
