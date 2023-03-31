# snake-game-code

아래 코드는 p5.js 라이브러리를 사용하여 구현된 뱀 게임입니다.

이 게임의 목적은 뱀을 조종하여 먹이를 먹고 몸길이를 키우는 것입니다.

게임에서 먹이를 먹으면 뱀의 몸길이가 커지고 점수가 오릅니다.

장애물과 부딪치거나 자기 몸에 부딪히면 게임이 끝나고 점수가 리셋됩니다.

이 코드의 주요 기능은 먹이와 장애물을 랜덤으로 생성하고 먹이를 먹으면 몸길이가 커지고 점수가 오르도록 하는 것입니다.

이 게임에서는 장애물이 추가되어 먹이와 부딪히면 게임이 끝나도록 구현되어 있습니다.


```ruby
let s; // Snake 객체를 저장하기 위한 변수

let scl = 20; //스케일 변수, 즉 뱀, 음식, 장애물의 크기를 결정하는 변수

let food; //음식을 저장하기 위한 변수

let obstacles = []; //장애물들을 저장하기 위한 배열

let img1, img2; //배경 이미지를 저장하기 위한 변수

playfield = 700; //게임 화면의 크기를 결정하는 변수

function preload() {   //loadImage 함수를 사용하여 배경 이미지를 로드합니다.
  img1 = loadImage(
    "https://raw.githubusercontent.com/kmans1123/img/main/forest.jpg"
  );
  img2 = loadImage(
    "https://raw.githubusercontent.com/kmans1123/img/main/desert.jpg"
  );
}

function setup() {  //게임 초기 설정을 담당합니다.
  createCanvas(playfield, 740);
  
  frameRate(30);  //초당 프레임 수를 설정합니다.
  
  background(51);  //게임 화면의 배경색을 설정합니다.

  s = new Snake();

  frameRate(10);

  pickLocation();  //음식의 위치를 무작위로 생성합니다.
}

function draw() {  //게임 화면을 업데이트합니다.
  background(51);

  if (s.score >= 10) { //스코어가 10점 이상이 되면 1번 배경으로 변경합니다.
    image(img1, -200, -200, width * 1.5, height * 1.5);
  }
  if (s.score >= 20) { //스코어가 20점 이상이 되면 2번 배경으로 변경합니다.
    image(img2, 0, 0, width, height);
  }

  scoreboard();  //현재 뱀의 점수를 스코어보드로 표시합니다.

  if (s.eat(food)) {
    pickLocation();
  }

  s.death(); //뱀이 자신의 몸통이나 장애물에 부딪혔는지 확인하고, 부딪혔으면 게임을 재시작하는 restart() 함수를 호출합니다.

  s.update();  //뱀의 위치를 업데이트합니다. 뱀의 머리 부분은 현재 이동 방향에 따라 한 칸 앞으로 이동하고, 뱀의 몸통 부분은 이전 머리 부분의 위치로 이동합니다.

  s.show(); //현재 뱀의 위치를 그립니다.

  fill(255, 0, 100);

  rect(food.x, food.y, scl, scl);

  for (let i = 0; i < obstacles.length; i++) {
    fill(255, 0, 0);

    rect(obstacles[i].x, obstacles[i].y, scl, scl);
  }
}

function pickLocation() {  //먹이와 장애물의 위치를 랜덤하게 선택합니다.
  let cols = floor(playfield / scl);
                                      //playfield(게임판)의 가로, 세로 칸 수를 scl(스케일)로 나눠 계산합니다. 게임판 전체를 scl 크기의 정사각형으로 나눈 칸 수를 구합니다.
  let rows = floor(playfield / scl);

  food = createVector(floor(random(cols)), floor(random(rows))); //랜덤한 위치에 먹이를 생성합니다.

  food.mult(scl); //먹이의 위치를 스케일 크기만큼 곱하여 실제 위치를 설정합니다.

  for (let i = 0; i < s.tail.length; i++) {  //루프에서는 뱀의 꼬리 위치와 생성된 먹이 위치가 겹치지 않도록 합니다.
    let pos = s.tail[i];

    let d = dist(food.x, food.y, pos.x, pos.y);

    if (d < 1) {
      pickLocation();
    }
  }

  if (s.score > 0 && s.score % 5 === 0) { // 5점 단위로 올라갈 때마다 장애물을 생성합니다.
    let obstacleX = floor(random(cols)) * scl;

    let obstacleY = floor(random(rows)) * scl;

    let obstacle = createVector(obstacleX, obstacleY);

    obstacles.push(obstacle);
  }

  for (let i = 0; i < obstacles.length; i++) { //루프에서는 먹이와 장애물이 겹치지 않도록 합니다.
    let d = dist(food.x, food.y, obstacles[i].x, obstacles[i].y);

    if (d < 1) {
      pickLocation();
    }
  }
}

function scoreboard() {   //현재 게임의 점수와 최고 점수를 표시합니다.
  fill(0);
            //검은색 사각형을 그려 게임 화면 아래에 점수를 표시하는 공간을 만듭니다.
  rect(0, 700, 700, 40); 

  fill(255);
              //흰색으로 글씨체를 설정합니다.
  textFont("Georgia");

  textSize(18);  //글씨 크기를 18로 설정합니다.

  text("Score: ", 10, 725);

  text("Highscore: ", 550, 725);

  text(s.score, 70, 725);

  text(s.highscore, 640, 725);
}

function keyPressed() {   //방향키 이벤트를 처리합니다.
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

function Snake() {   //뱀 객체를 생성하고 조작합니다.
  this.x = 0;

  this.y = 0;

  this.xspeed = 1;

  this.yspeed = 0;

  this.total = 0;

  this.tail = [];

  this.score = 1;

  this.highscore = 1;

  this.dir = function (x, y) { //방향을 설정합니다. 즉, x축 방향과 y축 방향의 이동속도를 설정합니다.
    this.xspeed = x;

    this.yspeed = y;
  };

  this.eat = function (pos) { //먹이를 먹었을 때 처리를 담당합니다. 스코어 값을 올립니다.
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

  this.death = function () {  //뱀이 죽었을 때 처리를 담당합니다.  스코어와 obstacles 배열을 초기화합니다.
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

  this.update = function () {  //뱀을 업데이트합니다.
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

  this.show = function () {  //뱀을 화면에 그립니다. 뱀의 몸통은 랜덤한 색으로, 머리는 검은색으로 그려집니다.
    fill(random(255), random(255), random(255));

    for (let i = 0; i < this.tail.length; i++) {
      rect(this.tail[i].x, this.tail[i].y, scl, scl);
    }

    rect(this.x, this.y, scl, scl);
  };
}

```
