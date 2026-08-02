{{/*
================================================
 World Boss RPG System

 Command:
 !revive

 Version: 2.0.0

 Description:
 Revive defeated players.

 Features:
 - Check death status
 - Restore HP
 - Restore alive state
 - Save profile

================================================
*/}}

{{$config := sdict
    "profilePrefix" "rpg_profile_"
    "successColor" 3066993
    "errorColor" 15158332
    "reviveCooldown" 3600
    "footer" "World Boss System v2.0.0"
}}

{{$profileKey := print $config.profilePrefix .User.ID}}

{{$profileDB := dbGet .User.ID $profileKey}}

{{if not $profileDB}}

    {{sendMessage nil (cembed
        "title" "❌ No RPG Profile"
        "description" "Create your profile first with `!profile`."
        "color" $config.errorColor
    )}}

    {{return}}

{{end}}

{{$profile := $profileDB.Value}}

{{/* Revive Cooldown - Admin Bypass */}}

{{$cooldownKey := print "revive_cd_" .User.ID}}

{{if not (hasPermissions 8)}}

    {{$cooldownDB := dbGet 0 $cooldownKey}}

{{if $cooldownDB}}

    {{$expires := $cooldownDB.ExpiresAt.Unix}}

    {{sendMessage nil (cembed
        "title" "⏳ REVIVE COOLDOWN"
        "description" (printf
            "💀 Your body is still recovering.\n\n⚔️ You can revive again **<t:%d:R>**."
            $expires
        )
        "color" $config.errorColor
        "footer" (sdict
            "text" $config.footer
        )
    )}}

    {{return}}

{{end}}

{{end}}

{{/* Check if already alive */}}

{{if $profile.alive}}

    {{sendMessage nil (cembed
        "title" "❤️ Already Alive"
        "description" "You are already ready for battle!"
        "color" $config.errorColor
        "footer" (sdict "text" $config.footer)
    )}}

    {{return}}

{{end}}


{{/* Revive Player */}}

{{$profile.Set "alive" true}}

{{$profile.Set "hp" $profile.maxHP}}

{{if not (hasPermissions 8)}}
    {{dbSetExpire 0 $cooldownKey true $config.reviveCooldown}}
{{end}}

{{dbSet .User.ID $profileKey $profile}}


{{$embed := cembed
    "title" "✨ Resurrection Complete!"
    "description" (printf
        "💀 You have returned to battle!\n\n❤️ HP Restored: **%d / %d**\n\n⚔️ You can attack the World Boss again!"
        $profile.hp
        $profile.maxHP
    )
    "color" $config.successColor
    "footer" (sdict "text" $config.footer)
}}

{{sendMessage nil $embed}}
