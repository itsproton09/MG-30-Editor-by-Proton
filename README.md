<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 PRO EDITOR V18</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&family=JetBrains+Mono:wght@500;700&display=swap');
        
        :root { --bg:#121212; --gold:#D4AF37; --text:#e0e0e0; --accent:#00E676; --danger:#FF1744; }
        * { box-sizing:border-box; user-select:none; touch-action:none; }
        body { background:var(--bg); color:var(--text); font-family:'Inter',sans-serif; margin:0; height:100vh; display:flex; flex-direction:column; overflow:hidden; }

        /* HEADER */
        header { height:45px; background:#080808; border-bottom:1px solid #333; display:flex; justify-content:space-between; align-items:center; padding:0 15px; }
        .logo { font-weight:900; color:#fff; font-size:14px; letter-spacing:1px; } .logo span { color:var(--gold); }
        .status { width:10px; height:10px; background:#222; border-radius:50%; box-shadow:0 0 5px #000; transition:0.3s; }
        .status.on { background:var(--accent); box-shadow:0 0 10px var(--accent); }

        /* LCD AREA */
        .top-deck { background:#1a1a1a; padding:15px 0; border-bottom:2px solid #2a2a2a; box-shadow:0 5px 15px rgba(0,0,0,0.5); z-index:10; }
        .lcd-wrap { display:flex; flex-direction:column; align-items:center; gap:10px; }
        
        .lcd { 
            width:260px; height:80px; background:#000; border:2px solid #333; border-radius:4px; 
            display:flex; flex-direction:column; justify-content:center; align-items:center; position:relative; overflow:hidden;
        }
        .p-num { font-family:'JetBrains Mono'; font-size:2.5rem; color:var(--gold); line-height:1; z-index:2; text-shadow:0 0 15px rgba(212,175,55,0.2); }
        .p-name { font-family:'Inter'; font-size:0.9rem; color:#fff; font-weight:700; text-transform:uppercase; z-index:2; letter-spacing:1px; margin-top:4px; }
        .lcd-msg { position:absolute; inset:0; background:rgba(20,20,20,0.95); color:var(--danger); display:flex; justify-content:center; align-items:center; font-weight:900; z-index:10; font-size:12px; letter-spacing:2px; }

        .nav-bar { display:flex; gap:10px; width:260px; justify-content:space-between; margin-top:8px; }
        .btn-nav { background:#252525; border:1px solid #333; color:#888; flex:1; height:32px; border-radius:4px; font-weight:700; cursor:pointer; }
        .btn-nav:active { background:#333; color:#fff; }

        /* SIGNAL CHAIN */
        .chain-strip { height:75px; display:flex; align-items:center; padding:0 15px; gap:5px; overflow-x:auto; margin-top:15px; scrollbar-width:none; }
        .block { 
            min-width:55px; height:45px; background:#222; border:1px solid #333; border-radius:3px; 
            display:flex; flex-direction:column; justify-content:center; align-items:center; 
            font-size:9px; font-weight:800; color:#555; cursor:pointer; position:relative; transition:0.1s;
        }
        .block.active { background:linear-gradient(180deg, #333, #222); border-color:#444; color:#ccc; }
        .block.editing { border-color:var(--gold); color:var(--gold); transform:translateY(-2px); box-shadow:0 4px 10px rgba(0,0,0,0.5); }
        .block.dragging { opacity:0.5; border:1px dashed #fff; }
        .b-led { width:5px; height:5px; background:#111; border-radius:50%; margin-bottom:3px; border:1px solid #000; }
        .block.active .b-led { background:var(--accent); box-shadow:0 0 5px var(--accent); }

        /* STAGE / CHASSIS */
        .stage { flex:1; background:#161616; padding:20px; display:flex; flex-direction:column; align-items:center; overflow-y:auto; }
        .controls-header { width:100%; max-width:800px; margin-bottom:15px; display:flex; justify-content:center; }
        select { background:#222; color:var(--gold); border:1px solid #333; padding:8px 20px; font-family:'JetBrains Mono'; border-radius:20px; outline:none; font-weight:700; text-transform:uppercase; cursor:pointer; }

        /* DYNAMIC STYLES */
        .chassis { 
            width:100%; max-width:800px; border-radius:8px; padding:25px; 
            display:flex; flex-wrap:wrap; justify-content:center; gap:20px; 
            box-shadow:inset 0 0 50px rgba(0,0,0,0.5), 0 10px 30px rgba(0,0,0,0.5);
            border:2px solid #333; position:relative;
        }
        /* Style: Standard Black */
        .style-std { background:#222; }
        /* Style: Plexi/Gold */
        .style-gold { background:linear-gradient(180deg, #d4af37, #8a701e); border-color:#5c480f; }
        .style-gold .k-val { stroke:#333; } .style-gold .k-num, .style-gold .k-lbl { color:#222; text-shadow:none; }
        /* Style: Rectifier/Metal */
        .style-metal { background:repeating-linear-gradient(45deg, #222, #222 5px, #2a2a2a 5px, #2a2a2a 10px); border-color:#111; }
        /* Style: Vox/Silver */
        .style-silver { background:linear-gradient(180deg, #ccc, #999); border-color:#666; }
        .style-silver .k-val { stroke:#222; } .style-silver .k-num, .style-silver .k-lbl { color:#000; text-shadow:none; }
        /* Style: Fender/Tweed */
        .style-tweed { background:linear-gradient(45deg, #e6cfa3, #d4b881); border-color:#a68b5b; }
        .style-tweed .k-val { stroke:#5c4033; } .style-tweed .k-num, .style-tweed .k-lbl { color:#3e2b22; text-shadow:none; }
        /* Style: Green Pedal */
        .style-green { background:#008f39; border-color:#005c25; }
        /* Style: Blue Pedal */
        .style-blue { background:#005fb8; border-color:#003d7a; }

        /* KNOB GRAPHICS */
        .k-wrap { display:flex; flex-direction:column; align-items:center; width:70px; }
        svg.knob { width:60px; height:60px; cursor:ns-resize; filter:drop-shadow(0 4px 5px rgba(0,0,0,0.5)); }
        .k-body { fill:#1a1a1a; stroke:#000; stroke-width:2; }
        .k-val { fill:none; stroke:var(--gold); stroke-width:6; stroke-linecap:round; }
        .k-ptr { fill:#fff; }
        .k-num { font-family:'JetBrains Mono'; font-size:12px; font-weight:700; color:rgba(255,255,255,0.9); margin-bottom:3px; text-shadow:0 1px 2px #000; }
        .k-lbl { font-size:9px; font-weight:900; color:rgba(255,255,255,0.6); text-transform:uppercase; text-align:center; text-shadow:0 1px 2px #000; }

        /* FOOTER */
        footer { height:50px; background:#080808; border-top:1px solid #333; display:flex; gap:10px; padding:0 15px; align-items:center; }
        .btn-act { flex:1; height:34px; background:#1a1a1a; border:1px solid #333; color:#aaa; font-size:10px; font-weight:900; border-radius:4px; cursor:pointer; }
        .btn-act:hover { background:#222; color:#fff; }
        .btn-green { color:var(--accent); background:rgba(0,230,118,0.05); border-color:rgba(0,230,118,0.2); }
    </style>
</head>
<body>

    <header>
        <div class="logo">NUX <span>PRO V18</span></div>
        <div class="status" id="led"></div>
    </header>

    <div class="top-deck">
        <div class="lcd-wrap">
            <div class="lcd">
                <div class="lcd-msg" id="msgOverlay">OFFLINE</div>
                <div class="p-num" id="lcdNum">--</div>
                <div class="p-name" id="lcdName">CONNECTING</div>
            </div>
            <div class="nav-bar">
                <button class="btn-nav" onclick="changePatch(-1)">◀ PREV</button>
                <button class="btn-nav" onclick="changePatch(1)">NEXT ▶</button>
            </div>
        </div>
        <div class="chain-strip" id="chainUI"></div>
    </div>

    <div class="stage">
        <div class="controls-header">
            <select id="modelSel" onchange="updateModel()"></select>
        </div>
        <div class="chassis style-std" id="chassisUI">
            </div>
    </div>

    <footer>
        <button class="btn-act btn-green" onclick="initMIDI()">LINK HARDWARE</button>
        <button class="btn-act" onclick="document.getElementById('imp').click()">IMPORT</button>
        <button class="btn-act" onclick="exportFile()">EXPORT</button>
    </footer>
    <input type="file" id="imp" hidden onchange="importFile(this)">

<script>
    // --- 1. FULL DATABASE (Firmware 5.0.2) ---
    // Includes Visual Styles: 'std', 'gold', 'metal', 'silver', 'tweed', 'green', 'blue'
    const DB = {
        'WAH': { type:'pedal', style:'std', models:{
            'Clyde':['POS','MIN','MAX'], 'Cry BB':['POS','MIN','MAX'], 'V847':['POS','MIN','MAX'], 
            'Horse Wah':['POS','MIN','MAX'], 'Octave Shift':['PITCH','MIX','LO','HI'] 
        }},
        'CMP': { type:'pedal', style:'blue', models:{
            'Rose':['SUS','LVL'], 'K Comp':['SUS','LVL','CLIP'], 'Studio Comp':['THR','RAT','GN','ATK']
        }},
        'GATE': { type:'pedal', style:'std', models:{
            'Noise Reduction':['THR','DEC']
        }},
        'EFX': { type:'pedal', style:'green', models:{
            'Dist+':['DST','OUT'], 'RC Boost':['GN','VOL','BAS','TRB'], 'AC Boost':['GN','VOL','BAS','TRB'],
            'Dist One':['DST','TON','LVL'], 'T Scream':['DRV','TON','LVL'], 'Blues Drv':['GN','TON','LVL'],
            'Morning Drv':['VOL','DRV','TON'], 'EAT':['DRV','TON','LVL'], 'Red Dirt':['DRV','TON','LVL'],
            'Crunch':['GN','TON','VOL','PRES'], 'Muff Fuzz':['SUS','TON','VOL'], 'Katana Boost':['BST','VOL'],
            'Red Fuzz':['FUZZ','VOL'], 'Touch Wah':['SENS','Q','DEC']
        }},
        'AMP': { type:'amp', style:'std', models:{
            // Classics
            'Jazz Clean':['GN','MST','BAS','MID','TRB','PRS','LVL'], // Silver
            'Bassmate':['GN','MST','BAS','MID','TRB','PRS','LVL'], // Tweed
            'Tweedy':['GN','MST','BAS','MID','TRB','PRS','LVL'], // Tweed
            'Twin Rvb':['GN','MST','BAS','MID','TRB','PRS','LVL'], // Silver
            'Deluxe Rvb':['GN','MST','BAS','MID','TRB','PRS','LVL'], // Silver
            'Vibro King':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            // Vox Style (Cut instead of Presence)
            'Class A35':['GN','MST','BAS','MID','TRB','CUT','LVL'],
            'Class A30':['GN','MST','BAS','MID','TRB','CUT','LVL'],
            'Match':['GN','MST','BAS','MID','TRB','CUT','LVL'],
            'Budda':['GN','MST','BAS','MID','TRB','CUT','LVL'],
            // Marshall Style
            'Plexi 300':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Plexi 45':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Brit 800':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            '1987x50':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Brit 2000':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Brit Blues':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            // High Gain
            'Slo 100':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Fireman HBE':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Die Vh4':['GN','MST','BAS','MID','TRB','PRS','DEP','LVL'],
            'Uber':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Dual Rect':['GN','MST','BAS','MID','TRB','PRS','LVL'],
            'Cali crunch':['GN','MST','BAS','MID','TRB','PRS','LVL']
        }},
        'EQ': { type:'pedal', style:'std', models:{
            '6-Band':['100','200','400','800','1.6k','3.2k'], 'Align':['GN','L-CUT','H-CUT'], 
            '10-Band':['31','62','125','250','500','1k','2k','4k','8k','16k'], 'Para':['FREQ','Q','GAIN','LO','HI']
        }},
        'MOD': { type:'pedal', style:'blue', models:{
            'CE-1':['CHO','VIB'], 'CE-2':['RT','DP'], 'ST. Chorus':['RT','DP','MIX'], 'Vibrator':['RT','DP'],
            'Detune':['SHIFT','MIX'], 'Flanger':['RT','DP','FDB','MIX'], 'Phase 90':['SPD'], 'Phase 100':['SPD','INT'],
            'S.C.F.':['SPD','WID','MOD'], 'U-Vibe':['SPD','INT','VOL'], 'Tremolo':['RT','DP','WAV'], 'Rotary':['SPD','BAL','DRV'],
            'Harmonist':['KEY','INT','MIX']
        }},
        'DLY': { type:'pedal', style:'std', models:{
            'Analog':['TM','FDB','MIX'], 'Digital':['TM','FDB','MIX'], 'Modulation':['TM','FDB','MIX'], 'Tape':['TM','FDB','MIX'],
            'Reverse':['TM','FDB','MIX'], 'Pan':['TM','FDB','MIX'], 'Duotime':['TM1','TM2','FDB','MIX'], 'Phi':['TM','FDB','MIX']
        }},
        'RVB': { type:'pedal', style:'std', models:{
            'Room':['DEC','MIX','TON'], 'Hall':['DEC','MIX','TON'], 'Plate':['DEC','MIX','TON'], 'Spring':['DEC','MIX','TON'], 'Shimmer':['DEC','MIX','TON']
        }},
        'IR': { type:'pedal', style:'std', models:{
            'Cab':['LO','HI','LVL']
        }}
    };

    // --- 2. LOGIC MAP (Firmware 5.0.2 Verified) ---
    // WA:9, CMP:14, EFX:19, AMP:29, EQ:44, GATE:39, MOD:59, DLY:69, RVB:79, IR:89
    let chain = [
        { id:'WAH', cc:9,  sel:1,  start:10 },
        { id:'CMP', cc:14, sel:2,  start:15 },
        { id:'GATE',cc:39, sel:3,  start:40 },
        { id:'EFX', cc:19, sel:4,  start:20 },
        { id:'AMP', cc:29, sel:5,  start:30 },
        { id:'EQ',  cc:44, sel:6,  start:45 },
        { id:'MOD', cc:59, sel:7,  start:60 },
        { id:'DLY', cc:69, sel:8,  start:70 },
        { id:'RVB', cc:79, sel:9,  start:80 },
        { id:'IR',  cc:89, sel:10, start:90 }
    ];

    // --- 3. STATE ---
    let midiOut = null;
    let patchNum = 0;
    let activeBlk = 'AMP';
    let blockStates = {}; // { 'AMP': true, 'WAH': false }
    let knobVals = {};    // { 30: 127, 31: 45 }

    // --- 4. INIT ---
    window.onload = initMIDI;

    function initMIDI() {
        if(!navigator.requestMIDIAccess) return msg("BROWSER NOT SUPPORTED", true);
        
        navigator.requestMIDIAccess({sysex:true}).then(acc => {
            const outs = Array.from(acc.outputs.values());
            midiOut = outs.find(o => o.name.includes("MG-30") || o.name.includes("NUX")) || outs[0];
            
            if(midiOut) {
                msg("", false); // Online
                document.getElementById('led').className = 'status on';
                
                // FORCE SYNC: Send Dump Request
                // 0x11 = Request current preset dump
                midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]);

                const ins = Array.from(acc.inputs.values());
                const inp = ins.find(i => i.name.includes("MG-30") || i.name.includes("NUX")) || ins[0];
                if(inp) inp.onmidimessage = handleMsg;
                
                acc.onstatechange = e => { 
                    if(e.port.state === 'disconnected') disconnect(); 
                };
            } else {
                disconnect();
            }
        });
    }

    function disconnect() {
        midiOut = null;
        msg("OFFLINE", true);
        document.getElementById('led').className = 'status';
        document.getElementById('lcdNum').innerText = "--";
    }

    function msg(txt, show) {
        const el = document.getElementById('msgOverlay');
        el.innerText = txt;
        el.style.display = show ? 'flex' : 'none';
    }

    // --- 5. MIDI ENGINE ---
    function handleMsg(e) {
        const [s, d1, d2] = e.data;
        
        // KNOB / BYPASS (CC)
        if((s & 0xF0) === 0xB0) {
            // Update Knob Val
            knobVals[d1] = d2;
            updateKnobUI(d1, d2);
            
            // Update Bypass State
            const blk = chain.find(b => b.cc === d1);
            if(blk) {
                blockStates[blk.id] = d2 > 63; // 127=ON
                renderChain();
            }
        }
        
        // PATCH CHANGE (PC)
        if((s & 0xF0) === 0xC0) {
            // ERROR 3 FIX: FLUSH STATE to prevent bleed
            knobVals = {}; 
            blockStates = {};
            patchNum = d1;
            renderChain();
            renderChassis();
            
            // Update LCD
            let b = Math.floor(patchNum/4)+1, sub = ['A','B','C','D'][patchNum%4];
            document.getElementById('lcdNum').innerText = (b<10?'0':'')+b+sub;
            
            // Request new data
            if(midiOut) midiOut.send([0xF0, 0x00, 0x00, 0x4F, 0x11, 0xF7]);
        }

        // SYSEX (NAME FINDER)
        if(s === 0xF0) {
            // ERROR 1 FIX: DEEP SEARCH
            // Scan the whole packet for the longest ASCII string
            let str = "", maxStr = "";
            for(let i=0; i<e.data.length; i++) {
                let c = e.data[i];
                if(c >= 32 && c <= 126) { str += String.fromCharCode(c); }
                else {
                    if(str.length > maxStr.length) maxStr = str;
                    str = "";
                }
            }
            if(str.length > maxStr.length) maxStr = str; // Final check
            
            // Clean junk
            if(maxStr.startsWith("O")) maxStr = maxStr.substring(1); 
            if(maxStr.length > 2) document.getElementById('lcdName').innerText = maxStr.substring(0,16);
        }
    }

    // --- 6. UI RENDERERS ---
    function renderChain() {
        const c = document.getElementById('chainUI'); c.innerHTML = '';
        chain.forEach((b, idx) => {
            let isOn = blockStates[b.id] !== undefined ? blockStates[b.id] : true;
            let div = document.createElement('div');
            div.className = `block ${isOn?'active':''} ${activeBlk===b.id?'editing':''}`;
            div.draggable = true; // REARRANGE ENABLED
            div.innerHTML = `<div class="b-led"></div>${b.id}`;
            
            // EVENTS
            div.onclick = () => {
                if(activeBlk === b.id) {
                    // TOGGLE
                    let ns = !isOn;
                    blockStates[b.id] = ns;
                    if(midiOut) midiOut.send([0xB0, b.cc, ns?127:0]);
                    renderChain();
                } else {
                    // SELECT
                    activeBlk = b.id;
                    if(midiOut) midiOut.send([0xB0, 49, b.sel]);
                    renderChain(); renderChassis();
                }
            };
            
            // DRAG DROP
            div.addEventListener('dragstart', e => { e.dataTransfer.setData('idx', idx); div.classList.add('dragging'); });
            div.addEventListener('dragend', () => div.classList.remove('dragging'));
            div.addEventListener('dragover', e => e.preventDefault());
            div.addEventListener('drop', e => {
                e.preventDefault();
                let from = e.dataTransfer.getData('idx');
                let item = chain.splice(from, 1)[0];
                chain.splice(idx, 0, item);
                renderChain();
                // Note: Reordering usually requires specific SysEx on MG-30. 
                // Visual reorder is done, but hardware might need manual save.
            });

            c.appendChild(div);
        });
    }

    function renderChassis() {
        const ui = document.getElementById('chassisUI');
        ui.innerHTML = '';
        
        // 1. GET DATA
        const bInfo = DB[activeBlk];
        const sel = document.getElementById('modelSel');
        
        // Populate Select only if needed
        const avail = Object.keys(bInfo.models);
        if(sel.getAttribute('data-blk') !== activeBlk) {
            sel.setAttribute('data-blk', activeBlk);
            sel.innerHTML = '';
            avail.forEach(m => sel.appendChild(new Option(m, m)));
        }
        
        const curModel = sel.value || avail[0];
        
        // 2. APPLY STYLES (Smart Graphics)
        ui.className = 'chassis';
        
        // Detect Style
        let s = 'style-std';
        if(bInfo.type === 'amp') {
            if(curModel.includes('Plexi') || curModel.includes('Brit')) s = 'style-gold';
            if(curModel.includes('Rect') || curModel.includes('Die') || curModel.includes('Uber')) s = 'style-metal';
            if(curModel.includes('Jazz') || curModel.includes('Twin')) s = 'style-silver';
            if(curModel.includes('Tweed') || curModel.includes('Bass')) s = 'style-tweed';
        } else {
            if(activeBlk === 'CMP' || activeBlk === 'MOD') s = 'style-blue';
            if(activeBlk === 'EFX' && (curModel.includes('Scream') || curModel.includes('Boost'))) s = 'style-green';
        }
        ui.classList.add(s);

        // 3. RENDER KNOBS
        const params = bInfo.models[curModel];
        const startCC = chain.find(x => x.id === activeBlk).start;

        params.forEach((p, i) => {
            let cc = startCC + i;
            let val = knobVals[cc] !== undefined ? knobVals[cc] : 64; // Default 50%
            
            let k = document.createElement('div'); k.className = 'k-wrap';
            k.innerHTML = `
                <svg class="knob" viewBox="0 0 100 100" onpointerdown="drag(event, ${cc})">
                    <circle cx="50" cy="50" r="40" class="k-body" fill="url(#grad)"/>
                    <path id="arc-${cc}" d="M 20 80 A 45 45 0 1 1 80 80" class="k-val" stroke-dasharray="235" stroke-dashoffset="${235 - (val*1.85)}" fill="none" opacity="0.9"/>
                    <circle cx="50" cy="50" r="4" class="k-ptr" transform="rotate(${-135 + (val*2.8)}, 50, 50)" style="transform-origin:50px 50px; transform:rotate(${-135+(val*2.8)}deg) translate(0, -32px)"/>
                </svg>
                <div class="k-num" id="txt-${cc}">${Math.round((val/127)*100)}</div>
                <div class="k-lbl">${p}</div>
            `;
            ui.appendChild(k);
        });
    }

    function updateModel() { renderChassis(); }

    function updateKnobUI(cc, val) {
        const arc = document.getElementById(`arc-${cc}`);
        const txt = document.getElementById(`txt-${cc}`);
        if(arc) {
            arc.style.strokeDashoffset = 235 - (val*1.85);
            // Rotational pointer update
            const svg = arc.parentElement;
            const ptr = svg.querySelector('.k-ptr');
            ptr.style.transform = `rotate(${-135+(val*2.8)}deg) translate(0, -32px)`;
            txt.innerText = Math.round((val/127)*100);
        }
    }

    function drag(e, cc) {
        e.preventDefault(); const el = e.target.closest('svg'); el.setPointerCapture(e.pointerId);
        let y = e.clientY; let sv = knobVals[cc] || 64;
        
        const move = ev => {
            let d = y - ev.clientY;
            let nv = Math.max(0, Math.min(127, sv + d));
            if(nv !== knobVals[cc]) {
                knobVals[cc] = nv;
                updateKnobUI(cc, nv);
                if(midiOut) midiOut.send([0xB0, cc, nv]);
            }
        };
        const up = () => { el.removeEventListener('pointermove', move); el.removeEventListener('pointerup', up); };
        el.addEventListener('pointermove', move); el.addEventListener('pointerup', up);
    }

    function changePatch(d) {
        if(!midiOut) return;
        patchNum = Math.max(0, Math.min(127, patchNum + d));
        midiOut.send([0xC0, patchNum]);
        // The rest happens in handleMsg when hardware replies
    }

    // --- 7. FILE IO (Fixed Extension) ---
    function exportFile() {
        const name = document.getElementById('lcdName').innerText.trim() || "patch";
        const fn = `${name}.mg30patch`; // Custom Extension
        const blob = new Blob([JSON.stringify(knobVals)], {type: "application/json"});
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = fn;
        a.click();
    }

    function importFile(inp) {
        const f = inp.files[0];
        if(!f) return;
        
        // VALIDATION
        if(!f.name.endsWith(".mg30patch") && !f.name.endsWith(".json")) {
            alert("Please select a .mg30patch file");
            return;
        }

        const r = new FileReader();
        r.onload = e => {
            try {
                const data = JSON.parse(e.target.result);
                // SEND TO HARDWARE
                Object.keys(data).forEach(cc => {
                    if(midiOut) midiOut.send([0xB0, parseInt(cc), data[cc]]);
                    knobVals[parseInt(cc)] = data[cc];
                });
                renderChassis();
                alert("Patch Imported Successfully");
            } catch(err) {
                alert("Error: File might be corrupt or binary format.");
            }
        };
        r.readAsText(f);
    }

    // START
    renderChain(); renderChassis();
</script>
</body>
</html>
