{{/*
================================================
 World Boss System

 Command:
 !inventory

 Version:
 2.1.0

 Created by:
 Ethelior

 Description:
 Displays a player's inventory.

================================================
*/}}

{{$config := sdict

    "profilePrefix" "rpg_profile_"

    "defaultSlots" 20

    "embedColor" 3447003
    "errorColor" 15158332

    "footer" "World Boss System • Inventory v2.1.0"

}}

{{/* =======================================================
   Target User
======================================================= */}}

{{$targetID := .User.ID}}

{{if gt (len .Message.Mentions) 0}}
    {{$targetID = (index .Message.Mentions 0).ID}}
{{end}}

{{/* =======================================================
   Load Profile
======================================================= */}}

{{$profileKey := print $config.profilePrefix $targetID}}

{{$profileDB := dbGet (toInt $targetID) $profileKey}}

{{if not $profileDB}}

{{sendMessage nil (cembed
    "title" "❌ Profile Not Found"
    "description" "Create your profile first using **!profile**."
    "color" $config.errorColor
)}}

{{return}}

{{end}}

{{$profile := $profileDB.Value}}

{{if not $profile.inventory}}
    {{$profile.Set "inventory" (sdict)}}
{{end}}

{{$inventory := $profile.inventory}}

{{$maxSlots := or $profile.maxInventorySlots $config.defaultSlots}}

{{/* =======================================================
   Item Database
======================================================= */}}

{{$itemDB := sdict}}

{{$itemDB.Set "hp" (sdict
    "name" "HP Potion"
    "emoji" "❤️"
)}}

{{$itemDB.Set "ghp" (sdict
    "name" "Greater HP Potion"
    "emoji" "💚"
)}}

{{$itemDB.Set "maxhp" (sdict
    "name" "Max HP Potion"
    "emoji" "💖"
)}}

{{/* =======================================================
   Inventory Parsing
======================================================= */}}

{{$usedSlots := 0}}

{{$inventoryText := ""}}

{{range $itemID, $amount := $inventory}}

    {{if gt (toInt $amount) 0}}

        {{$usedSlots = add $usedSlots 1}}

        {{$item := index $itemDB $itemID}}

        {{$emoji := "📦"}}
        {{$name := $itemID}}

        {{if $item}}
            {{$emoji = $item.emoji}}
            {{$name = $item.name}}
        {{end}}

        {{$inventoryText = print
            $inventoryText
            $emoji
            " "
            $name
            " ×"
            $amount
            "\n"
        }}

    {{end}}

{{end}}

{{if eq $inventoryText ""}}

{{$inventoryText = "Your inventory is empty."}}

{{end}}

{{$freeSlots := sub $maxSlots $usedSlots}}

{{$capacityText := print
"📦 Used: **" $usedSlots " / " $maxSlots "**\n"
"📭 Free: **" $freeSlots "**"
}}

{{/* =======================================================
   Inventory Embed
======================================================= */}}

{{$embed := cembed
    "title" "🎒 INVENTORY"

    "fields" (cslice

        (sdict
            "name" "👤 Player"
            "value" (print "<@" $targetID ">")
            "inline" false
        )

        (sdict
            "name" "📦 Inventory"
            "value" $inventoryText
            "inline" false
        )

        (sdict
            "name" "📊 Capacity"
            "value" $capacityText
            "inline" false
        )

    )

    "color" $config.embedColor

    "footer" (sdict
        "text" $config.footer
    )
}}

{{sendMessage nil $embed}}