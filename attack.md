{{/*
================================================
 World Boss System

 Command:
 !attack

 Version: 2.0.0

 Description:
 Attack the active World Boss.

 Features:
 - Random damage
 - Critical hits
 - Miss chance
 - Player statistics
 - Boss HP updates

================================================
*/}}

{{$config := sdict
    "bossKey" "wb_boss"
    "statsPrefix" "wb_stats_"

    "minDamage" 20
    "maxDamage" 50

    "critChance" 10
    "critMultiplier" 2

    "missChance" 5

    "successColor" 3066993
    "errorColor" 15158332

    "footer" "World Boss System v2.0.0"
}}

{{$profileKey := print "rpg_profile_" .User.ID}}

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

{{/* Attack Cooldown */}}

{{$cooldownKey := print "wb_attack_cd_" .User.ID}}

{{if not (hasPermissions 8)}}

    {{$cd := dbGet 0 $cooldownKey}}

{{if $cd}}

    {{$expires := $cd.ExpiresAt.Unix}}

    {{sendMessage nil (cembed
        "title" "⏳ ATTACK COOLDOWN"
        "description" (printf
            "⚔️ You are recovering from your previous attack.\n\n🕒 You can attack again **<t:%d:R>**."
            $expires
        )
        "color" $config.errorColor
        "footer" (sdict
            "text" $config.footer
        )
    )}}

    {{return}}

{{end}}

    {{dbSetExpire 0 $cooldownKey true 600}}

{{end}}

{{if not $profile.alive}}

    {{sendMessage nil (cembed
        "title" "💀 You are dead!"
        "description" "Wait until you revive before attacking again."
        "color" $config.errorColor
    )}}

    {{return}}

{{end}}

{{/* Get Active Boss */}}

{{$bossDB := dbGet 0 $config.bossKey}}

{{if not $bossDB}}

    {{$embed := cembed
        "title" "❌ No Active World Boss"
        "description" "There is currently no boss to attack."
        "color" $config.errorColor
        "footer" (sdict "text" $config.footer)
    }}

    {{sendMessage nil $embed}}
    {{return}}

{{end}}


{{$boss := $bossDB.Value}}


{{if not $boss.alive}}

    {{$embed := cembed
        "title" "🏁 Battle Finished"
        "description" "This World Boss has already been defeated."
        "color" $config.errorColor
        "footer" (sdict "text" $config.footer)
    }}

    {{sendMessage nil $embed}}
    {{return}}

{{end}}


{{/* Miss Check */}}

{{$missRoll := randInt 1 101}}

{{if le $missRoll $config.missChance}}

    {{$embed := cembed
        "title" "❌ Attack Missed!"
        "description" (printf
            "<@%d> attacked **%s %s** but missed!"
            .User.ID
            $boss.emoji
            $boss.name
        )
        "color" $config.errorColor
        "footer" (sdict "text" $config.footer)
    }}

    {{sendMessage nil $embed}}
    {{return}}

{{end}}


{{/* Damage Calculation */}}

{{$damage := add
    $profile.attack
    (randInt 0 21)
}}

{{$critical := false}}

{{$critRoll := randInt 1 101}}

{{if le $critRoll $config.critChance}}

    {{$damage = mult $damage $config.critMultiplier}}
    {{$critical = true}}

{{end}}


{{/* Update Boss HP */}}

{{$newHP := sub $boss.hp $damage}}

{{if lt $newHP 0}}
    {{$newHP = 0}}
{{end}}

{{$boss.Set "hp" $newHP}}

{{if eq $newHP 0}}
    {{$boss.Set "alive" false}}
{{end}}


{{dbSet 0 $config.bossKey $boss}}


{{$bossMessages := cslice}}

{{if eq $boss.id "dragon_001"}}

    {{$bossMessages = cslice
        "breathes scorching flames!"
        "slashes with its massive claws!"
        "lets out a terrifying roar!"
        "burns everything in its path!"
        "dives from the sky!"
        "unleashes dragon fire!"
    }}

{{else if eq $boss.id "demon_001"}}

    {{$bossMessages = cslice
        "casts dark magic!"
        "summons demonic power!"
        "corrupts the battlefield!"
        "laughs menacingly before attacking!"
        "unleashes cursed flames!"
        "drains your life force!"
    }}

{{else if eq $boss.id "kraken_001"}}

    {{$bossMessages = cslice
        "crushes you with its tentacles!"
        "creates a massive tidal wave!"
        "drags you beneath the sea!"
        "wraps its tentacles around you!"
        "splashes a giant wave!"
        "emerges from the deep!"
    }}

{{else if eq $boss.id "titan_001"}}

    {{$bossMessages = cslice
        "fires a laser barrage!"
        "launches a missile strike!"
        "activates heavy assault mode!"
        "charges its plasma cannon!"
        "fires its railgun!"
        "deploys combat drones!"
    }}

{{else}}

    {{$bossMessages = cslice
        "attacks!"
    }}

{{end}}

{{$bossAction := index $bossMessages (randInt 0 (len $bossMessages))}}

{{/* Boss Counter Attack */}}

{{$bossHit := randInt $boss.min_attack (add $boss.max_attack 1)}}

{{$bossCritical := false}}

{{if le (randInt 1 101) $boss.critChance}}
    {{$bossHit = mult $bossHit $boss.critMultiplier}}
    {{$bossCritical = true}}
{{end}}

{{$bossDamage := sub $bossHit $profile.defense}}

{{if lt $bossDamage 1}}
    {{$bossDamage = 1}}
{{end}}

{{$newPlayerHP := sub $profile.hp $bossDamage}}

{{if lt $newPlayerHP 0}}
    {{$newPlayerHP = 0}}
{{end}}

{{$profile.Set "hp" $newPlayerHP}}
{{$tokenReward := 1}}

{{if $critical}}
    {{$tokenReward = 5}}
{{end}}

{{$profile.Set "tokens" (add $profile.tokens $tokenReward)}}

{{$xpReward := randInt 5 13}}

{{if $critical}}
    {{$xpReward = add $xpReward 15}}
{{end}}

{{$profile.Set "xp" (add $profile.xp $xpReward)}}

{{$levelUp := false}}

{{$neededXP := mult $profile.level 100}}

{{if ge $profile.xp $neededXP}}

    {{$profile.Set "xp" (sub $profile.xp $neededXP)}}

    {{$profile.Set "level" (add $profile.level 1)}}

    {{$profile.Set "maxHP" (add $profile.maxHP 50)}}

    {{$profile.Set "hp" $profile.maxHP}}

    {{$profile.Set "attack" (add $profile.attack 2)}}

    {{$profile.Set "defense" (add $profile.defense 1)}}

    {{$levelUp = true}}

{{end}}

{{/* Death Check */}}

{{$deathMessage := ""}}

{{if and (eq $newPlayerHP 0) (not $levelUp)}}

    {{$profile.Set "alive" false}}

    {{$deathMessage = "\n\n☠️ **You have been defeated by the World Boss!**\nUse `!revive` when available."}}

{{end}}

{{dbSet .User.ID $profileKey $profile}}

{{/* Player Stats */}}

{{$statsKey := print $config.statsPrefix .User.ID}}

{{$statsDB := dbGet 0 $statsKey}}

{{if $statsDB}}

    {{$stats := $statsDB.Value}}

    {{$stats.Set "damage" (add $stats.damage $damage)}}
{{$stats.Set "attacks" (add $stats.attacks 1)}}

    {{if $critical}}
        {{$stats.Set "crits" (add $stats.crits 1)}}
    {{end}}

    {{if gt $damage $stats.best_hit}}
        {{$stats.Set "best_hit" $damage}}
    {{end}}

    {{dbSet 0 $statsKey $stats}}

{{else}}

    {{$critCount := 0}}

{{if $critical}}
    {{$critCount = 1}}
{{end}}

{{$stats := sdict
    "damage" $damage
    "attacks" 1
    "crits" $critCount
    "best_hit" $damage
}}

    {{dbSet 0 $statsKey $stats}}

{{end}}

{{$leaderKey := "wb_leaderboard"}}

{{$leaderDB := dbGet 0 $leaderKey}}

{{if $leaderDB}}
    {{$leader := $leaderDB.Value}}

    {{$current := toInt (index $leader (str .User.ID))}}

    {{$leader.Set (str .User.ID) (add $current $damage)}}

    {{dbSet 0 $leaderKey $leader}}

{{else}}

    {{$leader := sdict}}
    {{$leader.Set (str .User.ID) $damage}}

    {{dbSet 0 $leaderKey $leader}}

{{end}}


{{/* Result Embed */}}

{{$message := ""}}

{{if $critical}}
    {{$message = "🔥 **CRITICAL HIT!**"}}
{{end}}

{{/* Combat Result Text */}}

{{$counterTitle := print $boss.emoji " " $boss.name " " $bossAction}}



{{if $bossCritical}}
    {{$counterTitle = print "💥 " $boss.name " unleashes a DEVASTATING CRITICAL HIT!"}}
{{end}}

{{$counterMessage := print
    "\n\n"
    $counterTitle
    "\n"
    "💔 Damage Received: **"
    $bossDamage
    "**\n"
    "❤️ Your HP: **"
    $profile.hp
    "/"
    $profile.maxHP
    "**"
}}

{{$counterMessage = print
    $counterMessage
    $deathMessage
}}

{{$rewardText := print "🪙 Reward: **+" $tokenReward " Boss Token"}}

{{if ne $tokenReward 1}}
    {{$rewardText = print $rewardText "s"}}
{{end}}

{{$rewardText = print $rewardText "**"}}

{{$embed := cembed
    "title" "⚔️ World Boss Attack"
    "description" (printf
    "<@%d> attacked %s **%s**\n\n💥 Damage: **%d**\n🪙 Reward: **+%d Boss Token(s)**\n\n✨ XP Gained: **+%d XP**\n%s\n\n❤️ Boss HP: **%d / %d**%s"
    .User.ID
    $boss.emoji
    $boss.name
    $damage
    $tokenReward
    $xpReward
    $message
    $boss.hp
    $boss.max_hp
    $counterMessage
)
    "color" $config.successColor
    "footer" (sdict "text" $config.footer)
}}

{{sendMessage nil $embed}}

{{if $levelUp}}

{{sendMessage nil (cembed
    "title" "🎉 LEVEL UP!"
    "description" (printf
        "Congratulations <@%d>!\n\n⭐ You reached **Level %d**!\n\n❤️ Max HP: **+50**\n⚔️ Attack: **+2**\n🛡️ Defense: **+1**\n\n💚 Your HP has been fully restored!"
        .User.ID
        $profile.level
    )
    "color" $config.successColor
    "footer" (sdict "text" $config.footer)
    )
}}

{{end}}
