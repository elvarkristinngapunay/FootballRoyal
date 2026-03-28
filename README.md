# FootballRoyal

html


<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Who Has More? – Football Stats Game</title>
<style>
    body {
        font-family: "Segoe UI", Arial, sans-serif;
        background: linear-gradient(135deg,#0b4d10,#113d2c);
        color: #fff;
        text-align: center;
        margin: 0;
        padding: 20px;
    }
    h1 {margin-bottom:10px;}
    .hidden{display:none;}
    .box{background:#fff;color:#222;padding:30px;border-radius:15px;box-shadow:0 8px 25px rgba(0,0,0,.3);max-width:600px;margin:0 auto;}
    button{cursor:pointer;border:none;border-radius:10px;font-weight:bold;transition:transform .2s;}
    button:hover{transform:scale(1.05);}
    .category-btn{display:block;background:#218838;color:#fff;margin:10px auto;padding:15px;width:80%;}
    .player-row{display:flex;justify-content:center;flex-wrap:wrap;gap:25px;margin-top:20px;}
    .player-card{background:#fff;color:#111;border-radius:15px;padding:20px;width:220px;box-shadow:0 6px 15px rgba(0,0,0,.3);}
    .player-img{width:100px;height:100px;border-radius:50%;object-fit:cover;margin-bottom:10px;border:3px solid #218838;}
    .question{font-size:1.4rem;margin:15px 0;}
    .stats{text-transform:uppercase;font-size:.9rem;color:#444;}
    .lives{color:#f44;font-weight:600;}
    .score{color:#ff0;font-weight:600;}
    .btn-choice{display:inline-block;margin:8px;padding:10px 20px;background:#218838;color:white;}
    .result{margin-top:20px;font-size:1.2rem;font-weight:bold;}
    .correct{color:#2e7d32;}
    .wrong{color:#c62828;}
    .topbar{display:flex;justify-content:center;gap:25px;flex-wrap:wrap;margin:10px 0;}
    .small{font-size:.9rem;}
</style>
</head>
<body>
<h1>⚽ Who Has More?</h1>
<!-- START -->
<div id="start" class="box">
    <h2>Select a Category</h2>
    <p class="small">You have <strong>2 lives ❤️❤️</strong>. Guess which player has more of the chosen stat.</p>
    <div id="hiscores" class="small" style="margin-bottom:15px;"></div>
    <button class="category-btn" onclick="startGame('career_goals')">Career Goals</button>
    <button class="category-btn" onclick="startGame('assists')">Assists</button>
    <button class="category-btn" onclick="startGame('career_appearances')">Career Appearances</button>
    <button class="category-btn" onclick="startGame('intl_goals')">International Goals</button>
    <button class="category-btn" onclick="startGame('intl_caps')">International Caps</button>
    <button class="category-btn" onclick="startGame('trophies')">Trophies</button>
</div>
<!-- GAME -->
<div id="game" class="hidden">
    <div class="topbar">
        <div>Category: <b id="catName"></b></div>
        <div class="score">Score: <span id="score">0</span></div>
        <div class="lives">Lives: <span id="lives">❤️❤️</span></div>
        <div class="small">Players: <span id="progress"></span></div>
    </div>
    <button onclick="backHome()" style="margin-bottom:15px;">← Back to Categories</button>
    <div class="question" id="question"></div>
    <div class="player-row">
        <div class="player-card" id="cardA" onclick="choose('A')"></div>
        <div class="player-card" id="cardB" onclick="choose('B')"></div>
    </div>
    <div id="result" class="result hidden"></div>
    <button id="nextBtn" class="hidden" onclick="nextRound()" style="margin-top:15px;padding:12px 35px;background:#1976d2;color:white;">Next →</button>
</div>
<script>
// Data
const players=[
{name:"Diego Maradona",pos:"Midfield",nat:"Argentina",apps:350,goals:161,assists:107,caps:91,goalsInt:34,trophies:9,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/2/2c/Maradona-Mundial_86_con_la_copa.JPG)"},
{name:"Johan Cruyff",pos:"Forward",nat:"Netherlands",apps:606,goals:345,assists:254,caps:48,goalsInt:33,trophies:25,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/8/8e/Johan_Cruijff_1974c.jpg)"},
{name:"Michel Platini",pos:"Forward",nat:"France",apps:585,goals:314,assists:73,caps:72,goalsInt:41,trophies:15,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/4/43/Michel_Platini_1978.jpg)"},
{name:"Franz Beckenbauer",pos:"Defender",nat:"Germany",apps:622,goals:75,assists:75,caps:103,goalsInt:14,trophies:20,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/6/6f/Beckenbauer_1974.jpg)"},
{name:"Gerd Müller",pos:"Forward",nat:"Germany",apps:613,goals:570,assists:107,caps:62,goalsInt:68,trophies:14,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/a/a2/Gerd_M%C3%BCller%2C_Bayern_Munich_%281974%29.jpg)"},
{name:"Marco van Basten",pos:"Forward",nat:"Netherlands",apps:373,goals:277,assists:86,caps:58,goalsInt:24,trophies:14,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/5/5e/Marco_van_Basten_1988.jpg)"},
{name:"Ruud Gullit",pos:"Midfield",nat:"Netherlands",apps:576,goals:219,assists:126,caps:66,goalsInt:17,trophies:18,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/d/d3/Ruud_Gullit_1988.jpg)"},
{name:"Bobby Charlton",pos:"Midfield",nat:"England",apps:747,goals:245,assists:77,caps:106,goalsInt:49,trophies:11,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/2/23/Bobby_Charlton_1966.jpg)"},
{name:"Ronaldinho",pos:"Forward",nat:"Brazil",apps:647,goals:235,assists:182,caps:97,goalsInt:33,trophies:13,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/4/43/Ronaldinho_2007.jpg)"},
{name:"David Beckham",pos:"Midfield",nat:"England",apps:724,goals:127,assists:227,caps:115,goalsInt:17,trophies:20,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/9/9e/David_Beckham_Nov_11_2007.jpg)"},
{name:"Zlatan Ibrahimović",pos:"Forward",nat:"Sweden",apps:827,goals:496,assists:205,caps:122,goalsInt:62,trophies:34,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/a/a7/Zlatan_Ibrahimovi%C4%87_2018.jpg)"},
{name:"Ronaldo Nazário",pos:"Forward",nat:"Brazil",apps:499,goals:330,assists:82,caps:98,goalsInt:62,trophies:16,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/c/c8/Ronaldo_2002.jpg)"},
{name:"Zinedine Zidane",pos:"Midfield",nat:"France",apps:689,goals:125,assists:145,caps:108,goalsInt:31,trophies:13,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/e/e6/Zinedine_Zidane_2008.jpg)"},
{name:"Thierry Henry",pos:"Forward",nat:"France",apps:794,goals:360,assists:180,caps:123,goalsInt:51,trophies:16,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/e/e8/Thierry_Henry_2012.jpg)"},
{name:"Steven Gerrard",pos:"Midfield",nat:"England",apps:749,goals:191,assists:171,caps:114,goalsInt:21,trophies:11,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/d/d5/Steven_Gerrard_2014.jpg)"},
{name:"Frank Lampard",pos:"Midfield",nat:"England",apps:906,goals:273,assists:177,caps:106,goalsInt:29,trophies:15,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/6/6c/Frank_Lampard_2013.jpg)"},
{name:"Paul Scholes",pos:"Midfield",nat:"England",apps:716,goals:155,assists:83,caps:66,goalsInt:14,trophies:25,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/f/f6/Paul_Scholes_2011.jpg)"},
{name:"Didier Drogba",pos:"Forward",nat:"Ivory Coast",apps:688,goals:302,assists:125,caps:105,goalsInt:65,trophies:16,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/8/8e/Didier_Drogba_2012.jpg)"},
{name:"Mohamed Salah",pos:"Forward",nat:"Egypt",apps:687,goals:332,assists:170,caps:113,goalsInt:65,trophies:13,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/c/c1/Mohamed_Salah_2018.jpg)"},
{name:"Wayne Rooney",pos:"Forward",nat:"England",apps:764,goals:313,assists:168,caps:120,goalsInt:53,trophies:16,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/c/c6/Wayne_Rooney_2015.jpg)"},
{name:"Diego Costa",pos:"Forward",nat:"Spain/Brazil",apps:444,goals:154,assists:58,caps:26,goalsInt:10,trophies:16,img:"[upload.wikimedia.org](https://upload.wikimedia.org/wikipedia/commons/0/0c/Diego_Costa_2017.jpg)"}
];
const mapKey={
 career_goals:{label:"Career Goals",key:"goals"},
 assists:{label:"Assists",key:"assists"},
 career_appearances:{label:"Career Appearances",key:"apps"},
 intl_goals:{label:"International Goals",key:"goalsInt"},
 intl_caps:{label:"International Caps",key:"caps"},
 trophies:{label:"Trophies",key:"trophies"}
};
let category="",score=0,lives=2,remaining=[],A,B;
function startGame(cat){
 category=cat;
 score=0;lives=2;
 remaining=shuffle([...players]);
 nextPair();
 document.getElementById("start").classList.add("hidden");
 document.getElementById("game").classList.remove("hidden");
 document.getElementById("catName").textContent=mapKey[cat].label;
 updateHUD();
}
function shuffle(a){for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]]}return a;}
function nextPair(){
 if(remaining.length<2){winGame();return;}
 A=remaining.pop();B=remaining.pop();
 document.getElementById("question").textContent="Who has more "+mapKey[category].label+"?";
 renderCard("cardA",A);
 renderCard("cardB",B);
 document.getElementById("result").classList.add("hidden");
 document.getElementById("nextBtn").classList.add("hidden");
}
function renderCard(id,p){
 document.getElementById(id).innerHTML=`<img src="${p.img}" class="player-img" onerror="this.src='[via.placeholder.com](https://via.placeholder.com/100x100?text=${p.name)[0]}'"><h3>${p.name}</h3><div>${p.pos} • ${p.nat}</div>`;
}
function choose(choice){
 const valA=A[mapKey[category].key];
 const valB=B[mapKey[category].key];
 const correctChoice=valA===valB?"draw":valA>valB?"A":"B";
 let result=document.getElementById("result");
 result.classList.remove("hidden");
 result.classList.remove("correct","wrong");
 const chosen=choice===correctChoice;
 if(chosen||correctChoice==="draw"){score++;result.textContent="✅ Correct! "+(correctChoice==="draw"?"They are equal.":"");result.classList.add("correct");}
 else{lives--;result.textContent="❌ Wrong! "+A.name+" = "+valA+" , "+B.name+" = "+valB;result.classList.add("wrong");}
 updateHUD();
 if(lives<=0) gameOver();
 else document.getElementById("nextBtn").classList.remove("hidden");
}
function updateHUD(){
 const total=players.length-remaining.length;
 document.getElementById("score").textContent=score;
 document.getElementById("lives").textContent=lives===2?"❤️❤️":lives===1?"❤️🖤":"🖤🖤";
 document.getElementById("progress").textContent=total+"/"+players.length;
}
function nextRound(){nextPair();}
function gameOver(){
 document.getElementById("result").classList.remove("correct");
 document.getElementById("result").classList.add("wrong");
 document.getElementById("result").textContent="💀 Game Over! Final Score: "+score;
 document.getElementById("nextBtn").classList.add("hidden");
 saveHi();
 document.getElementById("start").classList.remove("hidden");
 document.getElementById("game").classList.add("hidden");
 showHiscores();
}
function winGame(){
 document.getElementById("result").classList.remove("hidden");
 document.getElementById("result").classList.add("correct");
 document.getElementById("result").textContent="🏆 You cleared all players! Final Score: "+score;
 document.getElementById("nextBtn").classList.add("hidden");
 saveHi();
 setTimeout(()=>{backHome();},3000);
}
function backHome(){
 document.getElementById("game").classList.add("hidden");
 document.getElementById("start").classList.remove("hidden");
 showHiscores();
}
function saveHi(){
 const key="hiscore_"+category;
 const prev=parseInt(localStorage.getItem(key)||0);
 if(score>prev)localStorage.setItem(key,score);
}
function showHiscores(){
 let html="";
 for(const cat in mapKey){
   const sc=localStorage.getItem("hiscore_"+cat)||0;
   html+=`${mapKey[cat].label}: <b>${sc}</b> &nbsp; `;
 }
 document.getElementById("hiscores").innerHTML="Top Scores → "+html;
}
showHiscores();
</script>
</body>
</html>
