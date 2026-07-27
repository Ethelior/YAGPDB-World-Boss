{{/*
================================================
 World Boss System

 Command:
 !attack

 Version: 1.0.0

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

    "footer" "World Boss System v1.0.0"
}}

{{/* Attack Cooldown */}}

{{$cooldownKey := print "wb_attack_cd_" .User.ID}}

{{if not (hasPermissions 8)}}

    {{$cd := dbGet 0 $cooldownKey}}

    {{if $cd}}

        {{sendMessage nil (cembed
    "title" "⏳ Attack Cooldown"
    "description" "You must wait **10 minutes** before attacking again."
    "color" 16753920
)}}

{{return}}

        {{return}}

    {{end}}

    {{dbSetExpire 0 $cooldownKey true 600}}

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

{{$damage := randInt $config.minDamage (add $config.maxDamage 1)}}

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

{{/* Update Global Leaderboard */}}

{{$leaderKey := "wb_leaderboard"}}

{{$leaderDB := dbGet 0 $leaderKey}}

{{if $leaderDB}}

    {{$leader := $leaderDB.Value}}

    {{$userID := str .User.ID}}

    {{$oldDamage := 0}}

    {{if $leader.Get $userID}}
        {{$oldDamage = toInt ($leader.Get $userID)}}
    {{end}}

    {{$leader.Set $userID (add $oldDamage $damage)}}

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


{{$embed := cembed
    "title" "⚔️ World Boss Attack"
    "description" (printf
        "<@%d> attacked %s **%s**\n\n💥 Damage: **%d**\n%s\n\n❤️ Boss HP: **%d / %d**"
        .User.ID
        $boss.emoji
        $boss.name
        $damage
        $message
        $boss.hp
        $boss.max_hp
    )
    "color" $config.successColor
    "footer" (sdict "text" $config.footer)
}}

{{sendMessage nil $embed}}