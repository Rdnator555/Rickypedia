
# BETA 0.1

Welcome to this solo project that aims to reimplement the menu in a certain degree, allowing customizations such as:

ANIMATED Portraits, with option for custom Idle animation/s and sfx, character custom requeriments to play as them (tokens, etc, still not implemented completely), Menu Code, custom callbacks and MORE.




### How Menu is created

First, we make a table with all the vanilla characters like this
- OldMenu
    - Normal
    - Tainted
We remove the hidden and locked ones

Now comes the tricky part, the modded characters...

From the vanilla Character max Id + 1, we repeat untill the Id is nil, therefore making a new table with all the moded characters like this.
- NewMenu
    - Normal
    - Tainted

After this, we run the custom callback "MenuReimagined_GetCharacterMenu", that lets us insert a table into the NewMenu table and overwrite some values

Example :
```
function base:Test()
    local characterMenuTable = {}
    characterMenuTable.Normal = {
        ["TEST"] = {
            ["#TAGS"] = { "IGNORE" },
            Name = "test",
            CharacterSprite = CharacterMenu:GetCharacterPortraitSprite(),
            SheetSprite = "gfx/ui/main menu/charactermenu.anm2", true),
            IsUnlocked = function(self)
                return 1
            end,
            MenuType = "Normal"
        }

    }
    return characterMenuTable
end

YourMod:AddCallback("MenuReimagined_GetCharacterMenu", base.Test)
```
This one makes the TEST character in the Normal menu to be IGNORED (in #TAGS) from the menu. The IsUnlocked allows us to make a function that return 1 if its unlocked, -1if is hidden and 0 if its locked, and MenuType says in what menu it belongs, needed to check the sheet bg for rendering if not given one

The NewCharacterSheet model is like follows:
```
--Includes Character Sprite and Is Unlocked, defaults to normal bg and the rest. Marks are manually handled
---@class NewCharacterSheet @Each new character Menu
---@field ["#TAGS"]?       table<CharacterTags>
---@field Id               PlayerType|number
---@field Name?             string
---@field CharacterSprite   Sprite
---@field IsUnlocked        function
---@field SheetSprite?      Sprite
---@field Background?        Sprite
---@field MenuCode?         function
---@field MenuType         string
---@field Position?         number
```


Current #tags are:
- "IGNORE" : Ignore the character in the menu.
- "INVISIBLE" : If it shouldn't be aded if not unlocked, deprecated, please use IsUnlocked returning -1 for that.
- "EDENVARIANT" = If it uses tokens as eden to be played as.

### Custom CharacterMenuType

In the same callback, you can also return a non existent CharacterMenuType, such as Tarnished (Epyphany) to create it
ALso, we add the "BGSpriteSheet" with the string of the png with the menu variant sheet

Example for Epyphany:
```
function base:Epiphany()
    if not Epiphany then return end

    ---@type table<string,table<string,NewCharacterSheet>>
    local characterMenuTable = {}
    characterMenuTable.Normal = {
        ["[TECHNICAL] C-Side Detect"] = {
            ["#TAGS"] = { "IGNORE" }
        },
    }
    characterMenuTable.Tarnished = {
        ["BGSpriteSheet"] = "gfx/ui/main menu/poster alt.png",
        ["[TECHNICAL] C-Side Detect"] = {
            ---@diagnostic disable-next-line: assign-type-mismatch
            CharacterSprite = Sprite("gfx/ui/main menu/MenuReimagined/OOIIA.anm2", true),
            --SheetSprite = , --"gfx/ui/main menu/charactermenu.anm2", true),
            Id = Isaac.GetPlayerTypeByName("[TECHNICAL] C-Side Detect"),
            IsUnlocked = function()
                return 1
            end,
            MenuType = "Tarnished",
        }
    }
    characterMenuTable.Tarnished["[TECHNICAL] C-Side Detect"].CharacterSprite:Play(characterMenuTable.Tarnished
        ["[TECHNICAL] C-Side Detect"].CharacterSprite:GetDefaultAnimation())
    return characterMenuTable
end

YourMod:AddCallback("MenuReimagined_GetCharacterMenu", base.Epiphany)
```

This example makes the default Door to be Ignored, and creates it into the new Tarnished type

### Menu Code

If you need to run something only when the character is selected, for any reason, there is now a callback to do that.
The modded callback "MenuReimagined_MenuCode" runs every time it renders and the character is selected, with the arguments of characterId.

Example:
```
function base:TestMenuCode(id)
    if id == PlayerType.PLAYER_CAIN then
        local tokens = Isaac.GetPersistentGameData():GetEventCounter(EventCounter.EDEN_TOKENS) .. " Tokens"
        local mouse = Isaac.WorldToScreen(Input.GetMousePosition(true))
        local a = Isaac.WorldToMenuPosition(MainMenuType.CHARACTER, Vector.Zero)
        --Font previously loaded
        YourMod.Font.Meat16:DrawString(tokens, mouse.X, mouse.Y,
            KColor(0.212, 0.184, 0.176, 1))
    end
end

YourMod:AddCallback("MenuReimagined_MenuCode", base.TestMenuCode)
```

This example renders the eden tokens when the character Cain is selected at the mouse position



## Animated Character Portraits

### Locked Portraits and Animations
    Currently, there are implemented 4 animations for animated portraits, 
    with support for variants for locked ones, these are aswell the Status.
    Names are CASE SENSITIVE.
- "OnSelect"
- "Selected"
- "OnUnselect"
- "UnSelected"
- "LockedOnSelect"
- "LockedSelected"
- "LockedOnUnselect"
- "LockedUnSelected"

### Basic Behaviour

Portraits will be animated as UnSelected or LockedUnSelected depending on their lock state, and will change animations accordingly to the character Carousel status.
- OnSelect and LockedOnSelect triggers when a character is selected as the next selected character.
- OnUnselect and LockedOnUnselect will trigger for the selected character when the next selectedCharacter isn't itself.
The rest will play repeatedly.

### Idles implementation

If you have an animation named Idle, it will run the Idle proces when replaying the "Selected" animation (currently only unlocked Idles are supported).
This process will do:

- Gets All animations that INCLUDE "Idle" on their name as the first part
- Store them in a table 
- Select one of them at randomly if a 5% RNG triggers and plays it.
(Custom sfx support not implemented yet) 

### CarouselRender Callback

With the custom Callback "MenuReimagined_CarouselRender", you can run a function that has the arguments CharacterId, CharacterSprite, status, and return a boolean, if the return is true, the custom management by the api is ignored in favour of yours.
## Custom Main Menu Types

For the new seed input menu, I've tweaked the MenuManager a bit, now with support for custom MainMenuTypes and a new way to SetViewPos withouth constantly setting it forcefully.

### New MenuManager Functions

- NewGetActiveMenu: returns the normal GetActiveMenu or the custom MainMenuType (string) that is currently set.
- NewSetActiveMenu: Accepts both MainMenuType and string, if setting a vanilla menu, it removes any SetViewPosition.
- NewSetViewPosition: Has Arguments position(vector) and isForced(boolean). If isForced we keep the view set to that position. The positionis given in Menu coordinates
- NewGetViewPosition: Returns the custom ViewPos in MenuCoords, or the GetViewPosition if not overriden.

The api uses this helper function to transform Menu coords to Screen Coords and viceverse:
```
---Get Screen coords from menu vector
---@param vectorPos Vector
---@return Vector
function helper:MenuToScreenPosition(vectorPos)
  return -vectorPos + Vector(Isaac.GetScreenWidth() / 2, Isaac.GetScreenHeight() / 2)
end
```

### How to set a new MainMenuType

To set a new custom menu type, we use NewSetActiveMenu(stringOfCustomMenu) and then NewSetViewPosition(NewMenuPosition) to set the view
```
MenuManager.NewSetActiveMenu("SeedsMenu")
MenuManager.NewSetViewPosition(Vector(760, 945), true)
```

This will make NewGetActiveMenu return the custom menu we just set.

### Custom callback OnModdedMenuUpdate

The custom callback "MenuReimagined_OnModdedMenuUpdate" runs ONCE when we change MainMenuTypes, both between vanilla and Modded types, it allows us to set the input mask to whichever we want ONCE

Example:
```
function base:OnModdedMenuUpdate(curMenu, prevMenu)
    if prevMenu == MainMenuType.CHARACTER and type(curMenu) == "number" then
        print("Bit vanilla")
        MenuManager.SetInputMask(4294967295)
    elseif curMenu == MainMenuType.CHARACTER then
        print("Bit Character")
        MenuManager.SetInputMask(base.Bitwise.CharacterMenu)
    elseif curMenu == "SeedsMenu" then
        print("Bit Seeds")
        MenuManager.SetInputMask(base.Bitwise.SeedMenu)
    end
end

YourMod:AddCallback("MenuReimagined_OnModdedMenuUpdate", base.OnModdedMenuUpdate)
```

This function will change the inputmask when changin menus, and is the default in this API


