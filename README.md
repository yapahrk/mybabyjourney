
<html lang="th">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>เกมเติมตัวอักษรภาษาอังกฤษ</title>
  <script src="/_sdk/element_sdk.js"></script>
  <style>
    body {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 50%, #ffeaa7 100%);
      min-height: 100%;
      width: 100%;
    }

    .game-container {
      width: 100%;
      min-height: 100%;
      padding: 40px 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    .game-card {
      background: linear-gradient(145deg, #fff5f7 0%, #ffe8f0 100%);
      border-radius: 30px;
      padding: 40px;
      max-width: 900px;
      width: 100%;
      box-shadow: 0 20px 60px rgba(252, 182, 159, 0.4);
      border: 3px solid rgba(255, 255, 255, 0.8);
    }

    .game-header {
      text-align: center;
      margin-bottom: 30px;
    }

    .game-title {
      font-size: 36px;
      background: linear-gradient(90deg, #ff9a9e 0%, #fad0c4 50%, #ffecd2 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      margin: 0 0 10px 0;
      font-weight: bold;
      text-shadow: 2px 2px 4px rgba(255, 182, 193, 0.3);
    }

    .instructions {
      font-size: 16px;
      color: #666;
      margin: 0;
    }

    .score-board {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%);
      padding: 20px;
      border-radius: 20px;
      margin-bottom: 30px;
      box-shadow: 0 4px 15px rgba(253, 203, 110, 0.3);
      flex-wrap: wrap;
      gap: 15px;
    }

    .timer-display {
      display: flex;
      align-items: center;
      gap: 8px;
      background: white;
      padding: 10px 20px;
      border-radius: 20px;
      font-size: 24px;
      font-weight: bold;
      color: #e17055;
      box-shadow: 0 2px 10px rgba(225, 112, 85, 0.2);
    }

    .timer-icon {
      font-size: 28px;
    }

    .category-badge {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
      color: #6c5ce7;
      padding: 12px 24px;
      border-radius: 30px;
      font-size: 18px;
      font-weight: 600;
      box-shadow: 0 4px 15px rgba(168, 237, 234, 0.4);
    }

    .score-display {
      font-size: 24px;
      font-weight: bold;
      color: #e17055;
      background: white;
      padding: 10px 20px;
      border-radius: 20px;
      box-shadow: 0 2px 10px rgba(225, 112, 85, 0.2);
    }

    .word-display {
      text-align: center;
      margin: 40px 0;
    }

    .hint-image {
      margin: 0 auto 30px auto;
      display: block;
      filter: drop-shadow(0 4px 15px rgba(0, 0, 0, 0.1));
    }

    .word-letters {
      display: flex;
      justify-content: center;
      gap: 8px;
      flex-wrap: wrap;
      margin-bottom: 20px;
    }

    .letter-box {
      width: 50px;
      height: 60px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 28px;
      font-weight: bold;
      text-transform: uppercase;
      border: 3px solid #fab1a0;
      border-radius: 15px;
      background: linear-gradient(145deg, #dfe6e9 0%, #b2bec3 100%);
      color: #2d3436;
      box-shadow: 0 4px 10px rgba(250, 177, 160, 0.3);
    }

    .letter-box.blank {
      background: linear-gradient(145deg, #ffeaa7 0%, #fdcb6e 100%);
      border-color: #fab1a0;
      border-style: dashed;
      border-width: 3px;
      animation: pulse 2s ease-in-out infinite;
    }

    @keyframes pulse {
      0%, 100% {
        transform: scale(1);
        box-shadow: 0 4px 10px rgba(253, 203, 110, 0.3);
      }
      50% {
        transform: scale(1.05);
        box-shadow: 0 6px 15px rgba(253, 203, 110, 0.5);
      }
    }

    .letter-box input {
      width: 100%;
      height: 100%;
      border: none;
      background: transparent;
      text-align: center;
      font-size: 28px;
      font-weight: bold;
      text-transform: uppercase;
      color: #333;
      outline: none;
    }

    .feedback-message {
      text-align: center;
      padding: 20px;
      border-radius: 12px;
      margin: 20px 0;
      font-size: 20px;
      font-weight: 600;
      min-height: 60px;
      display: flex;
      align-items: center;
      justify-content: center;
      opacity: 0;
      transition: opacity 0.3s ease;
    }

    .feedback-message.show {
      opacity: 1;
    }

    .feedback-message.correct {
      background: linear-gradient(135deg, #a8e6cf 0%, #dcedc1 100%);
      color: #00b894;
      border: 3px solid #55efc4;
      box-shadow: 0 4px 15px rgba(85, 239, 196, 0.4);
    }

    .feedback-message.incorrect {
      background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%);
      color: #e17055;
      border: 3px solid #fab1a0;
      box-shadow: 0 4px 15px rgba(250, 177, 160, 0.4);
    }

    .button-group {
      display: flex;
      gap: 15px;
      justify-content: center;
      margin-top: 30px;
    }

    .game-button {
      padding: 15px 40px;
      font-size: 18px;
      font-weight: 600;
      border: none;
      border-radius: 12px;
      cursor: pointer;
      transition: all 0.3s ease;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
    }

    .game-button:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(0, 0, 0, 0.15);
    }

    .game-button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
      transform: none;
    }

    .check-button {
      background: linear-gradient(135deg, #81ecec 0%, #74b9ff 100%);
      color: white;
      text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
    }

    .check-button:hover:not(:disabled) {
      background: linear-gradient(135deg, #74b9ff 0%, #a29bfe 100%);
    }

    .next-button {
      background: linear-gradient(135deg, #fd79a8 0%, #fdcb6e 100%);
      color: white;
      text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
    }

    .next-button:hover:not(:disabled) {
      background: linear-gradient(135deg, #fdcb6e 0%, #ff7675 100%);
    }

    .completion-screen {
      display: none;
      text-align: center;
      padding: 40px;
    }

    .completion-screen.show {
      display: block;
    }

    .completion-icon {
      font-size: 80px;
      margin-bottom: 20px;
    }

    .completion-title {
      font-size: 36px;
      background: linear-gradient(90deg, #ff9a9e 0%, #fad0c4 50%, #ffecd2 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      margin-bottom: 20px;
      font-weight: bold;
    }

    .final-score {
      font-size: 28px;
      color: #e17055;
      margin-bottom: 15px;
      background: white;
      padding: 15px 30px;
      border-radius: 25px;
      display: inline-block;
      box-shadow: 0 4px 15px rgba(225, 112, 85, 0.3);
    }

    @media (max-width: 768px) {
      .game-card {
        padding: 20px;
      }

      .game-title {
        font-size: 28px;
      }

      .letter-box {
        width: 40px;
        height: 50px;
        font-size: 22px;
      }

      .letter-box input {
        font-size: 22px;
      }

      .game-button {
        padding: 12px 30px;
        font-size: 16px;
      }

      .score-board {
        flex-direction: column;
        gap: 15px;
      }
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
 </head>
 <body>
  <main class="game-container">
   <div class="game-card">
    <header class="game-header">
     <svg width="120" height="120" viewbox="0 0 120 120" style="margin: 0 auto 20px auto; display: block;"><circle cx="60" cy="60" r="55" fill="#ffeaa7" opacity="0.3" /> <circle cx="60" cy="60" r="45" fill="#fdcb6e" opacity="0.5" /> <text x="60" y="75" font-size="50" text-anchor="middle" fill="#e17055">
       ABC
      </text> <circle cx="30" cy="30" r="8" fill="#74b9ff" /> <circle cx="90" cy="30" r="8" fill="#fd79a8" /> <circle cx="30" cy="90" r="8" fill="#55efc4" /> <circle cx="90" cy="90" r="8" fill="#a29bfe" />
     </svg>
     <h1 class="game-title" id="gameTitle">🎮 เกมเติมตัวอักษรภาษาอังกฤษ</h1>
     <p class="instructions" id="instructions">กรอกตัวอักษรที่หายไปให้ครบถ้วน (10 วินาที/ข้อ)</p>
    </header>
    <div id="gameContent">
     <div class="score-board">
      <div class="category-badge" id="categoryBadge"><span id="categoryEmoji">🌱</span> <span id="categoryName">Environment</span>
      </div>
      <div class="timer-display"><span class="timer-icon">⏱️</span> <span id="timerValue">10s</span>
      </div>
      <div class="score-display"><span id="scoreLabel">คะแนน:</span> <span id="scoreValue">0</span>/<span id="totalQuestions">20</span>
      </div>
     </div>
     <div class="word-display">
      <svg id="hintImage" class="hint-image" width="150" height="150" viewbox="0 0 150 150"></svg>
      <div class="word-letters" id="wordLetters"></div>
     </div>
     <div class="feedback-message" id="feedbackMessage"></div>
     <div class="button-group"><button class="game-button check-button" id="checkButton">✓ ตรวจสอบคำตอบ</button> <button class="game-button next-button" id="nextButton" style="display: none;">→ คำถัดไป</button>
     </div>
    </div>
    <div class="completion-screen" id="completionScreen">
     <div class="completion-icon">
      🎉
     </div>
     <div class="completion-title">
      ยินดีด้วย! เล่นจบแล้ว
     </div>
     <div class="final-score">
      คะแนนของคุณ: <span id="finalScore">0</span>/20
     </div>
     <div class="final-score">
      ⏱️ เวลาที่ใช้: <span id="finalTime">0</span>
     </div>
    </div>
   </div>
  </main>
  <script>
    const defaultConfig = {
      game_title: "🎮 เกมเติมตัวอักษรภาษาอังกฤษ",
      instructions_text: "กรอกตัวอักษรที่หายไปให้ครบถ้วน (10 วินาที/ข้อ)",
      check_button_text: "✓ ตรวจสอบคำตอบ",
      next_button_text: "→ คำถัดไป",
      correct_message: "🎉 ถูกต้อง! เยี่ยมมาก!",
      incorrect_message: "❌ ลองใหม่อีกครั้ง",
      score_label: "คะแนน:"
    };

    const vocabularyData = [
      { category: "🌱 Environment", emoji: "🌱", words: [
        { word: "waterfall", pattern: "w_t_rf_ll", missing: "aae", 
          hint: `<defs><linearGradient id="waterGrad" x1="0%" y1="0%" x2="0%" y2="100%"><stop offset="0%" style="stop-color:#74b9ff;stop-opacity:1" /><stop offset="100%" style="stop-color:#0984e3;stop-opacity:1" /></linearGradient></defs><rect x="30" y="20" width="40" height="15" rx="5" fill="#8B4513"/><rect x="80" y="20" width="40" height="15" rx="5" fill="#8B4513"/><path d="M 50 35 Q 55 40 60 50 Q 65 60 70 80 Q 72 90 75 110" stroke="url(#waterGrad)" stroke-width="8" fill="none" opacity="0.7"/><path d="M 100 35 Q 105 45 108 60 Q 110 75 112 95 Q 113 105 115 120" stroke="url(#waterGrad)" stroke-width="6" fill="none" opacity="0.6"/><ellipse cx="75" cy="125" rx="35" ry="8" fill="#74b9ff" opacity="0.4"/><circle cx="60" cy="122" r="3" fill="#ffffff" opacity="0.8"/><circle cx="85" cy="123" r="2" fill="#ffffff" opacity="0.8"/>` 
        },
        { word: "earthquake", pattern: "_arthq__ke", missing: "euae", 
          hint: `<rect x="20" y="90" width="110" height="30" fill="#8B4513" opacity="0.6"/><path d="M 30 90 L 35 70 L 45 90" fill="#A0522D"/><path d="M 50 90 L 58 65 L 70 90" fill="#A0522D"/><path d="M 75 90 L 82 75 L 95 90" fill="#A0522D"/><path d="M 100 90 L 108 70 L 120 90" fill="#A0522D"/><path d="M 20 100 Q 40 95 60 100 T 100 100 T 130 100" stroke="#ff7675" stroke-width="4" fill="none"/><path d="M 25 105 Q 45 110 65 105 T 105 105 T 135 105" stroke="#d63031" stroke-width="3" fill="none"/><circle cx="50" cy="50" r="4" fill="#fdcb6e" opacity="0.8"><animate attributeName="r" values="4;7;4" dur="1s" repeatCount="indefinite"/></circle><circle cx="90" cy="55" r="3" fill="#fdcb6e" opacity="0.7"><animate attributeName="r" values="3;6;3" dur="1.2s" repeatCount="indefinite"/></circle>` 
        },
        { word: "pollution", pattern: "p_ll_ti_n", missing: "ouo", 
          hint: `<ellipse cx="75" cy="110" rx="45" ry="12" fill="#74b9ff" opacity="0.4"/><rect x="50" y="70" width="50" height="40" fill="#95a5a6" opacity="0.7"/><rect x="60" y="50" width="30" height="25" fill="#7f8c8d" opacity="0.8"/><circle cx="75" cy="35" r="15" fill="#636e72" opacity="0.5"/><path d="M 70 35 Q 68 25 65 15" stroke="#2d3436" stroke-width="3" fill="none"/><path d="M 80 35 Q 82 25 85 15" stroke="#2d3436" stroke-width="3" fill="none"/><circle cx="40" cy="40" r="8" fill="#b2bec3" opacity="0.6"><animate attributeName="cy" values="40;25;40" dur="3s" repeatCount="indefinite"/></circle><circle cx="110" cy="45" r="6" fill="#b2bec3" opacity="0.5"><animate attributeName="cy" values="45;30;45" dur="3.5s" repeatCount="indefinite"/></circle><circle cx="55" cy="35" r="7" fill="#dfe6e9" opacity="0.6"><animate attributeName="cy" values="35;20;35" dur="2.8s" repeatCount="indefinite"/></circle>` 
        },
        { word: "wildlife", pattern: "w_ldl_f_", missing: "iie", 
          hint: `<ellipse cx="75" cy="120" rx="50" ry="10" fill="#55efc4" opacity="0.3"/><rect x="65" y="95" width="8" height="25" fill="#8B4513"/><circle cx="55" cy="95" r="18" fill="#27ae60" opacity="0.6"/><circle cx="75" cy="90" r="22" fill="#27ae60" opacity="0.6"/><circle cx="95" cy="95" r="18" fill="#27ae60" opacity="0.6"/><circle cx="50" cy="65" r="12" fill="#f39c12" opacity="0.8"/><circle cx="48" cy="63" r="3" fill="#2d3436"/><circle cx="54" cy="63" r="3" fill="#2d3436"/><path d="M 45 58 L 40 52 M 55 58 L 60 52" stroke="#f39c12" stroke-width="2"/><ellipse cx="50" cy="72" rx="5" ry="3" fill="#2d3436" opacity="0.6"/><circle cx="100" cy="70" r="8" fill="#e74c3c" opacity="0.7"/><path d="M 102 68 L 108 65 L 110 70 L 106 72 Z" fill="#c0392b" opacity="0.8"/><circle cx="98" cy="68" r="2" fill="#2d3436"/>` 
        },
        { word: "recycle", pattern: "r_c_cl_", missing: "eye", 
          hint: `<circle cx="75" cy="75" r="45" fill="#55efc4" opacity="0.2"/><path d="M 75 40 L 85 60 L 65 60 Z" fill="#27ae60" opacity="0.8"/><text x="75" y="58" font-size="16" text-anchor="middle" fill="#fff" font-weight="bold">1</text><path d="M 110 90 L 100 75 L 115 70 Z" fill="#27ae60" opacity="0.8" transform="rotate(120 75 75)"/><text x="105" y="95" font-size="16" text-anchor="middle" fill="#fff" font-weight="bold">2</text><path d="M 40 90 L 55 85 L 45 70 Z" fill="#27ae60" opacity="0.8" transform="rotate(240 75 75)"/><text x="45" y="95" font-size="16" text-anchor="middle" fill="#fff" font-weight="bold">3</text><path d="M 75 50 Q 95 55 105 75 M 105 85 Q 95 105 75 110 M 65 110 Q 45 105 40 85" stroke="#27ae60" stroke-width="4" fill="none" opacity="0.6"/>` 
        }
      ]},
      { category: "🚗 Transportation", emoji: "🚗", words: [
        { word: "pedestrian", pattern: "p_d_str__n", missing: "eeia", 
          hint: `<ellipse cx="75" cy="125" rx="40" ry="8" fill="#dfe6e9" opacity="0.4"/><circle cx="75" cy="45" r="12" fill="#ffeaa7" opacity="0.9"/><ellipse cx="75" cy="65" rx="15" ry="20" fill="#74b9ff" opacity="0.8"/><rect x="68" y="75" width="6" height="25" fill="#0984e3" opacity="0.8"/><rect x="76" y="75" width="6" height="25" fill="#0984e3" opacity="0.8"/><rect x="60" y="60" width="8" height="18" fill="#74b9ff" opacity="0.8" transform="rotate(-30 64 60)"/><rect x="82" y="60" width="8" height="18" fill="#74b9ff" opacity="0.8" transform="rotate(30 86 60)"/><path d="M 68 100 L 60 115 M 82 100 L 90 115" stroke="#0984e3" stroke-width="6" stroke-linecap="round"/><rect x="30" y="120" width="90" height="5" fill="#95a5a6" opacity="0.5"/><rect x="35" y="110" width="5" height="15" fill="#7f8c8d" opacity="0.6"/><rect x="85" y="110" width="5" height="15" fill="#7f8c8d" opacity="0.6"/>` 
        },
        { word: "departure", pattern: "d_p_rt_r_", missing: "eaue", 
          hint: `<rect x="30" y="90" width="90" height="25" rx="5" fill="#74b9ff" opacity="0.7"/><rect x="35" y="95" width="15" height="12" fill="#dfe6e9" opacity="0.8"/><rect x="55" y="95" width="15" height="12" fill="#dfe6e9" opacity="0.8"/><rect x="75" y="95" width="15" height="12" fill="#dfe6e9" opacity="0.8"/><rect x="95" y="95" width="15" height="12" fill="#dfe6e9" opacity="0.8"/><circle cx="50" cy="120" r="6" fill="#2d3436"/><circle cx="100" cy="120" r="6" fill="#2d3436"/><path d="M 120 75 L 135 90 L 120 105" fill="#fdcb6e" opacity="0.8"><animateTransform attributeName="transform" type="translate" values="0,0; 10,0; 0,0" dur="1.5s" repeatCount="indefinite"/></path><path d="M 110 75 L 125 90 L 110 105" fill="#fdcb6e" opacity="0.6"><animateTransform attributeName="transform" type="translate" values="0,0; 10,0; 0,0" dur="1.5s" begin="0.3s" repeatCount="indefinite"/></path>` 
        },
        { word: "destination", pattern: "d_st_n_ti_n", missing: "eiao", 
          hint: `<rect x="40" y="50" width="70" height="50" rx="3" fill="#fd79a8" opacity="0.6"/><path d="M 40 50 L 75 25 L 110 50" fill="#e17055" opacity="0.7"/><rect x="65" y="70" width="20" height="30" fill="#8B4513" opacity="0.7"/><rect x="50" y="65" width="12" height="12" fill="#74b9ff" opacity="0.7"/><rect x="88" y="65" width="12" height="12" fill="#74b9ff" opacity="0.7"/><circle cx="75" cy="20" r="8" fill="#fdcb6e" opacity="0.8"><animate attributeName="opacity" values="0.8;0.3;0.8" dur="2s" repeatCount="indefinite"/></circle><path d="M 75 20 L 73 10 L 75 12 L 77 10 Z" fill="#fdcb6e" opacity="0.9"><animate attributeName="opacity" values="0.9;0.4;0.9" dur="2s" repeatCount="indefinite"/></path><ellipse cx="75" cy="110" rx="35" ry="8" fill="#55efc4" opacity="0.3"/>` 
        },
        { word: "schedule", pattern: "sc__d_l_", missing: "heue", 
          hint: `<rect x="40" y="30" width="70" height="90" rx="5" fill="#fff" opacity="0.9" stroke="#74b9ff" stroke-width="3"/><circle cx="55" cy="45" r="3" fill="#e17055"/><circle cx="75" cy="45" r="3" fill="#e17055"/><circle cx="95" cy="45" r="3" fill="#e17055"/><rect x="48" y="60" width="54" height="8" rx="2" fill="#dfe6e9" opacity="0.6"/><rect x="48" y="75" width="54" height="8" rx="2" fill="#74b9ff" opacity="0.7"/><rect x="48" y="90" width="54" height="8" rx="2" fill="#dfe6e9" opacity="0.6"/><rect x="48" y="105" width="30" height="8" rx="2" fill="#dfe6e9" opacity="0.6"/><circle cx="120" cy="40" r="18" fill="#fdcb6e" opacity="0.3"/><rect x="118" y="30" width="4" height="12" fill="#e17055" opacity="0.8"><animateTransform attributeName="transform" type="rotate" values="0 120 40; 360 120 40" dur="4s" repeatCount="indefinite"/></rect><rect x="118" y="30" width="3" height="16" fill="#2d3436" opacity="0.8"><animateTransform attributeName="transform" type="rotate" values="90 120 40; 450 120 40" dur="0.5s" repeatCount="indefinite"/></rect>` 
        },
        { word: "highway", pattern: "h_gh_a_", missing: "iwy", 
          hint: `<rect x="30" y="60" width="90" height="50" fill="#636e72" opacity="0.7"/><rect x="35" y="80" width="80" height="8" fill="#f1c40f" opacity="0.8"/><rect x="40" y="84" width="10" height="3" fill="#fff" opacity="0.9"/><rect x="55" y="84" width="10" height="3" fill="#fff" opacity="0.9"/><rect x="70" y="84" width="10" height="3" fill="#fff" opacity="0.9"/><rect x="85" y="84" width="10" height="3" fill="#fff" opacity="0.9"/><rect x="100" y="84" width="10" height="3" fill="#fff" opacity="0.9"/><rect x="50" y="65" width="20" height="12" rx="2" fill="#e74c3c" opacity="0.8"/><rect x="47" y="72" width="4" height="5" fill="#fff" opacity="0.3"/><rect x="66" y="72" width="4" height="5" fill="#fff" opacity="0.3"/><circle cx="53" cy="78" r="2" fill="#2d3436"/><circle cx="67" cy="78" r="2" fill="#2d3436"/><path d="M 70 65 L 80 60 L 90 65 L 85 68 L 75 68 Z" fill="#74b9ff" opacity="0.7"/><ellipse cx="82" cy="72" rx="3" ry="2" fill="#dfe6e9" opacity="0.6"/>` 
        }
      ]},
      { category: "💻 Technology", emoji: "💻", words: [
        { word: "device", pattern: "d_v_c_", missing: "eie", 
          hint: `<rect x="45" y="40" width="60" height="80" rx="8" fill="#2d3436" opacity="0.8"/><rect x="50" y="45" width="50" height="60" rx="3" fill="#74b9ff" opacity="0.3"/><circle cx="75" cy="113" r="5" fill="#dfe6e9" opacity="0.6"/><rect x="60" y="55" width="30" height="5" rx="2" fill="#636e72" opacity="0.4"/><circle cx="70" cy="70" r="8" fill="#fdcb6e" opacity="0.6"/><rect x="80" y="75" width="15" height="3" rx="1" fill="#95a5a6" opacity="0.5"/><rect x="80" y="82" width="12" height="3" rx="1" fill="#95a5a6" opacity="0.5"/><rect x="80" y="89" width="18" height="3" rx="1" fill="#95a5a6" opacity="0.5"/><circle cx="58" cy="85" r="3" fill="#e74c3c" opacity="0.6"/>` 
        },
        { word: "upload", pattern: "_pl__d", missing: "uoa", 
          hint: `<rect x="50" y="80" width="50" height="30" rx="3" fill="#dfe6e9" opacity="0.7"/><rect x="45" y="105" width="60" height="5" rx="2" fill="#95a5a6" opacity="0.6"/><path d="M 75 75 L 75 45" stroke="#74b9ff" stroke-width="6" stroke-linecap="round"/><path d="M 75 45 L 65 55 M 75 45 L 85 55" stroke="#74b9ff" stroke-width="5" stroke-linecap="round"/><circle cx="75" cy="70" r="3" fill="#0984e3" opacity="0.8"><animate attributeName="cy" values="70;45;45" dur="1.5s" repeatCount="indefinite"/><animate attributeName="opacity" values="0.8;0;0" dur="1.5s" repeatCount="indefinite"/></circle><circle cx="75" cy="85" r="3" fill="#0984e3" opacity="0.8"><animate attributeName="cy" values="85;45;45" dur="1.5s" begin="0.5s" repeatCount="indefinite"/><animate attributeName="opacity" values="0.8;0;0" dur="1.5s" begin="0.5s" repeatCount="indefinite"/></circle>` 
        },
        { word: "password", pattern: "p_ssw_r_", missing: "aod", 
          hint: `<rect x="35" y="70" width="80" height="40" rx="5" fill="#636e72" opacity="0.7"/><rect x="65" y="50" width="20" height="25" rx="10" fill="none" stroke="#95a5a6" stroke-width="6" opacity="0.8"/><circle cx="75" cy="90" r="6" fill="#f1c40f" opacity="0.8"/><rect x="73" y="95" width="4" height="10" rx="2" fill="#f1c40f" opacity="0.8"/><circle cx="50" cy="88" r="3" fill="#fff" opacity="0.4"/><circle cx="60" cy="92" r="3" fill="#fff" opacity="0.4"/><circle cx="90" cy="88" r="3" fill="#fff" opacity="0.4"/><circle cx="100" cy="92" r="3" fill="#fff" opacity="0.4"/>` 
        },
        { word: "connection", pattern: "c_nn_ct__n", missing: "oeio", 
          hint: `<circle cx="45" cy="75" r="18" fill="#74b9ff" opacity="0.6" stroke="#0984e3" stroke-width="3"/><circle cx="105" cy="75" r="18" fill="#74b9ff" opacity="0.6" stroke="#0984e3" stroke-width="3"/><path d="M 63 75 L 87 75" stroke="#0984e3" stroke-width="4" stroke-dasharray="5,3"/><circle cx="45" cy="75" r="6" fill="#0984e3" opacity="0.8"/><circle cx="105" cy="75" r="6" fill="#0984e3" opacity="0.8"/><circle cx="75" cy="75" r="5" fill="#fdcb6e" opacity="0.8"><animate attributeName="r" values="5;8;5" dur="1.5s" repeatCount="indefinite"/></circle><path d="M 55 65 Q 65 60 75 65" stroke="#55efc4" stroke-width="2" fill="none" opacity="0.6"><animate attributeName="opacity" values="0.6;0.2;0.6" dur="2s" repeatCount="indefinite"/></path><path d="M 95 65 Q 85 60 75 65" stroke="#55efc4" stroke-width="2" fill="none" opacity="0.6"><animate attributeName="opacity" values="0.6;0.2;0.6" dur="2s" begin="1s" repeatCount="indefinite"/></path>` 
        },
        { word: "digital", pattern: "d_g_t_l", missing: "iai", 
          hint: `<rect x="40" y="50" width="70" height="50" rx="3" fill="#2d3436" opacity="0.8"/><rect x="45" y="55" width="60" height="35" fill="#0984e3" opacity="0.3"/><text x="52" y="78" font-size="24" fill="#55efc4" font-weight="bold" font-family="monospace">101</text><text x="80" y="78" font-size="24" fill="#fdcb6e" font-weight="bold" font-family="monospace">010</text><circle cx="100" cy="65" r="3" fill="#e74c3c" opacity="0.8"><animate attributeName="opacity" values="0.8;0.2;0.8" dur="1s" repeatCount="indefinite"/></circle><rect x="55" y="92" width="8" height="3" rx="1" fill="#95a5a6" opacity="0.6"/><rect x="67" y="92" width="8" height="3" rx="1" fill="#95a5a6" opacity="0.6"/><rect x="79" y="92" width="8" height="3" rx="1" fill="#95a5a6" opacity="0.6"/>` 
        }
      ]},
      { category: "🔧 Everyday & Academic", emoji: "🔧", words: [
        { word: "flashlight", pattern: "fl_shl_gh_", missing: "ait", 
          hint: `<rect x="55" y="70" width="40" height="50" rx="5" fill="#636e72" opacity="0.8"/><circle cx="75" cy="65" r="15" fill="#f1c40f" opacity="0.3"/><circle cx="75" cy="65" r="10" fill="#fff" opacity="0.4"/><circle cx="75" cy="65" r="6" fill="#fdcb6e" opacity="0.9"><animate attributeName="opacity" values="0.9;0.5;0.9" dur="1.5s" repeatCount="indefinite"/></circle><path d="M 75 50 L 70 35 L 80 35 Z" fill="#f1c40f" opacity="0.6"><animate attributeName="opacity" values="0.6;0.3;0.6" dur="1.5s" repeatCount="indefinite"/></path><path d="M 75 50 L 68 30 L 82 30 Z" fill="#f1c40f" opacity="0.4"><animate attributeName="opacity" values="0.4;0.1;0.4" dur="1.5s" repeatCount="indefinite"/></path><rect x="70" y="115" width="10" height="5" rx="1" fill="#95a5a6" opacity="0.5"/>` 
        },
        { word: "backpack", pattern: "b_ckp_c_", missing: "aak", 
          hint: `<rect x="50" y="50" width="50" height="60" rx="8" fill="#e74c3c" opacity="0.8"/><rect x="55" y="55" width="40" height="15" rx="3" fill="#c0392b" opacity="0.7"/><circle cx="75" cy="80" r="8" fill="#f39c12" opacity="0.7"/><rect x="72" y="90" width="6" height="20" rx="3" fill="#f39c12" opacity="0.6"/><path d="M 60 50 Q 58 40 60 35 L 60 25" stroke="#c0392b" stroke-width="4" fill="none" stroke-linecap="round"/><path d="M 90 50 Q 92 40 90 35 L 90 25" stroke="#c0392b" stroke-width="4" fill="none" stroke-linecap="round"/><rect x="45" y="65" width="8" height="25" rx="4" fill="#c0392b" opacity="0.6"/><rect x="97" y="65" width="8" height="25" rx="4" fill="#c0392b" opacity="0.6"/>` 
        },
        { word: "calculator", pattern: "c_lc_l_t_r", missing: "auao", 
          hint: `<rect x="45" y="35" width="60" height="85" rx="5" fill="#2d3436" opacity="0.9"/><rect x="52" y="42" width="46" height="20" rx="2" fill="#55efc4" opacity="0.4"/><text x="75" y="57" font-size="14" text-anchor="middle" fill="#2d3436" font-weight="bold">12345</text><circle cx="62" cy="75" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="75" cy="75" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="88" cy="75" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="62" cy="88" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="75" cy="88" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="88" cy="88" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="62" cy="101" r="5" fill="#dfe6e9" opacity="0.8"/><circle cx="75" cy="101" r="5" fill="#dfe6e9" opacity="0.8"/><rect x="83" y="96" width="10" height="10" rx="2" fill="#74b9ff" opacity="0.8"/>` 
        },
        { word: "dictionary", pattern: "d_ct__n_ry", missing: "iioa", 
          hint: `<rect x="45" y="40" width="60" height="70" rx="3" fill="#e17055" opacity="0.8"/><rect x="48" y="40" width="54" height="70" fill="#fff" opacity="0.9"/><path d="M 75 40 L 75 110" stroke="#e17055" stroke-width="2" opacity="0.3"/><text x="63" y="62" font-size="12" fill="#2d3436" font-family="serif">A</text><text x="85" y="62" font-size="10" fill="#636e72" font-family="serif">apple</text><text x="63" y="75" font-size="12" fill="#2d3436" font-family="serif">B</text><text x="85" y="75" font-size="10" fill="#636e72" font-family="serif">book</text><text x="63" y="88" font-size="12" fill="#2d3436" font-family="serif">C</text><text x="85" y="88" font-size="10" fill="#636e72" font-family="serif">cat</text><rect x="105" y="45" width="3" height="60" rx="1" fill="#fab1a0" opacity="0.6"/>` 
        },
        { word: "microscope", pattern: "m_cr_sc_p_", missing: "iooe", 
          hint: `<ellipse cx="75" cy="115" rx="35" ry="8" fill="#dfe6e9" opacity="0.5"/><rect x="70" y="85" width="10" height="30" fill="#95a5a6" opacity="0.8"/><circle cx="75" cy="75" r="20" fill="#74b9ff" opacity="0.3" stroke="#0984e3" stroke-width="3"/><circle cx="75" cy="75" r="12" fill="#55efc4" opacity="0.5"/><rect x="70" y="45" width="10" height="15" fill="#636e72" opacity="0.8"/><path d="M 65 45 L 60 35 L 70 35 Z" fill="#7f8c8d" opacity="0.7"/><path d="M 85 45 L 90 35 L 80 35 Z" fill="#7f8c8d" opacity="0.7"/><circle cx="75" cy="30" r="5" fill="#fdcb6e" opacity="0.8"/><rect x="55" y="85" width="8" height="3" rx="1" fill="#636e72" opacity="0.6"/><rect x="87" y="85" width="8" height="3" rx="1" fill="#636e72" opacity="0.6"/>` 
        }
      ]}
    ];

    let currentCategoryIndex = 0;
    let currentWordIndex = 0;
    let score = 0;
    let totalWords = 0;
    let currentAnswers = [];
    let timerInterval = null;
    let remainingSeconds = 10;
    let totalTime = 0;

    vocabularyData.forEach(cat => totalWords += cat.words.length);

    function startTimer() {
      remainingSeconds = 10;
      updateTimerDisplay();
      
      if (timerInterval) {
        clearInterval(timerInterval);
      }
      
      timerInterval = setInterval(() => {
        remainingSeconds--;
        totalTime++;
        updateTimerDisplay();
        
        if (remainingSeconds <= 0) {
          timeUp();
        }
      }, 1000);
    }

    function stopTimer() {
      if (timerInterval) {
        clearInterval(timerInterval);
        timerInterval = null;
      }
    }

    function timeUp() {
      stopTimer();
      const feedbackMessage = document.getElementById('feedbackMessage');
      feedbackMessage.textContent = '⏰ หมดเวลา! ไปข้อถัดไป';
      feedbackMessage.className = 'feedback-message incorrect show';
      
      document.getElementById('checkButton').style.display = 'none';
      document.getElementById('nextButton').style.display = 'inline-block';
      currentAnswers.forEach(input => input.disabled = true);
      
      document.getElementById('scoreValue').textContent = score;
    }

    function updateTimerDisplay() {
      const timeString = `${String(remainingSeconds).padStart(2, '0')}s`;
      const timerElement = document.getElementById('timerValue');
      timerElement.textContent = timeString;
      
      if (remainingSeconds <= 3) {
        timerElement.style.color = '#ff7675';
      } else if (remainingSeconds <= 5) {
        timerElement.style.color = '#fdcb6e';
      } else {
        timerElement.style.color = '#e17055';
      }
    }

    function formatTime(seconds) {
      const minutes = Math.floor(seconds / 60);
      const secs = seconds % 60;
      return `${minutes} นาที ${secs} วินาที`;
    }

    function displayWord() {
      startTimer();
      
      const category = vocabularyData[currentCategoryIndex];
      const wordData = category.words[currentWordIndex];
      
      document.getElementById('categoryEmoji').textContent = category.emoji;
      document.getElementById('categoryName').textContent = category.category.split(' ')[1];
      
      const hintSvg = document.getElementById('hintImage');
      hintSvg.innerHTML = wordData.hint;
      
      const wordLettersContainer = document.getElementById('wordLetters');
      wordLettersContainer.innerHTML = '';
      currentAnswers = [];
      
      let inputIndex = 0;
      for (let i = 0; i < wordData.pattern.length; i++) {
        const letterBox = document.createElement('div');
        letterBox.className = 'letter-box';
        
        if (wordData.pattern[i] === '_') {
          letterBox.classList.add('blank');
          const input = document.createElement('input');
          input.type = 'text';
          input.maxLength = 1;
          input.dataset.index = inputIndex;
          input.dataset.correct = wordData.word[i].toLowerCase();
          input.setAttribute('aria-label', `Letter ${inputIndex + 1}`);
          
          input.addEventListener('input', (e) => {
            e.target.value = e.target.value.toLowerCase();
            const nextInput = wordLettersContainer.querySelector(`input[data-index="${parseInt(e.target.dataset.index) + 1}"]`);
            if (e.target.value && nextInput) {
              nextInput.focus();
            }
          });
          
          letterBox.appendChild(input);
          currentAnswers.push(input);
          inputIndex++;
        } else {
          letterBox.textContent = wordData.pattern[i];
        }
        
        wordLettersContainer.appendChild(letterBox);
      }
      
      if (currentAnswers.length > 0) {
        setTimeout(() => currentAnswers[0].focus(), 100);
      }
      
      document.getElementById('checkButton').style.display = 'inline-block';
      document.getElementById('nextButton').style.display = 'none';
      document.getElementById('feedbackMessage').classList.remove('show', 'correct', 'incorrect');
    }

    function checkAnswer() {
      stopTimer();
      
      let allCorrect = true;
      let allFilled = true;
      
      currentAnswers.forEach(input => {
        const value = input.value ? input.value.trim() : '';
        if (value === '') {
          allFilled = false;
        }
        if (value.toLowerCase() !== input.dataset.correct.toLowerCase()) {
          allCorrect = false;
        }
      });
      
      const feedbackMessage = document.getElementById('feedbackMessage');
      const config = window.elementSdk ? window.elementSdk.config : defaultConfig;
      
      if (!allFilled) {
        feedbackMessage.textContent = '⚠️ กรุณากรอกตัวอักษรให้ครบทุกช่อง';
        feedbackMessage.className = 'feedback-message incorrect show';
        startTimer();
        setTimeout(() => {
          feedbackMessage.classList.remove('show');
        }, 2000);
        return;
      }
      
      if (allCorrect) {
        score++;
        feedbackMessage.textContent = config.correct_message || defaultConfig.correct_message;
        feedbackMessage.className = 'feedback-message correct show';
        document.getElementById('checkButton').style.display = 'none';
        document.getElementById('nextButton').style.display = 'inline-block';
        currentAnswers.forEach(input => {
          input.disabled = true;
        });
        document.getElementById('scoreValue').textContent = score;
      } else {
        feedbackMessage.textContent = config.incorrect_message || defaultConfig.incorrect_message;
        feedbackMessage.className = 'feedback-message incorrect show';
        startTimer();
        
        setTimeout(() => {
          feedbackMessage.classList.remove('show');
        }, 2000);
      }
    }

    function nextWord() {
      currentWordIndex++;
      
      if (currentWordIndex >= vocabularyData[currentCategoryIndex].words.length) {
        currentWordIndex = 0;
        currentCategoryIndex++;
      }
      
      if (currentCategoryIndex >= vocabularyData.length) {
        showCompletion();
        return;
      }
      
      displayWord();
    }

    function showCompletion() {
      stopTimer();
      document.getElementById('gameContent').style.display = 'none';
      document.getElementById('completionScreen').classList.add('show');
      document.getElementById('finalScore').textContent = score;
      document.getElementById('finalTime').textContent = formatTime(totalTime);
    }

    async function onConfigChange(config) {
      document.getElementById('gameTitle').textContent = config.game_title || defaultConfig.game_title;
      document.getElementById('instructions').textContent = config.instructions_text || defaultConfig.instructions_text;
      document.getElementById('checkButton').innerHTML = config.check_button_text || defaultConfig.check_button_text;
      document.getElementById('nextButton').innerHTML = config.next_button_text || defaultConfig.next_button_text;
      document.getElementById('scoreLabel').textContent = config.score_label || defaultConfig.score_label;
    }

    document.getElementById('checkButton').addEventListener('click', checkAnswer);
    document.getElementById('nextButton').addEventListener('click', nextWord);

    document.getElementById('totalQuestions').textContent = totalWords;

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig: defaultConfig,
        onConfigChange: onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [],
          borderables: [],
          fontEditable: undefined,
          fontSizeable: undefined
        }),
        mapToEditPanelValues: (config) => new Map([
          ["game_title", config.game_title || defaultConfig.game_title],
          ["instructions_text", config.instructions_text || defaultConfig.instructions_text],
          ["check_button_text", config.check_button_text || defaultConfig.check_button_text],
          ["next_button_text", config.next_button_text || defaultConfig.next_button_text],
          ["correct_message", config.correct_message || defaultConfig.correct_message],
          ["incorrect_message", config.incorrect_message || defaultConfig.incorrect_message],
          ["score_label", config.score_label || defaultConfig.score_label]
        ])
      });
    }

    displayWord();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a58136ed21e0886',t:'MTc2NDMxNDE0NS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
