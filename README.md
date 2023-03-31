# snake-game-code

```
let s;

let scl = 20;

let food;

let obstacles = [];

let img1, img2;

playfield = 700;

function preload() {
  img1 = loadImage(
    "https://raw.githubusercontent.com/kmans1123/img/main/forest.jpg"
  );
  img2 = loadImage(
    "https://raw.githubusercontent.com/kmans1123/img/main/desert.jpg"
  );
}

function setup() {
  createCanvas(playfield, 740);
  
  frameRate(30);
  
  background(51);

  s = new Snake();

  frameRate(10);

  pickLocation();
}

function draw() {
  background(51);

  if (s.score >= 10) {
    image(img1, -200, -200, width * 1.5, height * 1.5);
  }
  if (s.score >= 20) {
    image(img2, 0, 0, width, height);
  }

  scoreboard();

  if (s.eat(food)) {
    pickLocation();
  }

  s.death();

  s.update();

  s.show();

  fill(255, 0, 100);

  rect(food.x, food.y, scl, scl);

  for (let i = 0; i < obstacles.length; i++) {
    fill(255, 0, 0);

    rect(obstacles[i].x, obstacles[i].y, scl, scl);
  }
}

function pickLocation() {
  let cols = floor(playfield / scl);

  let rows = floor(playfield / scl);

  food = createVector(floor(random(cols)), floor(random(rows)));

  food.mult(scl);

  for (let i = 0; i < s.tail.length; i++) {
    let pos = s.tail[i];

    let d = dist(food.x, food.y, pos.x, pos.y);

    if (d < 1) {
      pickLocation();
    }
  }

  if (s.score > 0 && s.score % 5 === 0) {
    let obstacleX = floor(random(cols)) * scl;

    let obstacleY = floor(random(rows)) * scl;

    let obstacle = createVector(obstacleX, obstacleY);

    obstacles.push(obstacle);
  }

  for (let i = 0; i < obstacles.length; i++) {
    let d = dist(food.x, food.y, obstacles[i].x, obstacles[i].y);

    if (d < 1) {
      pickLocation();
    }
  }
}

function scoreboard() {
  fill(0);

  rect(0, 700, 700, 40);

  fill(255);

  textFont("Georgia");

  textSize(18);

  text("Score: ", 10, 725);

  text("Highscore: ", 550, 725);

  text(s.score, 70, 725);

  text(s.highscore, 640, 725);
}

function keyPressed() {
  if (keyCode === UP_ARROW) {
    s.dir(0, -1);
  } else if (keyCode === DOWN_ARROW) {
    s.dir(0, 1);
  } else if (keyCode === RIGHT_ARROW) {
    s.dir(1, 0);
  } else if (keyCode === LEFT_ARROW) {
    s.dir(-1, 0);
  }
}

function Snake() {
  this.x = 0;

  this.y = 0;

  this.xspeed = 1;

  this.yspeed = 0;

  this.total = 0;

  this.tail = [];

  this.score = 1;

  this.highscore = 1;

  this.dir = function (x, y) {
    this.xspeed = x;

    this.yspeed = y;
  };

  this.eat = function (pos) {
    let d = dist(this.x, this.y, pos.x, pos.y);

    if (d < 1) {
      this.total++;

      this.score++;

      text(this.score, 70, 725);

      if (this.score > this.highscore) {
        this.highscore = this.score;
      }

      text(this.highscore, 540, 725);

      return true;
    } else {
      return false;
    }
  };

  this.death = function () {
    for (let i = 0; i < this.tail.length; i++) {
      let pos = this.tail[i];

      let d = dist(this.x, this.y, pos.x, pos.y);

      if (d < 1) {
        this.total = 0;

        this.score = 0;

        this.tail = [];
        
        obstacles = [];
      }
    }
    
    for (let i = 0; i < obstacles.length; i++) {
    let d = dist(this.x, this.y, obstacles[i].x, obstacles[i].y);
    if (d < 1) {
      this.total = 0;
      this.score = 0;
      this.tail = [];
      obstacles = [];
      }
    }
  };

  this.update = function () {
    if (this.total === this.tail.length) {
      for (let i = 0; i < this.tail.length - 1; i++) {
        this.tail[i] = this.tail[i + 1];
      }
    }

    this.tail[this.total - 1] = createVector(this.x, this.y);

    this.x = this.x + this.xspeed * scl;

    this.y = this.y + this.yspeed * scl;

    this.x = constrain(this.x, 0, playfield - scl);

    this.y = constrain(this.y, 0, playfield - scl);
  };

  this.show = function () {
    fill(random(255), random(255), random(255));

    for (let i = 0; i < this.tail.length; i++) {
      rect(this.tail[i].x, this.tail[i].y, scl, scl);
    }

    rect(this.x, this.y, scl, scl);
  };
}

```
