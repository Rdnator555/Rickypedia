---
tags:
  - Enum
title: MenuReimaginedCallbacks
---

<h2> Enum "MenuReimaginedCallbacks" </h2>

This is the list of all the modded callbacks Menu Reimagined adds.

### MenuReimagined_GetCharacterMenu

| Value | Function Args | Optional Args | Return |
|:--|:--|:--|:--|
| MenuReimagined_GetCharacterMenu | void          | -             | [Character Table](../class/Character%20Table.md) |

Runs when we get the characters after the vanilla menu is generated.
Allows customization of the character portraits, menu type and create new menu type variants.

### MenuReimagined_CarouselRender

| Value | Function Args | Optional Args | Return |
|:--|:--|:--|:--|
|MenuReimagined_CarouselRender|([PlayerType](https://wofsauge.github.io/IsaacDocs/rep/enums/PlayerType.html?h=playerty#enum-playertype) Player,[PortraitSprite]() Sprite,[CarrouselStatus](CarrouselStatus.md) Status)| [PlayerType](https://wofsauge.github.io/IsaacDocs/rep/enums/PlayerType.html?h=playerty#enum-playertype) | void |

Runs every time the character carrousel renders.
It lets mods edit and make their own animations to their characters if needed, with the given [CarrouselStatus](CarrouselStatus.md)

### MenuReimagined_MenuCode

| Value                   | Function Args | Optional Args | Return |
|:--|:--|:--|:--|
| MenuReimagined_MenuCode |([PlayerType](https://wofsauge.github.io/IsaacDocs/rep/enums/PlayerType.html?h=playerty#enum-playertype) Player)|[PlayerType](https://wofsauge.github.io/IsaacDocs/rep/enums/PlayerType.html?h=playerty#enum-playertype)|void|

Runs when the given PlayerType is selected
It's used for mods to execute stuff when a character in specific is selected

??? example
    The next code renders the number of eden tokens on the mouse position when Cain is selected.

    ```lua
    local meat = Font()
    meat:Load("font/teammeatfont16bold.fnt")

    local function TestMenuCode(id)
        local tokens = Isaac.GetPersistentGameData():GetEventCounter(EventCounter.EDEN_TOKENS) .. " Tokens"
        local mouse = Isaac.WorldToScreen(Input.GetMousePosition(true))
        meat:DrawString(tokens, mouse.X, mouse.Y, KColor(0.212, 0.184, 0.176, 1))
    end

    mod:AddCallback("MenuReimagined_MenuCode", TestMenuCode(), PlayerType.PLAYER_CAIN) 
    ```

### MenuReimagined_OnModdedMenuUpdate

| Value                             | Function Args | Optional Args | Return |
|:--|:--|:--|:--|
| MenuReimagined_OnModdedMenuUpdate |([MenuType](https://repentogon.com/enums/MainMenuType.html) MenuType, [PreviousMenuType](https://repentogon.com/enums/MainMenuType.html) PreviousMenuType)|-| void |

This callback runs whenever the MenuType changes, works both with vanilla menu changes and the ModdedMenuTypes that are made with this api.

???+ info
    Using this callback to set the InputMask is recommended, as it triggers once and wont break other mods constantly reapplying an input mask.

??? example
    This code changes the input mask when changing menus to character menu or the modded menu SeedsMenu, and returns to the vanilla mask when leaving them

    ```lua
    local function OnModdedMenuUpdate(curMenu, prevMenu)
        if prevMenu == MainMenuType.CHARACTER and type(curMenu) == "number" then
            --print("Bit vanilla")
            MenuManager.SetInputMask(4294967295)
        elseif curMenu == MainMenuType.CHARACTER then
            --print("Bit Character")
            MenuManager.SetInputMask(base.Bitwise.CharacterMenu)
        elseif curMenu == "SeedsMenu" then
            --print("Bit Seeds")
            MenuManager.SetInputMask(base.Bitwise.SeedMenu)
        end
    end

    mod:AddCallback("MenuReimagined_OnModdedMenuUpdate", OnModdedMenuUpdate())
    ```
