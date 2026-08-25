



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
            background: #121212;
            padding: 20px;
            line-height: 1.6;
            color: #e0e0e0;
        }

        .notepad-container {
            max-width: 850px;
            margin: 0 auto;
            background: #1e1e1e;
            border: 3px solid #0059b3;
            border-radius: 8px;
            box-shadow: 0 4px 20px rgba(0, 89, 179, 0.25);
            overflow: hidden;
        }

        .met-header {
            background: linear-gradient(135deg, #00264d 0%, #003366 100%);
            color: #ffffff;
            padding: 18px 25px;
            border-bottom: 2px solid #0059b3;
        }

        .met-header h1 {
            font-size: 22px;
            letter-spacing: 0.5px;
            margin-bottom: 8px;
        }

        .met-header p {
            font-size: 14px;
            opacity: 0.8;
            color: #b3d9ff;
        }

        .profile-selector {
            background: #2a2a2a;
            padding: 12px 25px;
            border-bottom: 2px solid #333;
            display: flex;
            gap: 15px;
            align-items: center;
        }

        .profile-selector label {
            font-weight: 600;
            color: #99ccff;
        }

        select {
            padding: 6px 12px;
            font-size: 15px;
            border-radius: 4px;
            border: 2px solid #0059b3;
            background: #1e1e1e;
            color: #fff;
            cursor: pointer;
        }

        select:focus {
            outline: none;
            border-color: #0099ff;
        }

        .info-bar {
            background: #262626;
            padding: 12px 25px;
            border-bottom: 2px solid #333333;
            display: flex;
            flex-wrap: wrap;
            gap: 25px;
            font-size: 15px;
            font-weight: 500;
        }

        .info-bar span {
            color: #99ccff;
        }

        .notepad-body {
            padding: 20px 25px;
            background: #1a1a1a;
        }

        textarea {
            width: 100%;
            min-height: 320px;
            padding: 15px;
            font-size: 16px;
            line-height: 28px;
            border: 2px solid #334455;
            border-radius: 4px;
            resize: vertical;
            background: #242424;
            color: #e6e6e6;
            background-image: linear-gradient(#2f353a 1px, transparent 1px);
            background-size: 100% 28px;
        }

        textarea:focus {
            outline: none;
            border-color: #0066cc;
            box-shadow: 0 0 0 2px rgba(0, 102, 204, 0.2);
        }

        textarea::placeholder {
            color: #70757a;
        }

        .button-area {
            padding: 15px 25px 25px;
            text-align: right;
            background: #1e1e1e;
            border-top: 1px solid #333333;
        }

        button {
            background: linear-gradient(135deg, #004c99 0%, #0066cc 100%);
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 16px;
            font-weight: 600;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.25s ease;
        }

        button:hover {
            background: linear-gradient(135deg, #0059b3 0%, #0077e6 100%);
            box-shadow: 0 3px 10px rgba(0, 102, 204, 0.3);
        }

        button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        .status {
            margin-top: 12px;
            font-size: 14px;
            font-weight: 500;
            height: 22px;
        }

        .success { color: #66ff99; }
        .error   { color: #ff6666; }
    </style>
</head>
<body>

    <div class="notepad-container">
        <!-- Header -->
        <div class="met-header">
            <h1>METROPOLITAN POLICE SERVICE — OFFICIAL NOTEPAD</h1>
            <p>Police Station / Duty Log — Authorised Use Only</p>
        </div>

        <!-- Profile Selector -->
        <div class="profile-selector">
            <label>Select Officer:</label>
            <select id="profileSelect">
                <option value="kirsten">Kirsten McCaskill — 2140SN</option>
                <option value="roxie">Roxie Mae — 43BX</option>
            </select>
        </div>

        <!-- Fixed Info Bar -->
        <div class="info-bar">
            <span>Officer Name: <span id="officerName">Kirsten McCaskill</span></span>
            <span>Collar Number: <span id="collarNumber">2140SN</span></span>
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
        
        // Officer Profiles
        const profiles = {
            kirsten: { name: "Kirsten McCaskill", collar: "2140SN" },
            roxie:   { name: "Roxie Mae",        collar: "43BX" }
        };
        // ========================

        const profileSelect = document.getElementById('profileSelect');
        const nameDisplay = document.getElementById('officerName');
        const collarDisplay = document.getElementById('collarNumber');
        const timeDisplay = document.getElementById('current-time');
        const notesArea = document.getElementById('notes');
        const signBtn = document.getElementById('signBtn');
        const statusBox = document.getElementById('status');

        // Switch profile when selected
        profileSelect.addEventListener('change', () => {
            const profile = profiles[profileSelect.value];
            nameDisplay.textContent = profile.name;
            collarDisplay.textContent = profile.collar;
        });

        // Update UK time every second
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
            const profile = profiles[profileSelect.value];
            const timestamp = new Date().toLocaleString('en-GB', { timeZone: 'Europe/London' });

            if (!notes) {
                statusBox.textContent = "⚠️ Please write something before signing.";
                statusBox.className = "status error";
                return;
            }

            const payload = {
                embeds: [{
                    title: "📝 Officer Signed Notepad Entry",
                    color: 32896,
                    fields: [
                        { name: "Officer Name", value: profile.name, inline: true },
                        { name: "Collar Number", value: profile.collar, inline: true },
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
                    notesArea.value = "";
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
