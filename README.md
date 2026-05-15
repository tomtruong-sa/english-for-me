# english-for-me<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Native English Corrector - Gemini</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
  body {
    min-height: 100vh;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
  }
  .container {
    background: #fff;
    border-radius: 16px;
    box-shadow: 0 20px 60px rgba(0,0,0,0.3);
    width: 100%;
    max-width: 760px;
    padding: 30px;
  }
  h1 {
    text-align: center;
    color: #333;
    margin-bottom: 8px;
    font-size: 28px;
  }
  .subtitle {
    text-align: center;
    color: #666;
    margin-bottom: 25px;
    font-size: 14px;
  }
  .api-key {
    margin-bottom: 15px;
    display: flex;
    gap: 8px;
    align-items: center;
  }
  .api-key input {
    flex: 1;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 8px;
    font-size: 14px;
  }
  .api-key a {
    font-size: 12px;
    color: #667eea;
    text-decoration: none;
    white-space: nowrap;
  }
  .voice-settings {
    display: flex;
    gap: 10px;
    margin-bottom: 15px;
    align-items: center;
    flex-wrap: wrap;
  }
  .voice-settings label {
    font-size: 13px;
    color: #555;
  }
  .voice-settings select, .voice-settings input[type=range] {
    padding: 6px;
    border: 1px solid #ddd;
    border-radius: 6px;
    font-size: 13px;
  }
  textarea {
    width: 100%;
    min-height: 120px;
    padding: 14px;
    border: 2px solid #e0e0e0;
    border-radius: 12px;
    font-size: 16px;
    resize: vertical;
    transition: border 0.3s;
  }
  textarea:focus { outline: none; border-color: #667eea; }
  .buttons {
    display: flex;
    gap: 10px;
    margin-top: 15px;
    flex-wrap: wrap;
  }
  button {
    flex: 1;
    min-width: 110px;
    padding: 12px 18px;
    border: none;
    border-radius: 10px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s;
    color: #fff;
  }
  .btn-mic { background: #ef4444; }
  .btn-mic.recording { background: #b91c1c; animation: pulse 1s infinite; }
  .btn-correct { background: #667eea; }
  .btn-speak-input { background: #10b981; }
  .btn-clear { background: #6b7280; }
  button:hover { transform: translateY(-2px); opacity: 0.9; }
  button:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
  @keyframes pulse {
    0%,100% { box-shadow: 0 0 0 0 rgba(239,68,68,0.7); }
    50% { box-shadow: 0 0 0 12px rgba(239,68,68,0); }
  }
  .result {
    margin-top: 25px;
    padding: 20px;
    background: #f9fafb;
    border-radius: 12px;
    border-left: 4px solid #667eea;
    display: none;
  }
  .result h3 {
    color: #333;
    margin-bottom: 10px;
    font-size: 16px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .corrected-wrap {
    background: #ecfdf5;
    padding: 12px;
    border-radius: 8px;
    margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .corrected {
    flex: 1;
    color: #065f46;
    font-size: 16px;
    line-height: 1.5;
  }
  .speak-btn {
    background: #10b981;
    color: #fff;
    border: none;
    border-radius: 50%;
    width: 38px;
    height: 38px;
    cursor: pointer;
    font-size: 16px;
    flex-shrink: 0;
    transition: all 0.3s;
  }
  .speak-btn:hover { transform: scale(1.1); }
  .speak-btn.speaking { background: #059669; animation: pulse-green 1s infinite; }
  @keyframes pulse-green {
    0%,100% { box-shadow: 0 0 0 0 rgba(16,185,129,0.7); }
    50% { box-shadow: 0 0 0 10px rgba(16,185,129,0); }
  }
  .explanation {
    background: #fff;
    padding: 12px;
    border-radius: 8px;
    color: #444;
    font-size: 14px;
    line-height: 1.6;
    white-space: pre-wrap;
  }
  .loading {
    text-align: center;
    color: #667eea;
    margin-top: 15px;
    display: none;
  }
  .status { font-size: 13px; color: #666; margin-top: 8px; text-align: center; }
</style>
</head>
<body>
  <div class="container">
    <h1>🎯 Native English Corrector</h1>
    <p class="subtitle">Powered by Google Gemini (Free) • Type, Speak & Listen</p>

    <div class="api-key">
      <input type="password" id="apiKey" placeholder="Enter your Gemini API Key" />
      <a href="https://aistudio.google.com/app/apikey" target="_blank">Get key →</a>
    </div>

    <div class="voice-settings">
      <label>Voice:</label>
      <select id="voiceSelect"></select>
      <label>Speed:</label>
      <input type="range" id="rate" min="0.5" max="1.5" step="0.1" value="1">
      <span id="rateVal">1.0x</span>
    </div>

    <textarea id="inputText" placeholder="Type your English sentence here, or click 🎤 to speak..."></textarea>
    <div class="status" id="status"></div>

    <div class="buttons">
      <button class="btn-mic" id="micBtn">🎤 Speak</button>
      <button class="btn-speak-input" id="speakInputBtn">🔊 Listen</button>
      <button class="btn-correct" id="correctBtn">✨ Correct</button>
      <button class="btn-clear" id="clearBtn">🗑 Clear</button>
    </div>

    <div class="loading" id="loading">⏳ Analyzing with Gemini...</div>

    <div class="result" id="result">
      <h3>✅ Native Version:</h3>
      <div class="corrected-wrap">
        <div class="corrected" id="corrected"></div>
        <button class="speak-btn" id="speakCorrectedBtn" title="Listen">🔊</button>
      </div>
      <h3>💡 Explanation:</h3>
      <div class="explanation" id="explanation"></div>
    </div>
  </div>

<script>
  const inputText = document.getElementById('inputText');
  const micBtn = document.getElementById('micBtn');
  const speakInputBtn = document.getElementById('speakInputBtn');
  const correctBtn = document.getElementById('correctBtn');
  const clearBtn = document.getElementById('clearBtn');
  const result = document.getElementById('result');
  const correctedEl = document.getElementById('corrected');
  const explanationEl = document.getElementById('explanation');
  const loading = document.getElementById('loading');
  const statusEl = document.getElementById('status');
  const apiKeyInput = document.getElementById('apiKey');
  const speakCorrectedBtn = document.getElementById('speakCorrectedBtn');
  const voiceSelect = document.getElementById('voiceSelect');
  const rateSlider = document.getElementById('rate');
  const rateVal = document.getElementById('rateVal');

  // Load saved API key
  apiKeyInput.value = localStorage.getItem('gemini_key') || '';
  apiKeyInput.addEventListener('change', () => {
    localStorage.setItem('gemini_key', apiKeyInput.value);
  });

  // ---------- Text-to-Speech setup ----------
  const synth = window.speechSynthesis;
  let voices = [];

  function loadVoices() {
    voices = synth.getVoices().filter(v => v.lang.startsWith('en'));
    voiceSelect.innerHTML = '';
    voices.forEach((v, i) => {
      const opt = document.createElement('option');
      opt.value = i;
      opt.textContent = `${v.name} (${v.lang})`;
      if (v.name.includes('Google') || v.name.includes('Natural') || v.default) opt.selected = true;
      voiceSelect.appendChild(opt);
    });
  }
  loadVoices();
  if (synth.onvoiceschanged !== undefined) synth.onvoiceschanged = loadVoices;

  rateSlider.addEventListener('input', () => {
    rateVal.textContent = parseFloat(rateSlider.value).toFixed(1) + 'x';
  });

  function speak(text, btn) {
    if (!text) return;
    synth.cancel();
    const utter = new SpeechSynthesisUtterance(text);
    const selectedVoice = voices[voiceSelect.value];
    if (selectedVoice) utter.voice = selectedVoice;
    utter.rate = parseFloat(rateSlider.value);
    utter.pitch = 1;
    utter.lang = selectedVoice ? selectedVoice.lang : 'en-US';

    if (btn) {
      btn.classList.add('speaking');
      utter.onend = () => btn.classList.remove('speaking');
      utter.onerror = () => btn.classList.remove('speaking');
    }
    synth.speak(utter);
  }

  speakInputBtn.addEventListener('click', () => {
    const text = inputText.value.trim();
    if (!text) { alert('Nothing to read.'); return; }
    speak(text, speakInputBtn);
  });

  speakCorrectedBtn.addEventListener('click', () => {
    speak(correctedEl.textContent, speakCorrectedBtn);
  });

  // ---------- Speech Recognition ----------
  const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
  let recognition = null;
  let isRecording = false;

  if (SpeechRecognition) {
    recognition = new SpeechRecognition();
    recognition.lang = 'en-US';
    recognition.interimResults = true;
    recognition.continuous = false;

    recognition.onstart = () => {
      isRecording = true;
      micBtn.classList.add('recording');
      micBtn.textContent = '⏹ Stop';
      statusEl.textContent = '🎙 Listening...';
    };
    recognition.onresult = (e) => {
      let transcript = '';
      for (let i = 0; i < e.results.length; i++) {
        transcript += e.results[i][0].transcript;
      }
      inputText.value = transcript;
    };
    recognition.onerror = (e) => { statusEl.textContent = '⚠ Error: ' + e.error; };
    recognition.onend = () => {
      isRecording = false;
      micBtn.classList.remove('recording');
      micBtn.textContent = '🎤 Speak';
      statusEl.textContent = '';
    };
  } else {
    micBtn.disabled = true;
    micBtn.textContent = '🚫 Mic not supported';
  }

  micBtn.addEventListener('click', () => {
    if (!recognition) return;
    if (isRecording) recognition.stop();
    else recognition.start();
  });

  // ---------- Correct via Gemini ----------
  correctBtn.addEventListener('click', async () => {
    const text = inputText.value.trim();
    const apiKey = apiKeyInput.value.trim();

    if (!text) { alert('Please enter or speak something first.'); return; }
    if (!apiKey) { alert('Please enter your Gemini API key.'); return; }

    loading.style.display = 'block';
    result.style.display = 'none';

    const prompt = `You are a native English teacher. The user will give you an English sentence that may contain grammar mistakes, awkward phrasing, or non-native style.

Your tasks:
1. Rewrite it to sound natural like a native English speaker.
2. Provide a short, clear explanation IN VIETNAMESE about what was changed and why.

Respond ONLY in this strict JSON format (no markdown, no code fences):
{"corrected": "...", "explanation": "..."}

User sentence: "${text}"`;

    try {
      const response = await fetch(
        `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`,
        {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            contents: [{ parts: [{ text: prompt }] }],
            generationConfig: {
              temperature: 0.4,
              responseMimeType: "application/json"
            }
          })
        }
      );

      const data = await response.json();
      if (data.error) throw new Error(data.error.message);

      let rawText = data.candidates[0].content.parts[0].text.trim();
      // Cleanup if Gemini accidentally wraps in code fences
      rawText = rawText.replace(/^```json\s*/i, '').replace(/```$/, '').trim();

      const content = JSON.parse(rawText);
      correctedEl.textContent = content.corrected;
      explanationEl.textContent = content.explanation;
      result.style.display = 'block';

      // Auto play the corrected sentence
      setTimeout(() => speak(content.corrected, speakCorrectedBtn), 300);
    } catch (err) {
      alert('Error: ' + err.message);
      console.error(err);
    } finally {
      loading.style.display = 'none';
    }
  });

  // ---------- Clear ----------
  clearBtn.addEventListener('click', () => {
    inputText.value = '';
    result.style.display = 'none';
    statusEl.textContent = '';
    synth.cancel();
  });
</script>
</body>
</html>