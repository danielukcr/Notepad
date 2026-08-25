



<html lang="en-GB">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Met Police — Official Notepad</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Arial, sans-serif;
        }

        body {
            background: #f0f2f5;
            padding: 20px;
            line-height: 1.6;
        }

        .notepad-container {
            max-width: 850px;
            margin: 0 auto;
            background: #fff;
            border: 3px solid #003366;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
            overflow: hidden;
        }

        .met-header {
            background: linear-gradient(135deg, #003366 0%, #004080 100%);
            color: white;
            padding: 18px 25px;
        }

        .met-header h1 {
            font-size: 22px;
            letter-spacing: 0.5px;
            margin-bottom: 8px;
        }

        .met-header p {
            font-size: 14px;
            opacity: 0.9;
        }

        .info-bar {
            background: #e6edf5;
            padding: 12px 25px;
            border-bottom: 2px solid #ccc;
            display: flex;
            flex-wrap: wrap;
            gap: 25px;
            font-size: 15px;
            font-weight: 500;
        }

        .info-bar span {
            color: #002244;
        }

        .notepad-body {
            padding: 20px 25px;
        }

        textarea {
            width: 100%;
            min-height: 320px;
            padding: 15px;
            font-size: 16px;
            line-height: 1.7;
            border: 2px solid #ccd6e0;
            border-radius: 4px;
            resize: vertical;
            background: #fcfdfd;
            background-image: linear-gradient(#e8edf2 1px, transparent 1px);
            background-size: 100% 28px;
            line-height: 28px;
        }

        textarea:focus {
            outline: none;
            border-color: #003366;
        }

        .button-area {
            padding: 15px 25px 25px;
            text-align: right;
            background: #f8f9fa;
            border-top: 1px solid #ddd;
        }

        button {
            background: #003366;
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 16px;
            font-weight: 600;
            border-radius: 4px;
            cursor: pointer;
            transition: background 0.2s ease;
        }

        button:hover {
            background: #004c99;
        }

        button:active {
            background: #00264d;
        }

        .status {
            margin-top: 12px;
            font-size: 14px;
            font-weight: 500;
            height: 22px;
        }

        .success { color: #006622; }
        .error   { color: #cc0000; }
    </style>
</head>
<body>

    <div class="notepad-container">
        <!-- Header -->
        <div class="met-header">
            <h1>METROPOLITAN POLICE SERVICE — OFFICIAL NOTEPAD</h1>
            <p>Police Station / Duty Log — Authorised Use Only</p>
        </div>

        <!-- Fixed Info Bar -->
        <div class="info-bar">
            <span>Officer Name: Kirsten McCaskill</span>
            <span>Collar Number: 2140SN</span>
            <span id="current-time">Loading Time...</span>
        </div>

        <!-- Notepad Area -->
        <div class="notepad-body">
            <textarea id="notes" placeholder="Enter notes, observations or details here..."></textarea>
        </div>

        <!-- Submit Button -->
        <div class="button-area">
            <button id="signBtn">✓ Sign & Submit to Log</button>
            <div id="status" class="status"></div>
        </div>
    </div>

    <script>
        // ==== CONFIGURATION ====
        const WEBHOOK_URL = "https://discord.com/api/webhooks/1540429827981582406/Zw0QBENUbPQs6mILgTpApERPshZ1ArSO82AQJ6wUkzKgNCkqn-4c1oBOtSxgjn52Xtol";
        const OFFICER_NAME = "Kirsten McCaskill";
        const COLLAR_NUMBER = "2140SN";
        // ========================

        const timeDisplay = document.getElementById('current-time');
        const notesArea = document.getElementById('notes');
        const signBtn = document.getElementById('signBtn');
        const statusBox = document.getElementById('status');

        // Update time every second
        function updateTime() {
            const now = new Date();
            const ukTime = now.toLocaleString('en-GB', {
                timeZone: 'Europe/London',
                day: '2-digit',
                month: '2-digit',
                year: 'numeric',
                hour: '2-digit',
                minute: '2-digit',
                second: '2-digit'
            });
            timeDisplay.textContent = `Time: ${ukTime}`;
        }
        updateTime();
        setInterval(updateTime, 1000);

        // Send to Discord Webhook
        signBtn.addEventListener('click', async () => {
            const notes = notesArea.value.trim();
            const timestamp = new Date().toLocaleString('en-GB', { timeZone: 'Europe/London' });

            if (!notes) {
                statusBox.textContent = "⚠️ Please write something before signing.";
                statusBox.className = "status error";
                return;
            }

            const payload = {
                embeds: [{
                    title: "📝 Officer Signed Notepad Entry",
                    color: 32896, // Green
                    fields: [
                        { name: "Officer Name", value: OFFICER_NAME, inline: true },
                        { name: "Collar Number", value: COLLAR_NUMBER, inline: true },
                        { name: "Date / Time", value: timestamp, inline: false },
                        { name: "Notepad Content", value: `\n${notes}\n` }
                    ],
                    footer: { text: "Met Police — Signed & Submitted Log" }
                }]
            };

            try {
                signBtn.disabled = true;
                signBtn.textContent = "⏳ Sending...";
                statusBox.textContent = "";

                const response = await fetch(WEBHOOK_URL, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(payload)
                });

                if (response.ok) {
                    statusBox.textContent = "✅ Signed & submitted successfully!";
                    statusBox.className = "status success";
                    notesArea.value = ""; // Clear after send
                } else {
                    throw new Error(`Server responded: ${response.status}`);
                }
            } catch (err) {
                statusBox.textContent = `❌ Failed to send: ${err.message}`;
                statusBox.className = "status error";
            } finally {
                signBtn.disabled = false;
                signBtn.textContent = "✓ Sign & Submit to Log";
            }
        });
    </script>
</body>
</html>
