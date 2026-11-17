<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>SAFFRON-94 // NODE 1 [503-555-1994]</title>  
    <style>  
        @import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');  
  
        * {  
            box-sizing: border-box;  
            margin: 0;  
            padding: 0;  
        }  
  
        body {  
            background-color: #050505;  
            color: #00ff66;  
            font-family: 'VT323', monospace;  
            font-size: 20px;  
            line-height: 1.2;  
            height: 100vh;  
            display: flex;  
            justify-content: center;  
            align-items: center;  
            overflow: hidden;  
        }  
  
        /* CRT Screen Effect */  
        .crt {  
            width: 100%;  
            max-width: 900px;  
            height: 90vh;  
            max-height: 700px;  
            background: #0a0f0a;  
            border: 4px ridge #224422;  
            padding: 20px;  
            display: flex;  
            flex-direction: column;  
            position: relative;  
            box-shadow: 0 0 40px rgba(0, 255, 102, 0.15), inset 0 0 60px rgba(0, 0, 0, 0.8);  
            overflow: hidden;  
        }  
  
        .crt::before {  
            content: " ";  
            display: block;  
            position: absolute;  
            top: 0; left: 0; bottom: 0; right: 0;  
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));  
            z-index: 20;  
            background-size: 100% 3px, 3px 100%;  
            pointer-events: none;  
        }  
  
        .crt::after {  
            content: " ";  
            display: block;  
            position: absolute;  
            top: 0; left: 0; bottom: 0; right: 0;  
            background: radial-gradient(circle, rgba(0,0,0,0) 60%, rgba(0,0,0,0.6) 100%);  
            pointer-events: none;  
            z-index: 21;  
        }  
  
        #screen {  
            flex: 1;  
            overflow-y: auto;  
            white-space: pre-wrap;  
            word-wrap: break-word;  
            padding-right: 10px;  
            margin-bottom: 10px;  
            z-index: 10;  
        }  
  
        #screen::-webkit-scrollbar {  
            width: 8px;  
        }  
        #screen::-webkit-scrollbar-track {  
            background: #050a05;  
        }  
        #screen::-webkit-scrollbar-thumb {  
            background: #113311;  
        }  
  
        .input-line {  
            display: flex;  
            align-items: center;  
            z-index: 10;  
            border-top: 1px dashed #114411;  
            padding-top: 10px;  
        }  
  
        .prompt {  
            color: #ffb000;  
            margin-right: 8px;  
            font-weight: bold;  
        }  
  
        #command-input {  
            flex: 1;  
            background: transparent;  
            border: none;  
            color: #00ff66;  
            font-family: 'VT323', monospace;  
            font-size: 20px;  
            outline: none;  
        }  
  
        .ansi-cyan { color: #00ffff; }  
        .ansi-yellow { color: #ffff00; }  
        .ansi-magenta { color: #ff00ff; }  
        .ansi-white { color: #ffffff; }  
        .ansi-bright-green { color: #55ff55; text-shadow: 0 0 8px rgba(0,255,100,0.6); }  
        .ansi-red { color: #ff5555; }  
  
        #dial-overlay {  
            position: absolute;  
            top: 0; left: 0; width: 100%; height: 100%;  
            background: #000;  
            display: flex;  
            flex-direction: column;  
            justify-content: center;  
            align-items: center;  
            z-index: 50;  
            transition: opacity 1s ease;  
        }  
  
        .dial-btn {  
            background: #113311;  
            color: #00ff66;  
            border: 2px solid #00ff66;  
            padding: 12px 24px;  
            font-family: 'VT323', monospace;  
            font-size: 24px;  
            cursor: pointer;  
            box-shadow: 0 0 15px rgba(0,255,100,0.3);  
            margin-top: 20px;  
        }  
  
        .dial-btn:hover {  
            background: #00ff66;  
            color: #000;  
        }  
    </style>  
</head>  
<body>  
  
<div class="crt" id="crt-container">  
    <div id="dial-overlay">  
        <div style="color: #ffb000; font-size: 28px; margin-bottom: 10px; text-align: center;">SAFFRON-94 // REMOTE NODE</div>  
        <div style="color: #00ff66; font-size: 18px; text-align: center;">SPEED: 14400 bps | PROTOCOL: ZMODEM | RIG: ACTIVE</div>  
        <button class="dial-btn" onclick="startSession()">[ DIAL IN ]</button>  
    </div>  
  
    <div id="screen"></div>  
  
    <div class="input-line">  
        <span class="prompt">SAFFRON&gt;</span>  
        <input type="text" id="command-input" autofocus autocomplete="off" spellcheck="false" onkeydown="handleInput(event)" disabled>  
    </div>  
</div>  
  
<script>  
    const screen = document.getElementById('screen');  
    const input = document.getElementById('command-input');  
    const overlay = sessionStorage.getItem('dialed') ? document.getElementById('dial-overlay') : document.getElementById('dial-overlay');  
  
    if (sessionStorage.getItem('dialed')) {  
        overlay.style.display = 'none';  
        input.disabled = false;  
        bootBBS();  
    }  
  
    function playModemSound() {  
        const ctx = new (window.AudioContext || window.webkitAudioContext)();  
          
        // Helper to play tone  
        function playTone(freq, start, duration, type='sine', gainVal=0.1) {  
            try {  
                const osc = ctx.createOscillator();  
                const gain = ctx.createGain();  
                osc.type = type;  
                osc.frequency.value = freq;  
                gain.gain.setValueAtTime(gainVal, ctx.currentTime + start);  
                gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + start + duration);  
                osc.connect(gain);  
                gain.connect(ctx.destination);  
                osc.start(ctx.currentTime + start);  
                osc.stop(ctx.currentTime + start + duration);  
            } catch(e) {}  
        }  
  
        // Helper for noise (static hiss)  
        function playNoise(start, duration, gainVal=0.05) {  
            try {  
                const bufferSize = ctx.sampleRate * duration;  
                const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);  
                const data = buffer.getChannelData(0);  
                for (let i = 0; i < bufferSize; i++) {  
                    data[i] = Math.random() * 2 - 1;  
                }  
                const noise = ctx.createBufferSource();  
                noise.buffer = buffer;  
                const gain = ctx.createGain();  
                gain.gain.setValueAtTime(gainVal, ctx.currentTime + start);  
                gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + start + duration);  
                noise.connect(gain);  
                gain.connect(ctx.destination);  
                noise.start(ctx.currentTime + start);  
            } catch(e) {}  
        }  
  
        // Dialing beeps (DTMF)  
        playTone(1209, 0.1, 0.1); playTone(697, 0.1, 0.1);  
        playTone(1336, 0.3, 0.1); playTone(770, 0.3, 0.1);  
        playTone(1477, 0.5, 0.1); playTone(697, 0.5, 0.1);  
          
        // Handshake squeals & negotiation tones  
        let time = 1.2;  
        playTone(2400, time, 0.8, 'sine', 0.15);  
        time += 0.9;  
        playTone(1200, time, 0.6, 'sawtooth', 0.1);  
        time += 0.7;  
        // Answer tone  
        playTone(2100, time, 1.5, 'sine', 0.2);  
          
        // V.32bis / V.42bis chaotic high speed training noise bursts  
        for(let i=0; i<15; i++) {  
            let t = time + 1.6 + (i * 0.12);  
            playTone(800 + Math.random()*1800, t, 0.1, 'sawtooth', 0.08);  
            if(i % 3 === 0) playNoise(t, 0.1, 0.03);  
        }  
    }  
  
    function startSession() {  
        playModemSound();  
        overlay.style.opacity = '0';  
        setTimeout(() => {  
            overlay.style.display = 'none';  
            input.disabled = false;  
            sessionStorage.setItem('dialed', 'true');  
            bootBBS();  
        }, 3500);  
    }  
  
    function println(html) {  
        screen.innerHTML += html + '\n';  
        screen.scrollTop = screen.scrollHeight;  
    }  
  
    function bootBBS() {  
        println(`<span class="ansi-cyan">CONNECT 14400/ARQ/V32B/V42B</span>`);  
        println(`<span class="ansi-yellow">Initializing CP437 terminal driver... OK</span>\n`);  
          
        println(`<span class="ansi-bright-green">  
  ███████╗ █████╗ ███████╗███████╗██████╗  ██████╗  ███╗   ██╗      ██╗ ██████╗  
  ██╔════╝██╔══██╗██╔════╝██╔════╝██╔══██╗██╔═══██╗ ████╗  ██║     ███║██╔════╝  
  ███████╗███████║█████╗  █████╗  ██████╔╝██║   ██║ ██╔██╗ ██║     ╚██║╚█████╗   
  ╚════██║██╔══██║██╔══╝  ██╔══╝  ██╔══██╗██║   ██║ ██║╚██╗██║      ██║ ╚═══██╗  
  ███████║██║  ██║██║     ██║     ██║  ██║╚██████╔╝ ██║ ╚████║      ██║██████╔╝  
  ╚══════╝╚═╝  ╚═╝╚═╝     ╚═╝     ╚═╝  ╚═╝ ╚═════╝  ╚═╝  ╚═══╝      ╚═╝╚═════╝ </span>`);  
          
        println(`<span class="ansi-white">======================================================================</span>`);  
        println(`<span class="ansi-magenta">  WELCOME TO SAFFRON V9 // OFFSITE THINK TANK & DREAMSCAPING HUB</span>`);  
        println(`<span class="ansi-white">======================================================================</span>`);  
        println(`Type <span class="ansi-yellow">HELP</span> to view available commands, or <span class="ansi-yellow">MAZE</span> to enter the Right-Turn Labyrinth.\n`);  
    }  
  
    let inMaze = false;  
    let mazeStep = 0;  
  
    const mazeNodes = [  
        {  
            desc: "You stand at a glowing 16-color ANSI intersection. Corridors branch left and right.",  
            correct: "right",  
            hint: "A faint darkwave bassline echoes from the right corridor."  
        },  
        {  
            desc: "You face a stone wall with an ACiD Productions logo carved into the block art. Paths lead North and Right.",  
            correct: "right",  
            hint: "Never fight the walls. Turn right."  
        },  
        {  
            desc: "You enter a retro BBS sysop lounge. A cold pilsner sits on the terminal desk. Exits are Left and Right.",  
            correct: "right",  
            hint: "The exit door handle is on your right hand side."  
        },  
        {  
            desc: "The labyrinth hums with 14,400 baud static. Final junction ahead: Left into the firewall, Right into sanctuary.",  
            correct: "right",  
            hint: "Abundant care dictates the right turn."  
        }  
    ];  
  
    function handleInput(e) {  
        if (e.key === 'Enter') {  
            const cmd = input.value.trim().toLowerCase();  
            println(`<span class="prompt">SAFFRON&gt;</span> <span class="ansi-white">${input.value}</span>`);  
            input.value = '';  
  
            if (inMaze) {  
                handleMazeInput(cmd);  
                return;  
            }  
  
            switch(cmd) {  
                case 'help':  
                    println(`<span class="ansi-yellow">AVAILABLE COMMANDS:</span>`);  
                    println(`  <span class="ansi-cyan">ART</span>     - View 16-color ANSI & ASCII art archive info (16colo.rs)`);  
                    println(`  <span class="ansi-cyan">DOORS</span>   - Launch BBS door games matrix (LORD, TradeWars, Usurper)`);  
                    println(`  <span class="ansi-cyan">MAZE</span>    - Enter the Right-Turn-Only Labyrinth (5-min mental reset)`);  
                    println(`  <span class="ansi-cyan">STATUS</span>  - Display Variable 1 & Variable 3 architectural status`);  
                    println(`  <span class="ansi-cyan">ROFL</span>    - Summon the roflcopter`);  
                    println(`  <span class="ansi-cyan">CLEAR</span>   - Clear terminal buffer`);  
                    println(`  <span class="ansi-cyan">EXIT</span>    - Hang up carrier signal\n`);  
                    break;  
                case 'art':  
                    println(`<span class="ansi-cyan">[ARCHIVE]</span> Connecting to 16colo.rs node...`);  
                    println(`Active groups loaded: ACiD Productions, iCE Advertisements, Mistigris.`);  
                    println(`CP437 block shading verified. SAUCE metadata active.\n`);  
                    break;  
                case 'doors':  
                    println(`<span class="ansi-cyan">[DOOR GAMES]</span>`);  
                    println(`* Legend of the Red Dragon (LORD) - Tavern status: Innkeeper Violet is pouring.`);  
                    println(`* TradeWars 2002 - Warp sectors mapped. Hold status: Secure.`);  
                    println(`* Usurper - Dark dungeon crawler active.\n`);  
                    break;  
                case 'maze':  
                    inMaze = true;  
                    mazeStep = 0;  
                    println(`<span class="ansi-magenta">*** ENTERING RIGHT-TURN-ONLY LABYRINTH ***</span>`);  
                    println(`Axiom: Never fight the walls. Type <span class="ansi-yellow">RIGHT</span> at each junction.\n`);  
                    println(mazeNodes[0].desc);  
                    break;  
                case 'status':  
                    println(`<span class="ansi-yellow">[SYSTEM STATUS]</span>`);  
                    println(`- Compliance & Automation: Strategy B active (Timeline insulation).`);  
                    println(`- Sanctuary & Recovery: Alcohol markers, darkwave vocals, right-turn labyrinth protocol.`);  
                    println(`- Chief Inspector Rosie: Vetted feline security online.\n`);  
                    break;  
                case 'rofl':  
                    println(`<span class="ansi-bright-green">  
              ROFL:ROFL  
           L   O   L  
      ROFL           ROFL  
           L   O   L  
              OFL:OFL  
                 I  
              ROFLCOPTER  
             //   \\  
</span>`);  
                    break;  
                case 'clear':  
                    screen.innerHTML = '';  
                    break;  
                case 'exit':  
                    println(`<span class="ansi-red">NO CARRIER</span>`);  
                    input.disabled = true;  
                    setTimeout(() => {  
                        sessionStorage.removeItem('dialed');  
                        location.reload();  
                    }, 1500);  
                    break;  
                case '':  
                    break;  
                default:  
                    println(`Command not recognized. Type <span class="ansi-yellow">HELP</span> for command list.\n`);  
            }  
        }  
    }  
  
    function handleMazeInput(cmd) {  
        if (cmd === 'quit' || cmd === 'exit') {  
            inMaze = false;  
            println(`Exiting labyrinth back to main BBS prompt.\n`);  
            return;  
        }  
  
        if (cmd === mazeNodes[mazeStep].correct) {  
            mazeStep++;  
            if (mazeStep < mazeNodes.length) {  
                println(`<span class="ansi-bright-green">&gt;&gt; Clean right turn executed. Sector cleared.</span>`);  
                println(mazeNodes[mazeStep].desc + '\n');  
            } else {  
                println(`<span class="ansi-bright-green">*** LABYRINTH CONQUERED ***</span>`);  
                println(`You successfully navigated the coordinate grid using strictly right turns.`);  
                println(`Sanctuary restored. Returning to BBS prompt.\n`);  
                inMaze = false;  
            }  
        } else if (cmd === 'left' || cmd === 'straight' || cmd === 'back') {  
            println(`<span class="ansi-red">&gt;&gt; BUZZER: Wall collision! The axiom is Right-Turn-Only. Try again.</span>`);  
            println(`<span class="ansi-yellow">Hint:</span> ${mazeNodes[mazeStep].hint}\n`);  
        } else {  
            println(`Invalid vector. Type <span class="ansi-yellow">RIGHT</span> (or <span class="ansi-yellow">QUIT</span> to exit).\n`);  
        }  
    }  
</script>  
  
</body>  
</html>  
