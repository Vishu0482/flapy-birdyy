# flapy-birdyy
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Flappy Bird Clone</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    background:#70c5ce;
    overflow:hidden;
    font-family:Arial;
}

canvas{
    border:4px solid #333;
    background:linear-gradient(#70c5ce,#b8f3ff);
}
</style>
</head>
<body>

<canvas id="game" width="400" height="600"></canvas>

<script>

const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");

const gravity=0.5;
const jump=-9;

let score=0;
let gameOver=false;

const bird={
    x:80,
    y:200,
    w:35,
    h:28,
    velocity:0
};

const pipes=[];
const gap=170;
const pipeWidth=50;
const pipeSpeed=2.8;

function createPipe(){

    const topHeight=Math.random()*250+50;

    pipes.push({
        x:canvas.width,
        top:topHeight,
        bottom:topHeight+gap,
        passed:false
    });

}

setInterval(()=>{
    if(!gameOver){
        createPipe();
    }
},1800);

document.addEventListener("keydown",e=>{
    if(e.code==="Space"){
        if(gameOver){
            restart();
        }else{
            bird.velocity=jump;
        }
    }
});

canvas.addEventListener("click",()=>{

    if(gameOver){
        restart();
    }else{
        bird.velocity=jump;
    }

});

function restart(){

    bird.y=200;
    bird.velocity=0;
    pipes.length=0;
    score=0;
    gameOver=false;

}

function update(){

if(gameOver)return;

bird.velocity+=gravity;
bird.y+=bird.velocity;

if(bird.y<0){
bird.y=0;
bird.velocity=0;
}

if(bird.y+bird.h>canvas.height){
gameOver=true;
}

for(let i=0;i<pipes.length;i++){

let p=pipes[i];

p.x-=pipeSpeed;

if(
bird.x+bird.w>p.x &&
bird.x<p.x+pipeWidth &&
(
bird.y<p.top ||
bird.y+bird.h>p.bottom
)
){
gameOver=true;
}

if(!p.passed && bird.x>p.x+pipeWidth){

score++;
p.passed=true;

}

}

while(pipes.length && pipes[0].x<-pipeWidth){
pipes.shift();
}

}

function draw(){

ctx.clearRect(0,0,canvas.width,canvas.height);

// Ground
ctx.fillStyle="#d7f27d";
ctx.fillRect(0,560,400,40);

// Pipes

ctx.fillStyle="green";

pipes.forEach(p=>{

ctx.fillRect(p.x,0,pipeWidth,p.top);

ctx.fillRect(
p.x,
p.bottom,
pipeWidth,
560-p.bottom
);

});

// Bird

ctx.fillStyle="yellow";

ctx.beginPath();
ctx.arc(
bird.x+bird.w/2,
bird.y+bird.h/2,
16,
0,
Math.PI*2
);
ctx.fill();

// Eye

ctx.fillStyle="white";
ctx.beginPath();
ctx.arc(
bird.x+23,
bird.y+10,
4,
0,
Math.PI*2
);
ctx.fill();

ctx.fillStyle="black";
ctx.beginPath();
ctx.arc(
bird.x+24,
bird.y+10,
2,
0,
Math.PI*2
);
ctx.fill();

// Beak

ctx.fillStyle="orange";
ctx.beginPath();
ctx.moveTo(bird.x+30,bird.y+16);
ctx.lineTo(bird.x+40,bird.y+20);
ctx.lineTo(bird.x+30,bird.y+24);
ctx.fill();

// Score

ctx.fillStyle="white";
ctx.font="40px Arial";
ctx.fillText(score,180,60);

if(gameOver){

ctx.fillStyle="rgba(0,0,0,0.6)";
ctx.fillRect(0,0,400,600);

ctx.fillStyle="white";
ctx.font="40px Arial";
ctx.fillText("Game Over",95,250);

ctx.font="24px Arial";
ctx.fillText("Score: "+score,145,300);

ctx.fillText("Press SPACE",120,360);

ctx.fillText("or Click",145,395);

}

}

function loop(){

update();

draw();

requestAnimationFrame(loop);

}

loop();

</script>

</body>
</html>
