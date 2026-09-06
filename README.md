<!DOCTYPE html>

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>The Fifteenth Question</title>

<style>
*{
    box-sizing:border-box;
}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#050505;
    color:#eee;
    font-family:Arial,Helvetica,sans-serif;
}

body{
    display:flex;
    align-items:center;
    justify-content:center;
}

#game{
    width:min(100vw,1000px);
    height:min(100vh,700px);
    position:relative;
    overflow:hidden;
    background:#080808;
    border:2px solid #333;
    box-shadow:0 0 50px #000;
}

/* SCREENS */

.screen{
    position:absolute;
    inset:0;
    display:none;
    align-items:center;
    justify-content:center;
}

.screen.active{
    display:flex;
}

#menu{
    flex-direction:column;
    text-align:center;
    background:
        radial-gradient(circle at center,#171717 0%,#050505 70%);
}

#menu h1{
    font-size:clamp(35px,7vw,80px);
    letter-spacing:8px;
    margin:0;
    text-shadow:0 0 20px #666;
}

#menu p{
    color:#888;
    letter-spacing:3px;
}

button{
    font-family:inherit;
    background:#111;
    color:#eee;
    border:1px solid #555;
    padding:14px 30px;
    margin:8px;
    cursor:pointer;
    font-size:16px;
    transition:.2s;
}

button:hover{
    background:#eee;
    color:#111;
    transform:scale(1.04);
}

/* QUIZ */

#quizScreen{
    background:#111;
    flex-direction:column;
}

#quizHeader{
    position:absolute;
    top:20px;
    left:20px;
    right:20px;
    display:flex;
    justify-content:space-between;
    color:#888;
}

#questionBox{
    width:min(85%,700px);
    text-align:center;
}

#questionNumber{
    color:#777;
    margin-bottom:25px;
    letter-spacing:4px;
}

#question{
    font-size:clamp(22px,4vw,38px);
    line-height:1.4;
    min-height:120px;
}

#options{
    display:grid;
    grid-template-columns:1fr 1fr;
    gap:12px;
    margin-top:30px;
}

.option{
    min-height:65px;
}

.glitch{
    animation:glitch .12s infinite;
}

@keyframes glitch{
    0%{transform:translate(0)}
    25%{transform:translate(-4px,2px)}
    50%{transform:translate(4px,-2px)}
    75%{transform:translate(-2px,-1px)}
    100%{transform:translate(0)}
}

.distorted{
    color:#cfcfcf;
    text-shadow:
        3px 0 #600,
        -3px 0 #066;
}

/* BLACK MESSAGE */

#messageScreen{
    background:#000;
    flex-direction:column;
}

#messageText{
    font-size:clamp(30px,7vw,90px);
    letter-spacing:12px;
    animation:flicker 1.5s infinite;
}

@keyframes flicker{
    0%,100%{opacity:1}
    50%{opacity:.2}
    55%{opacity:1}
    70%{opacity:.4}
}

/* FOREST */

#forestScreen{
    background:#0a120c;
}

canvas{
    width:100%;
    height:100%;
    image-rendering:pixelated;
}

#hud{
    position:absolute;
    inset:0;
    pointer-events:none;
}

#objective{
    position:absolute;
    top:15px;
    left:50%;
    transform:translateX(-50%);
    background:#090909cc;
    border:1px solid #555;
    padding:12px 22px;
    text-align:center;
    min-width:280px;
    letter-spacing:2px;
}

#timer{
    position:absolute;
    top:20px;
    right:20px;
    font-size:30px;
    color:#ddd;
}

#inventory{
    position:absolute;
    bottom:20px;
    left:20px;
    background:#000b;
    padding:10px;
    border:1px solid #444;
}

#dialogue{
    position:absolute;
    bottom:35px;
    left:50%;
    transform:translateX(-50%);
    width:min(80%,750px);
    background:#000d;
    border:1px solid #555;
    padding:20px;
    text-align:center;
    display:none;
    font-size:20px;
}

/* END SCREEN */

#endingScreen{
    background:#050505;
    flex-direction:column;
    text-align:center;
    padding:40px;
}

#endingTitle{
    font-size:clamp(35px,7vw,80px);
    margin-bottom:20px;
}

#endingText{
    max-width:700px;
    line-height:1.8;
    color:#aaa;
}

/* CHOICE */

#choiceScreen{
    background:#050505;
    flex-direction:column;
    text-align:center;
}

#choiceScreen h2{
    font-size:35px;
}

#choiceButtons{
    margin-top:30px;
}

#flash{
    position:absolute;
    inset:0;
    background:white;
    opacity:0;
    pointer-events:none;
}

.danger{
    color:#c66;
}
</style>

</head>

<body>

<div id="game">

<!-- MENU -->

<div id="menu" class="screen active">
    <h1>THE FIFTEENTH QUESTION</h1>
    <p>ANIMAL GUESSING GAME</p>

```
<button onclick="startGame()">START GAME</button>

<p style="font-size:12px;margin-top:30px">
    Use WASD or Arrow Keys in the forest.
</p>
```

</div>

<!-- QUIZ -->

<div id="quizScreen" class="screen">

```
<div id="quizHeader">
    <div>ANIMAL QUIZ</div>
    <div id="progress">QUESTION 1 / 15</div>
</div>

<div id="questionBox">

    <div id="questionNumber">
        QUESTION 1
    </div>

    <div id="question">
        Loading...
    </div>

    <div id="options"></div>

</div>
```

</div>

<!-- MESSAGE -->

<div id="messageScreen" class="screen">
    <div id="messageText">SAVE ME</div>
</div>

<!-- FOREST -->

<div id="forestScreen" class="screen">

```
<canvas id="canvas" width="1000" height="700"></canvas>

<div id="hud">

    <div id="objective">
        OBJECTIVE: FIND ROBERT
    </div>

    <div id="timer">
        05:00
    </div>

    <div id="inventory">
        CLUES: <span id="clueCount">0</span>/4<br>
        AXE: <span id="axeStatus">NO</span><br>
        FLASHLIGHT: <span id="flashStatus">ON</span>
    </div>

    <div id="dialogue"></div>

</div>
```

</div>

<!-- CHOICE -->

<div id="choiceScreen" class="screen">

```
<h2>You made it home.</h2>

<p>What will you do with what you discovered?</p>

<div id="choiceButtons">
    <button onclick="endingFailedHero()">
        TELL THE POLICE
    </button>

    <button onclick="endingSilence()">
        KEEP IT SECRET
    </button>
</div>
```

</div>

<!-- ENDING -->

<div id="endingScreen" class="screen">

```
<div id="endingTitle"></div>

<div id="endingText"></div>

<button onclick="restartGame()">
    PLAY AGAIN
</button>
```

</div>

<div id="flash"></div>

</div>

<script>

/* =====================================================
   GAME DATA
===================================================== */

const questions = [

{
q:"I have a long neck and eat leaves from tall trees. What am I?",
options:["Lion","Giraffe","Tiger","Wolf"],
answer:"Giraffe"
},

{
q:"I am known as man's best friend. What am I?",
options:["Dog","Cat","Horse","Bear"],
answer:"Dog"
},

{
q:"I have black and white stripes and look like a horse.",
options:["Zebra","Tiger","Panda","Wolf"],
answer:"Zebra"
},

{
q:"I am the largest animal on Earth.",
options:["Elephant","Blue Whale","Giraffe","Shark"],
answer:"Blue Whale"
},

{
q:"I sleep during the day and hunt at night. What am I?",
options:["Owl","Cow","Rabbit","Horse"],
answer:"Owl"
},

{
q:"I live deep inside forests and sometimes watch without being seen.",
options:["Deer","Wolf","Bear","Fox"],
answer:"Deer"
},

{
q:"What animal follows another creature without making a sound?",
options:["Fox","Predator","Rabbit","Horse"],
answer:"Predator"
},

{
q:"What animal knows where you live?",
options:["Dog","Bird","Cat","Something else"],
answer:"Something else"
},

{
q:"What animal waits until you are alone?",
options:["Wolf","Tiger","Fox","I don't know"],
answer:"I don't know"
},

{
q:"What animal has been standing between the trees?",
options:["Deer","Bear","Wolf","I didn't see anything"],
answer:"I didn't see anything"
},

{
q:"Why do you keep hearing footsteps when nobody is there?",
options:["Wind","Animals","Someone","I don't know"],
answer:"Someone"
},

{
q:"Which animal knows your name?",
options:["Dog","Parrot","Nobody","Something else"],
answer:"Something else"
},

{
q:"What was that sound outside your window?",
options:["A cat","Wind","A branch","Someone"],
answer:"Someone"
},

{
q:"Why are you still playing?",
options:["Because it's a game","I don't know","Someone told me to","Robert"],
answer:"Robert"
},

{
q:"DO YOU RECOGNIZE THIS?",
options:["ROBERT"],
answer:"ROBERT"
}

];


/* =====================================================
   SCREEN SYSTEM
===================================================== */

function showScreen(id){

    document.querySelectorAll(".screen").forEach(screen=>{
        screen.classList.remove("active");
    });

    document.getElementById(id).classList.add("active");

}


/* =====================================================
   QUIZ
===================================================== */

let currentQuestion = 0;


function startGame(){

    currentQuestion=0;

    showScreen("quizScreen");

    loadQuestion();

}


function loadQuestion(){

    let data=questions[currentQuestion];

    document.getElementById("progress").textContent=
        "QUESTION "+(currentQuestion+1)+" / 15";

    document.getElementById("questionNumber").textContent=
        "QUESTION "+(currentQuestion+1);

    document.getElementById("question").textContent=data.q;

    let options=document.getElementById("options");

    options.innerHTML="";

    data.options.forEach(option=>{

        let button=document.createElement("button");

        button.className="option";

        button.textContent=option;

        button.onclick=()=>answerQuestion(option);

        options.appendChild(button);

    });


    if(currentQuestion>=7){

        document.getElementById("quizScreen")
        .classList.add("glitch");

    }else{

        document.getElementById("quizScreen")
        .classList.remove("glitch");

    }


    if(currentQuestion>=10){

        document.getElementById("question")
        .classList.add("distorted");

    }

}


function answerQuestion(answer){

    let data=questions[currentQuestion];

    if(answer!==data.answer){

        flashScreen();

        document.getElementById("question").textContent=
            "WRONG.";

        setTimeout(loadQuestion,700);

        return;

    }


    currentQuestion++;

    if(currentQuestion>=questions.length){

        robertReveal();

    }else{

        loadQuestion();

    }

}


function flashScreen(){

    let flash=document.getElementById("flash");

    flash.style.opacity=".8";

    setTimeout(()=>{
        flash.style.opacity="0";
    },100);

}


/* =====================================================
   ROBERT REVEAL
===================================================== */

function robertReveal(){

    showScreen("messageScreen");

    document.getElementById("messageText").textContent=
        "ROB... IS THAT YOU?";

    setTimeout(()=>{

        document.getElementById("messageText").textContent=
            "SAVE ME";

    },2500);


    setTimeout(()=>{

        startForest();

    },5000);

}


/* =====================================================
   FOREST GAME
===================================================== */

const canvas=document.getElementById("canvas");

const ctx=canvas.getContext("2d");

let keys={};

let player;
let killer;
let clues;
let axe;
let trees;

let gameRunning=false;

let timeLeft=300;

let lastTime=0;

let hasAxe=false;

let clueTotal=0;

let flashlight=true;

let killerActive=false;

let forestMessageTimer=0;


/* KEYBOARD */

window.addEventListener("keydown",e=>{

    keys[e.key.toLowerCase()]=true;

    if(e.key.toLowerCase()==="f"){

        flashlight=!flashlight;

        document.getElementById("flashStatus").textContent=
            flashlight?"ON":"OFF";

    }

});


window.addEventListener("keyup",e=>{

    keys[e.key.toLowerCase()]=false;

});


function startForest(){

    showScreen("forestScreen");

    player={
        x:120,
        y:350,
        speed:3.2,
        radius:14
    };


    killer={
        x:850,
        y:120,
        speed:2.3,
        radius:18,
        state:"patrol",
        patrolAngle:0
    };


    clues=[

        {
            x:250,
            y:150,
            collected:false,
            name:"Robert's Backpack"
        },

        {
            x:720,
            y:180,
            collected:false,
            name:"Robert's Watch"
        },

        {
            x:650,
            y:550,
            collected:false,
            name:"Robert's Bracelet"
        },

        {
            x:300,
            y:570,
            collected:false,
            name:"Robert's Photograph"
        }

    ];


    axe={
        x:480,
        y:340,
        collected:false
    };


    trees=[];

    for(let i=0;i<45;i++){

        trees.push({

            x:Math.random()*950+25,
            y:Math.random()*650+25,
            r:18+Math.random()*22

        });

    }


    trees.push(
        {x:500,y:200,r:40},
        {x:800,y:400,r:45},
        {x:180,y:500,r:38}
    );


    hasAxe=false;

    clueTotal=0;

    killerActive=false;

    timeLeft=300;

    gameRunning=true;

    updateHUD();

    showDialogue(
        "The forest is behind your house... Find Robert.",
        3500
    );

    requestAnimationFrame(gameLoop);

}


/* =====================================================
   GAME LOOP
===================================================== */

function gameLoop(timestamp){

    if(!gameRunning)return;

    let delta=(timestamp-lastTime)/1000;

    if(!lastTime)delta=0;

    lastTime=timestamp;

    update(delta);

    draw();

    requestAnimationFrame(gameLoop);

}


function update(delta){

    timeLeft-=delta;

    if(timeLeft<=0){

        timeLeft=0;

        endingVictim();

        return;

    }


    movePlayer();

    collectItems();

    updateKiller(delta);

    updateHUD();

}


/* =====================================================
   PLAYER
===================================================== */

function movePlayer(){

    let dx=0;
    let dy=0;

    if(keys["w"]||keys["arrowup"])dy-=1;

    if(keys["s"]||keys["arrowdown"])dy+=1;

    if(keys["a"]||keys["arrowleft"])dx-=1;

    if(keys["d"]||keys["arrowright"])dx+=1;


    if(dx!==0 && dy!==0){

        dx*=0.707;
        dy*=0.707;

    }


    player.x+=dx*player.speed;

    player.y+=dy*player.speed;


    player.x=Math.max(
        player.radius,
        Math.min(canvas.width-player.radius,player.x)
    );

    player.y=Math.max(
        player.radius,
        Math.min(canvas.height-player.radius,player.y)
    );

}


/* =====================================================
   ITEMS
===================================================== */

function collectItems(){

    clues.forEach(clue=>{

        if(!clue.collected &&
        distance(player.x,player.y,clue.x,clue.y)<30){

            clue.collected=true;

            clueTotal++;

            killerActive=true;

            showDialogue(
                "You found "+clue.name+".",
                2000
            );

            if(clueTotal===4){

                showDialogue(
                    "You found everything. GET HOME!",
                    3000
                );

                document.getElementById("objective")
                .textContent=
                "OBJECTIVE: RETURN HOME";

            }

        }

    });


    if(!axe.collected &&
    distance(player.x,player.y,axe.x,axe.y)<35){

        axe.collected=true;

        hasAxe=true;

        showDialogue(
            "You found Barry's missing axe.",
            2500
        );

    }


    if(clueTotal===4 &&
    player.x<140 &&
    player.y>280 &&
    player.y<420){

        gameRunning=false;

        showScreen("choiceScreen");

    }

}


/* =====================================================
   KILLER AI
===================================================== */

function updateKiller(delta){

    if(!killerActive)return;


    let d=distance(
        player.x,
        player.y,
        killer.x,
        killer.y
    );


    if(d<280){

        killer.state="chase";

    }


    if(killer.state==="chase"){

        let angle=Math.atan2(
            player.y-killer.y,
            player.x-killer.x
        );


        let speed=hasAxe?3.1:3.8;


        killer.x+=Math.cos(angle)*speed;

        killer.y+=Math.sin(angle)*speed;


        if(d<28){

            if(hasAxe){

                endingHero();

            }else{

                endingVictim();

            }

        }

    }else{

        killer.patrolAngle+=0.01;

        killer.x+=Math.cos(killer.patrolAngle)*1.2;

        killer.y+=Math.sin(killer.patrolAngle)*1.2;

    }


    killer.x=Math.max(
        killer.radius,
        Math.min(canvas.width-killer.radius,killer.x)
    );

    killer.y=Math.max(
        killer.radius,
        Math.min(canvas.height-killer.radius,killer.y)
    );

}


/* =====================================================
   DRAWING
===================================================== */

function draw(){

    ctx.clearRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    /* GROUND */

    ctx.fillStyle="#102014";

    ctx.fillRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    /* PATH */

    ctx.fillStyle="#1d2418";

    ctx.fillRect(
        70,
        250,
        120,
        200
    );


    /* TREES */

    trees.forEach(tree=>{

        ctx.fillStyle="#111b12";

        ctx.beginPath();

        ctx.arc(
            tree.x,
            tree.y,
            tree.r,
            0,
            Math.PI*2
        );

        ctx.fill();


        ctx.fillStyle="#172b18";

        ctx.beginPath();

        ctx.arc(
            tree.x-5,
            tree.y-5,
            tree.r*.7,
            0,
            Math.PI*2
        );

        ctx.fill();

    });


    /* CLUES */

    clues.forEach(clue=>{

        if(!clue.collected){

            ctx.fillStyle="#d6c46a";

            ctx.fillRect(
                clue.x-7,
                clue.y-7,
                14,
                14
            );

        }

    });


    /* AXE */

    if(!axe.collected){

        ctx.fillStyle="#8a5a3b";

        ctx.fillRect(
            axe.x-3,
            axe.y-12,
            6,
            24
        );


        ctx.fillStyle="#aaa";

        ctx.fillRect(
            axe.x-10,
            axe.y-14,
            20,
            7
        );

    }


    /* KILLER */

    if(killerActive){

        ctx.fillStyle="#161616";

        ctx.beginPath();

        ctx.arc(
            killer.x,
            killer.y,
            killer.radius,
            0,
            Math.PI*2
        );

        ctx.fill();


        ctx.fillStyle="#777";

        ctx.fillRect(
            killer.x-10,
            killer.y-8,
            20,
            16
        );

    }


    /* PLAYER */

    ctx.fillStyle="#d8d8d8";

    ctx.beginPath();

    ctx.arc(
        player.x,
        player.y,
        player.radius,
        0,
        Math.PI*2
    );

    ctx.fill();


    /* FLASHLIGHT DARKNESS */

    if(flashlight){

        ctx.fillStyle="rgba(0,0,0,.78)";

        ctx.fillRect(
            0,
            0,
            canvas.width,
            canvas.height
        );


        ctx.save();

        ctx.globalCompositeOperation="destination-out";

        let gradient=ctx.createRadialGradient(
            player.x,
            player.y,
            20,
            player.x,
            player.y,
            180
        );

        gradient.addColorStop(
            0,
            "rgba(0,0,0,1)"
        );

        gradient.addColorStop(
            1,
            "rgba(0,0,0,0)"
        );

        ctx.fillStyle=gradient;

        ctx.beginPath();

        ctx.arc(
            player.x,
            player.y,
            180,
            0,
            Math.PI*2
        );

        ctx.fill();

        ctx.restore();

    }


    /* FOG */

    ctx.fillStyle="rgba(150,170,160,.04)";

    for(let i=0;i<5;i++){

        ctx.fillRect(
            0,
            i*140+Math.sin(Date.now()/1000+i)*20,
            canvas.width,
            30
        );

    }

}


/* =====================================================
   HUD
===================================================== */

function updateHUD(){

    let minutes=Math.floor(timeLeft/60);

    let seconds=Math.floor(timeLeft%60);

    document.getElementById("timer").textContent=
        String(minutes).padStart(2,"0")+
        ":"+
        String(seconds).padStart(2,"0");


    document.getElementById("clueCount").textContent=
        clueTotal;


    document.getElementById("axeStatus").textContent=
        hasAxe?"YES":"NO";

}


/* =====================================================
   DIALOGUE
===================================================== */

let dialogueTimeout;


function showDialogue(text,duration){

    let dialogue=
        document.getElementById("dialogue");

    dialogue.textContent=text;

    dialogue.style.display="block";

    clearTimeout(dialogueTimeout);

    dialogueTimeout=setTimeout(()=>{

        dialogue.style.display="none";

    },duration);

}


/* =====================================================
   ENDINGS
===================================================== */

function showEnding(title,text){

    gameRunning=false;

    lastTime=0;

    showScreen("endingScreen");

    document.getElementById("endingTitle").textContent=
        title;

    document.getElementById("endingText").textContent=
        text;

}


function endingFailedHero(){

    showEnding(

        "ENDING: FAILED HERO",

        "You told the police everything you knew. But the story sounded impossible, and the investigation slowly turned toward you. You were left trapped inside a nightmare that nobody else seemed able to understand."

    );

}


function endingSilence(){

    showEnding(

        "ENDING: THE SILENCE",

        "You kept the truth to yourself. The authorities eventually concluded that Robert's disappearance was a tragic accident in the wilderness. But you knew there was something else in the forest. The secret followed you, becoming heavier every day."

    );

}


function endingVictim(){

    showEnding(

        "ENDING: VICTIM",

        "The forest became completely dark. Your escape route disappeared into the trees. The next morning, investigators found enough evidence to know that something terrible had happened, but nobody ever discovered the truth behind the masked figure."

    );

}


function endingHero(){

    showEnding(

        "ENDING: HERO",

        "With nowhere left to run, you fought back and survived. When the mask finally came off, the truth was almost impossible to believe: Robert's father had been behind the trap. You escaped the forest and brought the evidence to the authorities. The following day, Robert was finally laid to rest."

    );

}


/* =====================================================
   RESTART
===================================================== */

function restartGame(){

    keys={};

    gameRunning=false;

    currentQuestion=0;

    lastTime=0;

    showScreen("menu");

}


/* =====================================================
   UTILITY
===================================================== */

function distance(x1,y1,x2,y2){

    return Math.hypot(
        x2-x1,
        y2-y1
    );

}

</script>

</body>
</html>
