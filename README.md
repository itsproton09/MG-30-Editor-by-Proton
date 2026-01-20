<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>MG-30 MASTER SYNC</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&family=JetBrains+Mono:wght@500;700&display=swap');
        :root { --bg:#050505; --gold:#D4AF37; --text:#e0e0e0; --on:#00E676; --off:#FF1744; --panel:#121212; --border:#333; }
        * { box-sizing:border-box; user-select:none; touch-action:none; }
        body { background:var(--bg); color:var(--text); font-family:'Inter',sans-serif; margin:0; height:100vh; display:flex; flex-direction:column; overflow:hidden; }
        
        header { height:60px; background:#000; border-bottom:2px solid var(--gold); display:flex; justify-content:space-between; align-items:center; padding:0 20px; }
        .tab-btn { padding:10px 15px; border:none; background:#181818; color:#888; font-weight:800; cursor:pointer; font-size:10px; border-radius:5px; text-transform:uppercase; margin-left:5px; }
        .tab-btn.active { background:var(--gold); color:#000; }
        
        /* THE VISUAL SIGNAL CHAIN */
        .chain-container { height:100px; width:100%; background:#000; display:flex; align-items:center; gap:10px; padding:0 20px; overflow-x:auto; border-bottom:1px solid #222; }
        .pedal-block { min-width:85px; height:65px; background:#111; border:3px solid #333; border-radius:8px; display:flex; flex-direction:column; justify-content:center; align-items:center; font-size:11px; font-weight:900; color:#555; transition: all 0.15s ease; }
        
        /* GREEN FOR ON / RED FOR OFF */
        .pedal-block.on { border-color:var(--on); color:#fff; box-shadow: 0 0 15px rgba(0,230,118,0.25); }
        .pedal-block.off { border-color:var(--off); color:#fff; box-shadow: 0 0 10px rgba(255,23,68,0.15); }
        
        .pedal-block.selected { background:#252525; border-style: double; transform:scale(1.05); }
        .status-light { width:10px; height:10px; border-radius:50%; margin-bottom:6px; background:#333; }
        .on .status-light { background:var(--on); box-shadow: 0 0 10px var(--on); }
        .off .status-light { background:var(--off); box-shadow: 0 0 8px var(--off); }

        .stage { flex:1; padding:20px; display:flex; flex-direction:column; align-items:center; overflow-y:auto; }
        .pedal-chassis { width:100%; max-width:1000px; background:var(--panel); border-radius:20px; padding:30px; display:flex; flex-wrap:wrap; justify-content:center; gap:25px; border:1px solid #222; }
        
        /* UI BUTTONS */
        footer { height:85px; background:#000; border-top:1px solid #222; display:flex; gap:12px; padding:0 20px; align-items:center; justify-content:center; }
        .btn-action { flex:1; height:50px; background:#111; border:2px solid #333; color:#fff; font-size:11px; font-weight:900; border-radius:10px; text-transform:uppercase; cursor:pointer; }
        .btn-action:active { background:var(--gold); color:#000; }
        #debug-log { height:45px; background:#000; color:var(--on); font-family:'JetBrains Mono'; font-size:11px; padding:12px; border-top:1px solid #222; overflow:hidden; }
    </style>
</head>
<body>

<header>
    <div style="font-weight:900; font-size:18px;">MG-30 <span style="color:var(--gold)">V5 CORE</span></div>
    <div>
        <button class="tab-btn active" id="t-edit" onclick="switchTab('edit')">Tone</button>
        <button class="tab-btn" id="t-global" onclick="switchTab('global')">Global</button>
    </div>
</header>

<div id="editView">
    <div class="chain-container" id="chainUI"></div>
    <div class="stage">
        <select id="modelSel" style="margin-bottom:20px; background:#000; color:var(--gold); border:2px solid #444; padding:15px; width:100%; max-width:450px; border-radius:8px; font-size:16px; font-weight:700;" onchange="userSelectModel()"></select>
        <div class="pedal-chassis" id="knobUI"></div>
    </div>
</div>

<div id="globalView" style="display:none;" class="stage">
    <div class="pedal-chassis" id="globalKnobUI"></div>
</div>

<div id="debug-log">Ready. Connect MG-30 and tap LINK.</div>

<footer>
    <button class="btn-action" style="border-color:var(--on); color:var(--on);" onclick="initMidi()">LINK HARDWARE</button>
    <button class="btn-action" onclick="changePatch(-1)">PREV</button>
    <button class="btn-action" onclick="changePatch(1)">NEXT</button>
    <button class="btn-action" onclick="requestSync()">SYNC</button>
</footer>

<script>
    const log = (m) => document.getElementById('debug-log').innerText = "LOG: " + m;
    let midiOut = null, midiIn = null, stateBypass = {}, stateKnobs = {};

    const BLOCKS = {
        'WAH': {cc:89, sel:1, start:10, b:72}, 'CMP': {cc:14, sel:2, start:15, b:20}, 'GATE':{cc:39, sel:3, start:40, b:60},
        'EFX': {cc:19, sel:4, start:20, b:24}, 'AMP': {cc:29, sel:5, start:30, b:32}, 'IR':  {cc:9,  sel:10,start:90, b:12},
        'EQ':  {cc:44, sel:6, start:45, b:40}, 'MOD': {cc:59, sel:7, start:60, b:48}, 'DLY': {cc:69, sel:8, start:70, b:56}, 'RVB': {cc:79, sel:9, start:80, b:64}
    };

    const GLOBAL_CC = { 'Low Cut': 100, 'Bass': 101, 'Mid': 102, 'High': 103, 'High Cut': 104, 'Output': 105 };

    async function initMidi() {
        try {
            const access = await navigator.requestMIDIAccess({ sysex: true });
            midiOut = Array.from(access.outputs.values()).find(o => o.name.includes("MG-30"));
            midiIn = Array.from(access.inputs.values()).find(i => i.name.includes("MG-30"));
            
            if(midiOut && midiIn) {
                midiIn.onmidimessage = (e) => { if(e.data[0] === 0xF0) parseSysex(e.data); };
                requestSync();
                log("CONNECTED: " + midiOut.name);
            } else {
                log("DEVICE NOT FOUND. CHECK OTG.");
            }
        } catch(e) { log("MIDI PERMISSION DENIED."); }
    }

    function requestSync() { 
        if(midiOut) midiOut.send([0xF0, 0x00, 0x01, 0x4F, 0x11, 0x01, 0x00, 0x00, 0xF7]); 
    }

    function parseSysex(raw) {
        Object.keys(BLOCKS).forEach(key => {
            const b = BLOCKS[key];
            stateBypass[key] = raw[b.b + 1] > 0;
            for(let p=0; p<6; p++) stateKnobs[b.start+p] = raw[b.b+2+p];
        });
        renderChain(); renderKnobs(); renderGlobal();
    }

    // THE RELIABLE TOGGLE FUNCTION
    async function toggleBlock(id) {
        stateBypass[id] = !stateBypass[id];
        const val = stateBypass[id] ? 127 : 0;

        // Step 1: Select/Focus the block (CC 49)
        sendCC(49, BLOCKS[id].sel);
        
        // Step 2: Tiny wait for hardware to "Listen"
        await new Promise(r => setTimeout(r, 35));
        
        // Step 3: Send Bypass/Engage (CC)
        sendCC(BLOCKS[id].cc, val);
        
        log(`${id} set to ${stateBypass[id] ? 'ON' : 'OFF'}`);
        renderChain(); // Refresh lights
    }

    function renderChain() {
        const c = document.getElementById('chainUI'); c.innerHTML = '';
        Object.keys(BLOCKS).forEach(id => {
            const el = document.createElement('div');
            const isOn = stateBypass[id];
            el.className = `pedal-block ${isOn ? 'on' : 'off'} ${currentEditBlock === id ? 'selected' : ''}`;
            el.innerHTML = `<div class="status-light"></div><div>${id}</div>`;
            el.onclick = () => {
                if(currentEditBlock === id) toggleBlock(id);
                else { 
                    currentEditBlock = id; 
                    sendCC(49, BLOCKS[id].sel); 
                    renderChain(); 
                    renderKnobs(); 
                }
            };
            c.appendChild(el);
        });
    }

    function renderKnobs() {
        const c = document.getElementById('knobUI'); c.innerHTML = '';
        for(let i=0; i<6; i++) {
            const cc = BLOCKS[currentEditBlock].start + i;
            createKnob(c, cc, stateKnobs[cc] || 64, `Param ${i+1}`);
        }
    }

    function renderGlobal() {
        const c = document.getElementById('globalKnobUI'); c.innerHTML = '';
        Object.entries(GLOBAL_CC).forEach(([name, cc]) => createKnob(c, cc, 64, name));
    }

    function createKnob(container, cc, val, label) {
        const g = document.createElement('div'); g.className = 'knob-group';
        g.style.textAlign = 'center';
        g.innerHTML = `<svg width="70" height="70" viewBox="0 0 100 100" onpointerdown="startDrag(event, ${cc})">
            <circle cx="50" cy="50" r="40" fill="#000" stroke="#222" stroke-width="3"/>
            <path id="arc-${cc}" d="M 25 80 A 35 35 0 1 1 75 80" fill="none" stroke="${stateKnobs[cc] ? '#D4AF37' : '#555'}" stroke-width="8" stroke-dasharray="180" stroke-dashoffset="${180-(val*1.4)}"/>
        </svg><div style="font-size:14px; margin-top:5px;" id="txt-${cc}">${val}</div><div style="font-size:9px; color:#666; text-transform:uppercase;">${label}</div>`;
        container.appendChild(g);
    }

    function sendCC(cc, v) { if(midiOut) midiOut.send([0xB0, cc, v]); }

    function startDrag(e, cc) {
        const sY = e.clientY || e.touches[0].clientY;
        const sV = stateKnobs[cc] || 64;
        const move = (m) => {
            const mY = m.clientY || (m.touches ? m.touches[0].clientY : 0);
            let nV = Math.max(0, Math.min(127, sV + Math.floor((sY - mY)/1.2)));
            stateKnobs[cc] = nV;
            document.getElementById(`arc-${cc}`).style.strokeDashoffset = 180-(nV*1.4);
            document.getElementById(`txt-${cc}`).innerText = nV;
            sendCC(cc, nV);
        };
        const stop = () => { window.removeEventListener('mousemove', move); window.removeEventListener('touchmove', move); };
        window.addEventListener('mousemove', move); window.addEventListener('touchmove', move, {passive:false});
        window.addEventListener('mouseup', stop); window.addEventListener('touchend', stop);
    }

    function switchTab(t) {
        document.getElementById('editView').style.display = t==='edit'?'block':'none';
        document.getElementById('globalView').style.display = t==='global'?'block':'none';
        document.getElementById('t-edit').className = 'tab-btn' + (t==='edit'?' active':'');
        document.getElementById('t-global').className = 'tab-btn' + (t==='global'?' active':'');
    }
</script>
</body>
</html>
