# Retro Terminal Games Collection 🎮🐍⚔️

A collection of classic arcade and RPG terminal games built from scratch using **C** and **Python**. This repository showcases low-level system programming concepts in C alongside object-oriented design and game loops in Python.

---

## 🕹️ Included Games

### 1. C Terminal Snake (`snake.c`)
* **Description:** A classic grid-based Snake game written in modern C. 
* **Key Highlights:** 
  * Implements non-blocking POSIX input handling (`kbhit` / `getch`).
  * Features real-time collision detection (walls and self-intersection).
  * Dynamic tail-following logic and score computation.

### 2. Python Dungeon Crawler RPG (`dungeon_crawler.py`)
* **Description:** A turn-based rogue-lite dungeon crawler featuring procedural entity placement.
* **Key Highlights:**
  * Interactive map grid with custom ASCII rendering (`@` for player, `G` for goblins, `$` for loot, `E` for exit).
  * Turn-based combat loop with health tracking, attack variances, and healing potion inventory.
  * Win/loss state management.

---

## 📂 Project Directory Structure

```text
retro-terminal-games/
├── snake.c               # C Terminal Snake Game source code
├── dungeon_crawler.py    # Python RPG Dungeon Crawler source code
└── README.md             # Repository documentation
