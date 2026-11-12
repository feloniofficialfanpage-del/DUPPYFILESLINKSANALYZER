<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fornit — Media & Creator Deep-Dive</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: #0d0d0f;
            color: #f4f4f5;
            display: flex;
            justify-content: center;
            padding: 16px;
            margin: 0;
        }
        .container {
            background-color: #18181b;
            padding: 22px;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.6);
            width: 100%;
            max-width: 580px;
            border: 1px solid #27272a;
        }
        h1 { margin: 0 0 6px 0; font-size: 24px; text-align: center; }
        p.sub { color: #a1a1aa; font-size: 13px; text-align: center; margin-bottom: 18px; }

        /* Tabs */
        .tabs {
            display: flex;
            gap: 6px;
            margin-bottom: 14px;
        }
        .tab-btn {
            flex: 1;
            padding: 10px 6px;
            background: #27272a;
            border: 1px solid #3f3f46;
            color: #a1a1aa;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            font-size: 12px;
        }
        .tab-btn.active {
            background: #2563eb;
            color: white;
            border-color: #3b82f6;
        }

        /* Search Bar */
        .search-box {
            display: flex;
            gap: 8px;
            margin-bottom: 10px;
        }
        input {
            flex: 1;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid #3f3f46;
            background: #27272a;
            color: white;
            font-size: 14px;
        }
        button.action-btn {
            padding: 12px 18px;
            border-radius: 8px;
            border: none;
            background-color: #2563eb;
            color: white;
            font-weight: bold;
            cursor: pointer;
        }
        button.action-btn:hover { background-color: #1d4ed8; }

        /* Recent History Chips */
        .history-bar {
            display: flex;
            flex-wrap: wrap;
            gap: 6px;
            margin-bottom: 14px;
            align-items: center;
        }
        .history-chip {
            background: #202024;
            color: #a1a1aa;
            border: 1px solid #2e2e32;
            padding: 4px 10px;
            border-radius: 14px;
            font-size: 11px;
            cursor: pointer;
            max-width: 150px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
        .history-chip:hover { color: white; border-color: #3b82f6; }

        /* API Key Box */
        .api-box {
            background: #202024;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 14px;
            font-size: 12px;
            border: 1px solid #2e2e32;
        }
        .api-box input {
            width: 100%;
            box-sizing: border-box;
            margin-top: 6px;
            padding: 8px;
            font-size: 12px;
        }

        /* Cards & Sections */
        .card {
            background: #202024;
            padding: 16px;
            border-radius: 12px;
            margin-top: 14px;
            border: 1px solid #2e2e32;
            display: none;
        }
        .card h3 {
            margin: 0 0 10px 0;
            font-size: 15px;
            color: #e4e4e7;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        #infoResult img {
            width: 100%;
            border-radius: 8px;
            margin-bottom: 12px;
        }
        .meta-item { margin: 6px 0; font-size: 14px; }
        .label { color: #a1a1aa; font-weight: bold; }

        /* Non-AI Research Grid */
        .research-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            margin-top: 10px;
        }
        .res-link {
            display: block;
            text-align: center;
            background: #27272a;
            color: #60a5fa;
            text-decoration: none;
            padding: 10px 8px;
            border-radius: 8px;
            font-size: 12px;
            font-weight: 600;
            border: 1px solid #3f3f46;
        }
        .res-link:hover { background: #3f3f46; color: white; }

        /* One-Tap AI Preset Buttons */
        .preset-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 6px;
            margin-bottom: 12px;
        }
        .preset-btn {
            background: #27272a;
            color: #e4e4e7;
            border: 1px solid #3f3f46;
            padding: 7px 10px;
            border-radius: 20px;
            font-size: 12px;
            cursor: pointer;
        }
        .preset-btn:hover {
            background: #2563eb;
            border-color: #3b82f6;
            color: white;
        }

        /* Chat UI */
        .chat-box {
            max-height: 260px;
            overflow-y: auto;
            background: #121215;
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 10px;
            font-size: 14px;
            line-height: 1.5;
            border: 1px solid #27272a;
        }
        .msg-ai { color: #e4e4e7; margin-bottom: 12px; }
        .msg-user { color: #60a5fa; font-weight: bold; margin-bottom: 6px; }
        .copy-btn {
            background: #27272a;
            color: #a1a1aa;
            border: 1px solid #3f3f46;
            padding: 4px 8px;
            border-radius: 6px;
            font-size: 11px;
            cursor: pointer;
        }
        .copy-btn:hover { color: white; }
    </style>
</head>
<body>

<div class="container">
    <h1>🔍 Fornit</h1>
    <p class="sub">Uncover hidden details, skip long videos, or research creators & songs.</p>

    <!-- Optional Gemini Key (Uses free built-in AI automatically if left blank) -->
    <div class="api-box" id="apiBoxContainer">
        <span style="color: #a1a1aa;">⚡ <b>Free AI Built-In!</b> (Optional: paste a Gemini API key below for faster replies):</span>
        <input type="password" id="apiKeyInput" placeholder="Optional Gemini API key (leave blank to use free built-in AI)">
    </div>

    <!-- 3 Mode Tabs -->
    <div class="tabs">
        <button class="tab-btn active" id="tabLink" onclick="switchMode('link')">🔗 Link Deep-Dive</button>
        <button class="tab-btn" id="tabChannel" onclick="switchMode('channel')">👤 Creator Intel</button>
        <button class="tab-btn" id="tabDiscover" onclick="switchMode('discover')">🔍 Vibe & Topic</button>
    </div>

    <!-- Search Input -->
    <div class="search-box">
        <input type="text" id="mainInput" placeholder="Paste YouTube, SoundCloud, or Vimeo link...">
        <button class="action-btn" onclick="runSearch()">Inspect</button>
    </div>

    <!-- Recent History -->
    <div class="history-bar" id="historyBar"></div>

    <!-- 1. Metadata Result -->
    <div class="card" id="infoResult"></div>

    <!-- 2. Non-AI Deep Research Tools -->
    <div class="card" id="researchCard">
        <h3>🔎 Deep Research Tools</h3>
        <div class="research-grid" id="researchLinks"></div>
    </div>

    <!-- 3. One-Tap AI Analysis & Chat -->
    <div class="card" id="aiSection">
        <h3>
            <span>⚡ Instant Breakdown</span>
            <button class="copy-btn" onclick="copyLastReply()">📋 Copy Summary</button>
        </h3>
        
        <div class="preset-grid" id="presetButtons"></div>
        <div class="chat-box" id="chatHistory"></div>
        
        <div class="search-box" style="margin-bottom:0;">
            <input type="text" id="chatInput" placeholder="Ask a specific question...">
            <button class="action-btn" onclick="sendChat()">Ask</button>
        </div>
    </div>
</div>

<script>
    let currentMode = 'link';
    let currentContext = '';
    let lastAiResponse = '';

    // OPTIONAL: Paste your own Gemini API key inside the quotes below if you have one
    const HARDCODED_API_KEY = ""; 

    if (HARDCODED_API_KEY) {
        document.getElementById('apiBoxContainer').style.display = 'none';
    }

    renderHistory();

    function switchMode(mode) {
        currentMode = mode;
        document.getElementById('tabLink').classList.toggle('active', mode === 'link');
        document.getElementById('tabChannel').classList.toggle('active', mode === 'channel');
        document.getElementById('tabDiscover').classList.toggle('active', mode === 'discover');
        
        const input = document.getElementById('mainInput');
        if (mode === 'link') {
            input.placeholder = "Paste YouTube, SoundCloud, or Vimeo link...";
        } else if (mode === 'channel') {
            input.placeholder = "Type creator/channel name (e.g. Kai Cenat, Vybz Kartel)...";
        } else {
            input.placeholder = "Type a vibe, lyric, or topic (e.g. 'songs like Burna Boy')...";
        }
    }

    function getApiKey() {
        return HARDCODED_API_KEY || document.getElementById('apiKeyInput').value.trim();
    }

    function saveHistory(query, mode) {
        let hist = JSON.parse(localStorage.getItem('deepDiveHist') || '[]');
        hist = hist.filter(item => item.q !== query);
        hist.unshift({ q: query, m: mode });
        if (hist.length > 5) hist.pop();
        localStorage.setItem('deepDiveHist', JSON.stringify(hist));
        renderHistory();
    }

    function renderHistory() {
        const bar = document.getElementById('historyBar');
        const hist = JSON.parse(localStorage.getItem('deepDiveHist') || '[]');
        if (hist.length === 0) {
            bar.style.display = 'none';
            return;
