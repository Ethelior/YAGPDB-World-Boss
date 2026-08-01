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
   Boosts
======================================================= */}}

{{if eq $category "boosts"}}

{{$description := print
"🪙 **Your Boss Tokens:** **" $tokens "**\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"⚔️ **Attack Boost**\n"
"💰 **150 Boss Tokens**\n"
"⏳ Duration: **1 Hour**\n"
"Increases your Attack by **25%**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🛡️ **Defense Boost**\n"
"💰 **150 Boss Tokens**\n"
"⏳ Duration: **1 Hour**\n"
"Increases your Defense by **25%**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"⭐ **XP Boost**\n"
"💰 **250 Boss Tokens**\n"
"⏳ Duration: **2 Hours**\n"
"Earn **50%** more XP from attacks.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🪙 **Boss Token Boost**\n"
"💰 **300 Boss Tokens**\n"
"⏳ Duration: **2 Hours**\n"
"Earn **50%** more Boss Tokens.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"**Purchase Examples**\n"
"`!buy atkboost 1`\n"
"`!buy defboost 1`\n"
"`!buy xpboost 1`\n"
"`!buy tokenboost 1`"
}}

{{$embed := cembed
    "title" "🛒 WORLD BOSS SHOP • ⚡ Boosts"
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
   Inventory Upgrades
======================================================= */}}

{{if eq $category "inventory"}}

{{$description := print
"🪙 **Your Boss Tokens:** **" $tokens "**\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🎒 **Inventory Upgrade I**\n"
"💰 **250 Boss Tokens**\n"
"Increase your Inventory Capacity\n"
"From **20 ➜ 30 Slots**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🎒 **Inventory Upgrade II**\n"
"💰 **500 Boss Tokens**\n"
"Increase your Inventory Capacity\n"
"From **30 ➜ 40 Slots**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🎒 **Inventory Upgrade III**\n"
"💰 **1000 Boss Tokens**\n"
"Increase your Inventory Capacity\n"
"From **40 ➜ 50 Slots**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"**Purchase Examples**\n"
"`!buy bag1`\n"
"`!buy bag2`\n"
"`!buy bag3`"
}}

{{$embed := cembed
    "title" "🛒 WORLD BOSS SHOP • 🎒 Inventory Upgrades"
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
   Special Items
======================================================= */}}

{{if eq $category "special"}}

{{$description := print
"🪙 **Your Boss Tokens:** **" $tokens "**\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"💎 **Lucky Charm**\n"
"💰 **2,500 Boss Tokens**\n"
"Permanent **+5% Boss Token Gain**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"📜 **Ancient Knowledge**\n"
"💰 **3,000 Boss Tokens**\n"
"Permanent **+5% XP Gain**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🗡️ **Warrior's Emblem**\n"
"💰 **5,000 Boss Tokens**\n"
"Permanent **+5 Attack**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"🛡️ **Guardian's Crest**\n"
"💰 **5,000 Boss Tokens**\n"
"Permanent **+5 Defense**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"❤️ **Vitality Crystal**\n"
"💰 **7,500 Boss Tokens**\n"
"Permanent **+100 Max HP**.\n\n"

"━━━━━━━━━━━━━━━━━━\n\n"

"**Purchase Examples**\n"
"`!buy lucky`\n"
"`!buy knowledge`\n"
"`!buy emblem`\n"
"`!buy crest`\n"
"`!buy vitality`"
}}

{{$embed := cembed
    "title" "🛒 WORLD BOSS SHOP • ⭐ Special Items"
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
