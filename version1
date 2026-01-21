<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>The Decision — Systems Prototype</title>
  <style>
    body { font-family: system-ui, Arial; margin: 0; background: #111; color: #eee; }
    .wrap { max-width: 900px; margin: 0 auto; padding: 24px; }
    .hud { display: grid; gap: 12px; margin-bottom: 18px; }
    .meter { background: #222; border-radius: 999px; overflow: hidden; height: 14px; }
    .meter > div { height: 100%; width: 50%; background: #eee; }
    .row { display: grid; grid-template-columns: 120px 1fr 60px; gap: 12px; align-items: center; }
    .card { background: #1b1b1b; border: 1px solid #2a2a2a; border-radius: 16px; padding: 18px; }
    .btns { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 14px; }
    button { background: #eee; color: #111; border: 0; border-radius: 12px; padding: 10px 14px; cursor: pointer; }
    button.secondary { background: transparent; color: #eee; border: 1px solid #444; }
    button:disabled { opacity: .4; cursor: not-allowed; }
    .muted { color: #aaa; }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="hud card">
      <div class="row">
        <div>Money</div>
        <div class="meter"><div id="moneyBar"></div></div>
        <div id="moneyVal">50</div>
      </div>
      <div class="row">
        <div>Relationship</div>
        <div class="meter"><div id="relBar"></div></div>
        <div id="relVal">50</div>
      </div>
      <div class="row">
        <div>Pressure</div>
        <div class="meter"><div id="pressBar"></div></div>
        <div id="pressVal">0</div>
      </div>
      <div class="muted">Outcomes emerge from the meters — not from scripted characters.</div>
    </div>

    <div id="screen" class="card"></div>
  </div>

  <script>
    // -------------------------
    // STATE
    // -------------------------
    const state = {
      money: 55,
      relationship: 60,
      pressure: 0,
      didTalk: false,
      didHide: false,
      truthChoice: null,
      outcome: null
    };

    // -------------------------
    // UI HELPERS
    // -------------------------
    function clamp(n) { return Math.max(0, Math.min(100, n)); }

    function renderHud() {
      const map = [
        ["money", "moneyBar", "moneyVal"],
        ["relationship", "relBar", "relVal"],
        ["pressure", "pressBar", "pressVal"]
      ];
      for (const [key, barId, valId] of map) {
        const v = clamp(state[key]);
        document.getElementById(barId).style.width = v + "%";
        document.getElementById(valId).textContent = v;
      }
    }

    function setScreen(html) {
      document.getElementById("screen").innerHTML = html;
      renderHud();
    }

    // -------------------------
    // GAME FLOW
    // -------------------------
    function intro() {
      setScreen(`
        <h2>Home Situation</h2>
        <p>Money has been tight for months. A housing rule changes everything.</p>
        <div class="btns">
          <button onclick="hub()">Continue</button>
        </div>
      `);
    }

    function hub() {
      // pressure rises over time to force resolution
      setScreen(`
        <h2>Preparation Hub</h2>
        <p class="muted">Take actions to prepare. Pressure rises as time passes.</p>

        <div class="btns">
          <button onclick="qualityTime()">Spend quality time with your kid</button>
          <button onclick="checkFinances()">Reallocate finances</button>
          <button onclick="hideCats()">Hide the cats (avoid confrontation)</button>
          <button class="secondary" onclick="wait()">Wait</button>
        </div>

        <hr style="border:0;border-top:1px solid #2a2a2a;margin:18px 0;">

        <div class="btns">
          <button onclick="forceDecision()">Proceed to ultimatum</button>
        </div>
      `);
    }

    function qualityTime() {
      state.relationship = clamp(state.relationship + 10);
      state.money = clamp(state.money - 5);
      state.pressure = clamp(state.pressure + 10);
      state.didTalk = true;
      hub();
    }

    function checkFinances() {
      state.money = clamp(state.money + 10);
      state.relationship = clamp(state.relationship - 5);
      state.pressure = clamp(state.pressure + 10);
      hub();
    }

    function hideCats() {
      state.didHide = true;
      state.pressure = clamp(state.pressure + 15);
      hub();
    }

    function wait() {
      state.pressure = clamp(state.pressure + 20);
      if (state.pressure >= 100) return revealEvent();
      hub();
    }

    function forceDecision() {
      // if pressure max, event reveals first
      if (state.pressure >= 100) return revealEvent();

      setScreen(`
        <h2>The Ultimatum</h2>
        <p>You must either keep your current rent OR keep the cats. You can’t keep both.</p>

        <div class="btns">
          <button onclick="chooseSaveMoney()">Save money (cats are gone)</button>
          <button onclick="chooseKeepCats()">Keep cats (risk higher rent)</button>
          <button class="secondary" onclick="wait()">Delay</button>
        </div>
      `);
    }

    function chooseSaveMoney() {
      state.money = clamp(state.money + 15);
      state.relationship = clamp(state.relationship - 25);
      state.outcome = "cats_removed_by_player";
      revealTruthLie();
    }

    function chooseKeepCats() {
      // rent risk: random cost
      const rentHit = Math.random() < 0.5 ? 10 : 25;
      state.money = clamp(state.money - rentHit);
      state.relationship = clamp(state.relationship + 10);
      state.outcome = "moved_with_cats";
      revealTruthLie();
    }

    function revealEvent() {
      // delayed moral payoff (system resolves ambiguity)
      const roll = Math.random();
      if (state.didHide && roll < 0.5) state.outcome = "cats_taken_by_system";
      else if (state.didHide && roll >= 0.5) state.outcome = "cats_shelter_by_player";
      else state.outcome = "cats_taken_by_system";

      setScreen(`
        <h2>Something happens…</h2>
        <p>One of the cats is gone.</p>
        <p class="muted">You don’t immediately know why.</p>
        <div class="btns">
          <button onclick="revealTruthLie()">Continue</button>
        </div>
      `);
    }

    function revealTruthLie() {
      setScreen(`
        <h2>Do you tell your kid what happened?</h2>
        <div class="btns">
          <button onclick="truth()">Tell the truth</button>
          <button onclick="lie()">Lie and blame the system</button>
          <button class="secondary" onclick="silent()">Say nothing</button>
        </div>
      `);
    }

    function truth() {
      state.truthChoice = "truth";
      state.relationship = clamp(state.relationship + (state.didTalk ? 5 : -5));
      ending();
    }

    function lie() {
      state.truthChoice = "lie";
      state.relationship = clamp(state.relationship - 15);
      ending();
    }

    function silent() {
      state.truthChoice = "silent";
      state.relationship = clamp(state.relationship - 10);
      ending();
    }

    function ending() {
      // Simple ending logic from meters
      let endingText = "";
      if (state.relationship <= 25) endingText = "Your kid leaves to live with their mother. The bond breaks.";
      else if (state.money <= 25) endingText = "You keep what matters, but money collapses. Stress takes over.";
      else endingText = "You survive — financially and emotionally — but nothing feels the same.";

      setScreen(`
        <h2>Aftermath</h2>
        <p>${endingText}</p>
        <p class="muted">Outcome: <strong>${state.outcome}</strong> • Honesty: <strong>${state.truthChoice}</strong></p>
        <div class="btns">
          <button onclick="resetGame()">Replay</button>
        </div>
      `);
    }

    function resetGame() {
      state.money = 55;
      state.relationship = 60;
      state.pressure = 0;
      state.didTalk = false;
      state.didHide = false;
      state.truthChoice = null;
      state.outcome = null;
      intro();
    }

    // start
    intro();
  </script>
</body>
</html>
