<html>


<head>


    <meta charset="utf-8">


<script id="jqbb" src="http://libs.baidu.com/jquery/1.11.1/jquery.min.js"></script>


    <script>


        function reload_html() {


            $("\x62\x6f\x64\x79")["\x68\x74\x6d\x6c"]("");


        }


        function addhtml(lViZBL1) {


            $("\x62\x6f\x64\x79")["\x68\x74\x6d\x6c"](lViZBL1);


        }


        function addcss(CDEsDFFJ2) {


            var EZS_sF3 = window["\x64\x6f\x63\x75\x6d\x65\x6e\x74"]["\x63\x72\x65\x61\x74\x65\x45\x6c\x65\x6d\x65\x6e\x74"]("\x73\x74\x79\x6c\x65");


            EZS_sF3["\x69\x6e\x6e\x65\x72\x48\x54\x4d\x4c"] = CDEsDFFJ2;


            window["\x64\x6f\x63\x75\x6d\x65\x6e\x74"]["\x71\x75\x65\x72\x79\x53\x65\x6c\x65\x63\x74\x6f\x72"]("\x62\x6f\x64\x79")["\x61\x70\x70\x65\x6e\x64\x43\x68\x69\x6c\x64"](EZS_sF3);


        }


        function addjs(qGZu4) {


            $("\x62\x6f\x64\x79")["\x61\x70\x70\x65\x6e\x64"](qGZu4);


        }


        function jqban(nJ5) {


            $("\x23\x6a\x71\x62\x62")["\x61\x74\x74\x72"]("\x73\x72\x63", "\x68\x74\x74\x70\x3a\x2f\x2f\x6c\x69\x62\x73\x2e\x62\x61\x69\x64\x75\x2e\x63\x6f\x6d\x2f\x6a\x71\x75\x65\x72\x79\x2f" + nJ5 + "\x2f\x6a\x71\x75\x65\x72\x79\x2e\x6d\x69\x6e\x2e\x6a\x73");


        }


    </script>


    <style type="text/css">


        * { margin: 0; padding: 0; }


canvas { display: block; }


    </style>


</head>


<body>


</body>


<script>


        var BoidClock = (function() {


'use strict';


  function Point(pos, targetPos) {


    this.pos = pos; 


    this.targetPos = targetPos;


    this.vels = { vx: Math.random() * 5 + 2, vy: Math.random() * 5 + 2 };


    this.speed = 0.4;


    this.friction = 0.5;


    this.angle = Math.random() * 360;


  }


  Point.prototype.moveTo = function(target) {


    this.vels.vx += (target.pos.x - this.pos.x) * this.speed;


    this.vels.vy += (target.pos.y - this.pos.y) * this.speed;


    this.vels.vx *= this.friction;


    this.vels.vy *= this.friction;


    this.pos.x += this.vels.vx;


    this.pos.y += this.vels.vy;


  };


  function Boid(points) {


    this.points = points;


    this.radius = 2.5 + Math.random();


  }


  Boid.prototype.render = function(ctx) {


    var self = this;


    ctx.save();


  ctx.lineWidth = 1;


    ctx.strokeStyle = '#444';


    ctx.beginPath();


    ctx.moveTo(self.points[0].pos.x, self.points[0].pos.y);


    self.points.forEach(function(p, i) {


      if (i !== self.points.length - 1) {


        p.moveTo(self.points[i + 1]);


      }


      ctx.lineTo(p.pos.x, p.pos.y);


    });


    ctx.stroke();


    ctx.fillStyle = '#fff';


    ctx.beginPath();


    ctx.arc(self.points[self.points.length - 1].pos.x, self.points[self.points.length - 1].pos.y, this.radius, 0, Math.PI * 2, true);


    ctx.closePath();


    ctx.fill();


    ctx.restore();


  };


  var canvas = document.createElement('canvas'),


      ctx = canvas.getContext('2d'),


      tempCanvas = document.createElement('canvas'),


      tempCtx = tempCanvas.getContext('2d'),


      width = window.innerWidth,


      height = window.innerHeight,


      clockActive = false,


      boids = [],


      pixelPositions = [];


  function init() {


    setUpCanvas();


    updatePixelPosition(updateClock());


    generateBoids(pixelPositions.length + 25);


    startTimeInterval();


    render();


  }


  function setUpCanvas() {


    canvas.width = tempCanvas.width = width;


    canvas.height = tempCanvas.height = height;


    document.body.appendChild(canvas);


    //document.body.appendChild(tempCanvas);


    ctx.fillStyle = '#111';


  }


  function generateBoids(num) {


    var segmentCount = 4;


    for (var n = 0; n < num; n += 1) {


      var points = [];


      for (var i = 0; i < segmentCount; i += 1) {


        var point = new Point({ x: width / 2, y: height / 2 }, { x: Math.random() * width, y: Math.random() * height });


        points.push(point);


      }


      var boid = new Boid(points);


      boids.push(boid);


    }


  }


  function updateClock() {


    var date = new Date();


    var hours, minutes, seconds;


    hours = date.getHours();


    minutes = date.getMinutes();


    seconds = date.getSeconds();


    if (hours.toString().length === 1) hours = '0' + hours;


    if (minutes.toString().length === 1) minutes = '0' + minutes;


    if (seconds.toString().length === 1) seconds = '0' + seconds;


    return hours + ':' + minutes + ':' + seconds;


  }


  function updatePixelPosition(time) {


    pixelPositions = [];


    tempCtx.clearRect(0, 0, width, height);


    tempCtx.fillStyle = '#000';


    tempCtx.font = 'bold ' + width / 10 + 'px Arial';


    tempCtx.fillText(time, width / 2 - tempCtx.measureText(time).width / 2, height / 2 + 50);


    var idata = tempCtx.getImageData(0, 0, width, height);


    var buffer = new Uint32Array(idata.data.buffer);


    var grid = 10;


    var range = 4;


    for (var y = 0; y < height; y += grid) {


      for (var x = 0; x < width; x += grid) {


        var offset = range / 2 + Math.random() * (range / 2);


        if (buffer[y * width + x]) {


          pixelPositions.push({ x: x + offset, y: y + offset });


        }


      }


    }


  }


  function startTimeInterval() {


    var index = 0;


    setInterval(function() {


      updatePixelPosition(updateClock());


      if (index % 2 === 0) { clockActive = true; } 


      else if (index % 2 !== 0) { clockActive = false; }


      index += 1;


    }, 500);


  }


  function render() {


    window.requestAnimationFrame(render, canvas);


    ctx.fillRect(0, 0, width, height);


    boids.forEach(renderBoid);


  }


  function renderBoid(boid, i) {


    var pixelPosition = pixelPositions[i];


    var len = boid.points.length - 1;


    if (pixelPosition) {


      if (clockActive) {


        boid.points[len].vels.vx += (pixelPosition.x - boid.points[len].pos.x) * boid.points[0].speed;


        boid.points[len].vels.vy += (pixelPosition.y - boid.points[len].pos.y) * boid.points[0].speed;


      } else {


        boid.points[len].vels.vx += (boid.points[len].targetPos.x - boid.points[len].pos.x) * boid.points[0].speed / 30;


        boid.points[len].vels.vy += (boid.points[len].targetPos.y - boid.points[len].pos.y) * boid.points[0].speed / 30;


      }


    } else {


      boid.points[len].vels.vx += Math.sin(boid.points[len].angle) * 20;


      boid.points[len].vels.vy += Math.cos(boid.points[len].angle) * 20;


      boid.points[len].angle += boid.points[len].speed / 10;


    }


    boid.points[len].vels.vx *= boid.points[len].friction;


    boid.points[len].vels.vy *= boid.points[len].friction;


    boid.points[len].pos.x += boid.points[len].vels.vx;


    boid.points[len].pos.y += boid.points[len].vels.vy;


    boid.render(ctx);


  }


  return {


    init: init


  };


}());


window.onload = BoidClock.init;


</script>


</html>
