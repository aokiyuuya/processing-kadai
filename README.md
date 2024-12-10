// A class to describe a single Particle
class Particle {
  PVector position;
  PVector velocity;
  PVector acceleration;
  float lifespan;
  color c;  // 色

  Particle(PVector l) {
    acceleration = new PVector(0, 0.05);
    velocity = new PVector(random(-1, 1), random(-2, 0));
    position = l.copy();
    lifespan = 255.0;
    c = color(random(255), random(255), random(255));  // ランダムな色
  }

  void run() {
    update();
    display();
  }

  void update() {
    velocity.add(acceleration);
    position.add(velocity);
    lifespan -= 1.0;
  }

  void display() {
    stroke(c, lifespan);
    fill(c, lifespan);
    ellipse(position.x, position.y, 8, 8);
  }

  boolean isDead() {
    return lifespan < 0.0;
  }
}

// A class to describe a group of Particles
class ParticleSystem {
  ArrayList<Particle> particles;
  PVector origin;

  ParticleSystem(PVector position) {
    origin = position.copy();
    particles = new ArrayList<Particle>();
  }

  void addParticle(PVector pos) {
    // 一度に複数のパーティクルを追加
    for (int i = 0; i < 5; i++) {  // 1回のクリックで5個のパーティクルを追加
      particles.add(new Particle(pos));  
    }
  }

  void run() {
    for (int i = particles.size() - 1; i >= 0; i--) {
      Particle p = particles.get(i);
      p.run();
      if (p.isDead()) {
        particles.remove(i);
      }
    }
  }
}

// グローバル変数
ParticleSystem ps;
boolean isMousePressed = false;  // マウスが押されているかどうかを管理するフラグ
int lastParticleTime = 0;        // 最後にパーティクルを発生させた時間

void setup() {
  size(640, 360);
  ps = new ParticleSystem(new PVector(width / 2, 50));  // パーティクルシステムの初期位置
}

void draw() {
  background(0);

  // 長押ししている間、パーティクルを定期的に発生させる
  if (isMousePressed) {
    int currentTime = millis();  // 現在の時間をミリ秒で取得
    if (currentTime - lastParticleTime > 50) {  // 50msごとにパーティクルを発生させる
      ps.addParticle(new PVector(mouseX, mouseY));  // クリック位置からパーティクルを発生
      lastParticleTime = currentTime;  // 最後のパーティクル発生時間を更新
    }
  }

  ps.run();  // パーティクルシステムの実行
}

void mousePressed() {
  // マウスが押されたとき、フラグを立てる
  isMousePressed = true;
}

void mouseReleased() {
  // マウスが離されたとき、フラグをリセット
  isMousePressed = false;
}
