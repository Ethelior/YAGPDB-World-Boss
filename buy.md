{{/*
================================================
 World Boss System

 Command:
 !buy

 Version:
 1.0.0

 Description:
 Purchase items from the World Boss Shop.

================================================
*/}}

{{$config := sdict
    "profilePrefix" "rpg_profile_"

    "successColor" 3066993
    "errorColor" 15158332
    "embedColor" 3447003
"defaultInventorySlots" 20
    "footer" "World Boss System • Buy v1.0.0"
}}

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

{{if lt (len .CmdArgs) 1}}

{{sendMessage nil (cembed
"title" "❌ Missing Item"

"description"
"Usage:\n`!buy <item> [quantity]`\n\nExample:\n`!buy hp 5`"

"color" $config.errorColor

"footer" (sdict
"text" $config.footer
)
)}}

{{return}}

{{end}}

{{/* =======================================================
   Item Database
======================================================= */}}

{{$items := sdict}}

{{$items.Set "hp" (sdict
    "name" "HP Potion"
    "price" 25
    "sellPrice" 12
    "category" "consumable"
    "stackable" true
)}}

{{$items.Set "ghp" (sdict
    "name" "Greater HP Potion"
    "price" 60
    "sellPrice" 30
    "category" "consumable"
    "stackable" true
)}}

{{$items.Set "maxhp" (sdict
    "name" "Max HP Potion"
    "price" 120
    "sellPrice" 60
    "category" "consumable"
    "stackable" true
)}}

{{/* =======================================================
   Quantity Parsing
======================================================= */}}

{{$itemAlias := lower (index .CmdArgs 0)}}

{{$quantity := 1}}

{{if ge (len .CmdArgs) 2}}

    {{$quantity = toInt (index .CmdArgs 1)}}

    {{if lt $quantity 1}}

        {{sendMessage nil (cembed
            "title" "❌ Invalid Quantity"
            "description" "Quantity must be greater than **0**."
            "color" $config.errorColor
            "footer" (sdict "text" $config.footer)
        )}}

        {{return}}

    {{end}}

{{end}}

{{/* =======================================================
   Item Validation
======================================================= */}}

{{$item := index $items $itemAlias}}

{{if not $item}}

{{sendMessage nil (cembed
    "title" "❌ Unknown Item"
    "description" "This item does not exist."
    "color" $config.errorColor
    "footer" (sdict "text" $config.footer)
)}}

{{return}}

{{end}}

{{$totalCost := mult $item.price $quantity}}

{{/* =======================================================
   Boss Token Check
======================================================= */}}

{{if lt $profile.tokens $totalCost}}

{{sendMessage nil (cembed
    "title" "❌ Not Enough Boss Tokens"

    "description" (printf
"You need **%d Boss Tokens** but only have **%d**."
$totalCost
$profile.tokens
    )

    "color" $config.errorColor

    "footer" (sdict
        "text" $config.footer
    )
)}}

{{return}}

{{end}}

{{/* =======================================================
   Inventory Creation
======================================================= */}}

{{if not $profile.inventory}}

{{$profile.Set "inventory" (sdict)}}

{{end}}

{{$inventory := $profile.inventory}}

{{/* =======================================================
   Inventory Capacity Check
======================================================= */}}

{{$usedSlots := 0}}

{{range $k, $v := $inventory}}
    {{if gt (toInt $v) 0}}
        {{$usedSlots = add $usedSlots 1}}
    {{end}}
{{end}}

{{$maxSlots := or $profile.maxInventorySlots $config.defaultInventorySlots}}

{{$alreadyOwned := false}}

{{with (index $inventory $itemAlias)}}
    {{$alreadyOwned = true}}
{{end}}

{{if and (not $alreadyOwned) (ge $usedSlots $maxSlots)}}

{{sendMessage nil (cembed
    "title" "🎒 Inventory Full"
    "description" "You don't have enough free inventory slots."
    "color" $config.errorColor
    "footer" (sdict
        "text" $config.footer
    )
)}}

{{return}}

{{end}}

{{/* =======================================================
   Add Item
======================================================= */}}

{{$current := 0}}

{{with (index $inventory $itemAlias)}}
    {{$current = toInt .}}
{{end}}

{{$inventory.Set $itemAlias (add $current $quantity)}}

{{/* =======================================================
   Remove Boss Tokens
======================================================= */}}

{{$profile.Set "tokens" (sub $profile.tokens $totalCost)}}

{{dbSet .User.ID $profileKey $profile}}



{{/*Purchase success*/}}

{{sendMessage nil (cembed
    "title" "🛒 Purchase Successful"

    "description" (printf
"Successfully purchased:\n\n**%s**\n\n📦 Quantity: **x%d**\n\n💰 Cost: **%d Boss Tokens**\n\n🪙 Remaining Balance: **%d Boss Tokens**"
        $item.name
        $quantity
        $totalCost
        $profile.tokens
    )

    "color" $config.successColor

    "footer" (sdict
        "text" $config.footer
    )
)}}