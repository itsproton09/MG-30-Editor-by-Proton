<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>NUX MG-30 MASTER EDITOR</title>
    <style>
        /* --- 1. PROTON THEME --- */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;900&family=JetBrains+Mono:wght@500;700&display=swap');

        :root { --bg:#050505; --panel:#111; --gold:#D4AF37; --text:#eee; --border:1px solid #333; --accent:#00E676; }
        
        * { box-sizing:border-box; -webkit-tap-highlight-color:transparent; user-select:none; }
        
        body { background:var(--bg); color:var(--text); font-family:'Inter',sans-serif; margin:0; height:100vh; display:flex; flex-direction:column; overflow:hidden; }

        /* STARTUP SCREEN */
        #startup { position:fixed; inset:0; background:#000; z-index:10000; display:flex; flex-direction:column; justify-content:center; align-items:center; text-align:center; padding:20px; }
        .start-btn { background:var(--gold); color:#000; border:none; padding:15px 40px; font-size:18px; font-weight:900; border-radius:50px; cursor:pointer; box-shadow:0 0 20px rgba(212,175,55,0.4); margin-top:20px; }
        
        /* HEADER */
        header { height:60px; background:#0c0c0c; border-bottom:var(--border); display:flex; justify-content:space-between; align-items:center; padding:0 20px; z-index:100; }
        .logo { font-weight:900; letter-spacing:1px; color:#fff; font-size:16px; } .logo span { color:var(--gold); }
        .status-led { width:12px; height:12px; background:#333; border-radius:50%; box-shadow:0 0 5px #000; transition:0.3s; }
        .status-led.on { background:var(--accent); box-shadow:0 0 15px var(--accent); }

        /* LCD RIG AREA */
        .rig-section { padding:15px; background:#0f0f0f; border-bottom:var(--border); display:flex; flex-direction:column; align-items:center; gap:10px; }
        .lcd-screen { width:320px; height:130px; background:#000; border:2px solid #222; border-radius:6px; display:flex; flex-direction:column; position:relative; overflow:hidden; box-shadow:0 10px 40px rgba(0,0,0,0.6); }
        .lcd-header { height:24px; background:#1a1a1a; display:flex; justify-content:space-between; padding:0 12px; align-items:center; font-family:'JetBrains Mono'; font-size:10px; color:#666; }
        .lcd-body { flex:1; display:flex; flex-direction:column; justify-content:center; align-items:center; background:radial-gradient(circle at 50% 120%, #151515 0%, #000 90%); }
        .patch-id { font-family:'JetBrains Mono'; font-size:3.5rem; font-weight:700; color:var(--gold); line-height:1; }
        .patch-lbl { font-family:'Inter'; font-size:1.1rem; letter-spacing:2px; color:#fff; margin-top:5px; text-transform:uppercase; font-weight:700; text-shadow:0 0 10px rgba(255,255,255,0.2); }

        .nav-bar { display:flex; gap:12px; width:320px; justify-content:space-between; }
        .nav-btn { background:#1a1a1a; border:1px solid #333; color:#888; width:60px; height:36px; border-radius:4px; cursor:pointer; transition:0.2s; font-weight:700; font-size:18px; display:grid; place-items:center; }
        .nav-btn:hover { background:#222; color:#fff; border-color:#555; } .nav-btn:active { background:var(--gold); color:#000; }

        /* CHAIN STRIP (DRAGGABLE) */
        .chain-strip { height:80px; background:#141414; border-bottom:var(--border); display:flex; align-items:center; padding:0 20px; gap:6px; overflow-x:auto; touch-action:pan-x; }
        .chain-strip::-webkit-scrollbar { display:none; }
        .pedal { min-width:60px; height:45px; background:#1a1a1a; border:1px solid #2a2a2a; border-radius:4px; display:flex; justify-content:center; align-items:center; font-size:10px; font-weight:700; color:#666; cursor:grab; position:relative; }
        .pedal:active { cursor:grabbing; }
        .pedal.selected { border:1px solid #fff; background:#222; color:#fff; }
        .pedal.on { border-color:rgba(212, 175, 55, 0.5); color:var(--gold); background:linear-gradient(180deg, #221e0f, #111); }
        .pedal.dragging { opacity:0.5; border:2px dashed #666; }

        /* KNOB STAGE */
        .stage { flex:1; background:#0a0a0a; padding:30px; display:flex; flex-direction:column; align-items:center; overflow-y:auto; touch-action:pan-y; }
        .model-list { margin-bottom:30px; padding:10px 20px; background:#111; color:var(--gold); border:1px solid #333; border-radius:6px; font-family:'JetBrains Mono'; font-size:13px; outline:none; cursor:pointer; min-width: 250px; text-align:center; }
        .knob-grid { display:grid; grid-template-columns:repeat(auto-fit, minmax(80px, 1fr)); gap:25px; width:100%; max-width:800px; }
        .knob-wrapper { display:flex; flex-direction:column; align-items:center; gap:8px; }
        
        /* SVG KNOB */
        svg.knob { width:70px; height:70px; cursor:ns-resize; touch-action:none; filter:drop-shadow(0 5px 10px rgba(0,0,0,0.5)); pointer-events:auto; }
        .k-track { fill:none; stroke:#1a1a1a; stroke-width:8; stroke-linecap:round; pointer-events:none; }
        .k-val { fill:none; stroke:var(--gold); stroke-width:8; stroke-linecap:round; pointer-events:none; }
        .k-ptr { stroke:#fff; stroke-width:4; stroke-linecap:round; pointer-events:none; }
        .k-num { font-family:'JetBrains Mono'; font-size:14px; color:var(--gold); font-weight:700; }
        .k-name { font-size:10px; font-weight:700; color:#666; text-transform:uppercase; }

        /* FOOTER */
        footer { height:65px; background:#0c0c0c; border-top:var(--border); display:flex; gap:10px; padding:0 20px; align-items:center; z-index:200; }
        .btn { flex:1; height:40px; background:#1a1a1a; border:1px solid #333; color:#aaa; font-size:11px; font-weight:700; border-radius:6px; cursor:pointer; text-transform:uppercase; transition:0.2s; }
        .btn:hover { background:#222; color:#fff; border-color:#555; }
        .btn-green { background:rgba(0,230,118,0.05); border-color:rgba(0,230,118,0.2); color:#00E676; }
        .btn-green:hover { background:rgba(0,230,118,0.15); box-shadow:0 0 20px rgba(0,230,118,0.1); }

        /* MODAL */
        .overlay { position:fixed; inset:0; background:rgba(0,0,0,0.95); z-index:999; display:none; justify-content:center; align-items:center; backdrop-filter:blur(5px); }
        .modal-box { width:700px; max-width:95%; background:#111; border:1px solid #333; border-top:4px solid var(--gold); padding:20px; border-radius:8px; display:flex; flex-direction:column; height:80vh; }
        .slot-grid { display:grid; grid-template-columns:repeat(4, 1fr); gap:6px; flex:1; overflow-y:auto; touch-action:pan-y; }
        .slot { background:#1a1a1a; padding:10px; text-align:center; border:1px solid #2a2a2a; color:#666; font-family:'JetBrains Mono'; font-size:11px; cursor:pointer; display:flex; flex-direction:column; gap:4px; }
        .slot:hover { background:#252525; color:#fff; border-color:#666; }
        .slot.active { background:var(--gold); color:#000; font-weight:bold; }
        .btn-scan { background:var(--gold); color:#000; border:none; padding:8px 20px; font-weight:800; cursor:pointer; font-size:11px; border-radius:4px; }
    </style>
</head>
<body>

    <div id="startup">
        <h1 style="color:#fff; margin-bottom:10px;">NUX MG-30 EDITOR</h1>
        <p style="color:#888;">Please use Google Chrome on Desktop or Android.</p>
        <button class="start-btn" onclick="startApp()">START EDITOR</button>
    </div>

    <div id="modal" class="overlay">
        <div class="modal-box">
            <div style="display:flex; justify-content:space-between; margin-bottom:15px;">
                <h3 style="color:#fff; margin:0;">PATCH LIST</h3>
                <button class="btn-scan" onclick="scanNames()">SCAN NAMES</button>
            </div>
            <div class="slot-grid" id="grid"></div>
            <button class="btn" style="margin-top:15px;" onclick="closeModal()">CLOSE</button>
        </div>
    </div>

    <header>
        <div class="logo">NUX <span>MASTER</span></div>
        <div class="status-led" id="led"></div>
    </header>

    <div class="rig-section">
        <div class="lcd-screen">
            <div class="lcd-header"><span>USB: <b id="usb-state">--</b></span><span>BPM 120</span></div>
            <div class="lcd-body">
                <div class="patch-id" id="lcd-num">--</div>
                <div class="patch-lbl" id="lcd-name">OFFLINE</div>
            </div>
        </div>
        <div class="nav-bar">
            <button class="nav-btn" onclick="nav(-1)">◀</button>
            <button class="nav-btn" onclick="nav(1)">▶</button>
        </div>
    </div>

    <div class="chain-strip" id="chainContainer"></div>

    <div class="stage">
        <select class="model-list" id="models" onchange="userSelectModel()"></select>
        <div class="knob-grid" id="knobs"></div>
    </div>

    <footer>
        <button class="btn" onclick="openModal()">Patch Manager</button>
        <button class="btn" onclick="doExport()">Export Patch</button>
        <button class="btn btn-green" id="btn-connect" onclick="initUSB()">CONNECT USB</button>
    </footer>

    <input type="file" id="fileInput" hidden onchange="fileHandler(this)">

    <script>
        // 1. DATA (v5.0.2)
        const DB = {
            'WAH': { 'Clyde':[{n:'POS',cc:10}], 'Cry BB':[{n:'POS',cc:10}], 'V847':[{n:'POS',cc:10}], 'Horse Wah':[{n:'POS',cc:10}], 'Octave Shift':[{n:'POS',cc:10}] },
            'CMP': { 'Rose':[{n:'SUS',cc:15},{n:'LVL',cc:16}], 'K Comp':[{n:'SUS',cc:15},{n:'LVL',cc:16},{n:'CLP',cc:17}], 'Studio':[{n:'THR',cc:15},{n:'RAT',cc:16},{n:'GN',cc:17},{n:'ATT',cc:18}], 'Blue':[{n:'SUS',cc:15},{n:'LVL',cc:16}] },
            'NR': { 'Noise Reduc':[{n:'THR',cc:40},{n:'DEC',cc:41}] },
            'EFX': { 'Dist+':[{n:'DST',cc:20},{n:'OUT',cc:22}], 'RC Boost':[{n:'GN',cc:20},{n:'VOL',cc:21},{n:'BAS',cc:22},{n:'TRB',cc:23}], 'AC Boost':[{n:'GN',cc:20},{n:'VOL',cc:21},{n:'BAS',cc:22},{n:'TRB',cc:23}], 'Dist One':[{n:'DST',cc:20},{n:'TON',cc:21},{n:'LVL',cc:22}], 'T Scream':[{n:'DRV',cc:20},{n:'TON',cc:21},{n:'LVL',cc:22}], 'Blues Drv':[{n:'GN',cc:20},{n:'TON',cc:21},{n:'LVL',cc:22}], 'Morning Drv':[{n:'VOL',cc:20},{n:'DRV',cc:21},{n:'TON',cc:22}], 'Red Dirt':[{n:'DRV',cc:20},{n:'TON',cc:21},{n:'LVL',cc:22}], 'Crunch':[{n:'GN',cc:20},{n:'VOL',cc:21},{n:'PRS',cc:22}], 'Muff Fuzz':[{n:'SUS',cc:20},{n:'TON',cc:21},{n:'LVL',cc:22}], 'Katana':[{n:'BST',cc:20},{n:'VOL',cc:21}], 'Red Fuzz':[{n:'FUZZ',cc:20},{n:'LVL',cc:21}], 'Touch Wah':[{n:'SENS',cc:20},{n:'RES',cc:21},{n:'DEC',cc:22}] },
            'AMP': { 'Jazz':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}], 'Recto':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}], 'Diezel':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'DEP',cc:36}], 'AC30':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'CUT',cc:33},{n:'TRB',cc:34}], 'Plexi':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}], 'Friedman':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}], 'Dr. Z':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'CUT',cc:33},{n:'TRB',cc:34}], 'Dual Rect':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'PRS',cc:35}], 'AGL':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}], 'MLD':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}], 'Starlift':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}], 'Vibro King':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34}], 'Budda':[{n:'GN',cc:30},{n:'MST',cc:31},{n:'BAS',cc:32},{n:'MID',cc:33},{n:'TRB',cc:34},{n:'CUT',cc:35}] },
            'EQ': { '6-Band':[{n:'100',cc:45},{n:'200',cc:46},{n:'400',cc:47},{n:'800',cc:48},{n:'1.6K',cc:49},{n:'3.2K',cc:50}], 'Align':[{n:'GN',cc:45},{n:'FREQ',cc:46},{n:'Q',cc:47}], '10-Band':[{n:'31',cc:45},{n:'63',cc:46},{n:'125',cc:47},{n:'250',cc:48},{n:'500',cc:49},{n:'1K',cc:50},{n:'2K',cc:51},{n:'4K',cc:52},{n:'8K',cc:53},{n:'16K',cc:54}], 'Para':[{n:'FREQ',cc:45},{n:'GN',cc:46},{n:'Q',cc:47}] },
            'MOD': { 'CE-1':[{n:'CHO',cc:60},{n:'VIB',cc:61}], 'CE-2':[{n:'RT',cc:60},{n:'DP',cc:61}], 'ST Chorus':[{n:'RT',cc:60},{n:'DP',cc:61},{n:'LVL',cc:62}], 'Tremolo':[{n:'RT',cc:60},{n:'DP',cc:61},{n:'LVL',cc:62}], 'Vibe':[{n:'RT',cc:60},{n:'DP',cc:61},{n:'MOD',cc:62}], 'Detune':[{n:'MIX',cc:60},{n:'PIT',cc:61},{n:'LVL',cc:62}], 'Flanger':[{n:'RT',cc:60},{n:'DP',cc:61},{n:'FB',cc:62},{n:'MIX',cc:63}], 'Phase 90':[{n:'SPD',cc:60}], 'Phase 100':[{n:'SPD',cc:60},{n:'INT',cc:61}], 'Harmonist':[{n:'MST',cc:60},{n:'KEY',cc:61},{n:'INT',cc:62}], 'Rotary':[{n:'SPD',cc:60},{n:'BAL',cc:61}] },
            'DLY': { 'Analog':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Digital':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Tape':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Reverse':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Pan':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Duotime':[{n:'TM1',cc:70},{n:'TM2',cc:71},{n:'FB',cc:72},{n:'MX',cc:73}], 'Phi':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}], 'Modulation':[{n:'TM',cc:70},{n:'FB',cc:71},{n:'MX',cc:72}] },
            'RVB': { 'Room':[{n:'DEC',cc:80},{n:'MX',cc:82}], 'Hall':[{n:'DEC',cc:80},{n:'MX',cc:82}], 'Plate':[{n:'DEC',cc:80},{n:'MX',cc:82}], 'Spring':[{n:'DEC',cc:80},{n:'MX',cc:82}], 'Shimmer':[{n:'DEC',cc:80},{n:'MX',cc:82},{n:'PIT',cc:83}] },
            'IR': { 'Cab':[{n:'LC',cc:91},{n:'HC',cc:92},{n:'LV',cc:93}] }
        };

        const BLOCK_IDS = { 'WAH':1, 'CMP':2, 'EFX':3, 'AMP':4, 'NR':5, 'EQ':6, 'MOD':7, 'DLY':8, 'RVB':9, 'IR':10 };

        // 2. STATE
        let midiOut=null, curPatch=0, actBlock='AMP', selSlot=0, scanMode=false, scanIdx=0;
        let chainOrder = ['WAH','CMP','EFX','AMP','NR','EQ','MOD','DLY','RVB','IR'];
        let modelMemory = {}; 

        // 3. START
        function startApp() {
            document.getElementById('startup').style.display='none';
            renderChain();
            setBlock('AMP');
            makeGrid();
        }

        // 4. CHAIN (DRAG)
        function renderChain() {
            const r = document.getElementById('chainContainer');
            r.innerHTML='';
            chainOrder.forEach((b,i)=>{
                let d = document.createElement('div');
                d.className = `pedal ${b===actBlock?'selected':''}`;
                d.innerText=b; d.draggable=true;
                d.onclick=()=>setBlock(b);
                d.addEventListener('dragstart',(e)=>{e.dataTransfer.setData('text/plain',i);d.classList.add('dragging');});
                d.addEventListener('dragend',()=>{d.classList.remove('dragging');});
                d.addEventListener('dragover',(e)=>e.preventDefault());
                d.addEventListener('drop',(e)=>{
                    e.preventDefault();
                    let from=parseInt(e.dataTransfer.getData('text/plain'));
                    let item=chainOrder.splice(from,1)[0];
                    chainOrder.splice(i,0,item);
                    renderChain();
                });
                r.appendChild(d);
            });
        }

        // 5. BLOCKS & KNOBS
        function setBlock(b) {
            actBlock=b; renderChain();
            const s=document.getElementById('models'); s.innerHTML='';
            let list=Object.keys(DB[b]||{});
            list.forEach(m=>s.appendChild(new Option(m,m)));
            
            let key = `${curPatch}_${b}`;
            if(modelMemory[key] && list.includes(modelMemory[key])) s.value=modelMemory[key];
            
            drawKnobs();
        }

        function userSelectModel() {
            let v = document.getElementById('models').value;
            modelMemory[`${curPatch}_${actBlock}`] = v;
            drawKnobs();
        }

        function drawKnobs() {
            const div=document.getElementById('knobs'); div.innerHTML='';
            let mod=document.getElementById('models').value;
            let p=DB[actBlock][mod]||[];
            p.forEach(k=>{
                let box=document.createElement('div');
                box.className='knob-wrapper';
                box.innerHTML=`
                    <svg class="knob" viewBox="0 0 100 100" onpointerdown="dragKnob(event, ${k.cc})">
                        <circle cx="50" cy="50" r="40" class="k-track"/>
                        <circle cx="50" cy="50" r="40" class="k-val" id="arc-${k.cc}" stroke-dasharray="200" stroke-dashoffset="100" transform="rotate(135 50 50)"/>
                        <line x1="50" y1="50" x2="50" y2="10" class="k-ptr" id="ptr-${k.cc}" transform="rotate(0 50 50)"/>
                    </svg>
                    <div class="k-num" id="val-${k.cc}">50</div>
                    <div class="k-name">${k.n}</div>
                `;
                div.appendChild(box);
            });
        }

        // 6. KNOB LOGIC (FOLLOW FOCUS)
        let isDrag=false, dy=0, dval=50, dcc=0;
        function dragKnob(e,cc) {
            e.preventDefault(); e.target.setPointerCapture(e.pointerId);
            isDrag=true; dy=e.clientY; dcc=cc;
            dval=parseInt(document.getElementById('val-'+cc).innerText);
            
            // FOLLOW FOCUS
            if(midiOut && BLOCK_IDS[actBlock]) midiOut.send([0xB0, 49, BLOCK_IDS[actBlock]]);

            e.target.onpointermove=(ev)=>{
                if(!isDrag)return;
                let diff=dy-ev.clientY;
                dval=Math.max(0, Math.min(100, dval+diff));
                let pct=dval/100;
                document.getElementById('arc-'+dcc).style.strokeDashoffset=200-(pct*200);
                document.getElementById('ptr-'+dcc).setAttribute('transform',`rotate(${-135+(pct*270)} 50 50)`);
                document.getElementById('val-'+dcc).innerText=dval;
                if(midiOut) midiOut.send([0xB0, dcc, Math.floor(dval*1.27)]);
                dy=ev.clientY;
            };
            e.target.onpointerup=(ev)=>{isDrag=false;e.target.onpointermove=null;e.target.releasePointerCapture(ev.pointerId);};
        }

        // 7. MIDI
        function initUSB() {
            if(!navigator.requestMIDIAccess) return alert("Use Chrome!");
            navigator.requestMIDIAccess({sysex:true}).then(m=>{
                let out=Array.from(m.outputs.values())[0];
                let inp=Array.from(m.inputs.values())[0];
                if(out&&inp) {
                    midiOut=out; inp.onmidimessage=onMsg;
                    document.getElementById('led').classList.add('on');
                    document.getElementById('lcd-name').innerText="CONNECTED";
                    document.getElementById('usb-state').innerText="OK";
                    document.getElementById('usb-state').style.color="#00E676";
                    midiOut.send([0xC0, curPatch]); setTimeout(dump,100);
                } else alert("Not Found");
            });
        }
        function dump() { if(midiOut) midiOut.send([0xF0,0x00,0x00,0x4F,0x11,0xF7]); }

        function onMsg(e) {
            let d=e.data;
            if((d[0]&0xF0)===0xC0) { curPatch=d[1]; updLCD(); setTimeout(dump,50); }
            if(d[0]===0xF0 && d.length>20) {
                if(scanMode) {
                    let n=parseName(d);
                    document.getElementById('s-'+scanIdx).innerHTML=`<strong>${(scanIdx+1)+getSub(scanIdx)}</strong><br>${n}`;
                    scanIdx++; scanNext();
                } else {
                    document.getElementById('lcd-name').innerText=parseName(d);
                    setBlock(actBlock); // REFRESH KNOBS FOR NEW PATCH
                }
            }
        }

        function parseName(d) {
            let s=""; let f=false;
            for(let i=10;i<d.length-5;i++) {
                if(d[i]>=32&&d[i]<=126) { s+=String.fromCharCode(d[i]); f=true; } else if(f) break;
            }
            return s.length>0?s:"PATCH";
        }

        // 8. HELPERS
        function nav(d) {
            curPatch+=d; if(curPatch>127)curPatch=0; if(curPatch<0)curPatch=127;
            updLCD(); if(midiOut) { midiOut.send([0xC0, curPatch]); setTimeout(dump,100); }
        }
        function updLCD() {
            let b=Math.floor(curPatch/4)+1; let s=['A','B','C','D'][curPatch%4];
            document.getElementById('lcd-num').innerText=(b<10?'0'+b:b)+s;
        }
        function getSub(i) { return ['A','B','C','D'][i%4]; }

        function openModal() { document.getElementById('modal').style.display='flex'; }
        function closeModal() { document.getElementById('modal').style.display='none'; }

        function makeGrid() {
            const g=document.getElementById('grid');
            for(let i=0;i<128;i++) {
                let d=document.createElement('div');
                d.className='slot'; d.id='s-'+i;
                let b=Math.floor(i/4)+1; let s=['A','B','C','D'][i%4];
                d.innerHTML=`<strong>${b+s}</strong><br>---`;
                d.onclick=()=>{selSlot=i;document.getElementById('fileInput').click();};
                g.appendChild(d);
            }
        }

        function fileHandler(inp) {
            let f=inp.files[0]; if(!f)return;
            let r=new FileReader();
            r.onload=(e)=>{
                let dat=new Uint8Array(e.target.result);
                if(midiOut) {
                    if(confirm("Overwrite?")) {
                        midiOut.send([0xC0, selSlot]);
                        setTimeout(()=>{ midiOut.send(dat); curPatch=selSlot; updLCD(); setTimeout(dump,200); },150);
                    }
                } else alert("Connect First");
            };
            r.readAsArrayBuffer(f); closeModal(); inp.value='';
        }

        function doExport() { if(!midiOut)return alert("Connect First"); alert("Check Console"); dump(); }

        function scanNames() {
            if(!midiOut) return alert("Connect First");
            scanMode=true; scanIdx=0; alert("Scanning..."); scanNext();
        }
        function scanNext() {
            if(scanIdx>127) { scanMode=false; midiOut.send([0xC0, curPatch]); return; }
            midiOut.send([0xC0, scanIdx]); setTimeout(dump,150);
        }
    </script>
</body>
</html>
