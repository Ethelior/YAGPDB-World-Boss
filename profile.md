{{/*
================================================
 World Boss System

 Command:
 !profile

 Version:
 2.0.0

 Created by:
 Ethelior

 Description:
 Displays an RPG player's profile.

 Features:
 - Self profile
 - Mention profile
 - XP Progress Bar
 - RPG Stats
 - Professional Embed

================================================
*/}}

{{$config := sdict
    "profilePrefix" "rpg_profile_"
    "embedColor" 3447003
    "errorColor" 15158332
    "footer" "World Boss System • v2.0.0"
}}

{{/* Target User */}}

{{$targetID := .User.ID}}

{{if gt (len .Message.Mentions) 0}}
    {{$targetID = (index .Message.Mentions 0).ID}}
{{end}}

{{/* Load Profile */}}

{{$profileKey := print $config.profilePrefix $targetID}}

{{$profileDB := dbGet (toInt $targetID) $profileKey}}

{{if not $profileDB}}

    {{sendMessage nil (cembed
        "title" "❌ Profile Not Found"
        "description" "This player does not have an RPG profile yet."
        "color" $config.errorColor
    )}}

    {{return}}

{{end}}

{{$profile := $profileDB.Value}}

{{/* XP Progress */}}

{{$level := $profile.level}}
{{$currentXP := $profile.xp}}

{{/* Change this if your level system uses another formula */}}
{{$requiredXP := mult $level 100}}

{{if lt $requiredXP 1}}
    {{$requiredXP = 1}}
{{end}}

{{$xpPercent := div (mult $currentXP 100) $requiredXP}}

{{if gt $xpPercent 100}}
    {{$xpPercent = 100}}
{{end}}

{{$filled := div $xpPercent 10}}

{{$xpBar := ""}}

{{range seq 1 10}}

    {{if le . $filled}}
        {{$xpBar = print $xpBar "█"}}
    {{else}}
        {{$xpBar = print $xpBar "□"}}
    {{end}}

{{end}}
{{/* Profile Embed */}}

{{$embed := cembed
    "title" "⚔️ RPG PROFILE"

    "fields" (cslice

    (sdict
        "name" "👤 Player"
        "value" (print "<@" $targetID ">")
        "inline" false
    )

    (sdict
        "name" "⭐ Level"
        "value" (print $profile.level)
        "inline" true
    )

    (sdict
        "name" "❤️ HP"
        "value" (printf "%d / %d" $profile.hp $profile.maxHP)
        "inline" true
    )

    (sdict
        "name" "🪙 Boss Tokens"
        "value" (print $profile.tokens)
        "inline" true
    )

    (sdict
        "name" "⚔️ Attack"
        "value" (print $profile.attack)
        "inline" true
    )

    (sdict
        "name" "🛡️ Defense"
        "value" (print $profile.defense)
        "inline" true
    )

    (sdict
        "name" "‎"
        "value" "‎"
        "inline" true
    )

    (sdict
        "name" "📈 Experience"
        "value" (printf "%s **%d%%**\n\n**%d / %d XP**"
            $xpBar
            $xpPercent
            $currentXP
            $requiredXP
        )
        "inline" false
    )

)

    "color" $config.embedColor

    "footer" (sdict
        "text" $config.footer
    )
}}

{{sendMessage nil $embed}}
