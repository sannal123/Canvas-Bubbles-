<!DOCTYPE html>
<html>
    <head>
        <title>snl.exe_</title>
        <style>
        *{
    position:fixed;
    margin:0;
}
canvas{
width:100%;
background:rgb(50,50,50);
}
p{
color:#fff;margin:10px;
}
        </style>
        <script>
        addEventListener("load",()=>{

const selQuery= (q) => document.querySelector(q);
const creQuery = (e) => document.createElement(e);
const scale = 2;
const {PI,random,sin,cos,sqrt} = Math; 
const randomVal = (max,min) => random()*(max-min)+min;
let cvx = selQuery("canvas");
let ctx = cvx.getContext("2d");

let W = cvx.width = innerWidth * scale;
let H = cvx.height = innerHeight * scale;

class Vec2d{
  constructor(x,y){
      this.x = x;
      this.y = y;
  }
  add(v){
      return new Vec2d(this.x + v.x , this.y + v.y);
  }
  sub(v){
      return new Vec2d(this.x - v.x , this.y - v.y);
  }
  mult(n){
      return new Vec2d(this.x * n, this.y * n);
  }
}

function  isCollideOther(a,b){
        let x = (a.pos.x ) - (b.pos.x );
        let y = (a.pos.y ) - (b.pos.y )
        let d = sqrt(x*x+y*y);
        return d <= a.r + b.r;
}

class Particles{
   constructor(x,y,src){
       this.pos = new Vec2d(x,y);
       this.vel = new Vec2d(0,0);
       this.accl = new Vec2d(0,-0.1);
       this.life = 100;this.r = randomVal(60,30);
       this.flow = (random()*10);
       this.img = new Image();
       this.img.src = src;
       this.fill =  `hsl(${this.life},100% , 50%)`;
   }
   draw(){
     ctx.save();
     ctx.beginPath();
     ctx.fillStyle =  this.fill;     ctx.drawImage(this.img,this.pos.x,this.pos.y,this.r*2,this.r*2);
     ctx.fill();
     ctx.restore();
   }
   update(){
       this.vel = this.vel.add(this.accl);
       this.pos = this.pos.add(this.vel);
       this.life -= 1;
       this.fill =  `hsl(${this.life},100% , 50%)`;
       this.pos.x += cos(this.flow);
       this.pos.y += sin(this.flow);
   }   
   bouncy(cvx){
       let inclife = 20;
       if(this.pos.x > cvx.width - this.r*2){
           this.pos.x = cvx.width - this.r*2;
           this.vel.x -= 10;
       }
       else if(this.pos.x < 0){
           this.pos.x = 0;
           this.vel.x += 10;
       }
       if(this.pos.y > cvx.height - this.r*2){
           this.pos.y = cvx.height - this.r*2;
           this.vel.y -= 10;
       }
       else if(this.pos.y < 0){
           this.pos.y = 0;
           this.vel.y += 10;
       }
   }
  
}


let lastloop = 0;
let p = creQuery("p");
function calcFps(){
    let thisLoop = performance.now();
    let fps = ~~(1000 / (thisLoop - lastloop));
    lastloop  = thisLoop;
    p.innerHTML = `fps : ${fps}`;
    document.body.appendChild(p);
}


let effectT = [];


for(let i = 0;i < 15;i++){
    effectT.push(new Particles(random()*W,random()*H,"https://www.dropbox.com/s/kmhe5znwrmvnnue/food-blue-bubble.png?dl=1"));
}
addEventListener("dblclick",()=>{
effectT.push(new Particles(random()*W,random()*H,"https://www.dropbox.com/s/kmhe5znwrmvnnue/food-blue-bubble.png?dl=1"));
});

function animate(){
ctx.clearRect(0,0,W,H);



for(let i = 0;i < effectT.length;i++){
    effectT[i].draw();
    effectT[i].update();
    effectT[i].bouncy(cvx); 
    
    for(let j = 0;j < i;j++){
        if(isCollideOther(effectT[i],effectT[j])){
            if(effectT[i].pos.x > effectT[j].pos.x + effectT[j].r){
                effectT[i].vel.x += 2;effectT[j].vel.x -= 2;
            }
          else if(effectT[i].pos.x + effectT[i].r  <  effectT[j].pos.x){
                effectT[i].vel.x -= 2;effectT[j].vel.x += 2;
            }
            if(effectT[i].pos.y + effectT[i].r < effectT[j].pos.y){
                 effectT[i].vel.y -= 2;effectT[j].vel.y += 2;
            }
            else if(effectT[i].pos.y > effectT[j].pos.y + effectT[j].r){
                effectT[i].vel.y += 2;effectT[j].vel.y -= 2;
            }
        }
     }
    
}
calcFps();

requestAnimationFrame(animate);
}
animate();

addEventListener("resize",()=>{
W = cvx.width = innerWidth * scale;
H = cvx.height = innerHeight * scale;

});





});

alert(`Images may take little time to load :
 #Double tap to add a new bubble`);
        </script>
    </head>
    <body>
        <canvas></canvas>
         
    </body>
</html>
