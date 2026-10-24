import random
import os
import time

MAP_WIDTH = 12
MAP_HEIGHT = 8

class Player:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.hp = 100
        self.max_hp = 100
        self.attack = 15
        self.gold = 0
        self.potions = 2

class Enemy:
    def __init__(self, name, hp, attack):
        self.name = name
        self.hp = hp
        self.attack = attack

class DungeonGame:
    def __init__(self):
        self.player = Player(1, 1)
        self.exit_x = MAP_WIDTH - 2
        self.exit_y = MAP_HEIGHT - 2
        self.enemies = {}
        self.gold_drops = {}
        self.running = True
        self.generate_level()

    def generate_level(self):
        # Place random gold and enemies
        for _ in range(4):
            gx, gy = random.randint(1, MAP_WIDTH-2), random.randint(1, MAP_HEIGHT-2)
            if (gx, gy) != (1, 1) and (gx, gy) != (self.exit_x, self.exit_y):
                self.gold_drops[(gx, gy)] = random.randint(10, 30)

        for _ in range(3):
            ex, ey = random.randint(1, MAP_WIDTH-2), random.randint(1, MAP_HEIGHT-2)
            if (ex, ey) != (1, 1) and (ex, ey) != (self.exit_x, self.exit_y):
                self.enemies[(ex, ey)] = Enemy("Goblin", random.randint(25, 40), random.randint(8, 14))

    def clear_screen(self):
        os.system('cls' if os.name == 'nt' else 'clear')

    def render_map(self):
        self.clear_screen()
        print("=== PYTHON DUNGEON CRAWLER RPG ===")
        print(f"HP: {self.player.hp}/{self.player.max_hp} | Gold: {self.player.gold} | Potions: {self.player.potions}\n")

        for y in range(MAP_HEIGHT):
            line = ""
            for x in range(MAP_WIDTH):
                if x == 0 or x == MAP_WIDTH - 1 or y == 0 or y == MAP_HEIGHT - 1:
                    line += "# "
                elif x == self.player.x and y == self.player.y:
                    line += "@ " # Player
                elif (x, y) == (self.exit_x, self.exit_y):
                    line += "E " # Exit
                elif (x, y) in self.enemies:
                    line += "G " # Enemy Goblin
                elif (x, y) in self.gold_drops:
                    line += "$ " # Gold
                else:
                    line += ". "
            print(line)
        print("\nControls: (W) Up, (A) Left, (S) Down, (D) Right, (P) Heal, (Q) Quit")

    def battle(self, enemy_pos):
        enemy = self.enemies[enemy_pos]
        print(f"\n[!] A wild {enemy.name} appears! Combat Started!")
        time.sleep(1)

        while enemy.hp > 0 and self.player.hp > 0:
            print(f"\nYour HP: {self.player.hp} | {enemy.name} HP: {enemy.hp}")
            action = input("Action: (1) Attack, (2) Heal Potion: ").strip()

            if action == '1':
                dmg = self.player.attack + random.randint(-3, 3)
                enemy.hp -= dmg
                print(f"[>] You struck the {enemy.name} for {dmg} damage!")
            elif action == '2':
                if self.player.potions > 0:
                    self.player.hp = min(self.player.max_hp, self.player.hp + 30)
                    self.player.potions -= 1
                    print(f"[+] You used a potion! Restored 30 HP.")
                else:
                    print("[-] No potions left!")
                    continue
            else:
                print("[-] Invalid choice!")
                continue

            if enemy.hp <= 0:
                print(f"[★] You defeated the {enemy.name}!")
                del self.enemies[enemy_pos]
                time.sleep(1.5)
                break

            # Enemy Attacks Back
            e_dmg = enemy.attack + random.randint(-2, 2)
            self.player.hp -= e_dmg
            print(f"[<] {enemy.name} attacked you for {e_dmg} damage!")

            if self.player.hp <= 0:
                print("\n[☠] You were slain in the dungeon... Game Over!")
                self.running = False
                break

    def move_player(self, dx, dy):
        new_x = self.player.x + dx
        new_y = self.player.y + dy

        # Wall collisions
        if new_x <= 0 or new_x >= MAP_WIDTH - 1 or new_y <= 0 or new_y >= MAP_HEIGHT - 1:
            return

        self.player.x, self.player.y = new_x, new_y
        pos = (new_x, new_y)

        # Check interactions
        if pos in self.gold_drops:
            amount = self.gold_drops[pos]
            self.player.gold += amount
            del self.gold_drops[pos]

        if pos in self.enemies:
            self.battle(pos)

        if pos == (self.exit_x, self.exit_y):
            print("\n[🏆] VICTORY! You escaped the dungeon with all your treasures!")
            self.running = False

    def start(self):
        while self.running:
            self.render_map()
            cmd = input("\nEnter movement command: ").strip().lower()

            if cmd == 'w': self.move_player(0, -1)
            elif cmd == 's': self.move_player(0, 1)
            elif cmd == 'a': self.move_player(-1, 0)
            elif cmd == 'd': self.move_player(1, 0)
            elif cmd == 'p':
                if self.player.potions > 0:
                    self.player.hp = min(self.player.max_hp, self.player.hp + 30)
                    self.player.potions -= 1
            elif cmd == 'q':
                print("Exiting game.")
                break

if __name__ == "__main__":
    game = DungeonGame()
    game.start()

