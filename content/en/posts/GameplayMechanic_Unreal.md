---
author: "Ritschie"
title: "Gameplay Mechanics implementation in Unreal"
date: 2021-04-01T12:00:06+09:00
description: "Gameplay Mechanics implementation in Unreal - University Coursework, inspired by similar systems in Diablo/Path of Exile"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: Hristo Vuchev
authorEmoji: 👻
image: /PostImages/cmp302_unreal/banner.png
tags: 
- mechanic
- cpp
categories:
- university
- coursework
- unreal
series:
- University Projects
---
## Summary
The mechanics I have implemented is a player resource (Health/Mana) management and
manipulation mechanic in similar fashion to MMORPG/ARPG/MOBA etc games. Diablo
and Path of Exile served as inspiration. The mechanic is made up of several components:

- Health system, where:
    - Taking damage causes the player to lose HP
    - Picking up HP Potions recovers Player HP
    - Easy access to HP values
    
- Mana system, where:
    - Spells cost mana to cast
    - Spells cost mana to channel
    - Mana regenerates over time
    - Picking up Mana Potions recovers a flat Mana amount
    - Picking up Mana Regen Buffs increases mana recovery rate drastically for a limited period of time
    - Spells can only be cast if the Player has enough Mana
    
- Spell Cooldowns:
    - Single Cast Spells can only be cast while not on Cooldown
    - Channeling Spells do not have a Cooldown (although some games in the genre implement a Cooldown for some Channeling Spells and this can easily be added here as well)
    
- Health/Mana Orbs UI:
    - Used showcase current and max Player Health and Mana resources
    
- Multiple abilities/spells that can be Cast:
    - “Vortex” - a channeling ability which deals steady damage over time to enemies in it’s area of effect. Has an Initial Mana Cost associated with it on first use, later has a relatively small and fixed channeling cost/
    - “Rock Blast” - a single cast ability with high base damage, high mana cost and high cooldown.
    
- Pickups:
    - Health Potion which restores a flat amount of HP to the Player when picked up
    - Mana Potion which restores a flat amount of Mana to the Player when picked up
    - Mana Regen Buff which greatly increases Player Mana Regen for a short period
    - Explosive Bomb which damages and subtracts a flat amount of HP from the Player on contact

- Enemy Dummy
    - Has a detection radius for the player, where if the player is inside the enemy starts chasing the player
    - Has an attack radius, where if the player is inside the enemy starts attacking the player
	  - Attack has a separate hitbox
    - Health
    - Death conditions
