<!DOCTYPE html>
<html lang="pt">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Love Protocol // Maria Edition</title>
  <style>
    * { box-sizing: border-box; }

    body {
      margin: 0;
      overflow: hidden;
      background: radial-gradient(circle at top, #101424 0%, #05070d 45%, #000000 100%);
      font-family: "Courier New", monospace;
      color: #00ff9f;
      height: 100vh;
    }

    canvas {
      position: fixed;
      inset: 0;
      z-index: 0;
    }

    .overlay {
      position: fixed;
      inset: 0;
      background:
        linear-gradient(rgba(255,255,255,0.03), rgba(255,255,255,0.01)),
        repeating-linear-gradient(
          to bottom,
          rgba(255,255,255,0.03) 0px,
          rgba(255,255,255,0.03) 1px,
          transparent 2px,
          transparent 4px
        );
      pointer-events: none;
      z-index: 1;
      mix-blend-mode: soft-light;
    }

    .container {
      position: relative;
      z-index: 2;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      padding: 24px;
    }

    .terminal {
      width: min(900px, 100%);
      min-height: 520px;
      background: rgba(5, 10, 20, 0.72);
      border: 1px solid rgba(0, 255, 160, 0.28);
      border-radius: 18px;
      box-shadow:
        0 0 20px rgba(0, 255, 160, 0.15),
        0 0 60px rgba(0, 180, 255, 0.10),
        inset 0 0 40px rgba(0, 255, 160, 0.04);
      backdrop-filter: blur(8px);
      overflow: hidden;
      position: relative;
    }

    .terminal-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 14px 18px;
      border-bottom: 1px solid rgba(0, 255, 160, 0.18);
      background: linear-gradient(to right, rgba(0,255,160,0.08), rgba(0,160,255,0.05));
    }

    .dots {
      display: flex;
      gap: 8px;
    }

    .dot {
      width: 11px;
      height: 11px;
      border-radius: 50%;
      box-shadow: 0 0 10px currentColor;
    }

    .dot.red { color: #ff4d6d; background: #ff4d6d; }
    .dot.yellow { color: #ffd166; background: #ffd166; }
    .dot.green { color: #06d6a0; background: #06d6a0; }

    .title {
      color: #8ef9d2;
      font-size: 14px;
      letter-spacing: 1px;
      text-transform: uppercase;
      text-shadow: 0 0 10px rgba(0,255,160,0.35);
    }

    .status {
      color: #58c7ff;
      font-size: 12px;
      letter-spacing: 1px;
    }

    .terminal-body {
      padding: 24px;
      min-height: 460px;
      white-space: pre-wrap;
      font-size: 17px;
      line-height: 1.7;
      text-shadow: 0 0 8px rgba(0,255,160,0.22);
      position: relative;
      z-index: 2;
    }

    .line-glow { color: #00ff9f; }
    .line-accent { color: #58c7ff; }
    .line-alert { color: #ff77c8; }

    .line-love {
      color: #ffffff;
      text-shadow:
        0 0 6px rgba(255,255,255,0.25),
        0 0 14px rgba(88,199,255,0.20),
        0 0 18px rgba(0,255,159,0.20);
    }

    .line-secret {
      color: #ffd166;
      text-shadow:
        0 0 6px rgba(255,209,102,0.35),
        0 0 14px rgba(255,119,200,0.20),
        0 0 22px rgba(255,209,102,0.25);
    }

    .footer {
      margin-top: 14px;
      color: rgba(142, 249, 210, 0.65);
      font-size: 12px;
      letter-spacing: 1px;
      text-transform: uppercase;
    }

    .hint {
      margin-top: 18px;
      color: rgba(255,255,255,0.16);
      font-size: 11px;
      letter-spacing: 1px;
      text-transform: uppercase;
    }

    .glitch {
      position: relative;
      display: inline-block;
    }

    .glitch::before,
    .glitch::after {
      content: attr(data-text);
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      overflow: hidden;
      opacity: 0.5;
      pointer-events: none;
    }

    .glitch::before {
      color: #ff00c8;
      transform: translate(1px, 0);
      animation: glitch1 2s infinite linear alternate-reverse;
    }

    .glitch::after {
      color: #00d9ff;
      transform: translate(-1px, 0);
      animation: glitch2 2.4s infinite linear alternate-reverse;
    }

    .glitch-screen {
      animation: screenGlitch 0.12s linear infinite;
    }

    .flash-screen {
      animation: screenFlash 0.4s ease-out;
    }

    .access-overlay {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      background:
        radial-gradient(circle, rgba(0,255,159,0.12) 0%, rgba(0,0,0,0.82) 60%, rgba(0,0,0,0.96) 100%);
      z-index: 5;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.2s ease;
    }

    .access-overlay.show {
      opacity: 1;
    }

    .access-text {
      font-size: clamp(28px, 6vw, 72px);
      font-weight: bold;
      color: #00ff9f;
      letter-spacing: 4px;
      text-transform: uppercase;
      text-shadow:
        0 0 8px rgba(0,255,159,0.7),
        0 0 24px rgba(0,255,159,0.5),
        0 0 40px rgba(88,199,255,0.4);
      animation: accessPulse 0.3s infinite alternate;
    }

    /* hotspot invisível */
    .secret-hotspot {
      position: fixed;
      top: 0;
      right: 0;
      width: 90px;
      height: 90px;
      z-index: 20;
      background: transparent;
      -webkit-tap-highlight-color: transparent;
    }

    @keyframes accessPulse {
      from { transform: scale(1); }
      to { transform: scale(1.03); }
    }

    @keyframes screenGlitch {
      0%   { transform: translate(0, 0) skew(0deg); filter: hue-rotate(0deg); }
      20%  { transform: translate(-2px, 1px) skew(0.5deg); }
      40%  { transform: translate(2px, -1px) skew(-0.5deg); }
      60%  { transform: translate(-1px, 2px) skew(0.2deg); filter: hue-rotate(20deg); }
      80%  { transform: translate(1px, -2px) skew(-0.2deg); }
      100% { transform: translate(0, 0) skew(0deg); filter: hue-rotate(0deg); }
    }

    @keyframes screenFlash {
      0% { box-shadow: inset 0 0 0 rgba(255,255,255,0); }
      50% { box-shadow: inset 0 0 120px rgba(0,255,159,0.22); }
      100% { box-shadow: inset 0 0 0 rgba(255,255,255,0); }
    }

    @keyframes glitch1 {
      0% { clip-path: inset(0 0 85% 0); }
      20% { clip-path: inset(30% 0 40% 0); }
      40% { clip-path: inset(65% 0 10% 0); }
      60% { clip-path: inset(10% 0 70% 0); }
      80% { clip-path: inset(45% 0 20% 0); }
      100% { clip-path: inset(75% 0 5% 0); }
    }

    @keyframes glitch2 {
      0% { clip-path: inset(70% 0 8% 0); }
      20% { clip-path: inset(5% 0 75% 0); }
      40% { clip-path: inset(40% 0 25% 0); }
      60% { clip-path: inset(20% 0 50% 0); }
      80% { clip-path: inset(55% 0 12% 0); }
      100% { clip-path: inset(8% 0 82% 0); }
    }

    @media (max-width: 600px) {
      .terminal-body {
        font-size: 15px;
        padding: 18px;
      }

      .title, .status {
        font-size: 11px;
      }

      .secret-hotspot {
        width: 80px;
        height: 80px;
      }
    }
  </style>
</head>
<body>
  <canvas id="rain"></canvas>
  <div class="overlay"></div>

  <!-- botão invisível -->
  <div id="secretHotspot" class="secret-hotspot" aria-hidden="true"></div>

  <div class="container">
    <div class="terminal" id="terminalBox">
      <div class="access-overlay" id="accessOverlay">
        <div class="access-text">ACCESS GRANTED</div>
      </div>

      <div class="terminal-header">
        <div class="dots">
          <span class="dot red"></span>
          <span class="dot yellow"></span>
          <span class="dot green"></span>
        </div>
        <div class="title glitch" data-text="LOVE_PROTOCOL // SECURE TERMINAL">
          LOVE_PROTOCOL // SECURE TERMINAL
        </div>
        <div class="status">ENCRYPTED CHANNEL</div>
      </div>

      <div class="terminal-body" id="terminal"></div>
    </div>
  </div>

  <script>
    const params = new URLSearchParams(window.location.search);
    const nomeParam = params.get("nome");
    const nomeBebe = nomeParam && nomeParam.trim() !== ""
      ? decodeURIComponent(nomeParam).trim()
      : "meu pequeno arco-íris";

    const secretCode = "rainbow";
    const secretTapCountRequired = 5;

    // ===== Chuva digital =====
    const canvas = document.getElementById("rain");
    const ctx = canvas.getContext("2d");

    function resizeCanvas() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    resizeCanvas();

    const chars = "アァカサタナハマヤャラワ0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ$#<>/=+";
    const fontSize = 16;
    let columns = Math.floor(window.innerWidth / fontSize);
    let drops = Array(columns).fill(1);

    function drawRain() {
      ctx.fillStyle = "rgba(0, 0, 0, 0.08)";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "#00ff9f";
      ctx.font = fontSize + "px monospace";

      for (let i = 0; i < drops.length; i++) {
        const text = chars[Math.floor(Math.random() * chars.length)];
        ctx.fillText(text, i * fontSize, drops[i] * fontSize);

        if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
          drops[i] = 0;
        }

        drops[i]++;
      }
    }

    setInterval(drawRain, 40);

    window.addEventListener("resize", () => {
      resizeCanvas();
      columns = Math.floor(window.innerWidth / fontSize);
      drops = Array(columns).fill(1);
    });

    // ===== Terminal =====
    const terminal = document.getElementById("terminal");
    const terminalBox = document.getElementById("terminalBox");
    const accessOverlay = document.getElementById("accessOverlay");
    const secretHotspot = document.getElementById("secretHotspot");

    const lines = [
      { text: "[BOOT] Initializing cinematic interface...", cls: "line-accent" },
      { text: "[BOOT] Loading memory fragments...", cls: "line-accent" },
      { text: "[AUTH] Identity check: PAI_CONFIRMED ✔", cls: "line-glow" },
      { text: "[SYS ] Establishing emotional uplink...", cls: "line-glow" },
      { text: "", cls: "" },
      { text: ">>> run blessing_protocol.exe", cls: "line-alert" },
      { text: "", cls: "" },
      { text: `${nomeBebe} 🌈,`, cls: "line-love" },
      { text: "és a nossa maior bênção.", cls: "line-love" },
      { text: "Amo-te hoje e sempre 💙", cls: "line-love" },
      { text: "", cls: "" },
      { text: "[DATA] Hope restored after storm event.", cls: "line-accent" },
      { text: "[DATA] Rainbow signal detected.", cls: "line-accent" },
      { text: "[CORE] LOVE_LEVEL = ∞", cls: "line-glow" },
      { text: "[LINK] CONNECTION_STATUS = FOREVER", cls: "line-glow" },
      { text: "", cls: "" },
      { text: "[END ] Transmission complete ✔", cls: "line-alert" }
    ];

    let lineIndex = 0;
    let charIndex = 0;
    let currentLineEl = null;
    let typedBuffer = "";
    let easterEggUnlocked = false;
    let tapCount = 0;
    let tapResetTimer = null;

    function appendLine(text, cls = "") {
      const div = document.createElement("div");
      if (cls) div.className = cls;
      div.textContent = text;
      terminal.appendChild(div);
      terminal.scrollTop = terminal.scrollHeight;
      window.scrollTo({ top: document.body.scrollHeight, behavior: "smooth" });
    }

    function appendHint() {
      const footer = document.createElement("div");
      footer.className = "footer";
      footer.textContent = "Transmission complete.";
      terminal.appendChild(document.createTextNode("\n"));
      terminal.appendChild(footer);

      const hint = document.createElement("div");
      hint.className = "hint";
      hint.textContent = "hint: type the hidden color code... or find the invisible mobile trigger";
      terminal.appendChild(hint);
    }

    function type() {
      if (lineIndex >= lines.length) {
        appendHint();
        return;
      }

      const line = lines[lineIndex];

      if (!currentLineEl) {
        currentLineEl = document.createElement("div");
        currentLineEl.className = line.cls;
        terminal.appendChild(currentLineEl);
      }

      if (charIndex < line.text.length) {
        currentLineEl.textContent += line.text.charAt(charIndex);
        charIndex++;
        window.scrollTo({ top: document.body.scrollHeight, behavior: "smooth" });
        setTimeout(type, Math.random() * 18 + 18);
      } else {
        lineIndex++;
        charIndex = 0;
        currentLineEl = null;
        setTimeout(type, line.text === "" ? 140 : 280);
      }
    }

    function playAccessGrantedSequence() {
      if (easterEggUnlocked) return;
      easterEggUnlocked = true;

      terminalBox.classList.add("glitch-screen");
      terminalBox.classList.add("flash-screen");
      accessOverlay.classList.add("show");

      setTimeout(() => {
        accessOverlay.classList.remove("show");
      }, 1100);

      setTimeout(() => {
        terminalBox.classList.remove("glitch-screen");
      }, 1200);

      setTimeout(() => {
        terminalBox.classList.remove("flash-screen");
      }, 700);

      setTimeout(() => {
        appendLine("", "");
        appendLine("[SECURITY] Secret code accepted ✔", "line-secret");
        appendLine("[VAULT] Hidden memory decrypted...", "line-secret");
        appendLine("", "");
        appendLine("Tu és a nossa luz depois da tempestade 🌈💛", "line-secret");
        appendLine("Missão secreta: amar-te para sempre ✔", "line-secret");
      }, 1250);
    }

    // desbloqueio por teclado
    document.addEventListener("keydown", (event) => {
      if (easterEggUnlocked) return;

      if (event.key.length === 1) {
        typedBuffer += event.key.toLowerCase();

        if (typedBuffer.length > 30) {
          typedBuffer = typedBuffer.slice(-30);
        }

        if (typedBuffer.includes(secretCode)) {
          playAccessGrantedSequence();
        }
      }
    });

    // desbloqueio por toques no hotspot
    function registerSecretTap() {
      if (easterEggUnlocked) return;

      tapCount++;

      clearTimeout(tapResetTimer);
      tapResetTimer = setTimeout(() => {
        tapCount = 0;
      }, 1800);

      if (tapCount >= secretTapCountRequired) {
        tapCount = 0;
        playAccessGrantedSequence();
      }
    }

    secretHotspot.addEventListener("click", registerSecretTap);
    secretHotspot.addEventListener("touchstart", registerSecretTap, { passive: true });

    setTimeout(() => {
      terminal.innerHTML = "";
      type();
    }, 700);
  </script>
</body>
</html>
