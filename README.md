# virtual-clock-
it is a watch which is used for study and also to set the timer for any work 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>My Countdown Timer</title>
<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: 'Segoe UI', sans-serif;
    background: #111;
    color: #fff;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  h1 {
    font-size: 6rem;
    letter-spacing: 2px;
    margin: 10px 0 30px;
    font-weight: 700;
  }
  .inputs {
    margin-bottom: 20px;
  }
  input {
    width: 80px;
    font-size: 1.4rem;
    padding: 6px;
    margin: 0 5px;
    border-radius: 8px;
    border: none;
    text-align: center;
    outline: none;
  }
  button {
    margin: 5px;
    padding: 12px 28px;
    font-size: 1.2rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    background: #007bff;
    color: white;
    transition: background 0.2s ease;
  }
  button:hover { background: #0056b3; }
  button:disabled { background: #555; cursor: not-allowed; }
  .footer {
    position: absolute;
    bottom: 15px;
    font-size: 0.9rem;
    opacity: 0.7;
  }
</style>
</head>
<body>

<h1 id="timer">00:00:00</h1>

<div class="inputs">
  <input type="number" id="hours" value="0" min="0" max="99"> h
  <input type="number" id="minutes" value="1" min="0" max="59"> m
  <input type="number" id="seconds" value="0" min="0" max="59"> s
</div>

<div>
  <button id="startBtn" onclick="startTimer()">Start</button>
  <button id="pauseBtn" onclick="pauseTimer()" disabled>Pause</button>
  <button id="resetBtn" onclick="resetTimer()">Reset</button>
</div>

<audio id="alarm"></audio>

<div class="footer">Made by Rajput G</div>

<script>
  // Elements
  const timerDisplay = document.getElementById('timer');
  const alarm = document.getElementById('alarm');
  const startBtn = document.getElementById('startBtn');
  const pauseBtn = document.getElementById('pauseBtn');

  let countdown;
  let remaining = 0;
  let running = false;

  // Default sound
  let alarmSound = "https://actions.google.com/sounds/v1/alarms/xylophone_alarm.ogg";
  let loopSound = true;

  // ----------- Functions -----------
  function updateDisplay() {
    let hrs = Math.floor(remaining / 3600);
    let mins = Math.floor((remaining % 3600) / 60);
    let secs = remaining % 60;
    timerDisplay.textContent =
      `${hrs.toString().padStart(2,'0')}:${mins.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
  }

  function startTimer() {
    if (!running) {
      let h = parseInt(document.getElementById('hours').value) || 0;
      let m = parseInt(document.getElementById('minutes').value) || 0;
      let s = parseInt(document.getElementById('seconds').value) || 0;
      if (remaining === 0) remaining = h*3600 + m*60 + s;
      if (remaining <= 0) return;

      running = true;
      startBtn.disabled = true;
      pauseBtn.disabled = false;

      countdown = setInterval(() => {
        if (remaining > 0) {
          remaining--;
          updateDisplay();
        } else {
          clearInterval(countdown);
          running = false;
          startBtn.disabled = false;
          pauseBtn.disabled = true;
          playAlarm();
        }
      }, 1000);
    }
  }

  function pauseTimer() {
    if (running) {
      clearInterval(countdown);
      running = false;
      startBtn.disabled = false;
      pauseBtn.disabled = true;
      alarm.pause();
    }
  }

  function resetTimer() {
    clearInterval(countdown);
    running = false;
    remaining = 0;
    updateDisplay();
    startBtn.disabled = false;
    pauseBtn.disabled = true;
    alarm.pause();
    alarm.currentTime = 0;
  }

  function playAlarm() {
    alarm.src = alarmSound;
    alarm.loop = loopSound;
    alarm.play();
  }

  // ----------- URL Parameters -----------
  function applyURLParams() {
    const params = new URLSearchParams(window.location.search);

    if (params.has("countdown")) {
      const parts = params.get("countdown").split(":");
      const h = parseInt(parts[0]) || 0;
      const m = parseInt(parts[1]) || 0;
      const s = parseInt(parts[2]) || 0;
      document.getElementById('hours').value = h;
      document.getElementById('minutes').value = m;
      document.getElementById('seconds').value = s;
      remaining = h*3600 + m*60 + s;
    }

    if (params.has("sound")) {
      const s = params.get("sound");
      if (s === "xylophone") {
        alarmSound = "https://actions.google.com/sounds/v1/alarms/xylophone_alarm.ogg";
      } else if (s === "beep") {
        alarmSound = "https://actions.google.com/sounds/v1/alarms/beep_short.ogg";
      }
    }

    if (params.has("loop")) {
      loopSound = params.get("loop") === "1";
    }

    updateDisplay();
  }

  // Initialize
  applyURLParams();
  updateDisplay();
</script>

</body>
</html>
