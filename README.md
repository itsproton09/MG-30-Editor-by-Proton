<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>NUX MG-30 Advanced Detective</title>
    <style>
        body { font-family: monospace; background: #222; color: #0f0; padding: 20px; }
        button { font-size: 20px; padding: 10px 20px; cursor: pointer; background: #444; color: white; border: 1px solid #666; margin: 5px; }
        button:hover { background: #666; }
        #console { margin-top: 20px; border: 1px solid #444; padding: 10px; height: 300px; overflow-y: scroll; background: #111; white-space: pre-wrap; }
        .highlight { color: yellow; font-weight: bold; }
        .tx { color: cyan; }
    </style>
</head>
<body>

    <h1>NUX MG-30 Deep Detective</h1>
    <p>1. Connect & Click 'Connect'.<br>2. Click 'Send Handshake'.<br>3. Turn the GAIN knob on the Amp.</p>
    
    <button onclick="connect()">🔌 1. Connect MIDI</button>
    <button onclick="sendHandshake()">👋 2. Send Handshake</button>
    <br><br>
    
    <div id="console">Waiting...</div>

    <script>
        let midiAccess = null;
        let outputPort = null;
        const logDiv = document.getElementById('console');

        function log(msg, type = '') {
            const div = document.createElement('div');
            div.innerText = msg;
            if(type === 'rx') div.className = 'highlight';
            if(type === 'tx') div.className = 'tx';
            logDiv.prepend(div);
        }

        async function connect() {
            try {
                midiAccess = await navigator.requestMIDIAccess({ sysex: true });
                const inputs = midiAccess.inputs.values();
                const outputs = midiAccess.outputs.values();
                
                let found = false;

                for (let input of inputs) {
                    if(input.name.includes("MG-30")) {
                        log(`Found Input: ${input.name}`);
                        input.onmidimessage = handleMessage;
                        found = true;
                    }
                }
                for (let output of outputs) {
                    if(output.name.includes("MG-30")) {
                        log(`Found Output: ${output.name}`);
                        outputPort = output;
                    }
                }
                
                if(found) log("✅ Connected! Now click 'Send Handshake'.");
                else log("❌ MG-30 not found. Check USB.");
                
            } catch (err) {
                log("❌ Error: " + err);
            }
        }

        function sendHandshake() {
            if(!outputPort) return log("❌ No Output connected");
            
            // Standard "Universal Identity Request"
            // This asks any MIDI device "Who are you?"
            const msg = [0xF0, 0x7E, 0x7F, 0x06, 0x01, 0xF7];
            outputPort.send(msg);
            log(`Tx: F0 7E 7F 06 01 F7 (Who are you?)`, 'tx');
        }

        function handleMessage(event) {
            const data = event.data;
            if (data[0] === 0xFE || data[0] === 0xF8) return; // Ignore clock

            const hexArray = Array.from(data, byte => 
                ('0' + (byte & 0xFF).toString(16)).toUpperCase().slice(-2)
            );
            
            const isSysex = data[0] === 0xF0;
            const prefix = isSysex ? "URGENT [SysEx]: " : "Message: ";
            
            log(prefix + hexArray.join(" "), isSysex ? 'rx' : '');
        }
    </script>
</body>
</html>
