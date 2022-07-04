# Laevis

Laevis is a simple gzDoom mod, with high compatibility, where your weapons grow more powerful with use.

Based on the damage you do, weapons will gain XP, and upon leveling up, will gain permanent bonuses.

It is designed for maximum compatibility, and supports weapon/enemy replacements and total conversions. It also has special integration with [Lazy Points](https://forum.zdoom.org/viewtopic.php?f=105&t=66565) and [Legendoom](https://forum.zdoom.org/viewtopic.php?t=51035).

Most settings are configurable through the gzDoom options menu and through cvars, so you can adjust things like the level-up rate and the amount of bonus damage to suit your taste.

## Installation & Setup

Add `Laevis-<version>.pk3` to your load order. It doesn't matter where.

The first time you play, check your keybindings for "Laevis - Display Info" and, if you're using Legendoom, "Laevis - Cycle Legendoom Weapon Effect" to make sure they're acceptable. You may also want to check the various settings under "Options - Laevis Mod Options".

That's all -- if equipping a weapon and then pressing the "display info" key (default I) in game brings up the Laevis status screen, you should be good to go.

## Mod Compatibility

This should be compatible with every IWAD and pretty much every mod, including weapon/enemy replacements and total conversions. It relies entirely on event handlers and runtime reflection, so as long as the player's guns are still subclasses of `Weapon` it should behave properly.

It has been tested (although not necessarily extensively) in:
- Doom, Doom 2, Heretic, and Chex Quest
- Ashes 2063
- DoomRL Arsenal
- Hedon Bloodrite
- Hideous Destructor
- Legendoom
- Trailblazer

Some mods have specific integration features or compatibility concerns; these are detailed below.

### Legendoom

If you have Legendoom installed, legendary weapons can gain new Legendoom effects on level up. Only one effect can be active at a time, but you can change effects at any time. Weapons can hold a limited number of effects; if you gain a new effect and there's no room for it, you'll be prompted to choose an effect to delete. (Make sure you choose the effect you want to **get rid of**, not one of the ones you want to keep!)

When using a Legendoom weapon, you can press the "Cycle Legendoom Weapon Effect" key to cycle through effects, or manually select an effect from the "Laevis Info" screen.

There are a lot of settings for this in the mod options, including which weapons can learn effects, how rapidly effects are learned, how many effect slots weapons have, etc. If you want to play with Legendoom installed but turn off integration with Laevis, set `Gun Levels per Legendoom Effect` to 0/Disabled in the settings.

### Score mods

Laevis has optional integration with scoremods such as Lazy Points. To enable this, turn on `Earn XP based on player score` in the mod settings. As long as it's on, you will earn XP equal to the points you score, rather than equal to the damage you deal. This generally results in much faster XP gain, so you may also want to tweak the `XP gain multiplier for score mods` setting.

This should work with any mod that uses the `pawn.score` property to record points, but Lazy Points is the only one it's actually been tested with.

### Universal Pistol Start

Laevis works by storing upgrade information in an item in the player's inventory. If this item gets removed all of your levels and upgrades will disappear. If you want to lose your weapons but keep your upgrades, make sure that `Keep Inventory Items` is enabled in the UPS settings.

### DoomRL Arsenal

This works fine in general, but building an assembly out of a weapon will reset it to level 0 and clear all upgrades on it, even if the upgrade binding mode is to set to `weapon with inheritance` or `weapon class` (because the assembly is not just a different weapon but an entirely different weapon class from the base weapon you used to assemble it).

## FAQ

### Why "Laevis"?

It's named after *Lepidobatrachus laevis*, aka the Wednesday Frog, which consumes anything smaller than itself and grows more powerful thereby.

I have also considered renaming it "Gun Bonsai".

### What do the upgrades do?

See the end of this file.

### Doesn't this significantly unbalance the game in the player's favour?

Yep! You might want to pair it with a mod like *Champions* or *Colourful Hell* to make things a bit spicier, if, unlike me, you are actually good at Doom. (Or you can pair it with *Russian Overkill*, load up Okuplok, and go nuts.)

### Can I use parts of this in my mod?

Go nuts! It's released under the MIT license; see COPYING.md for details. See also the "modding notes" section.

### Can I add Laevis integration to my mod?

See "modding notes" below.

## Known Issues

- XP is assigned to the currently wielded weapon at the time the damage is dealt, so it possible for XP to be assigned to the wrong weapon if you switch weapons while projectiles are in flight.
- When using Legendoom, it is possible to permanently downgrade (or, in some cases, upgrade) weapons by changing which effect is active on them before dropping them.
- The distinction between projectile and hitscan weapons is guesswork and may in some cases be incorrect.
- Most effects will trigger only on shots that hit a monster, e.g. HE Rounds will not detonate if you shoot a wall.
- Piercing Shots may interfere with the detonation of exploding shots like rockets.
- Upgrade-specific sound effects (at the moment just the HE and fragmentation sounds) may not play when using iwads other than `DOOM2.WAD`.

## Modding Notes

The ZScript files included in this mod are not loadable as-is; they need to be preprocessed with `zspp`, which is included. The easiest way to do this is simply to run `make` and then retrieve the compiled pk3 from the `release` directory. In addition to `make` itself you will need `find` and `luajit` (for the zscript preprocessor) and the ImageMagick `convert` command (to generate the HUD textures).

You can also simply download a release pk3, unzip it, and edit the preprocessed files.

Parts of this mod may be of interest to other modders; in particular `TooltipOptionsMenu.zs` is a drop-in replacement for `OptionsMenu` that supports tooltips in the MENUDEF, and the other menus are useful examples of how to do dynamic menu creation in ZScript.

If you want to integrate Laevis with another mod -- in particular, if you want to add new Laevis upgrades -- see `BaseUpgrade.zs` for detailed instructions. The short form is: you need to subclass `TFLV_Upgrade_BaseUpgrade`, override some virtual methods, and then register your new upgrade class(es) on mod startup, probably in `StaticEventHandler.OnRegister()`.

# Upgrade List

This is a list of all the upgrades in the game and their effects and prerequisites. Upgrades have brief in-game descriptions, but this list often has more details.

## General Upgrades

General-purpose player and weapon upgrades.

### Agonizer *(Melee only)*

Hitting an enemy flinches them for 2/5ths of a second. More levels increase the duration.

### Armour *(Player only)*

Reduces incoming damage by 1 point per level. Cannot reduce it below 2.

### Armour Leech *(Player only)*

Restores armour when you attack, by 2% per level of the damage dealt.

### Beam *(Hitscan only)*

Replaces the weapon's normal attack with a perfectly accurate beam that does reduced damage but goes through enemies. Increased level increases the damage dealt, with diminishing returns.

### Bouncy Shots *(Projectile only)*

Shots bounce off walls. Higher levels increase the number of bounces and decrease the amount of velocity lost on bounce. At level 3, shots bounce off enemies as well.

### Dark Harvest *(Melee only)*

Killing an enemy grants you health and armour equal to 5% of its max health. Unlike the health/armour leech upgrades, this ignores normal health/armour limits and can boost you even beyond Megasphere levels.

### Damage

As a player upgrade, increases *all* damage you deal by 5% per level. As a weapon upgrade, increases damage dealt by *that weapon* by 10% per level. In either case it will always add at least one point of damage per level to each of your attacks.

### Explosive Death *(Ranged only)*

Killing an enemy causes an explosion dealing 20% of (its health + the amount you overkilled it by). Increasing the level increases the damage (with diminishing returns), increases the blast radius (linearly), and reduces the damage you take from the blast.

### Fast Shots *(Projectile only)*

Projectiles move 50% faster per level.

### Fragmentation Shots *(Projectile only)*

On impact, projectiles release a ring of hitscan attacks. Increasing the upgrade level adds more fragments; damage is based on the base damage of the shot. These can't self-damage.

### HE Rounds *(Hitscan only)*

Creates a small explosion on hit doing 40% of the original attack damage. More levels increase the damage and blast radius, and reduce the damage you take from your own explosions.

### Homing Shots *(Projectile only)*

Projectiles home in on enemies. Higher levels will lock on from further away and be more maneuverable when homing.

### Life Leech *(Player only)*

Restores health when you attack, by 1% per level of the damage dealt. Cannot exceed your normal health limit.

### Piercing Shots *(Projectile only)*

Shots go through enemies (but not walls). Each level allows shots to go through one additional enemy. Note that most shots will hit enemies multiple times as they pass through, so this also acts as a damage bonus.

### Poison Shots *(Weapon only)*

Shots poison enemies. Poison is a weak and short-lived damage-over-time effect, but stacks cause both the damage and duration to rapidly increase, making this highly effective on rapid-fire weapons.

### Putrefaction *(Weapon only; requires two levels in Poison Shots)*

Poisoned enemies explode in a poison cloud when killed, transferring half their poison stacks to nearby enemies.

### Resistance *(Player only)*

Reduces incoming damage by 5%. This has diminishing returns as you take more levels of it.

### Shield *(Melee only, max two levels)*

Reduces incoming damage by 50% (at level 1) or 75% (at level 2).

### Swiftness *(Melee only)*

Killing an enemy gives you a brief moment of time freeze (and some brief slow-mo as it wears off). Killing multiple enemies in rapid succession will extend the duration, as will increasing the level of Swiftness.

# Elemental Upgrades

Elemental upgrades add powerful debuffs and damage-over-time effects to your attacks. They work a bit differently from other upgrades. Each element has four associated upgrades:

- a basic upgrade that activates that elemental status effect on the weapon
- an intermediate upgrade that improves the status effect in a different way than just leveling up the base upgrade
- two *mastery upgrades* that add a powerful new effect, only one of which can be chosen on each weapon; one is designed for AoE combat, the other for tackling individual hard targets.

Lower-rank skills are required to have more levels than higher-rank ones, so to learn the intermediate upgrade you need at least two levels in the basic upgrade, and to (e.g.) upgrade a mastery to level 2, you need 3 ranks in the intermediate upgrade (and thus 4 in the basic upgrade). While each tree has two masteries, you can only choose one; doing so permanently locks out the other on that weapon.

Each weapon can only have two different elements on it. When you choose your first elemental upgrade, that element is "locked in" until you choose a mastery upgrade for it. At that point you can (if you wish) choose a second element next time it levels up.

Note that unlike the non-elemental upgrades, elemental AoE effects like `Acid Splash` and `Putrefaction` will never harm the player.

## Fire

Fire does more damage the more health the target has, and "burns out" once they're below 50% health. If an enemy that has "burned out" heals, it will start taking fire damage again, making this particularly effective against modded enemies with regeneration or self-healing. More stacks cause it to do the damage faster (but do not increase the total damage dealt). Once an enemy has fire stacks it never loses them; they just become dormant once it drops below the health threshold.

More powerful attacks apply more fire stacks, so it should be good on all weapons.

### Incendiary Shots *(Fire basic upgrade)*

Shots cause enemies to ignite. Higher levels apply fire stacks faster.

### Searing Heat *(Fire intermediate upgrade)*

Reduces the threshold at which fire burns out by 20% (so one level takes it from 50% to 40%). This can be stacked but has diminishing returns.

### Conflagration *(Fire mastery)*

Burning enemies with enough stacks on them will pass a proportion of their stacks on to nearby enemies. Higher levels of Conflagration will transfer more stacks and do so in a wider range, as will adding more stacks to the victim.

### Infernal Kiln *(Fire mastery)*

Attacking a burning enemy gives you a stacking bonus to attack and defence that gradually wears off once you stop.

## Poison

Poison is a weak and short-lived damage-over-time effect, but adding more stacks increases both the duration and the damage per second. Damage has diminishing returns, but with no upper bound.

The amount of stacks applied is independent of weapon damage, so it's best used with rapid-fire weapons like the chaingun and plasma rifle.

### Poison Shots *(Poison basic upgrade)*

Shots poison enemies. Leveling up the upgrade increases how many stacks are applied per attack.

### Weakness *(Poison intermediate upgrade)*

Poisoned enemies do diminished damage. Each stack reduces damage by 1%, with diminishing returns. Leveling this up increases the amount damage is reduced by per stack, although it can never be reduced below 1.

### Putrefaction *(Poison mastery)*

Killing a poisoned enemy causes it to explode in a cloud of poison gas, poisoning everything nearby. Enemies with more poison stacks on them when they die will spread more poison. Leveling this up increases the proportion of poison that gets spread.

### Hallucinogens *(Poison mastery)*

Once an enemy has enough poison stacks on it to eventually kill it, it fights on your side until it dies. Enemies affected by hallucinogens gets a damage bonus from Weakness rather than a damage penalty.

## Acid

Acid stacks are slowly converted into damage on a 1:1 basis, but the less health the target has and the more acid stacks they have, the faster this happens.

Acid stacks have a cap based on the damage dealt by the attack that inflicted them, so they're best used with weapons that have high per-shot damage like the rocket launcher and SSG. (For shotguns, the total damage of all the pellets that hit is used, not the per-pellet damage.)

### Corrosive Shots *(Acid basic upgrade)*

Shots poison enemies. The amount of acid applied, and the cap, is 50% of the damage dealt, increased by another 50% per level.

### Concentrated Acid *(Acid mastery)*

Each level increases the threshold at which acid damage starts accelerating by 10%, and the ratio at which acid stacks are converted into damage by 10%. Both have diminishing returns.

### Acid Spray *(Acid mastery)*

Attacks that exceed the acid cap for the target will splash the acid onto nearby enemies instead. Spray range and the level of the applied acid depends on your level of Acid Spray.

### Embrittlement *(Acid mastery)*

Enemies with acid stacks on them take 1% more damage from all sources per stack. Enemies with low enough HP die instantly; the threshold is based on the number of acid stacks and your Concentrated Acid and Embrittlement levels.
