---
tags:
    - Enum
title: PortraitAnimations
---
<h2> Enum "PortraitAnimations" </h2>
|Value|Comment|
|:--|:--|
|OnSelect|Starts when the character is selected|
|Selected|After finishing OnSelect, we loop this|
|OnUnselect|Starts after unselecting the character|
|Unselected|Loops when finishing OnUnselect|
|LockedOnSelect|Same as OnSelect, but the locked animation|
|LockedSelected|Same as Selected, but the locked animation|
|LockedOnUnselect|Same as OnUnselect, but the locked animation|
|LockedUnSelected|Same as UnSelected, but the locked animation|
|Idle| If exists, will randomly play any Idle-IdleX animation from the sprite at a rate of 5%|

???+ info
    Idle and its variants only accepts idles that start by "Idle" in case sensitive, such as Idle,Idle2,Idle1  
    and Idle-Flip, the only requirement to enable idles are the existence of the "Idle" animation.