⚙️ Installation Guide

This guide will walk you through the installation process for the World Boss System on your Discord server using YAGPDB.

---

📋 Requirements

Before installing, make sure you have:

- A Discord server with Administrator permissions.
- YAGPDB invited to your server.
- Custom Commands enabled.
- Database access enabled in YAGPDB.

---

📥 Step 1 – Import the Commands

Import all Custom Commands included in this repository.

It is recommended to import them in the following order:

Admin Commands

- "!spawnboss"
- "!endboss"
- "!setstat"
- "!resetprofile"
- "!item"

User Commands

- "!profile"
- "!boss"
- "!attack"
- "!revive"
- "!bossstats"
- "!leaderboard"
- "!inventory"
- "!shop"
- "!buy"
- "!sell"
- "!use"
- "!heal"

---

⚙️ Step 2 – Configure the System

Open every command and edit the configuration variables to match your server.

Configuration options include:

- Embed colors
- Cooldowns
- Starting player stats
- Shop prices
- Boss settings
- Database keys
- Webhook settings

For a complete explanation of every setting, see SETTINGS.md.

---

🌐 Step 3 – Configure Webhooks (Optional)

If you want the system to automatically post boss announcements, create a Discord webhook and paste the webhook URL into the configuration section of the required commands.

---

🧪 Step 4 – Test the Installation

Run the following commands to verify that everything works correctly.

!profile
!spawnboss
!boss
!attack
!leaderboard

If all commands execute successfully, the installation is complete.

---

📂 Recommended Channel Structure

Although not required, the following channel layout is recommended:

- "#world-boss"
- "#boss-log"
- "#bot-commands"

This keeps all RPG activity organized.

---

🔒 Permissions

Only trusted administrators should have access to the following commands:

- "!spawnboss"
- "!endboss"
- "!setstat"
- "!resetprofile"
- "!item"

Restrict these commands using Discord permissions or YAGPDB command restrictions.

---

✅ Installation Complete

Your World Boss System is now ready to use.

Create your first profile with "!profile", spawn a boss with "!spawnboss", and start battling!