<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MG-30 TOTAL SYNC</title>
    <style>
        :root { --gold: #D4AF37; --bg: #000; --lcd: #050505; --green: #00E676; --red: #FF3D00; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #000; color: #eee; font-family: 'Segoe UI', sans-serif; margin: 0; height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* LCD Header */
        .lcd-panel { background: #1a1a1b; padding: 15px; border-bottom: 2px solid #333; }
        .lcd { background: var(--lcd); border: 1px solid #444; border-radius: 6px; padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--gold); }
        .p-num { font-size: 3rem; color: var(--gold); font-family: monospace; font-weight: 900; line-height: 1; }
        .p-name { font-size: 1.2rem; font-weight: bold; color: #fff; text-transform: uppercase; }

        /* Signal Chain */
        .chain-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; padding: 12px; background: #080808; border-bottom: 1px solid #222; }
        .block { height: 50px; background: #1a1a1a; border: 1px solid #333; border-radius: 4px; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 10px; font-weight: 800; color: #777; cursor: pointer; position: relative; }
        
        .led { width: 7px; height: 7px; border-radius: 50%; margin-bottom: 4px; transition: background 0.3s; }
        .led-on { background: var(--green); box-shadow: 0 0 8px var(--green); }
        .led-off { background: var(--red); box-shadow: 0 0 8px var(--red); }
        
        /* Blinking logic for Selected Block */
        .selected .led { animation: blink 1s infinite; }
        @keyframes blink { 0% { opacity: 1; } 50% { opacity: 0.3; } 100% { opacity: 1; } }

        .block.engaged { color: #fff; }
        .block.selected { border: 2px solid var(--gold); background: #222; }

        /* Knobs */
        .knob-workspace { flex: 1; overflow-y: auto; padding: 20px; display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; justify-items: center; align-content: start; }
        .knob-wrapper { text-align: center; width: 85px; }
        .knob-outer { width: 60px; height: 60px; border-radius: 50%; background: radial-gradient(circle, #444 0%, #222 100%); border: 3px solid #333; position: relative; margin: 0 auto 8px auto; touch-action: none; }
        .knob-indicator { position: absolute; width: 4px; height: 15px; background: var(--gold); left: 50%; top: 5px; transform-origin: 50% 25px; transform: translateX(-50%) rotate(-135deg); border-radius: 2px; }
        .knob-label { font-size: 9px; color: #999; font-weight: bold; text-transform: uppercase; margin-bottom: 2px; }
        .knob-value { font-size: 12px; color: var(--gold); font-family: monospace; font-weight: bold; }

        footer { background: #111; padding: 12px; display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; border-top: 1px solid #333; }
        .btn { padding: 15px 5px; background: #333; border: 1px solid #555; color: #FFF; border-radius: 4px; font-size: 10px; font-weight: bold; text-align: center; cursor: pointer; }
        .btn-gold { background: var(--gold); color: #000; border: none; }
    </style>
</head>
<body>

<div class="lcd-panel">
    <div class="lcd">
        <div id="ui-num" class="p-num">--</div>
        <div class="p-meta">
            <div id="ui-name" class="p-name">DISCONNECTED</div>
            <div id="ui-sync-msg" style="font-size:9px; color:var(--gold); font-weight:bold;">READY TO LINK</div>
        </div>
    </div>
</div>

<div class="chain-grid" id="ui-chain"></div>
<div class="knob-workspace" id="ui-params"></div>

<footer>
    <div class="btn btn-gold" onclick="initMIDI()">Link</div>
    <div class="btn" onclick="syncData()">Sync</div>
    <div class="btn" onclick="patchChange(-1)">Prev</div>
    <div class="btn" onclick="patchChange(1)">Next</div>
</footer>

<script>
    let mOut, mIn, pNum = 0, activeBlock = 'AMP';
    const BLOCKS = {
        'WAH': {id:1, cc:89, s:10, p:['Pos','Mix','Vol']},
        'CMP': {id:2, cc:14, s:15, p:['Sust','Lvl','Clip']},
        'GATE':{id:3, cc:39, s:40, p:['Thr','Ran','Rel']},
        'EFX': {id:4, cc:19, s:20, p:['Gain','Lvl','Tone']},
        'AMP': {id:5, cc:29, s:30, p:['Gain','Mast','Bass','Mid','Tre','Pres']},
        'EQ':  {id:6, cc:44, s:45, p:['100H','400H','1.6K','3.2K']},
        'MOD': {id:7, cc:59, s:60, p:['Rate','Dep','Mix','Tone']},
        'DLY': {id:8, cc:69, s:70, p:['Time','Rpt','Mix','Mod']},
        'RVB': {id:9, cc:79, s:80, p:['Dcy','Mix','Tone','Dmp']},
        'IR':  {id:10,cc:9,  s:90, p:['Lvl','H-Cut','L-Cut']},
        'S/R': {id:11,cc:34, s:35, p:['Snd','Rtn']}
    };

    let bypassMap = {}; 
    let midiValues = {};

    async function initMIDI() {
        try {
            const access = await navigator.requestMIDIAccess({ sysex: true });
            mOut = Array.from(access.outputs.values()).find(o => /MG-30|NUX/i.test(o.name));
            if (mOut) {
                mIn = Array.from(access.inputs.values()).find(i => i.name === mOut.name);
                mIn.onmidimessage = (m) => handleSysex(m.data);
                document.getElementById('ui-sync-msg').innerText = "LINKED & SYNCING...";
                syncData(); // Instant Auto-Sync on Link
            } else { alert("Hardware not found."); }
        } catch(e) { alert("MIDI Permission denied."); }
    }

    function handleSysex(d) {
        if (d[0] === 0xF0 && d[3] === 0x4F) {
            pNum = d[12];
            document.getElementById('ui-num').innerText = pNum.toString().padStart(2, '0');
            
            let name = "";
            for (let i = 14; i < 26; i++) if (d[i] > 31) name += String.fromCharCode(d[i]);
            document.getElementById('ui-name').innerText = name.trim() || "PATCH";
            
            // Critical: Audits the hardware's bypass states (V5 offset map)
            Object.keys(BLOCKS).forEach(key => {
                const id = BLOCKS[key].id;
                bypassMap[key] = d[32 + (id * 2)] > 0;
            });
            
            document.getElementById('ui-sync-msg').innerText = "HARDWARE SYNCED";
            renderUI();
        }
    }

    function renderUI() {
        const chain = document.getElementById('ui-chain');
        chain.innerHTML = '';
        Object.keys(BLOCKS).forEach(key => {
            const isEngaged = bypassMap[key];
            const div = document.createElement('div');
            div.className = `block ${isEngaged ? 'engaged' : ''} ${activeBlock === key ? 'selected' : ''}`;
            
            const led = document.createElement('div');
            led.className = `led ${isEngaged ? 'led-on' : 'led-off'}`;
            
            div.appendChild(led);
            div.appendChild(Object.assign(document.createElement('span'), {innerText: key}));

            div.onclick = () => {
                if(activeBlock === key) {
                    bypassMap[key] = !bypassMap[key];
                    sendMIDI(49, BLOCKS[key].id);
                    sendMIDI(BLOCKS[key].cc, bypassMap[key] ? 127 : 0);
                }
                activeBlock = key;
                sendMIDI(49, BLOCKS[key].id);
                renderUI();
            };
            chain.appendChild(div);
        });

        const params = document.getElementById('ui-params');
        params.innerHTML = '';
        BLOCKS[activeBlock].p.forEach((label, i) => {
            const cc = BLOCKS[activeBlock].s + i;
            params.appendChild(createKnob(label, cc, midiValues[cc] || 64));
        });
    }

    function createKnob(label, cc, startM) {
        const wrapper = document.createElement('div');
        wrapper.className = 'knob-wrapper';
        const toPct = (m) => Math.round((m / 127) * 100);
        wrapper.innerHTML = `
            <div class="knob-label">${label}</div>
            <div class="knob-outer" id="k-${cc}"><div class="knob-indicator" id="ind-${cc}"></div></div>
            <div class="knob-value"><span id="val-${cc}">${toPct(startM)}</span>%</div>
        `;

        const knob = wrapper.querySelector('.knob-outer');
        let startY, startVal;

        const update = (m) => {
            m = Math.max(0, Math.min(127, m));
            midiValues[cc] = m;
            document.getElementById(`val-${cc}`).innerText = toPct(m);
            const deg = (m / 127) * 270 - 135;
            document.getElementById(`ind-${cc}`).style.transform = `translateX(-50%) rotate(${deg}deg)`;
            sendMIDI(cc, m);
        };

        knob.onpointerdown = e => {
            startY = e.clientY; startVal = midiValues[cc] || 64;
            knob.setPointerCapture(e.pointerId);
            knob.onpointermove = ev => update(startVal + Math.round((startY - ev.clientY) / 1.5));
        };
        knob.onpointerup = () => knob.onpointermove = null;
        update(startM); // Initial set
        return wrapper;
    }

    function sendMIDI(c, v) { if(mOut) mOut.send([0xB0, c, v]); }
    function syncData() { if(mOut) mOut.send([0xF0, 0x00, 0x01, 0x4F, 0x11, 0x01, 0x00, 0x00, 0xF7]); }
    
    function patchChange(dir) {
        pNum = Math.max(0, Math.min(127, pNum + dir));
        if(mOut) mOut.send([0xC0, pNum]);
        document.getElementById('ui-sync-msg').innerText = "LOADING...";
        setTimeout(syncData, 300); // 300ms delay to allow hardware engine to cycle
    }

    renderUI(); // Render dummy UI on load
</script>
</body>
</html>
