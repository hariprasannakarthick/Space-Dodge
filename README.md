# 🚀 Space Dodge

An Android arcade game built with pure Java — no game engine, no third-party libraries.
Pilot your spaceship through a galaxy and dodge falling asteroids for as long as you can!

---

## 🎮 Gameplay

- Touch and drag to move your spaceship left and right
- Avoid the falling asteroids
- Every asteroid dodged = +1 score
- Speed increases every 5 points
- Game over on collision — tap Restart to try again

---

## 📸 Screenshots

| Gameplay | Game Over |
|----------|-----------|
| _(add your screenshot here)_ | _(add your screenshot here)_ |

---

## 🛠️ Built With

- **Java** — 100% pure Android Java
- **Android Canvas API** — custom drawing, no XML layouts
- **Custom Game Loop** — dedicated Thread at ~60 FPS
- **Bitmap rendering** — pixel-art spaceship, asteroid & galaxy background

---

## 📁 Project Structure
```
app/src/main/java/com/example/game/
│
├── MainActivity.java     # Entry point, handles lifecycle
└── GameView.java         # Game loop, rendering, touch, collision

app/src/main/res/drawable/
├── spaceship.png         # Player character
├── asteroid.png          # Enemy
├── galaxy.png            # Background
└── phi.png               # App launcher icon
```

---

## ⚙️ Features

- [x] Smooth 60 FPS game loop
- [x] Touch controls
- [x] Live score & high score
- [x] Increasing difficulty
- [x] Game Over screen with restart
- [x] Custom pixel-art assets
- [x] Galaxy background
- [x] Custom app icon

---

## 🚀 How to Run

1. Clone the repo
```bash
   git clone https://github.com/YOUR_USERNAME/space-dodge.git
```
2. Open in **Android Studio**
3. Add your images to `res/drawable/`
4. Connect your Android phone (USB Debugging ON)
5. Hit **Run ▶️**

---

## 📋 Requirements

- Android Studio Hedgehog or later
- Android SDK 21+
- Java 8+

---

## 🔮 Future Plans

- [ ] Multiple asteroid lanes
- [ ] Power-ups (shield, slow-down)
- [ ] Sound effects & background music
- [ ] Leaderboard with local storage
- [ ] Animated explosions on collision

---

Code

```java

package com.example.game;

import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Typeface;
import android.view.MotionEvent;
import android.view.View;

import java.util.Random;

public class GameView extends View implements Runnable {

    // --- Thread & State ---
    private Thread gameThread;
    private boolean isPlaying = false;
    private boolean isGameOver = false;

    // --- Bitmaps ---
    private Bitmap playerBitmap;
    private Bitmap enemyBitmap;
    private Bitmap backgroundBitmap;

    // --- Paint objects ---
    private Paint scorePaint;
    private Paint gameOverPaint;
    private Paint restartPaint;
    private Paint overlayPaint;

    // --- Player ---
    private float playerX;
    private float playerY;
    private final int PLAYER_SIZE = 340;

    // --- Enemy ---
    private float enemyX;
    private float enemyY;
    private final int ENEMY_SIZE = 300;
    private float enemySpeed = 18f;

    // --- Score ---
    private int score = 0;
    private int highScore = 0;

    // --- Misc ---
    private Random random = new Random();
    private int screenWidth, screenHeight;

    // --- Restart button bounds ---
    private float restartLeft, restartTop, restartRight, restartBottom;

    public GameView(Context context) {
        super(context);
        loadBitmaps(context);
        initPaints();
    }

    private void loadBitmaps(Context context) {
        // Load and scale player (spaceship)
        Bitmap rawPlayer = BitmapFactory.decodeResource(context.getResources(), R.drawable.spaceship);
        playerBitmap = Bitmap.createScaledBitmap(rawPlayer, PLAYER_SIZE, PLAYER_SIZE, true);

        // Load and scale enemy (asteroid)
        Bitmap rawEnemy = BitmapFactory.decodeResource(context.getResources(), R.drawable.asteroid);
        enemyBitmap = Bitmap.createScaledBitmap(rawEnemy, ENEMY_SIZE, ENEMY_SIZE, true);
    }

    private void initPaints() {
        scorePaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        scorePaint.setColor(Color.WHITE);
        scorePaint.setTextSize(60f);
        scorePaint.setTypeface(Typeface.DEFAULT_BOLD);
        scorePaint.setShadowLayer(6f, 2f, 2f, Color.BLACK);

        gameOverPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        gameOverPaint.setColor(Color.parseColor("#FF1744"));
        gameOverPaint.setTextSize(110f);
        gameOverPaint.setTextAlign(Paint.Align.CENTER);
        gameOverPaint.setTypeface(Typeface.DEFAULT_BOLD);
        gameOverPaint.setShadowLayer(10f, 3f, 3f, Color.BLACK);

        restartPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        restartPaint.setColor(Color.parseColor("#00E676"));
        restartPaint.setTextSize(70f);
        restartPaint.setTextAlign(Paint.Align.CENTER);
        restartPaint.setTypeface(Typeface.DEFAULT_BOLD);

        overlayPaint = new Paint();
        overlayPaint.setColor(Color.parseColor("#AA000000")); // semi-transparent dark
    }

    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        screenWidth  = w;
        screenHeight = h;

        // Scale background to screen size
        Bitmap rawBg = BitmapFactory.decodeResource(getResources(), R.drawable.galaxy);
        backgroundBitmap = Bitmap.createScaledBitmap(rawBg, screenWidth, screenHeight, true);

        resetGame();
    }

    private void resetGame() {
        playerX = screenWidth / 2f - PLAYER_SIZE / 2f;
        playerY = screenHeight - PLAYER_SIZE - 120f;

        enemyX  = random.nextInt(Math.max(1, screenWidth - ENEMY_SIZE));
        enemyY  = -ENEMY_SIZE;

        enemySpeed = 18f;
        score      = 0;
        isGameOver = false;

        float btnW = 420f, btnH = 110f;
        restartLeft   = screenWidth / 2f - btnW / 2f;
        restartTop    = screenHeight / 2f + 90f;
        restartRight  = screenWidth / 2f + btnW / 2f;
        restartBottom = screenHeight / 2f + 90f + btnH;
    }

    // ─── GAME LOOP ────────────────────────────────────────────────

    @Override
    public void run() {
        while (isPlaying) {
            update();
            postInvalidate();
            try {
                Thread.sleep(17); // ~60 FPS
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void update() {
        if (isGameOver) return;

        enemyY += enemySpeed;

        if (enemyY > screenHeight) {
            enemyY  = -ENEMY_SIZE;
            enemyX  = random.nextInt(Math.max(1, screenWidth - ENEMY_SIZE));
            score++;

            // Speed up every 5 points, max 60
            if (score % 5 == 0 && enemySpeed < 60f) {
                enemySpeed += 5f;
            }
        }

        // Collision detection (AABB) with slight padding for fairness
        int pad = 20;
        if (playerX + pad < enemyX + ENEMY_SIZE - pad &&
                playerX + PLAYER_SIZE - pad > enemyX + pad &&
                playerY + pad < enemyY + ENEMY_SIZE - pad &&
                playerY + PLAYER_SIZE - pad > enemyY + pad) {

            isGameOver = true;
            if (score > highScore) highScore = score;
        }
    }

    // ─── DRAW ─────────────────────────────────────────────────────

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        // 1. Galaxy background
        if (backgroundBitmap != null) {
            canvas.drawBitmap(backgroundBitmap, 0, 0, null);
        }

        if (!isGameOver) {
            // 2. Asteroid (enemy)
            if (enemyBitmap != null) {
                canvas.drawBitmap(enemyBitmap, enemyX, enemyY, null);
            }

            // 3. Spaceship (player)
            if (playerBitmap != null) {
                canvas.drawBitmap(playerBitmap, playerX, playerY, null);
            }

            // 4. Score HUD
            canvas.drawText("⭐ " + score,       40f,  80f,  scorePaint);
            canvas.drawText("🏆 " + highScore,   40f,  155f, scorePaint);

        } else {
            drawGameOverScreen(canvas);
        }
    }

    private void drawGameOverScreen(Canvas canvas) {
        float cx = screenWidth / 2f;
        float cy = screenHeight / 2f;

        // Dim overlay
        canvas.drawRect(0, 0, screenWidth, screenHeight, overlayPaint);

        canvas.drawText("GAME OVER", cx, cy - 140f, gameOverPaint);

        scorePaint.setTextAlign(Paint.Align.CENTER);
        canvas.drawText("Score:     " + score,     cx, cy - 30f, scorePaint);
        canvas.drawText("Best:      " + highScore, cx, cy + 55f, scorePaint);
        scorePaint.setTextAlign(Paint.Align.LEFT);

        // Restart button box
        Paint btnBg = new Paint(Paint.ANTI_ALIAS_FLAG);
        btnBg.setColor(Color.parseColor("#1E1E1E"));
        canvas.drawRoundRect(
                restartLeft, restartTop, restartRight, restartBottom,
                28f, 28f, btnBg
        );
        canvas.drawText("▶  RESTART", cx, restartBottom - 28f, restartPaint);
    }

    // ─── TOUCH ────────────────────────────────────────────────────

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int action = event.getAction();
        if (action == MotionEvent.ACTION_DOWN || action == MotionEvent.ACTION_MOVE) {

            if (isGameOver) {
                float tx = event.getX(), ty = event.getY();
                if (tx >= restartLeft && tx <= restartRight &&
                        ty >= restartTop  && ty <= restartBottom) {
                    resetGame();
                }
            } else {
                playerX = event.getX() - PLAYER_SIZE / 2f;
                // Clamp within screen
                if (playerX < 0) playerX = 0;
                if (playerX + PLAYER_SIZE > screenWidth)
                    playerX = screenWidth - PLAYER_SIZE;
            }
        }
        return true;
    }

    // ─── LIFECYCLE ────────────────────────────────────────────────

    public void pause() {
        isPlaying = false;
        try {
            if (gameThread != null) gameThread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void resume() {
        isPlaying = true;
        gameThread = new Thread(this);
        gameThread.start();
    }
}
```
---

## 👨‍💻 Author

Made with ❤️ and Java.  
Feel free to fork, star ⭐, and improve!

---

## 📄 License
```
MIT License — free to use, modify and distribute.
```
