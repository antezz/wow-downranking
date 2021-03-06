# World of Warcraft - Downranking tips
Downranking tool for classic World of Warcraft, will help you decide which spell rank has the highest heal per mana based on your +Healing and level. The metrics used are Healing per Mana Efficiency (HpME), Healing per Second (HpS) and a merged metric we've named Healing Efficiency Score (HES), which considers both HpME and HpS. HES is used when recommending ranks, since looking at only HpME or HpS wouldn't give accurate results.

The website is hosted by Heroku and can be found here https://www.wowdownrank.com/

Currently supported spells:

Druid:
* Rejuvenation
* Regrowth
* Healing Touch

Paladin:
* Flash of Light
* Holy Light
* Holy Shock

Priest:
* Flash Heal
* Greater Heal
* Heal
* Renew

Shaman:
* Chain Heal
* Healing Wave
* Lesser Healing Wave

#### Update log

#### 2020-01-24
* Added buff: Healing Way (3 stacks applied) for Shamans. Adds a 18% extra healing done to the target, this is applied after all other considerations. In other words, a cast that would normally heal the target for 1000 health, will heal 1180 with Healing Wave toggled.

#### 2020-01-22
* Add support for active buffs. The buff selection will appear if the class has any available buffs and buffs may be toggled on or off by clicking them. Hovering a buff will display a tooltip of the buff which is applied.
* Added buff: Blessing of Light (Rank 3) for Paladins. Adds a 400 healing power bonus to Holy Light and 115 to Flash of Light. These are applied as extra healing power for these spells after caluclating the cast time penalties, but before applying the sub level 20 penalty. This means that all ranks trainable at level 20 or above will receive a flat bonus according to the tooltip, and sub level 20 spells will be penalized in accordance with that formula.

#### 2019-10-08
* Druid talent Nature's Graces added. Will apply an average cast time reduction to spells based on the spell's crit chance. As an example: If you have 30% base crit, and 5 points in Improved Regrowth, landing Regrowth at a total of 80% chance to land critically, Regrowth will have it's cast time reduced by 0.8 * 0.5 = 0.4 seconds.

#### 2019-08-19
* Druid talent Moonglow now increments by the correct 3% per rank, instead of 1%.
* Calculation now applies any extra Healing Power from talents to the base healing amount, instead of the effective amount.

#### 2019-07-15 Expert's notes
* Add "Expert's Notes" for all spells, which gives a short description of how experienced players use each spell and what ranks they predominantly stick to.

#### 2019-07-02 Crit for all classes, and more talents for Priest and Shaman
* All classes can now input a critical effect chance, which will impact the calculations.
* Added Priest talents Improved Renew, Holy Specialization and Mental Agility.
* Added Shaman talent Tidal Mastery.

#### 2019-07-01 More talents for Druid and Paladin. 
* Added Druid talents Improved Rejuvenation and Improved Regrowth, and Paladin talent Holy Power.
* Updated some link texts, and minor improvements to descriptive texts.
* Updated spacing for class and spell icons in the compare menu, to make room for the wider scrollbar on Windows.

#### 2019-06-30 YouTube link and Gift of Nature
* Added embedded YouTube video in the "How it works!" modal, where Defcamp & Melderon explain downranking and show case the tool. Watch it here [Classic WoW Downranking Guide & Tool](https://www.youtube.com/watch?v=HBDfRiB1Zlk)
* Added Druid talent Gift of Nature (Don't know how I forgot about that)

#### 2019-06-29 Compare spells and styling upgrade
* Added title art (World of Warcraft: Classic). And other minor improvements to site styling.
* Changed Spell details link in the spell tooltip to a button.
* Added button in the spell tooltip to pit spells against eachother for comparison.

#### 2019-06-26 Spell details with graphs
* Added a spell details modal, opened by clicking on the details link within a spell tooltip. Spell details present graphs for the selected spell, showing how HES, HpME and HpS increase with +Healing.
* HES slider and breakpoints can now be found in the Spell details modal.
* Added Holy Shock for Paladins.

#### 2019-06-24 Talents, Slider and HES
* Added all talents that affect either healing power or spell cost.
* Added Spiritual Guidance talent for Priests, along with an input field for spirit.
* Added a slider for adjusting the importance of either HpME or HpS. The resulting value is reffered to as Healing Efficiency Score (HES) and is calculated as ```[HpME]^X * [HpS]^Y```, default is ```X = Y = 1```, but the values may be changed by moving the slider. 

#### 2019-06-12 Talents and formula
* Talents for Paladin (*Illumination* and *Healing Light*) and *Moonglow* for Druid are now used in calculations.
* Updated formula for calculating the best rank to take the HpS in to consideration, formula is ```[HpME] * log([HpS])```

#### 2019-06-12 Talents and formula
* Noticed error in the how the ```[Sub Level 20 Penalty]``` was applied and this has now been fixed.
* Added talent selection for talents that affect the breakpoints. (Currenlty not used in calculation)
* Temporarily removed the ```[Downranking Penalty]```, due to different sources having conflicts in how it is applied.

## Spell Coefficient
The Spell Coefficient (SC) is the factor that the +Healing or Spell damage is multiplied with in order to determine the final power of the spell. The way the SC is calculated depends on the spell mechanics, there are some general rules for how to calculate the SC, in addition to this a few spells have unique SCs due to balance.

The sections below will describe how this tool calculates Spell Coefficients, should you encounter any errors your are welcome to report a bug, or create a pull request. However, unless a reliable source is provided the requests will be rejected.

[DISCLAIMER]: Due to being a healer myself, I have focused on healing spells to start with. In addition to this, since not all spells make any sense to downrank these have not been included, but might be added later if there is an interest.

### Direct spells and Over-Time spells
These two are the most straigt forward, since they are comprised of a single dividing factor.

Direct spells are spells that only have a direct impact such as Smite, Shadowbolt, Flash Heal or Healing Touch. The coefficient is calculated by:

```[Direct spell coefficient] = [Cast Time of Spell] / 3.5```

Over-Time spells includes both Damage over time (DoTs) and Healing over Time (HoTs), such as Rejuvenation or Curse of Agony. The added damage or healing is divided equally among each tick of the spell and the standard formula is:

```[Over-Time spell coefficient] = [Duration of Spell] / 15```


### Hybrid spells
Hybrid spells is a little bit more complicated, these are spells with both a direct effect and a Over-Time portion, such as Regrowth, or Fire Ball. The standard formula for hybrid spells is:

```[Over-Time part] = ([Duration] / 15) / (([Duration] / 15) + ([Cast Time] / 3.5))```

```[Direct part] = 1 - [Over-Time portion]```

The duration and cast time limitations are then applied:

```[Over-Time coefficient] = ([Duration] / 15) * [Over-Time part]```

```[Direct coefficient] = ([Cast Time / 3.5) * [Direct part]```

DISCLAIMER: Regrowth is actually not calculated in this way, and has it's own spell specific coefficients as with:
```[Direct coefficient] = 0.325```
```[Over-Time coefficient] = 0.513```

### Spells below level 20
Casting a spell that is below level 20 incurs a significant penalty to the coefficient of the spell. The formula for this is:

```((20 - [Spell Level]) * .0375 = [Sub Level 20 Penalty]```

### Putting it all together
Now when we have all the parts we can calculate the final effective coefficient for the spell which is defined by:

```[Effective Coefficient] = [Basic Coefficient] * (1 - [Sub Level 20 Penalty]```

Thus the final power of the spell is ```[Base power] + [+Power] * [Effective Coefficient]```

The result of the function above is what is used when calculating HpME, HpS and finally HEM.

A few notes on this process:

* All cast times are considered before any spell haste or casting time talents are applied.
* All talents or buffs that increase the damage or healing from spells are applied after the coefficient calculations.
* All DoT/HoT durations are considered before any duration buffs or talents are applied. Keep in mind that these talents simply add extra ticks of damage or healing for the same amount that the spell would do otherwise.

# Sources
All the spell data used in the project has been gathered from Classic WoW Beta Servers and [ClassicWowHead](https://classic.wowhead.com/). Calculation formulas from have been derived from several sources and verified in the Classic WoW Beta.

# Contributions
As stated above I am happy to receive constructive feedback, in order to make this tool better.

Feel free to post bug reports on this repo, or create a Pull Request, but bare in mind that unless you provide a reasonable source to your claims they will be disregarded.

If you're not a developer, or simply don't care for GitHub you can also message me on Reddit [u/Oggzor](https://www.reddit.com/user/Oggzor).

## Getting started
1. Make sure that you have node.js installed [download link](https://nodejs.org/en/download/)
2. Clone the project through git, or download as a zip.
3. Navigate to project directory and run ```npm install```.
4. Start the app localy with ```npm start```.
5. Good luck!

