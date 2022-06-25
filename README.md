# Laevis

Laevis is a simple gzDoom mod, with high compatibility, where your weapons grow more powerful with use.

Based on the damage you do, weapons will gain XP, and upon leveling up, will gain permanent bonuses.

It is designed for maximum compatibility, and supports weapon/enemy replacements and total conversions. It also has special integration with [Lazy Points](https://forum.zdoom.org/viewtopic.php?f=105&t=66565) and [Legendoom](https://forum.zdoom.org/viewtopic.php?t=51035).

Most settings are configurable through the gzDoom options menu and through cvars, so you can adjust things like the level-up rate and the amount of bonus damage to suit your taste.

## Installation & Setup

Add `Laevis-<version>.pk3` to your load order. It doesn't matter where.

The first time you play, check your keybindings for "Laevis - Display Info" and, if you're using Legendoom, "Laevis - Cycle Legendoom Weapon Effect" to make sure they're acceptable. You may also want to check the various settings under "Options - Laevis Mod Options".

That's all -- if equipping a weapon and then pressing the "display info" key (default I) in game brings up the Laevis status screen, you should be good to go.

## Legendoom Integration

If you have Legendoom installed, legendary weapons can gain new Legendoom effects on level up. Only one effect can be active at a time, but you can change effects at any time. Weapons can hold a limited number of effects; if you gain a new effect and there's no room for it, you'll be prompted to choose an effect to delete. (Make sure you choose the effect you want to **get rid of**, not one of the ones you want to keep!)

When using a Legendoom weapon, you can press the "Cycle Legendoom Weapon Effect" key to cycle through effects, or manually select an effect from the "Laevis Info" screen.

There are a lot of settings for this in the mod options, including which weapons can learn effects, how rapidly effects are learned, how many effect slots weapons have, etc. If you want to play with Legendoom installed but turn off integration with Laevis, set `Gun Levels per Legendoom Effect` to 0/Disabled in the settings.

## Lazy Points Integration

To enable this, turn on `Earn XP based on player score` in the mod settings. As long as it's on, you will earn XP equal to the points you score, rather than equal to the damage you deal. This generally results in much faster XP gain, so you may also want to tweak the `XP gain multiplier for score mods` setting.

This should work with any mod that uses the `pawn.score` property to record points, but Lazy Points is the only one it's actually been tested with.

## FAQ

### Why "Laevis"?

It's named after *Lepidobatrachus laevis*, aka the Wednesday Frog, which consumes anything smaller than itself and grows more powerful thereby.

### What do the upgrades do?

See the end of this file.

### What IWADS/mods is this compatible with?

It should be compatible with every IWAD and pretty much every mod. It relies entirely on event handlers and runtime reflection, so as long as the player's guns are still subclasses of `Weapon` it should behave properly. It even works in recent commercial games using gzDoom, like *Hedon Bloodrite*, and total conversions, like *Ashes 2063*.

Note that weapon bonuses are stored in invisible items in your inventory. This ensures that they are properly written to savegames and whatnot, but also means that anything that takes away your entire inventory will also remove your bonuses, even if "remember missing weapons" is enabled. In particular, if you want to use *Universal Pistol Starter* with this mod but keep your bonuses across maps, you must turn on the "Keep Inventory Items" setting for it.

### Doesn't this significantly unbalance the game in the player's favour?

Yep! You might want to pair it with a mod like *Champions* or *Colourful Hell* to make things a bit spicier, if, unlike me, you are actually good at Doom. (Or you can pair it with *Russian Overkill*, load up Okuplok, and go nuts.)

### Can I use parts of this in my mod?

Go nuts! It's released under the MIT license; see COPYING.md for details. See also the "modding notes" section.

### Can I add Laevis integration to my mod?

See "modding notes" below.

## Known Issues

- Mods that allow you to modify or upgrade weapons, such as DRLA, may cause the weapons to reset to level 0 when you do so.
- XP is assigned to the currently wielded weapon at the time the damage is dealt, so it possible for XP to be assigned to the wrong weapon if you switch weapons while projectiles are in flight.
- When using Legendoom, it is possible to permanently downgrade (or, in some cases, upgrade) weapons by changing which effect is active on them before dropping them.
- Mods that clear your inventory, like *Universal Pistol Starter*, will also clear all your Laevis upgrades unless you configure them to leave non-weapon inventory items alone.
- The HUD is not configurable and may overlap the custom HUD included in some mods like Lithium.
- The distinction between projectile and hitscan weapons is guesswork and may in some cases be incorrect.

## Modding Notes

Parts of this mod may be of interest to other modders; in particular `TooltipOptionsMenu.zs` is a drop-in replacement for `OptionsMenu` that supports tooltips in the MENUDEF, and the other menus are useful examples of how to do dynamic menu creation in ZScript.

If you want to integrate Laevis with another mod -- in particular, if you want to add new Laevis upgrades -- see `BaseUpgrade.zs` for detailed instructions. The short form is: you need to subclass `TFLV_Upgrade_BaseUpgrade`, override some virtual methods, and then register your new upgrade class(es) on mod startup, probably in `StaticEventHandler.OnRegister()`.

## Upgrade List

This is a list of all the upgrades in the game and their effects and prerequisites.

### Armour *(Player only)*

Reduces incoming damage by 1 point per level. Cannot reduce it below 2.

### Armour Leech *(Player only)*

Restores armour when you attack, by 2% per level of the damage dealt.

### Damage

As a player upgrade, increases *all* damage you deal by 5% per level. As a weapon upgrade, increases damage dealt by *that weapon* by 10% per level. In either case it will always add at least one point of damage per level to each of your attacks.

### Explosive Shots *(Hitscan only)*

Creates a small explosion on hit. Higher levels do more damage and have a wider blast radius.

### Fast Shots *(Projectile only)*

Projectiles move 50% faster per level.

### Homing Shots *(Projectile only)*

Projectiles home in on enemies. Higher levels will lock on from further away and be more maneuverable when homing.

### Incendiary Shots *(Weapon only)*

Shots cause enemies to ignite. Fire does more damage the more health the target has, up to a maximum of 50% of the target's max health. If an enemy that has "burned out" heals, it will start taking fire damage again, making this particularly effective against modded enemies with regeneration or self-healing. Stacks cause it to do the damage faster (but do not increase the total damage dealt).

### Life Leech *(Player only)*

Restores health when you attack, by 1% per level of the damage dealt. Cannot exceed your normal health limit.

### Piercing Shots *(Projectile only)*

Shots go through enemies (but not walls).

### Poison Shots *(Weapon only)*

Shots poison enemies. Poison is a weak and short-lived damage-over-time effect, but stacks cause both the damage and duration to rapidly increase, making this highly effective on rapid-fire weapons.

### Putrefaction *(Weapon only; requires two levels in Poison Shots)*

Poisoned enemies explode in a poison cloud when killed, transferring half their poison stacks to nearby enemies.

### Pyre *(Weapon only; requires two levels in Incendiary Shots)*

Burning enemies explode in flames when they die, igniting all nearby enemies. The more fire stacks the victim had the hotter the pyre will burn.

### Resistance *(Player only)*

Reduces incoming damage by 5%. This has diminishing returns as you take more levels of it.
