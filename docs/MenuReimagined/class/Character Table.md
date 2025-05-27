---
    tags:
        - Class
---
## Class "Character Table"

???+ info
    You can edit it by using the [MenuReimagined_GetCharacterMenu](../enums/MenuReimaginedCallbacks/#menureimagined_getcharactermenu) callback.

    ??? example

        ```lua
            local function Test()
            ---@type table<string,table<string,NewCharacterSheet>>
            local characterMenuTable = {}
            characterMenuTable.Tainted = {
                Eden = {
                    CharacterSprite = Sprite("gfx/ui/main menu/MenuReimagined/Tainted_Eden.anm2", true)
                }
            }
            characterMenuTable.Tainted.Eden.CharacterSprite:Play(
                characterMenuTable.Tainted.Eden.CharacterSprite:GetDefaultAnimationName(), true)
            return characterMenuTable
            end

        mod:AddCallback("MenuReimagined_GetCharacterMenu", Test)

        ```
        This changes the tainted Eden Sprite to an animated one

## Structure
This table is composed by other subtables.
Each of them are named as their menu varian type, the default ones are:

|Type|Comments|
|:--|:--|
|Vanilla|The normal character type|
|Tainted|The Tainted variants menu type|

???+ info
    Returning a non vanilla subtable generates it and includes it into the menutypes, more explained at [Custom MenuTypes](#custom-menutypes)


# Custom MenuTypes
To do documentation.  
It's functional already.


