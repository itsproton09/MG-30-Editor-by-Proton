<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NUX MG-30 Data Extractor</title>
    <style>
        body { background: #111; color: #0f0; font-family: monospace; padding: 20px; display: flex; flex-direction: column; height: 90vh; }
        h1 { color: #fff; text-align: center; }
        .btn { background: #333; color: white; border: 1px solid #555; padding: 15px; width: 100%; font-size: 1.2rem; margin-bottom: 10px; cursor: pointer; }
        .btn.active { background: #00aa00; }
        .btn.copy { background: #0088ff; border-color: #0055aa; margin-top: auto; }
        #status { text-align: center; margin-bottom: 15px; color: #aaa; }
        #log-container { flex: 1; border: 1px solid #333; background: #000; overflow-y: scroll; padding: 10px; font-size: 0.8rem; white-space: pre-wrap; word-break: break-all; }
        .entry { border-bottom: 1px solid #222; padding: 2px 0; }
        .cc { color: #ffcc00; } /* Control Change (Knobs) */
        .pc { color: #00ccff; } /* Patch Change (Presets) */
        .sysex { color: #ff5555; } /* System Exclusive (Deep Data) */
    </style>
</head>
<body>

    <h1>NUX DATA EXTRACTOR</h1>
    <div id="status">🔴 Disconnected</div>

    <button class="btn" onclick="connect()" id="btn-connect">1. CONNECT USB</button>
    <button class="btn copy" onclick="copyData()">2. COPY DATA FOR AI</button>

    <div style="margin: 10px 0; color: #fff;">
        <strong>INSTRUCTIONS:</strong><br>
        1. Click Connect.<br>
        2. <u>Switch Presets</u> on the pedal (Wait 2 sec).<br>
        3. <u>Turn every Knob</u> you want to use (Gain, Bass, Master, etc).<br>
        4. Click "COPY DATA" and paste it to Gemini.
    </div>

    <div id="log-container">Waiting for input...</div>

    <script>
        let midiAccess = null;
        let capturedData = [];
        const logBox = document.getElementById('log-container');
        const statusEl = document.getElementById('status');

        async function connect() {
            try {
                midiAccess = await navigator.requestMIDIAccess({ sysex: true });
                statusEl.innerText = "🟡 Searching for NUX...";
                
                let found = false;
                for (let input of midiAccess.inputs.values()) {
                    if (input.name.includes("MG-30") || input.name.includes("NUX") || input.name.includes("MIDI")) {
                        input.onmidimessage = handleMessage;
                        found = true;
                        statusEl.innerText = "🟢 CONNECTED: " + input.name;
                        document.getElementById('btn-connect').classList.add('active');
                        document.getElementById('btn-connect').innerText = "LISTENING...";
                        log("✅ Device Found! Now move knobs on the hardware.");
                    }
                }
                if (!found) statusEl.innerText = "❌ NUX MG-30 Not Found (Check OTG)";
            } catch (err) {
                statusEl.innerText = "❌ Error: " + err;
            }
        }

        function handleMessage(event) {
            const data = event.data;
            if (data[0] === 0xFE || data[0] === 0xF8) return; // Ignore clock signals

            // Convert to Hex
            const hex = Array.from(data, byte => ('0' + (byte & 0xFF).toString(16)).toUpperCase().slice(-2)).join(' ');
            
            // Analyze Type
            let type = "UNKNOWN";
            let cssClass = "";
            let description = "";

            if ((data[0] & 0xF0) === 0xB0) {
                type = "CC (Knob)";
                cssClass = "cc";
                description = `Knob ID: ${data[1]} | Val: ${data[2]}`;
            } else if ((data[0] & 0xF0) === 0xC0) {
                type = "PC (Preset)";
                cssClass = "pc";
                description = `Preset: ${data[1] + 1}`;
            } else if (data[0] === 0xF0) {
                type = "SYSEX (Data)";
                cssClass = "sysex";
                description = "Deep Edit Data";
            }

            // Store for AI
            const logEntry = `[${type}] ${hex} -- ${description}`;
            capturedData.push(logEntry);

            // Show on Screen
            const div = document.createElement('div');
            div.className = "entry " + cssClass;
            div.innerText = logEntry;
            logBox.prepend(div);
        }

        function copyData() {
            const textBlob = "--- START NUX DATA ---\n" + capturedData.join('\n') + "\n--- END NUX DATA ---";
            navigator.clipboard.writeText(textBlob).then(() => {
                alert("COPIED! Now paste this into Gemini.");
            }).catch(err => {
                alert("Copy failed. Please manually select the text.");
            });
        }

        function log(msg) {
            const div = document.createElement('div');
            div.innerText = msg;
            div.style.color = "#fff";
            logBox.prepend(div);
        }
    </script>
</body>
</html>
