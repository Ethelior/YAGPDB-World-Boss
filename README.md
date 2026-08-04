⚔️ World Boss System for YAGPDB

A complete World Boss RPG System built with YAGPDB Custom Commands for Discord.

Fight powerful bosses with your community, level up your character, collect items, manage your inventory, and compete on the global leaderboard.

---

✨ Features

- 🐉 Community World Boss battles
- ⚔️ Turn-based attack system
- ❤️ Player HP & revive system
- 🎒 Inventory management
- 🛒 Item shop
- 💊 Healing system
- 📦 Item usage
- 💰 Buy & sell items
- 📊 Personal battle statistics
- 🏆 Global leaderboard
- 👤 RPG player profiles
- 🛠️ Fully configurable
- 💾 Database powered using YAGPDB

---

👤 User Commands

| Command | Description | Permission |
| "!attack"| Attack the active World Boss and deal damage. | 👤 User |
| "!boss"| Display detailed information about the current World Boss, including its HP and battle status. | 👤 User |
| "!bossstats" | View your personal World Boss battle statistics. | 👤 User |
| "!leaderboard" | Display the global leaderboard ranked by total damage dealt. | 👤 User |
| "!profile" | Create your RPG profile (if it doesn't exist) and view your character's statistics. | 👤 User |
| "!inventory" | Display all items currently stored in your inventory.| 👤 User |
| "!shop" | Browse the available items in the World Boss Shop. | 👤 User |
| "!buy" | Purchase an item from the shop using Boss Tokens. | 👤 User |
| "!sell" | Sell items from your inventory for Boss Tokens. | 👤 User |
| "!use" | Use an item from your inventory. | 👤 User |
| "!heal" | Restore your HP using the available healing system. | 👤 User |
| "!revive" | Revive your character after being defeated by a World Boss. | 👤 User |

---

🛡️ Admin Commands

Command| Description| Permission
"!spawnboss"| Spawn a new World Boss encounter for all players.| 🛡️ Admin
"!endboss"| Immediately end the currently active World Boss battle.| 🛡️ Admin
"!setstat"| Modify a player's RPG statistics such as HP, Attack, Defense, Tokens, or other configurable stats.| 🛡️ Admin
"!resetprofile"| Permanently reset a player's RPG profile. This action cannot be undone and does not require confirmation.| 🛡️ Admin
"!item"| Give one or more items directly to a player's inventory.| 🛡️ Admin

---

🎮 Gameplay

1. An administrator spawns a World Boss.
2. Players create their profile using "!profile".
3. Attack the boss using "!attack".
4. Earn Boss Tokens and rewards.
5. Purchase items from the shop.
6. Use items to improve your chances in battle.
7. Climb the leaderboard and become the strongest hunter.

---

📦 Installation

The complete installation guide is available in INSTALLATION.md.

It includes:

- Importing the Custom Commands
- Command setup
- Database configuration
- Webhook configuration
- Recommended installation order

---

⚙️ Configuration

All configurable values are documented in SETTINGS.md.

Examples include:

- Starting player stats
- Attack cooldown
- Boss settings
- Shop prices
- Item values
- Embed colors
- Database keys

---

📁 Project Structure

README.md
INSTALLATION.md
SETTINGS.md
CHANGELOG.md
LICENSE

Commands/
 ├── User/
 └── Admin/

---

🤝 Contributing

Suggestions, bug reports and improvements are always welcome.

If you discover an issue or have an idea for a new feature, feel free to open an Issue or submit a Pull Request.

---

📜 License

This project is released under the MIT License.

See the LICENSE file for more information.

---

❤️ Credits

Developed for the YAGPDB community.

If you enjoy this project, consider giving the repository a ⭐ to support future development.