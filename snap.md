---
layout: page
title: "SNAP"
permalink: /snap
---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Eurostar Ticket Monitor</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px 20px;
            text-align: center;
        }

        .header h1 {
            font-size: 28px;
            margin-bottom: 5px;
        }

        .header p {
            opacity: 0.9;
            font-size: 14px;
        }

        .content {
            padding: 30px 20px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            font-weight: 600;
            margin-bottom: 8px;
            color: #333;
            font-size: 14px;
        }

        .form-group input {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-size: 16px;
            transition: border-color 0.3s;
        }

        .form-group input:focus {
            outline: none;
            border-color: #667eea;
        }

        .url-preview {
            background: #f5f5f5;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            word-break: break-all;
            font-size: 12px;
            color: #0066cc;
            border: 1px solid #e0e0e0;
        }

        .button-group {
            display: flex;
            gap: 10px;
            margin-bottom: 30px;
        }

        button {
            flex: 1;
            padding: 15px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-start {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-start:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .btn-stop {
            background: #ff4757;
            color: white;
        }

        .btn-stop:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(255, 71, 87, 0.4);
        }

        button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .status-card {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 15px;
            text-align: center;
        }

        .status-card.active {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
        }

        .status-card h3 {
            font-size: 14px;
            opacity: 0.9;
            margin-bottom: 5px;
        }

        .status-card p {
            font-size: 20px;
            font-weight: 600;
        }

        .info-card {
            background: #f8f9fa;
            border-left: 4px solid #667eea;
            padding: 15px;
            border-radius: 5px;
            margin-bottom: 15px;
        }

        .info-card h4 {
            color: #667eea;
            margin-bottom: 8px;
            font-size: 14px;
        }

        .info-card p {
            font-size: 13px;
            color: #666;
            line-height: 1.5;
        }

        .log {
            max-height: 200px;
            overflow-y: auto;
            background: #f8f9fa;
            border-radius: 10px;
            padding: 15px;
            font-size: 12px;
            font-family: 'Courier New', monospace;
        }

        .log-entry {
            padding: 5px 0;
            border-bottom: 1px solid #e0e0e0;
        }

        .log-entry:last-child {
            border-bottom: none;
        }

        .log-entry.changed {
            color: #ff4757;
            font-weight: 600;
        }

        @media (max-width: 600px) {
            body {
                padding: 10px;
            }
            
            .header h1 {
                font-size: 24px;
            }
            
            .content {
                padding: 20px 15px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🚄 Eurostar Monitor</h1>
            <p>Check for ticket availability changes</p>
        </div>

        <div class="content">
            <div class="form-group">
                <label>Travel Date (YYYY-MM-DD)</label>
                <input type="text" id="dateInput" value="2025-11-28" placeholder="2025-11-28">
            </div>

            <div class="form-group">
                <label>Number of Adults</label>
                <input type="number" id="adultsInput" value="2" min="1" max="9">
            </div>

            <div class="form-group">
                <label>Origin Station ID (7015400 = London)</label>
                <input type="text" id="originInput" value="7015400" placeholder="7015400">
            </div>

            <div class="form-group">
                <label>Destination Station ID (8727100 = Paris)</label>
                <input type="text" id="destinationInput" value="8727100" placeholder="8727100">
            </div>

            <div class="url-preview" id="urlPreview"></div>

            <div class="button-group">
                <button class="btn-start" id="startBtn">Start Monitoring</button>
                <button class="btn-stop" id="stopBtn" disabled>Stop Monitoring</button>
            </div>

            <div class="status-card" id="statusCard">
                <h3>Status</h3>
                <p id="statusText">Stopped</p>
            </div>

            <div class="info-card">
                <h4>ℹ️ How it works</h4>
                <p>This app checks the Eurostar page every 30 minutes. Keep this tab open or pinned in Firefox. You'll get browser notifications when changes are detected.</p>
            </div>

            <div class="info-card">
                <h4>📝 Activity Log</h4>
                <div class="log" id="logContainer">
                    <div class="log-entry">No checks yet...</div>
                </div>
            </div>
        </div>
    </div>

    <script>
        let monitoringInterval = null;
        let previousHash = null;
        let checkCount = 0;

        const dateInput = document.getElementById('dateInput');
        const adultsInput = document.getElementById('adultsInput');
        const originInput = document.getElementById('originInput');
        const destinationInput = document.getElementById('destinationInput');
        const urlPreview = document.getElementById('urlPreview');
        const startBtn = document.getElementById('startBtn');
        const stopBtn = document.getElementById('stopBtn');
        const statusText = document.getElementById('statusText');
        const statusCard = document.getElementById('statusCard');
        const logContainer = document.getElementById('logContainer');

        // Request notification permission on load
        if ('Notification' in window && Notification.permission === 'default') {
            Notification.requestPermission();
        }

        function updateUrlPreview() {
            const url = buildUrl();
            urlPreview.textContent = url;
        }

        function buildUrl() {
            const date = dateInput.value;
            const adults = adultsInput.value;
            const origin = originInput.value;
            const destination = destinationInput.value;
            return `https://snap.eurostar.com/uk-en/search?adult=${adults}&origin=${origin}&destination=${destination}&outbound=${date}`;
        }

        function addLog(message, isChange = false) {
            const timestamp = new Date().toLocaleTimeString();
            const logEntry = document.createElement('div');
            logEntry.className = 'log-entry' + (isChange ? ' changed' : '');
            logEntry.textContent = `[${timestamp}] ${message}`;
            logContainer.insertBefore(logEntry, logContainer.firstChild);
            
            // Keep only last 20 entries
            while (logContainer.children.length > 20) {
                logContainer.removeChild(logContainer.lastChild);
            }
        }

        function showNotification(title, body) {
            if ('Notification' in window && Notification.permission === 'granted') {
                new Notification(title, {
                    body: body,
                    icon: '🚄',
                    tag: 'eurostar-monitor'
                });
            }
        }

        async function hashString(str) {
            const encoder = new TextEncoder();
            const data = encoder.encode(str);
            const hashBuffer = await crypto.subtle.digest('SHA-256', data);
            const hashArray = Array.from(new Uint8Array(hashBuffer));
            return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
        }

        async function checkPage() {
            const url = buildUrl();
            addLog(`Checking ${url}...`);
            
            try {
                // Use a CORS proxy since we can't directly fetch from snap.eurostar.com
                const proxyUrl = `https://api.allorigins.win/raw?url=${encodeURIComponent(url)}`;
                const response = await fetch(proxyUrl);
                
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const html = await response.text();
                const currentHash = await hashString(html);
                
                checkCount++;
                
                if (previousHash === null) {
                    previousHash = currentHash;
                    addLog(`Initial check complete. Monitoring started. (Check #${checkCount})`);
                    showNotification('Monitoring Started', 'Eurostar ticket monitor is now active');
                } else if (previousHash !== currentHash) {
                    previousHash = currentHash;
                    addLog(`🚨 PAGE CHANGED! Tickets may be available!`, true);
                    showNotification('Eurostar Page Changed!', 'The search results have been updated. Check for tickets!');
                } else {
                    addLog(`No changes detected. (Check #${checkCount})`);
                }
                
                // Save state
                localStorage.setItem('eurostar_hash', currentHash);
                localStorage.setItem('eurostar_check_count', checkCount);
                localStorage.setItem('eurostar_last_check', new Date().toISOString());
                
            } catch (error) {
                addLog(`Error: ${error.message}`);
                console.error('Check failed:', error);
            }
        }

        function startMonitoring() {
            if (Notification.permission === 'default') {
                Notification.requestPermission().then(permission => {
                    if (permission === 'granted') {
                        startMonitoringProcess();
                    } else {
                        alert('Please enable notifications to receive alerts about ticket changes.');
                    }
                });
            } else if (Notification.permission === 'denied') {
                alert('Notifications are blocked. Please enable them in your browser settings to receive alerts.');
                startMonitoringProcess();
            } else {
                startMonitoringProcess();
            }
        }

        function startMonitoringProcess() {
            // Load previous state
            previousHash = localStorage.getItem('eurostar_hash');
            checkCount = parseInt(localStorage.getItem('eurostar_check_count') || '0');
            
            // Save current settings
            localStorage.setItem('eurostar_monitoring', 'true');
            localStorage.setItem('eurostar_date', dateInput.value);
            localStorage.setItem('eurostar_adults', adultsInput.value);
            localStorage.setItem('eurostar_origin', originInput.value);
            localStorage.setItem('eurostar_destination', destinationInput.value);
            
            statusText.textContent = `Monitoring ${dateInput.value}`;
            statusCard.classList.add('active');
            startBtn.disabled = true;
            stopBtn.disabled = false;
            
            // Check immediately
            checkPage();
            
            // Then check every 30 minutes (1800000 ms)
            monitoringInterval = setInterval(checkPage, 1800000);
            
            addLog('Monitoring started - checking every 30 minutes');
        }

        function stopMonitoring() {
            if (monitoringInterval) {
                clearInterval(monitoringInterval);
                monitoringInterval = null;
            }
            
            localStorage.setItem('eurostar_monitoring', 'false');
            
            statusText.textContent = 'Stopped';
            statusCard.classList.remove('active');
            startBtn.disabled = false;
            stopBtn.disabled = true;
            
            addLog('Monitoring stopped');
        }

        // Event listeners
        [dateInput, adultsInput, originInput, destinationInput].forEach(input => {
            input.addEventListener('input', updateUrlPreview);
        });

        startBtn.addEventListener('click', startMonitoring);
        stopBtn.addEventListener('click', stopMonitoring);

        // Initialize
        updateUrlPreview();

        // Restore previous session if monitoring was active
        window.addEventListener('load', () => {
            if (localStorage.getItem('eurostar_monitoring') === 'true') {
                const savedDate = localStorage.getItem('eurostar_date');
                const savedAdults = localStorage.getItem('eurostar_adults');
                const savedOrigin = localStorage.getItem('eurostar_origin');
                const savedDestination = localStorage.getItem('eurostar_destination');
                
                if (savedDate) dateInput.value = savedDate;
                if (savedAdults) adultsInput.value = savedAdults;
                if (savedOrigin) originInput.value = savedOrigin;
                if (savedDestination) destinationInput.value = savedDestination;
                
                updateUrlPreview();
                addLog('Resuming previous monitoring session...');
                startMonitoringProcess();
            }
            
            const lastCheck = localStorage.getItem('eurostar_last_check');
            if (lastCheck) {
                const lastCheckDate = new Date(lastCheck);
                addLog(`Last check was at ${lastCheckDate.toLocaleString()}`);
            }
        });

        // Keep page alive with periodic heartbeat
        setInterval(() => {
            console.log('Page alive:', new Date().toLocaleTimeString());
        }, 60000);
    </script>
</body>
</html>
