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

## 👨‍💻 Author

Made with ❤️ and Java.  
Feel free to fork, star ⭐, and improve!

---

## 📄 License
```
MIT License — free to use, modify and distribute.
```
