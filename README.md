<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MG-30 QuickTone Editor</title>
    <style>
        body { 
            font-family: -apple-system, sans-serif; 
            background-color: #121212; 
            color: #ffffff; 
            padding: 20px; 
            text-align: center; 
            margin: 0;
        }
        .container { 
            max-width: 100%; 
            background: #1e1e1e; 
            padding: 20px; 
            border-radius: 12px; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.5); 
        }
        .btn { 
            background-color: #2196f3; 
            color: white; 
            border: none; 
            padding: 18px 20px; /* Large touch targets for mobile */
            margin: 10px 0; 
            width: 100%; 
            border-radius: 8px; 
            font-size: 16px; 
            font-weight: bold; 
            cursor: pointer; 
            transition: background-color 0.2s;
        }
        .btn:active { background-color: #1976d2; }
        .btn:disabled { background-color: #555; color: #888; cursor: not-allowed; }
        .btn-success { background-color: #4CAF50; }
        .btn-success:active { background-color: #388E3C; }
        .btn-warning { background-color: #FF9800; }
        .btn-warning:active { background-color: #F57C00; }
        
        #log { 
            background: #000; 
            color: #00ffcc; 
            padding: 12px; 
            height: 250px; 
            overflow-y: auto; 
            text-align: left; 
            font-family: monospace; 
            font-size: 13px; 
            border-radius: 8px; 
            margin-top: 20px; 
            word-wrap: break-word;
        }
        /* Hidden file input */
        #fileInput { display: none; }
    </style>
</head>
<body>

<div class="container">
    <h2>MG-30 Patch Manager</h2>
    
    <button id="btnConnect" class="btn">🔌 Connect to MG-30</button>
    
    <button id="btnImport" class="btn btn-success" disabled>📥 Import Patch to Pedal</button>
    <button id="btnExport" class="btn btn-warning" disabled>📤 Export Patch to Phone</button>
    
    <input type="file" id="fileInput" accept=".mg30patch">

    <div id="log">System Ready...<br></div>
</div>

<script>
    const logEl = document.getElementById('log');
    const btnConnect = document.getElementById('btnConnect');
    const btnImport = document.getElementById('btnImport');
    const btnExport = document.getElementById('btnExport');
    const fileInput = document.getElementById('fileInput');

    let mg30Device = null;

    function log(msg) {
        logEl.innerHTML += msg + "<br>";
        logEl.scrollTop = logEl.scrollHeight;
    }

    // --- 1. CONNECT & CLAIM INTERFACE ---
    btnConnect.addEventListener('click', async () => {
        try {
            // Filter specifically for the NUX MG-30 based on your previous scan
            mg30Device = await navigator.usb.requestDevice({ 
                filters: [{ vendorId: 0x1FC9, productId: 0x8260 }] 
            });
            
            log("🔄 Opening device...");
            await mg30Device.open();
            
            log("⚙️ Selecting Configuration 1...");
            await mg30Device.selectConfiguration(1);
            
            log("📂 Claiming Interface 4...");
            await mg30Device.claimInterface(4);
            
            log("✅ Connection Successful! Ready for data.");
            
            // Unlock the import/export buttons
            btnConnect.disabled = true;
            btnConnect.innerText = "Connected";
            btnImport.disabled = false;
            btnExport.disabled = false;
            
        } catch (e) {
            log(`❌ Connection Error: ${e.message}`);
        }
    });

    // --- 2. IMPORT FLOW (Phone -> Pedal) ---
    btnImport.addEventListener('click', () => {
        // This simulates a tap on the hidden file input
        fileInput.click(); 
    });

    fileInput.addEventListener('change', async (event) => {
        const file = event.target.files[0];
        if (!file) return;

        log(`📄 Selected file: ${file.name}`);
        
        const reader = new FileReader();
        reader.onload = async function(e) {
            const arrayBuffer = e.target.result;
            const dataView = new Uint8Array(arrayBuffer);
            
            try {
                log(`📤 Sending ${dataView.length} bytes to Pipe 2...`);
                // Endpoint 2 OUT to pedal
                await mg30Device.transferOut(2, dataView); 
                log(`✅ Patch imported successfully!`);
            } catch (err) {
                log(`❌ Transfer failed: ${err.message}`);
            }
        };
        // Read the file as raw binary data
        reader.readAsArrayBuffer(file);
    });

    // --- 3. EXPORT FLOW (Pedal -> Phone) ---
    btnExport.addEventListener('click', async () => {
        try {
            log("📥 Requesting current patch from pedal...");
            
            // NOTE: To make the pedal spit out its current patch, you usually have to 
            // send a specific "SysEx Request Command" first. 
            // Example placeholder (you will need the exact NUX hex code here):
            // const requestCommand = new Uint8Array([0xF0, 0x00, 0x00, 0x00, 0xF7]);
            // await mg30Device.transferOut(2, requestCommand);

            // Listen on Endpoint 2 IN for the response
            // We use a large buffer size (e.g., 64 bytes or higher depending on the patch size)
            log("⏳ Waiting for data...");
            const result = await mg30Device.transferIn(2, 1024); 
            
            if (result.data && result.data.byteLength > 0) {
                log(`✅ Received ${result.data.byteLength} bytes.`);
                
                // Package the data into a downloadable file
                const blob = new Blob([result.data.buffer], { type: "application/octet-stream" });
                const url = window.URL.createObjectURL(blob);
                
                // Create a temporary link to force the Android browser to download it
                const a = document.createElement('a');
                a.style.display = 'none';
                a.href = url;
                a.download = "MyTone.mg30patch";
                document.body.appendChild(a);
                a.click();
                
                window.URL.revokeObjectURL(url);
                log("💾 Patch saved to your phone's downloads folder!");
            } else {
                log("⚠️ No data received from pedal.");
            }
        } catch (err) {
            log(`❌ Export failed: ${err.message}`);
        }
    });
</script>
</body>
</html>
