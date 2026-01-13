# https-olivers-games-scary.47.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Nightwatch: Home Alone</title>
  <style>
    body { font-family: Arial, sans-serif; background: #0b0e14; color: #e8ecff; margin: 0; }
    .wrap { max-width: 760px; margin: 0 auto; padding: 18px; }
    .card { background: #121827; border: 1px solid #25304a; border-radius: 14px; padding: 16px; box-shadow: 0 10px 30px rgba(0,0,0,.35); }
    h1 { margin: 0 0 6px; font-size: 26px; }
    .sub { opacity: .85; margin: 0 0 14px; }
    .log { white-space: pre-wrap; line-height: 1.45; font-size: 16px; min-height: 180px; }
    .choices { display: grid; gap: 10px; margin-top: 14px; }
    button {
      cursor: pointer; border: 1px solid #2f3b5a; background: #18213a; color: #e8ecff;
      padding: 12px 12px; border-radius: 12px; font-size: 15px; text-align: left;
    }
    button:hover { background: #1e2a49; }
    .bar { display:flex; gap:10px; flex-wrap:wrap; margin-top:12px; opacity:.9; font-size: 14px; }
    .pill { border:1px solid #2f3b5a; padding:6px 10px; border-radius:999px; background:#0f1422; }
    .small { font-size: 13px; opacity:.85; margin-top: 12px; }
    .danger { color: #ffb4b4; }
    a { color: #9ecbff; }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>Nightwatch: Home Alone</h1>
      <p class="sub">A short, original “home alone” suspense game. Make choices. Get endings.</p>

      <div class="bar">
        <div class="pill">Nerves: <span id="nerves">0</span>/10</div>
        <div class="pill">Time: <span id="time">8:12 PM</span></div>
        <div class="pill">Door: <span id="door">Locked</span></div>
        <div class="pill">Phone: <span id="phone">10%</span></div>
      </div>

      <div id="log" class="log"></div>
      <div id="choices" class="choices"></div>

      <div class="small">
        Tip: If you want sound, you can add it later. For now this is story + choices.<br>
        Want me to add a flashlight, hiding, and creepy sound effects? Tell me.
      </div>
    </div>
  </div>

  <script>
    // --- Simple interactive story engine (original story) ---
    const state = {
      nerves: 0,
      timeIndex: 0,
      doorLocked: true,
      phone: 10,
      sawShadow: false,
      neighborCalled: false,
      hideSpot: null,
      endings: 0
    };

    const times = ["8:12 PM","8:26 PM","8:41 PM","8:59 PM","9:11 PM","9:28 PM","9:44 PM","10:03 PM"];

    const $log = document.getElementById("log");
    const $choices = document.getElementById("choices");
    const $nerves = document.getElementById("nerves");
    const $time = document.getElementById("time");
    const $door = document.getElementById("door");
    const $phone = document.getElementById("phone");

    function ui() {
      $nerves.textContent = state.nerves;
      $time.textContent = times[state.timeIndex] || times[times.length - 1];
      $door.textContent = state.doorLocked ? "Locked" : "Unlocked";
      $phone.textContent = Math.max(0, state.phone) + "%";
    }

    function setText(text) {
      $log.textContent = text.trim();
    }

    function setChoices(arr) {
      $choices.innerHTML = "";
      arr.forEach(c => {
        const b = document.createElement("button");
        b.textContent = c.label;
        b.onclick = c.onClick;
        $choices.appendChild(b);
      });
    }

    function tick(minutes = 1) {
      // minutes just controls how fast time moves in our story
      state.timeIndex = Math.min(times.length - 1, state.timeIndex + 1);
      state.phone = Math.max(0, state.phone - 1);
      ui();
    }

    function addNerves(n) {
      state.nerves = Math.min(10, Math.max(0, state.nerves + n));
      ui();
    }

    function endScreen(title, text) {
      state.endings += 1;
      setText(`${title}\n\n${text}\n\nEnding #${state.endings}`);
      setChoices([
        { label: "🔁 Play again", onClick: start },
        { label: "🏠 Go to homepage (index.html)", onClick: () => location.href = "index.html" }
      ]);
    }

    // --- Scenes ---
    function start() {
      state.nerves = 0;
      state.timeIndex = 0;
      state.doorLocked = true;
      state.phone = 10;
      state.sawShadow = false;
      state.neighborCalled = false;
      state.hideSpot = null;
      ui();

      setText(`
You’re home alone for the night.
Your parent texted: “Back later. Keep the door locked. Pizza money on the counter.”

The house is quiet… like it’s holding its breath.

Your phone buzzes: LOW BATTERY.
      `);

      setChoices([
        { label: "🔒 Double-check the front door lock", onClick: doorCheck },
        { label: "🍕 Order pizza (uses phone battery)", onClick: orderPizza },
        { label: "🎮 Ignore it and play games in your room", onClick: playGames }
      ]);
    }

    function doorCheck() {
      tick();
      setText(`
You walk to the front door. The lock is on.
You press it anyway. Click.

From outside… you hear a soft sound.
Like a shoe scraping the doormat.

You freeze.
      `);
      addNerves(2);

      setChoices([
        { label: "👀 Look through the peephole", onClick: peephole },
        { label: "💡 Turn on the porch light", onClick: porchLight },
        { label: "🚶 Walk away slowly and stay quiet", onClick: stayQuiet }
      ]);
    }

    function orderPizza() {
      tick();
      state.phone = Math.max(0, state.phone - 2);
      ui();
      setText(`
You order a pizza.
The app says: “Arriving in 35–45 minutes.”

As you put your phone down… a notification pops up:
“Unknown device tried to connect to your Wi-Fi.”

That’s weird.
      `);
      addNerves(1);

      setChoices([
        { label: "📶 Unplug the Wi-Fi router", onClick: unplugWifi },
        { label: "🔐 Change the Wi-Fi password", onClick: changeWifi },
        { label: "😅 Ignore it", onClick: ignoreWifi }
      ]);
    }

    function playGames() {
      tick();
      setText(`
You go to your room and play a game with the volume low.
For a while… everything feels normal.

Then you hear it:
A faint *tap tap tap*… on a window.

Not your window.
Downstairs.
      `);
      addNerves(2);

      setChoices([
        { label: "🪟 Go check the downstairs window", onClick: windowCheck },
        { label: "📞 Call a neighbor (uses phone battery)", onClick: callNeighbor },
        { label: "🧍 Stay still and listen carefully", onClick: listenCarefully }
      ]);
    }

    function peephole() {
      tick();
      setText(`
You look through the peephole…

Nothing.

But the porch light is OFF, so it’s hard to see.
Then—something moves at the very edge of the view.

A shadow steps away from the door.

Your heart punches your ribs.
      `);
      state.sawShadow = true;
      addNerves(3);

      setChoices([
        { label: "💡 Turn on the porch light", onClick: porchLight },
        { label: "📞 Call a neighbor", onClick: callNeighbor },
        { label: "🚪 UNLOCK and open the door (bad idea?)", onClick: openDoorBad }
      ]);
    }

    function porchLight() {
      tick();
      setText(`
You flick the porch light switch.

The porch glows.

On the welcome mat… there’s a small object.
A keychain flashlight.
Not yours.

And the doormat looks slightly… moved.
Like someone stood there a moment ago.

The street is empty.
      `);
      addNerves(2);

      setChoices([
        { label: "📞 Call a neighbor", onClick: callNeighbor },
        { label: "🚓 Call for help (uses phone battery)", onClick: callHelp },
        { label: "🔒 Stay inside and move away from windows", onClick: stayInside }
      ]);
    }

    function stayQuiet() {
      tick();
      setText(`
You back away, slow.
You hold your breath.
The house feels louder than ever.

A *ding-dong* suddenly rings out.

The doorbell.

Then a voice, muffled through the door:
“Hello? Delivery.”

But… you didn’t order anything.
      `);
      addNerves(3);

      setChoices([
        { label: "👀 Look through peephole", onClick: peephole },
        { label: "🗣️ Speak through the door: “Who is it?”", onClick: talkThroughDoor },
        { label: "🤫 Don’t answer. Go hide.", onClick: hideNow }
      ]);
    }

    function talkThroughDoor() {
      tick();
      setText(`
You try to sound brave.

“Who is it?”

The voice pauses… too long.

Then: “Wrong house.”

Footsteps hurry away.
But you don’t hear a car.
Just… silence.

Your phone buzzes again:
1% battery.
      `);
      state.phone = Math.min(state.phone, 1);
      ui();
      addNerves(2);

      setChoices([
        { label: "🔌 Find a charger", onClick: findCharger },
        { label: "🫣 Hide right now", onClick: hideNow },
        { label: "🚓 Call for help NOW (might die mid-call)", onClick: callHelp }
      ]);
    }

    function findCharger() {
      tick();
      setText(`
You rush to find a charger.
In the kitchen drawer—where it always is.

But it’s not there.

You look on the counter.
Not there either.

Then you see it:
The charger is on the floor by the back door…
like someone dropped it.

Your back door is supposed to be locked.
      `);
      addNerves(3);

      setChoices([
        { label: "🔒 Check back door lock", onClick: backDoorCheck },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run out the front door to a neighbor", onClick: runToNeighbor }
      ]);
    }

    function backDoorCheck() {
      tick();
      setText(`
You check the back door.

The lock is… UNLOCKED.

You swear you locked it.

Outside, the yard is dark and still.
But you feel like you’re being watched.
      `);
      state.doorLocked = false;
      ui();
      addNerves(4);

      setChoices([
        { label: "🔒 Lock it NOW", onClick: lockBackDoor },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor }
      ]);
    }

    function lockBackDoor() {
      tick();
      state.doorLocked = true;
      ui();
      setText(`
You lock the back door hard. Click.

A second later…
a gentle knock comes from the glass.
*knock… knock… knock*

Right on the back door window.

You can’t see anything outside.
      `);
      addNerves(4);

      setChoices([
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🚓 Call for help (if phone survives)", onClick: callHelp },
        { label: "🗣️ Yell: “GO AWAY!”", onClick: yellGoAway }
      ]);
    }

    function yellGoAway() {
      tick();
      setText(`
You yell, voice cracking: “GO AWAY!”

Silence.

Then… slow footsteps on the patio.
Walking away.

You wait.
And wait.

Your phone dies.
      `);
      state.phone = 0;
      ui();
      addNerves(1);

      setChoices([
        { label: "🫣 Hide and stay quiet", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor }
      ]);
    }

    function unplugWifi() {
      tick();
      setText(`
You unplug the Wi-Fi router.

The little lights go dark.

For a second you feel safer.
Then your smart TV turns on by itself.

A menu opens.
Then closes.

Like someone is clicking around.
      `);
      addNerves(3);

      setChoices([
        { label: "🔌 Unplug the TV too", onClick: unplugTV },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor }
      ]);
    }

    function changeWifi() {
      tick();
      state.phone = Math.max(0, state.phone - 2);
      ui();
      setText(`
You change the Wi-Fi password.
Your phone battery drops fast.

A new notification pops up:
“Unknown device disconnected.”

Good…

Then another pops up right away:
“Unknown device connected.”

How?
      `);
      addNerves(4);

      setChoices([
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🚓 Call for help NOW", onClick: callHelp },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor }
      ]);
    }

    function ignoreWifi() {
      tick();
      setText(`
You ignore it. It’s probably nothing.

But then you notice:
The hallway light is ON.

You don’t remember turning it on.

And you can hear a faint… humming.
From somewhere downstairs.
      `);
      addNerves(3);

      setChoices([
        { label: "💡 Turn on all lights", onClick: lightsOn },
        { label: "🪟 Check downstairs", onClick: windowCheck },
        { label: "🫣 Hide", onClick: hideNow }
      ]);
    }

    function unplugTV() {
      tick();
      setText(`
You unplug the TV.
The screen goes black.

In the reflection of the black screen…
you see movement behind you.

You spin around.

Nothing.

But the back door handle is slowly turning.
      `);
      addNerves(6);

      setChoices([
        { label: "🔒 Lock + push a chair against the door", onClick: barricade },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run out the front door", onClick: runFrontDoor }
      ]);
    }

    function windowCheck() {
      tick();
      setText(`
Downstairs window: curtains half-open.

You peek.

A shape is outside… near the bushes.
You can’t see a face.
Just… someone standing too still.

Then your porch light flickers.

The shape is gone.
      `);
      state.sawShadow = true;
      addNerves(4);

      setChoices([
        { label: "🚓 Call for help", onClick: callHelp },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor }
      ]);
    }

    function listenCarefully() {
      tick();
      setText(`
You stand still and listen.

Tap tap tap…

Then a scrape.
Like something being dragged lightly along the wall.

Then… your kitchen drawer closes.

But you’re upstairs.

You didn’t go downstairs.
      `);
      addNerves(4);

      setChoices([
        { label: "🫣 Hide NOW", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor },
        { label: "🚓 Call for help", onClick: callHelp }
      ]);
    }

    function callNeighbor() {
      tick();
      state.phone = Math.max(0, state.phone - 2);
      ui();
      state.neighborCalled = true;

      if (state.phone === 0) {
        endScreen("📵 Ending: Dead Phone",
          "You try to call… but your phone dies mid-tap.\nYou’re alone with the sounds in the house.\nYou wish you called sooner.");
        return;
      }

      setText(`
You call your neighbor.

They answer: “Hey! Everything okay?”
Your voice comes out tiny: “I think someone is outside.”

Neighbor: “Stay inside. Lock everything. I’m coming to your door right now.”
      `);

      setChoices([
        { label: "🔒 Stay quiet and wait away from windows", onClick: waitForNeighbor },
        { label: "🫣 Hide until they arrive", onClick: hideNow },
        { label: "🏃 Run outside to meet them (risky)", onClick: runOutsideRisky }
      ]);
    }

    function waitForNeighbor() {
      tick();
      setText(`
You move to the middle of the house, away from windows.

Minutes feel like hours.

Then you hear it:
A knock — but it’s the *back* door.

And a whisper:
“Open up.”
      `);
      addNerves(5);

      setChoices([
        { label: "🤫 Don’t move. Don’t answer.", onClick: dontMove },
        { label: "🚓 Call for help", onClick: callHelp },
        { label: "🏃 Run to the front door and get ready to leave", onClick: runFrontDoor }
      ]);
    }

    function dontMove() {
      tick();
      setText(`
You don’t move.

The whisper stops.

Then you hear a second knock—this time the FRONT door.

A familiar voice:
“Hey! It’s me. Neighbor. Are you okay?”

Relief hits you so hard you almost cry.
      `);

      setChoices([
        { label: "✅ Open the front door (safe)", onClick: safeNeighborEnding },
        { label: "👀 Check peephole first", onClick: checkBeforeOpen }
      ]);
    }

    function checkBeforeOpen() {
      tick();
      setText(`
You check the peephole.

It’s your neighbor. For real.

They wave.

You unlock the door.
      `);
      setChoices([{ label: "✅ Leave the house with neighbor", onClick: safeNeighborEnding }]);
    }

    function safeNeighborEnding() {
      endScreen("✅ Ending: Safe Exit",
        "You leave with your neighbor and wait at their house.\nLater, your parent calls the police.\nNobody is found… but the back door shows scratches.\nYou did the smart thing.");
    }

    function callHelp() {
      tick();
      state.phone = Math.max(0, state.phone - 3);
      ui();

      if (state.phone === 0) {
        endScreen("📵 Ending: No Signal, No Battery",
          "You try to call for help, but your phone dies.\nThe house feels bigger. Darker.\nYou realize: silence can be loud.");
        return;
      }

      setText(`
You call for help.
Your voice shakes but you say your address.

Dispatcher: “Stay inside. Lock doors. Officers are on the way.”

Outside, a car door slams.

Not a police car.
Too close.
      `);
      addNerves(4);

      setChoices([
        { label: "🫣 Hide until officers arrive", onClick: hideNow },
        { label: "🔒 Barricade the back door", onClick: barricade },
        { label: "🏃 Run to neighbor (now!)", onClick: runToNeighbor }
      ]);
    }

    function lightsOn() {
      tick();
      setText(`
You turn on every light you can.

The house looks… normal again.

For a moment, you feel silly.

Then you see it:
A muddy footprint. Inside. Near the back door.

One footprint.
Then nothing.
      `);
      addNerves(5);

      setChoices([
        { label: "🫣 Hide", onClick: hideNow },
        { label: "🏃 Run to neighbor", onClick: runToNeighbor },
        { label: "🚓 Call for help", onClick: callHelp }
      ]);
    }

    function hideNow() {
      tick();
      const hideOptions = [
        { spot: "closet", label: "🚪 Hide in a closet" },
        { spot: "bed", label: "🛏️ Hide under the bed" },
        { spot: "bathroom", label: "🛁 Lock yourself in the bathroom" }
      ];

      setText(`
You decide to hide.

Pick a hiding spot.
      `);

      setChoices(hideOptions.map(o => ({
        label: o.label,
        onClick: () => hideSpot(o.spot)
      })));
    }

    function hideSpot(spot) {
      tick();
      state.hideSpot = spot;
      addNerves(1);

      let spotText = "";
      if (spot === "closet") spotText = "You squeeze into the closet and pull the door almost closed.";
      if (spot === "bed") spotText = "You slide under the bed, dust in your nose, trying not to breathe loudly.";
      if (spot === "bathroom") spotText = "You lock the bathroom door and sit in the tub, hugging your knees.";

      setText(`
${spotText}

Downstairs… a slow creak.
Then another.

Someone is walking through your house.

You can hear them pause… like they’re listening for you.
      `);
      addNerves(4);

      setChoices([
        { label: "🤫 Stay completely still", onClick: stillEndingCheck },
        { label: "📢 Make noise to scare them (risky)", onClick: riskyNoise },
        { label: "🏃 Try to escape out a window", onClick: escapeWindow }
      ]);
    }

    function stillEndingCheck() {
      tick();
      // If they called neighbor or help, better odds
      if (state.neighborCalled) {
        endScreen("✅ Ending: Quiet Win",
          "You stay still. Footsteps move away.\nSoon, you hear your neighbor at the door and you’re safe.\nSometimes the best move is no move.");
        return;
      }
      if (state.phone > 0 && state.nerves < 9) {
        endScreen("✅ Ending: They Leave",
          "You stay still for what feels like forever.\nEventually the footsteps stop.\nA door clicks.\nLater, your parent comes home.\nYou never find out who it was.");
        return;
      }
      endScreen("❌ Ending: Too Close",
        "You panic-breathe. The footsteps stop.\nA whisper: “I can hear you.”\nThe closet door (or bedframe) shifts.\nYou scream.\n(You survived… but it was way too close.)");
    }

    function riskyNoise() {
      tick();
      addNerves(2);
      setText(`
You clap loudly and yell: “I CALLED THE POLICE!”

Silence.

Then fast footsteps—running OUT.

A back door slams.

Your hands shake.
      `);
      setChoices([
        { label: "✅ Stay hidden until parent comes home", onClick: () => endScreen("✅ Ending: Brave Bluff", "Your bluff worked.\nYou stayed safe.\nYou didn’t have to see them.\nYou’ll never forget that slam.") },
        { label: "🏃 Run to neighbor now", onClick: runToNeighbor }
      ]);
    }

    function escapeWindow() {
      tick();
      addNerves(2);
      setText(`
You try to open a window quietly.

It sticks.

You push harder.

It pops open with a loud *CLACK*.

Footsteps sprint toward the sound.
      `);
      setChoices([
        { label: "🏃 Jump out and RUN", onClick: runOutsideRisky },
        { label: "🫣 Hide again", onClick: hideNow }
      ]);
    }

    function runOutsideRisky() {
      tick();
      addNerves(3);
      if (state.nerves >= 8) {
        endScreen("❌ Ending: Trip",
          "You run outside… but your legs feel like noodles.\nYou trip on the step.\nYou scream.\nA porch light snaps on across the street and someone shouts.\nYou’re safe… but barely.");
        return;
      }
      endScreen("✅ Ending: Sprint to Safety",
        "You bolt to a neighbor’s house and bang on the door.\nThey let you in and call for help.\nYou did not stay where it was quiet.");
    }

    function runToNeighbor() {
      tick();
      addNerves(2);
      endScreen("✅ Ending: Neighbor Safehouse",
        "You run to a neighbor’s house and they bring you inside.\nWarm lights. Locked doors.\nYou finally breathe.\nThat’s what smart looks like.");
    }

    function runFrontDoor() {
      tick();
      addNerves(2);
      setText(`
You rush to the front door.

You hear a sound behind you—
a gentle *click* from the back door handle.

Not again.

You can leave… or you can barricade.
      `);
      setChoices([
        { label: "🏃 Leave NOW and run to neighbor", onClick: runToNeighbor },
        { label: "🪑 Barricade doors and hide", onClick: barricade }
      ]);
    }

    function barricade() {
      tick();
      addNerves(2);
      setText(`
You drag a chair to the door and wedge it.
You lock everything you can.

You hide in a corner of the hallway.
No windows. No views.

Minutes later… you hear sirens far away.

Then closer.

Then a loud knock:
“Police! Anyone inside?”

You almost cry from relief.
      `);
      setChoices([
        { label: "✅ Answer and go to the door", onClick: () => endScreen("✅ Ending: Help Arrived", "You did the right things: locked up, stayed hidden, got help.\nThe officers check the yard.\nThey find a dropped flashlight by the bushes.\nYou’re safe.") },
        { label: "👀 Check first (smart)", onClick: () => endScreen("✅ Ending: Careful and Safe", "You check before opening.\nIt’s really the police.\nThey walk you through what to do next.\nYou’re safe—and you were careful.") }
      ]);
    }

    function stayInside() {
      tick();
      setText(`
You stay inside and move away from windows.

The house is still.
Too still.

Then your fridge makes an ice-drop sound.
You nearly jump out of your skin.

You can either: get help, or hide.
      `);
      addNerves(2);

      setChoices([
        { label: "🚓 Call for help", onClick: callHelp },
        { label: "🫣 Hide", onClick: hideNow },
        { label: "📞 Call neighbor", onClick: callNeighbor }
      ]);
    }

    function openDoorBad() {
      tick();
      addNerves(3);
      endScreen("❌ Ending: Don’t Do That",
        "You unlock the door.\nA cold draft hits your face.\nNobody is there…\nBut the keychain flashlight is suddenly gone.\nYou lock the door again and realize:\nThat was a terrible idea.");
    }

    // Start game
    start();
  </script>
</body>
</html>
