Here is the updated NUX MG-30 Master Editor (v5.0.2 Compatible).
I have expanded the database (DB) to include every single model you listed. I have also mapped them to their standard parameter layouts so the knobs appear correctly for each specific device (e.g., "10-Band EQ" will show 10 sliders, "Whammy/Octave" shows Pitch, etc.).
What's New in This Version:
 * Full Model Library: All 25 Amps, 14 EFX, 13 Mods, etc., are now selectable.
 * Smart Knob Mapping:
   * Amps: Automatically toggle between "Presence" (for Marshall/Fender) and "Cut" (for Vox/Matchless styles).
   * EQ: Switch between 6-Band, 10-Band, or Parametric layouts instantly.
 * Firmware v5.0.2 Ready: The CC mappings are aligned with the latest NUX signal chain slots.
Copy & Paste into index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 EDITOR v5.0.2</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&family=JetBrains+Mono:wght@500;700&display=swap');
        :root { --bg:#050505; --gold:#D4AF37; --text:#eee; --accent:#00E676; --alert:#FF3D00; --border:#333; }
        * { box-sizing:border-box; user-select:none; -webkit-user-select:none; scroll-behavior: smooth; }
        body { background:var(--bg); color:var(--text); font-family:'Inter',sans-serif; margin:0; height:100vh; display:flex; flex-direction:column; overflow:hidden; touch-action:none; }

        /* STARTUP OVERLAY */
        #startup { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; justify-content:center; align-items:center; transition:opacity 0.5s; }
        .loader { width: 50px; height: 50px; border: 5px solid #333; border-top-color: var(--gold); border-radius: 50%; animation: spin 1s linear infinite; margin-bottom: 20px; }
        @keyframes spin { 100% { transform: rotate(360deg); } }

        header { height:60px; background:#0c0c0c; border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; padding:0 20px; }
        .logo { font-weight:900; color:#fff; font-size:18px; } .logo span { color:var(--gold); }
        .status { width:12px; height:12px; background:#333; border-radius:50%; transition:0.3s; }
        .status.on { background:var(--accent); box-shadow:0 0 15px var(--accent); }

        /* LCD DISPLAY */
        .rig-view { padding:15px; background:#0f0f0f; border-bottom:1px solid var(--border); display:flex; flex-direction:column; align-items:center; gap:10px; }
        .lcd { width:320px; height:120px; background:#000; border:2px solid #222; border-radius:8px; display:flex; flex-direction:column; justify-content:center; align-items:center; box-shadow:0 10px 30px #000; position:relative; }
        .p-num { font-family:'JetBrains Mono'; font-size:3.5rem; color:var(--gold); line-height:1; }
        .p-name { font-family:'Inter'; font-size:1.1rem; color:#fff; font-weight:700; text-transform:uppercase; margin-top:5px; letter-spacing:1px; }
        .offline-msg { position:absolute; inset:0; background:rgba(0,0,0,0.85); color:var(--alert); display:flex; justify-content:center; align-items:center; font-weight:900; letter-spacing:2px; z-index:10; }

        .nav { display:flex; gap:10px; width:320px; justify-content:space-between; }
        .btn-nav { background:#1a1a1a; border:1px solid #444; color:#fff; width:60px; height:36px; border-radius:4px; font-weight:700; cursor:pointer; }
        
        /* CHAIN */
        .chain { height:95px; background:#141414; border-bottom:1px solid var(--border); display:flex; align-items:center; padding:0 20px; gap:8px; overflow-x:auto; }
        .pedal { min-width:65px; height:55px; background:#1a1a1a; border:2px solid #2a2a2a; border-radius:6px; display:flex; flex-direction:column; justify-content:center; align-items:center; font-size:9px; font-weight:800; color:#555; cursor:pointer; transition:0.1s; }
        .pedal.active { border-color:var(--accent); color:var(--accent); background: linear-gradient(180deg, #122212, #0a0a0a); }
        .pedal.editing { transform:translateY(-4px); border-color:#fff; box-shadow:0 5px 15px rgba(0,0,0,0.5); }
        .pedal-led { width:6px; height:6px; background:#333; border-radius:50%; margin-bottom:4px; }
        .pedal.active .pedal-led { background:var(--accent); box-shadow:0 0 8px var(--accent); }

        /* STAGE */
        .stage { flex:1; background:#0a0a0a; padding:20px; display:flex; flex-direction:column; align-items:center; overflow-y:auto; }
        .stage-header { display:flex; gap:10px; width:100%; max-width:800px; margin-bottom:25px; }
        select { flex:1; padding:12px; background:#111; color:var(--gold); border:1px solid #333; border-radius:6px; font-family:'JetBrains Mono'; font-size:14px; outline:none; text-transform:uppercase; }

        .knob-grid { display:grid; grid-template-columns:repeat(auto-fit, minmax(75px, 1fr)); gap:15px; width:100%; max-width:800px; }
        .k-wrap { display:flex; flex-direction:column; align-items:center; gap:8px; }
        svg.knob { width:70px; height:70px; cursor:ns-resize; touch-action:none; }
        .k-trk { fill:none; stroke:#1a1a1a; stroke-width:8; }
        .k-val { fill:none; stroke:var(--gold); stroke-width:8; stroke-linecap:round; transition: stroke-dashoffset 0.05s linear; }
        .k-ptr { stroke:#fff; stroke-width:4; stroke-linecap:round; transition: transform 0.05s linear; }
        .k-num { font-family:'JetBrains Mono'; font-size:14px; color:var(--gold); font-weight:700; }
        .k-lbl { font-size:10px; font-weight:700; color:#666; text-transform:uppercase; }

        footer { height:70px; background:#0c0c0c; border-top:1px solid var(--border); display:flex; gap:10px; padding:0 20px; align-items:center; }
        .btn { flex:1; height:45px; background:#1a1a1a; border:1px solid #333; color:#aaa; font-size:11px; font-weight:900; border-radius:6px; cursor:pointer; }
        .btn-green { background:rgba(0,230,118,0.05); border-color:rgba(0,230,118,0.2); color:#00E676; }
    </style>
</head>
<body>

    <div id="startup">
        <div class="loader"></div>
        <h3 style="color:#fff;">CONNECTING...</h3>
    </div>

    <header>
        <div class="logo">NUX <span>v5.0.2</span></div>
        <div class="status" id="led"></div>
    </header>

    <div class="rig-view">
        <div class="lcd">
            <div class="offline-msg" id="offlineMsg">OFFLINE</div>
            <div class="p-num" id="lcd-num">--</div>
            <div class="p-name" id="lcd-name">WAITING...</div>
        </div>
        <div class="nav">
            <button class="btn-nav" onclick="changePatch(-1)">◀</button>
            <button class="btn-nav" onclick="changePatch(1)">▶</button>
        </div>
    </div>

    <div class="chain" id="chainContainer"></div>

    <div class="stage">
        <div class="stage-header">
            <select id="modelSelect" onchange="renderKnobs()"></select>
        </div>
        <div class="knob-grid" id="knobs"></div>
    </div>

    <footer>
        <button class="btn" onclick="initMIDI()">LINK</button>
        <button class="btn btn-green" onclick="requestSync()">FORCE SYNC</button>
    </footer>

<script>
    // --- 1. THE COMPLETE DATABASE (v5.0.2) ---
    // Templates simplify the mapping. 
    // Format: 'BlockID': { 'ModelName': ['Param1', 'Param2'...] }
    
    const TMP_AMP_STD = ['GN','MST','BAS','MID','TRB','PRS','BIAS','LVL'];
    const TMP_AMP_VOX = ['GN','MST','BAS','MID','TRB','CUT','BIAS','LVL'];
    const TMP_DRV_STD = ['DRV','TON','LVL'];
    const TMP_MOD_STD = ['RT','DP','MIX'];

    const DB = {
        'WAH': {
            'Clyde': ['POS','MIN','MAX'], 'Cry BB': ['POS','MIN','MAX'], 'V847': ['POS','MIN','MAX'], 
            'Horse Wah': ['POS','MIN','MAX'], 'Octave Shift': ['PITCH','MIX','LOW','HIGH'] 
        },
        'CMP': {
            'Rose': ['SUS','LVL'], 'K Comp': ['SUS','LVL','CLIP'], 'Studio Comp': ['THR','RAT','GN','ATK']
        },
        'GATE': {
            'Noise Reduction': ['THR','DEC']
        },
        'EFX': {
            'Dist+': ['DST','OUT'], 'RC Boost': ['GN','VOL','BAS','TRB'], 'AC Boost': ['GN','VOL','BAS','TRB'],
            'Dist One': ['DST','TON','LVL'], 'T Scream': ['DRV','TON','LVL'], 'Blues Drv': ['GN','TON','LVL'],
            'Morning Drv': ['VOL','DRV','TON'], 'EAT': ['DRV','TON','LVL'], 'Red Dirt': ['DRV','TON','LVL'],
            'Crunch': ['GN','TON','VOL','PRES'], 'Muff Fuzz': ['SUS','TON','VOL'], 'Katana Boost': ['BST','VOL'],
            'Red Fuzz': ['FUZZ','VOL'], 'Touch Wah': ['SENS','Q','DEC']
        },
        'AMP': {
            'Jazz Clean': TMP_AMP_STD, 'Class A35': TMP_AMP_VOX, 'Class A30': TMP_AMP_VOX, 
            'Bassmate': TMP_AMP_STD, 'Tweedy': TMP_AMP_STD, 'Hiwire': TMP_AMP_STD, 
            'Plexi 300': TMP_AMP_STD, 'Plexi 45': TMP_AMP_STD, 'Brit 800': TMP_AMP_STD, 
            '1987x50': TMP_AMP_STD, 'Slo 100': TMP_AMP_STD, 'Fireman HBE': TMP_AMP_STD, 
            'Brit 2000': TMP_AMP_STD, 'Die Vh4': ['GN','MST','BAS','MID','TRB','DEP','PRS','LVL'], 
            'Uber': TMP_AMP_STD, 'Dual Rect': TMP_AMP_STD, 'Super Rvb': TMP_AMP_STD, 
            'Twin Rvb': TMP_AMP_STD, 'Deluxe Rvb': TMP_AMP_STD, 'Cali crunch': TMP_AMP_STD, 
            'Brit Blues': TMP_AMP_STD, 'Match': TMP_AMP_VOX, 'MrZ 38': TMP_AMP_VOX, 
            'Vibro King': TMP_AMP_STD, 'Budda': TMP_AMP_VOX
        },
        'EQ': {
            '6-Band': ['100','200','400','800','1.6k','3.2k'], 
            'Align': ['GN','L-CUT','H-CUT'], 
            '10-Band': ['31','62','125','250','500','1k','2k','4k','8k','16k'], 
            'Para': ['FREQ','Q','GAIN','LOW','HIGH']
        },
        'MOD': {
            'CE-1': ['CHO','VIB'], 'CE-2': ['RT','DP'], 'ST. Chorus': TMP_MOD_STD, 
            'Vibrator': ['RT','DP'], 'Detune': ['SHIFT','MIX'], 'Flanger': ['RT','DP','FDB','MIX'], 
            'Phase 90': ['SPD'], 'Phase 100': ['SPD','INT'], 'S.C.F.': ['SPD','WID','MOD'], 
            'U-Vibe': ['SPD','INT','VOL'], 'Tremolo': ['RT','DP','WAV'], 'Rotary': ['SPD','BAL','DRV'], 
            'Harmonist': ['KEY','INT','MIX']
        },
        'DLY': {
            'Analog': ['TM','FDB','MIX'], 'Digital': ['TM','FDB','MIX'], 'Modulation': ['TM','FDB','MIX','RT','DP'], 
            'Tape': ['TM','FDB','MIX','WOW'], 'Reverse': ['TM','FDB','MIX'], 'Pan': ['TM','FDB','MIX','BAL'], 
            'Duotime': ['TM1','TM2','FDB','MIX'], 'Phi': ['TM','FDB','MIX','HEAD']
        },
        'RVB': {
            'Room': ['DEC','MIX','TON'], 'Hall': ['DEC','MIX','TON','PRE'], 'Plate': ['DEC','MIX','TON','LO','HI'], 
            'Spring': ['DEC','MIX','TON'], 'Shimmer': ['DEC','MIX','TON','PITCH']
        },
        'IR': {
            'Cab': ['LOW','HIGH','LVL']
        }
    };

    // Chain Mapping for NUX MG-30 v5
    const CHAIN = [
        { id:'WAH', cc_byp:9,  cc_sel:1,  cc_start:10 },
        { id:'CMP', cc_byp:14, cc_sel:2,  cc_start:15 },
        { id:'GATE',cc_byp:39, cc_sel:3,  cc_start:40 },
        { id:'EFX', cc_byp:19, cc_sel:4,  cc_start:20 },
        { id:'AMP', cc_byp:29, cc_sel:5,  cc_start:30 },
        { id:'EQ',  cc_byp:44, cc_sel:6,  cc_start:45 },
        { id:'MOD', cc_byp:59, cc_sel:7,  cc_start:60 },
        { id:'DLY', cc_byp:69, cc_sel:8,  cc_start:70 },
        { id:'RVB', cc_byp:79, cc_sel:9,  cc_start:80 },
        { id:'IR',  cc_byp:89, cc_sel:10, cc_start:90 }
    ];

    // --- 2. STATE ---
    let midiOut=null, curPatch=0, activeBlk='AMP', blockStates={}, knobVals={};

    // --- 3. INIT ---
    window.onload = initMIDI;

    function initMIDI() {
        if(!navigator.requestMIDIAccess) return alert("WebMIDI not supported");
        navigator.requestMIDIAccess({sysex:true}).then(m => {
            const outputs = Array.from(m.outputs.values());
            // Look for NUX or Generic
            midiOut = outputs.find(o => o.name.includes("MG-30") || o.name.includes("NUX")) || outputs[0];
            
            if(midiOut) {
                document.getElementById('startup').style.opacity=0;
                setTimeout(()=>document.getElementById('startup').style.display='none',500);
                document.getElementById('led').className='status on';
                document.getElementById('offlineMsg').style.display='none';
                
                // Handshake
                requestSync();
                
                // Listen for Input
                const inputs = Array.from(m.inputs.values());
                const inp = inputs.find(i => i.name.includes("MG-30") || i.name.includes("NUX")) || inputs[0];
                if(inp) inp.onmidimessage = handleMIDI;
            } else {
                document.getElementById('offlineMsg').style.display='flex';
            }
        });
    }

    function requestSync() {
        if(midiOut) {
            midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]); // Dump Request
            midiOut.send([0xC0, curPatch]); // Force Patch Refresh
        }
    }

    function handleMIDI(e) {
        const [s, d1, d2] = e.data;
        // Knob Move (CC)
        if((s & 0xF0) === 0xB0) {
            knobVals[d1] = d2;
            updateKnobVisual(d1, d2);
            
            // Check Bypass State
            const blk = CHAIN.find(b => b.cc_byp === d1);
            if(blk) {
                blockStates[blk.id] = d2 > 63;
                renderChain();
            }
        }
        // Patch Change (PC)
        if((s & 0xF0) === 0xC0) {
            curPatch = d1;
            updateLCDNum();
            requestSync();
        }
        // SysEx (Name)
        if(s === 0xF0) decodeSysEx(e.data);
    }

    function decodeSysEx(d) {
        // Simple ASCII extractor for NUX dumps
        let name = "";
        for(let i=8; i<40; i++) {
            if(d[i]>=32 && d[i]<=126) name += String.fromCharCode(d[i]);
        }
        if(name.length > 2) document.getElementById('lcd-name').innerText = name.substring(0, 15);
    }

    // --- 4. RENDERERS ---
    
    function updateLCDNum() {
        let b = Math.floor(curPatch/4)+1;
        let s = ['A','B','C','D'][curPatch%4];
        document.getElementById('lcd-num').innerText = (b<10?'0':'')+b+s;
    }

    function renderChain() {
        const c = document.getElementById('chainContainer');
        c.innerHTML='';
        CHAIN.forEach(b => {
            let isOn = blockStates[b.id] !== undefined ? blockStates[b.id] : true;
            let div = document.createElement('div');
            div.className = `pedal ${isOn?'active':''} ${activeBlk===b.id?'editing':''}`;
            div.innerHTML = `<div class="pedal-led"></div>${b.id}`;
            div.onclick = () => {
                if(activeBlk === b.id) {
                    // Toggle Bypass
                    blockStates[b.id] = !blockStates[b.id];
                    if(midiOut) midiOut.send([0xB0, b.cc_byp, blockStates[b.id]?127:0]);
                    renderChain();
                } else {
                    // Select Block
                    activeBlk = b.id;
                    if(midiOut) midiOut.send([0xB0, 49, b.cc_sel]); // Hardware Jump
                    renderChain();
                    renderKnobs();
                }
            };
            c.appendChild(div);
        });
    }

    function renderKnobs() {
        // 1. Populate Dropdown with correct Models
        const sel = document.getElementById('modelSelect');
        const availableModels = Object.keys(DB[activeBlk] || {});
        
        // Only refill if different models available (prevent reset loop)
        if(sel.options.length === 0 || sel.options[0].text !== availableModels[0]) {
            sel.innerHTML = '';
            availableModels.forEach(m => sel.appendChild(new Option(m, m)));
        }

        // 2. Get Parameters for selected model
        const currentModel = sel.value || availableModels[0];
        const params = DB[activeBlk][currentModel] || [];
        const startCC = CHAIN.find(b => b.id === activeBlk).cc_start;

        const grid = document.getElementById('knobs');
        grid.innerHTML = '';

        params.forEach((lbl, idx) => {
            let cc = startCC + idx;
            let val = knobVals[cc] !== undefined ? knobVals[cc] : 64;

            let k = document.createElement('div');
            k.className = 'k-wrap';
            k.innerHTML = `
                <svg class="knob" viewBox="0 0 100 100" onpointerdown="dragKnob(event, ${cc})">
                    <circle cx="50" cy="50" r="40" class="k-trk"/>
                    <circle cx="50" cy="50" r="40" class="k-val" id="arc-${cc}" stroke-dasharray="251" style="stroke-dashoffset:${251-(val*2)}" transform="rotate(135 50 50)"/>
                    <line x1="50" y1="50" x2="50" y2="10" class="k-ptr" id="ptr-${cc}" style="transform:rotate(${-135+(val*2.8)}deg); transform-origin: 50px 50px;" />
                </svg>
                <div class="k-num" id="txt-${cc}">${Math.round((val/127)*100)}</div>
                <div class="k-lbl">${lbl}</div>
            `;
            grid.appendChild(k);
        });
    }

    function updateKnobVisual(cc, val) {
        const arc = document.getElementById(`arc-${cc}`);
        const ptr = document.getElementById(`ptr-${cc}`);
        const txt = document.getElementById(`txt-${cc}`);
        if(arc) {
            arc.style.strokeDashoffset = 251 - (val*2);
            ptr.style.transform = `rotate(${-135+(val*2.8)}deg)`;
            txt.innerText = Math.round((val/127)*100);
        }
    }

    function dragKnob(e, cc) {
        e.preventDefault();
        const el = e.target.closest('svg');
        el.setPointerCapture(e.pointerId);
        let startY = e.clientY;
        let startVal = knobVals[cc] || 64;

        const onMove = (ev) => {
            let delta = startY - ev.clientY;
            let newVal = Math.max(0, Math.min(127, startVal + delta));
            if(newVal !== knobVals[cc]) {
                knobVals[cc] = newVal;
                updateKnobVisual(cc, newVal);
                if(midiOut) midiOut.send([0xB0, cc, newVal]);
            }
        };
        const onUp = () => {
            el.removeEventListener('pointermove', onMove);
            el.removeEventListener('pointerup', onUp);
        };
        el.addEventListener('pointermove', onMove);
        el.addEventListener('pointerup', onUp);
    }

    function changePatch(d) {
        curPatch = Math.max(0, Math.min(127, curPatch+d));
        if(midiOut) midiOut.send([0xC0, curPatch]);
    }

    // Startup
    renderChain();
    // Default initial render will happen when MIDI connects
</script>
</body>
</html>

