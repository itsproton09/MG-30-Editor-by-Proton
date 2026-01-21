<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>NUX MG-30 Detective</title>
    <style>
        body { font-family: monospace; background: #222; color: #0f0; padding: 20px; }
        button { font-size: 20px; padding: 10px 20px; cursor: pointer; background: #444; color: white; border: 1px solid #666; }
        #console { margin-top: 20px; border: 1px solid #444; padding: 10px; height: 300px; overflow-y: scroll; background: #111; white-space: pre-wrap; }
        .highlight { color: yellow; font-weight: bold; }
    </style>
</head>
<body>

    <h1>NUX MG-30 Protocol Sniffer</h1>
    <p>1. Connect MG-30 via USB.<br>2. Click Connect.<br>3. Turn a knob on the device.<br>4. Copy the log below.</p>
    
    <button onclick="connect()">🔌 Connect MIDI</button>
    <div id="console">Waiting for connection...</div>

    <script>
        let midiAccess = null;
        const logDiv = document.getElementById('console');

        function log(msg, isHex = false) {
            const div = document.createElement('div');
            div.innerText = msg;
            if(isHex) div.className = 'highlight';
            logDiv.prepend(div);
        }

        async function connect() {
            try {
                midiAccess = await navigator.requestMIDIAccess({ sysex: true });
                log("✅ MIDI Access Granted!");
                
                const inputs = midiAccess.inputs.values();
                for (let input of inputs) {
                    log(`Found Device: ${input.name}`);
                    input.onmidimessage = handleMessage;
                    log(`👂 Listening to ${input.name}...`);
                }
            } catch (err) {
                log("❌ Connection Failed: " + err);
                log("Make sure you are using Chrome/Edge and have granted MIDI permissions.");
            }
        }

        function handleMessage(event) {
            const data = event.data;
            // Filter: Ignore "Active Sensing" (0xFE) and "Clock" (0xF8) spam
            if (data[0] === 0xFE || data[0] === 0xF8) return;

            // Convert to HEX string
            const hexArray = Array.from(data, byte => 
                ('0' + (byte & 0xFF).toString(16)).toUpperCase().slice(-2)
            );
            
            // We specifically look for System Exclusive (F0 ... F7)
            const isSysex = data[0] === 0xF0;
            const prefix = isSysex ? "URGENT [SysEx]: " : "Message: ";
            
            log(prefix + hexArray.join(" "), isSysex);
        }
    </script>
</body>
</html>
