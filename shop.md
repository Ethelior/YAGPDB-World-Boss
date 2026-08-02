{{/*
================================================
 World Boss System

 Command:
 !shop

 Version:
 1.0.0

 Created by:
 Ethelior

 Description:
 Browse the World Boss Shop.

 Features:
 - Category Menu
 - Boss Token Balance
 - Professional Embed

================================================
*/}}

{{$config := sdict
    "profilePrefix" "rpg_profile_"

    "embedColor" 3447003
    "errorColor" 15158332

    "footer" "World Boss System • Shop v1.0.0"
}}

{{/* =======================================================
   Item Database
======================================================= */}}

{{$items := sdict}}

{{/* =======================================================
   ❤️ Healing
======================================================= */}}

{{$items.Set "hp" (sdict
    "id" "hp"
    "name" "HP Potion"
    "emoji" "❤️"

    "category" "healing"

    "buyPrice" 25
    "sellPrice" 12

    "stackable" true

    "effectType" "heal"
    "effectValue" 250

    "rarity" "common"

    "description" "Restores 250 HP."

    "enabled" true
)}}

{{$items.Set "ghp" (sdict
    "id" "ghp"
    "name" "Greater HP Potion"
    "emoji" "💚"

    "category" "healing"

    "buyPrice" 60
    "sellPrice" 30

    "stackable" true

    "effectType" "heal"
    "effectValue" 500

    "rarity" "uncommon"

    "description" "Restores 500 HP."

    "enabled" true
)}}

{{$items.Set "maxhp" (sdict
    "id" "maxhp"
    "name" "Max HP Potion"
    "emoji" "💖"

    "category" "healing"

    "buyPrice" 120
    "sellPrice" 60

    "stackable" true

    "effectType" "heal"
    "effectValue" -1

    "rarity" "rare"

    "description" "Fully restores your HP."

    "enabled" true
)}}

{{/* =======================================================
   Load Profile
======================================================= */}}

{{$profileKey := print $config.profilePrefix .User.ID}}

{{$profileDB := dbGet .User.ID $profileKey}}

{{if not $profileDB}}

    {{sendMessage nil (cembed
        "title" "❌ No RPG Profile"
        "description" "Create your profile first with **!profile**."
        "color" $config.errorColor
    )}}

    {{return}}

{{end}}

{{$profile := $profileDB.Value}}

{{$tokens := $profile.tokens}}

{{/* =======================================================
   Category
======================================================= */}}

{{$category := ""}}

{{if ge (len .CmdArgs) 1}}
    {{$category = lower (index .CmdArgs 0)}}
{{end}}

{{/* =======================================================
   Main Shop Menu
======================================================= */}}

{{if eq $category ""}}

{{$description := print
"🪙 **Your Boss Tokens:** **" $tokens "**\n\n"
"━━━━━━━━━━━━━━━━━━\n\n"
"❤️ **Consumables**\n"
"Healing Potions\n\n"
"⚡ **Boosts**\n"
"Temporary Buffs\n\n"
"🎒 **Inventory**\n"
"Backpack Upgrades\n\n"
"⭐ **Special Items**\n"
"Permanent Upgrades\n\n"
"━━━━━━━━━━━━━━━━━━\n\n"
"**Commands**\n"
"`!shop consumables`\n"
"`!shop boosts`\n"
"`!shop inventory`\n"
"`!shop special`"
}}

{{$embed := cembed
    "title" "🛒 WORLD BOSS SHOP"
    "description" $description
    "color" $config.embedColor
    "footer" (sdict
        "text" $config.footer
    )
}}

{{sendMessage nil $embed}}

{{return}}

{{end}}

{{/* =======================================================
   Consumables
======================================================= */}}

{{if eq $category "consumables"}}

{{$description := print
"🪙 **Your Boss Tokens:** **" $tokens "**\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"❤️ **HP Potion**\n"
"💰 **25 Boss Tokens**\n"
"Restores **25%** of your Max HP.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"💖 **Greater HP Potion**\n"
"💰 **60 Boss Tokens**\n"
"Restores **50%** of your Max HP.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"💚 **Max HP Potion**\n"
"💰 **120 Boss Tokens**\n"
"Fully restores your HP.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"**Purchase Examples**\n"
"`!buy hp 5`\n"
"`!buy ghp 2`\n"
"`!buy maxhp 1`"
}}

{{$embed := cembed
    "title" "🛒 WORLD BOSS SHOP • ❤️ Consumables"
    "description" $description
    "color" $config.embedColor
    "footer" (sdict
        "text" $config.footer
    )
}}

{{sendMessage nil $embed}}
{{return}}

{{end}}

{{/* =======================================================
   ⚔️ Boosts
======================================================= */}}

{{$items.Set "atk" (sdict
    "id" "atk"
    "name" "Attack Boost"
    "emoji" "⚔️"

    "category" "boost"

    "buyPrice" 150
    "sellPrice" 75

    "stackable" true

    "effectType" "attackBoost"
    "effectValue" 25

    "duration" 3600

    "rarity" "uncommon"

    "description" "Increase Attack by 25% for 1 hour."

    "enabled" true
)}}

{{$items.Set "def" (sdict
    "id" "def"
    "name" "Defense Boost"
    "emoji" "🛡️"

    "category" "boost"

    "buyPrice" 150
    "sellPrice" 75

    "stackable" true

    "effectType" "defenseBoost"
    "effectValue" 25

    "duration" 3600

    "rarity" "uncommon"

    "description" "Increase Defense by 25% for 1 hour."

    "enabled" true
)}}

{{$items.Set "xp" (sdict
    "id" "xp"
    "name" "XP Boost"
    "emoji" "⭐"

    "category" "boost"

    "buyPrice" 250
    "sellPrice" 125

    "stackable" true

    "effectType" "xpBoost"
    "effectValue" 50

    "duration" 7200

    "rarity" "rare"

    "description" "Gain 50% more XP for 2 hours."

    "enabled" true
)}}

{{$items.Set "token" (sdict
    "id" "token"
    "name" "Boss Token Boost"
    "emoji" "🪙"

    "category" "boost"

    "buyPrice" 300
    "sellPrice" 150

    "stackable" true

    "effectType" "tokenBoost"
    "effectValue" 50

    "duration" 7200

    "rarity" "rare"

    "description" "Gain 50% more Boss Tokens for 2 hours."

    "enabled" true
)}}

{{/* =======================================================
   🎒 Inventory
======================================================= */}}

{{$items.Set "bag" (sdict
    "id" "bag"
    "name" "Inventory Expansion"
    "emoji" "🎒"

    "category" "inventory"

    "buyPrice" 250
    "sellPrice" 125

    "stackable" false

    "effectType" "inventorySlots"
    "effectValue" 10

    "rarity" "rare"

    "description" "Increase your inventory capacity by 10 slots."

    "enabled" true
)}}

{{/* =======================================================
   ✨ Special
======================================================= */}}

{{/* =======================================================
   Invalid Category
======================================================= */}}

{{sendMessage nil (cembed
    "title" "❌ Unknown Shop Category"

    "description" (print
"Please choose one of the available shop categories.\n\n"

"❤️ **Consumables**\n"
"`!shop consumables`\n\n"

"⚡ **Boosts**\n"
"`!shop boosts`\n\n"

"🎒 **Inventory**\n"
"`!shop inventory`\n\n"

"⭐ **Special Items**\n"
"`!shop special`"
    )

    "color" $config.errorColor

    "footer" (sdict
        "text" $config.footer
    )
)}}

{{return}}
