# Filter Item Economy Aspects.

## Introduction

When evaluating items in the Path of Exile economy to establish tiering in a filter, just using economy information is insufficient.
Aspects provide the filter generation of NeverSink's filter with meta information that is usually known by players to tier items correctly.
Aspects are maintained by hand by @neversinkdev while being assisted by the FilterBlade team.

## Rough Workflow

NeverSink's filter is getting (re)generated every 4 hours. 

This is done by combining the following files:

- The aspects the you will find in this repository.
- Previous tiering data (tiering.exo)
- Current economy snapshot for SC/HC (from poe.ninja)
- Additional informaiton, such as variables, basetype data etc.

The result of this algorithm is the new tiering. This is merged with the (body.exo). 
Later on styles and strictness data is combined with all of the previously mentioned data to create all economy/style/strictness combinations.

## Aspect explanations

It's important to note that aspects are deeply integrated into the filter generation and have a lot of special cases, exceptions and influences the tiering massively.
This documentation provides general information about what each aspect does. This information might be incomplete or change over time.

### Aspect: Handled 

- Used in all tierlists.
- Function: The handled aspect is added on every basetype in every tierlist to signalize that the item has been reviewed and aspects added to it. It's used to detect new items in the poe.ninja economy. Additionally items that DON'T have the handled aspect (notably: uniques) are handled with extra care and are forbidden from dropping into the lowest tiers.
> Example: Every known unique has the aspect. Every non-existent (unknown new league uniques) doesn't have it.

### Aspect: Anchor

- Used in all tierlists.
- Function: Anchored items are not tiered by the economy. They will always stay in the same tier as they're described in the tiering.exo file
> Example: The most valuable items (mirrors, magebloods, divine orbs), certain items that are crucial for general gameplay (such as alchemy orbs), multi-unique bases with tricky handling and cases where economy pricing is too unreliable (often low value divination cards) are examples where the anchor aspect is used.

### Aspect: PreventHiding

- Used in all tierlists.
- Function: Sets a 'lowest tier' in a tierlist. Items are not allowed to drop below this tier no matter the economy data. Used extensively with uniques, fragments, div cards.
> Example: All timeless emblems, guardian fragments are not allowed to drop into the lowest tier of fragments.

### Aspect: NonDrop

- Used in the Unique tierlist
- Function: The item does not drop naturally in the world and is either currently non-acquirable or acquirable through other means (such as vendor recipes). Nondrop prices are not considered when establishing unique tiering. Cheap uniques with expensive non-drop bases get moved into the hideable2 tier.
> Example: The Kingmaker unique is marked as nondrop. It is only created through a vendor recipe. It may drop when a player guardian with it dies, but this is not a natural occurance.

### Aspect: Exclude

- Used in the Unique tierlist
- Function: Item drops ingame, but is handled by a different rule in the filter and shouldn't be considered for the tiering
> Example: Synthesis rings have their own rules and have the exclude aspect.

### Aspect: NonWorldDrop

- Used in the Unique tierlist
- Function: Symbolizes that the unique doesn't just drop anywhere in the (end)game, but instead requires specific conditions, such as a bosskill or a special league mechanic. Considered when evaluating price data and when making the decision if a unique goes into the Multibase or Bossdrop tier.
> Example: Starforge, Echofroge and Voidforge are all nonworlddrops. They're all pretty expensive, but since they don't drop in the world the only worlddrop Infernal Sword (Oro's Sacrifice) that is usually very cheap will never be tiered above the bossdrop tier. If they wouldn't have the aspects it'd go into multi-base tier

### Aspect: Uncommon

- Used in the Unique tierlist
- Function: Signalises that the unique is a very rare drop compared to the other uniques on the same basetype. Used as a price multipler to establish if an item should go into the multi-base tier
- TODO: review if necessary. Potentially could be removed.
> Example: Bloodseeker is a very uncommon drop, but since it also is very expensive (at the time of writing), this goes into the multibase tier

### Aspects: Reworked, Buff, Nerf, STRONK

- Used in all tierlists. Only considered during earlyleague-timing - see (*A) for more details
- Function: Used to give items a tiering advantage or protection from entering too low/high tiers at the beginning of the league to represent the difference between the previous economy. All 4 aspects are removed after a league and are placed individually if necessary.
> Example: In 3.20 divinarius got buffed significantly and was also made VERY rare. The STRONK aspect was used to tier it correctly early on, before the natural economy data could be used to tier it correctly (once the market is established).

### Aspect: EarlyLeagueInterest
- Used in all tierlists. Only considered during earlyleague-timing - see (*A) for more details
- Function: Gives items a tiering advantage during the beginning of new leagues. Used to prevent items from dropping into the lower tiers see (*B) for more details.
> Example: At the time of writing Obliteration is a popular start-of-the-league wand that is very expensive during the first 1-3 days, but becomes very cheap later. The wand and the card that grants it both have the aspect.

### Aspect: HighVariety
- Used in the Unique tierlist
- Function: Signalizes that the item economy data is not reliable, because it's practical cost varies greatly. This is usually due to roll ranges.
> Example: Low roll Ventor's gambles or Black Sun Crests are pretty cheap, high roll ones can be excessivly expensive

### Aspect: NoResolution
- Used in the Divination tierlist
- Function: Tells the algorithm to not resolve the specific divination card. See (*C) for more details. This is usually used in divination cards that can be turned in at a lower level to grant a specific crafting or corruption base or with cards that have a high-variety reward, such as the Voices jewel card.
> Example: The lumninous trove divination card would resolve in the prices of a low or high tier voice jewel. Taking the average doesn't work here either, since the rewards are highly weighted.

### Aspect: CurrencyType
- Used in the Divination tierlist
- Function: Used to mark that the outcome is fairly-easy-to-sell-or-stack which usually means currency (this also includes most fragments). CurrencyType cards land in the T4c and T5c tiers, instead of T4 and T5, which are highlighted a bit more and hidden later in the strictness pyramid.
> Example: 'The Survivalist' is a currency type card as it grants alchemy orbs.

### Aspect: LargeRandomPool
- Used in the Divination tierlist. 
- Function: Signalizes that the card CAN sometimes result in a pretty valuable outcome. Practically it prevents the item from dropping below T4/T4c. These cards usually are valued higher by players and are often just fun to use
- TODO: functionally similar to preventhiding
> Example: Hunter's Resolve grants a random unique bow. This could be a Lioneye's Glare (it won't be, but who knows!)

### Aspect: TimelessResult
- Used in the Divination tierlist. 
- Funtion: Practically it prevents cards from dropping below T3. It is usually used on cards that CAN or will grant VERY valuable items (such as unique belts) or should always hold some value
> Example: 'The Wretched' will always be somewhat popular, because it can grant you a mageblood or headhunter. 'Chaotic Disposition' always grants 5c with a 1-stack-size.

### Aspect: FarmableOrb
- Used in the Divination tierlist. 
- Function: Prevents a divination card from dropping below T4c. Used for popular farming cards. Some of these are not very efficient and might drop into T5c based on pure prices, but should not be hidden as they're good farming tagets.
- TODO: evaluate merge with PreventHiding
> Example: Emperor's Luck is a fun card to find, even if pretty inefficient. 

### Aspect: LateLeagueValueLoss
- Used in the Divination tierlist.
- Function: Please read (*C) first. Signalizes that a specific card will likely not hold any value. Reduces the price of the divinaton card by a flat value to offset the noise in the economy data.
> Example: The 'Rats' divination card offers a pretty earlyleague helmet that has fallen out of favour years ago.

### Aspects: Tierable, TierableFragment, TierableSplinter
- Used in Currency, Splinter, Fragment and other tierlists
- Function: Some tierlists use a white-listing approach. Instead of tiering *everything*, they only tier items that are listed as tierable. This has proven itself to a be a more stable approach. These aspects also perform some technical tasks to correctly handle itemtypes.
> Example: 'Vaal Orbs' are tierable. 'Alchemy Orbs' are not.

### Aspects: DivineBottom, ChaosBottom, AlchemyButtom, SilverBottom, ChanceBottom
- Used in Currency
- Function: Marks the lowest allowed tier for a specific currency. Usually used as a bottom 'safety' layer.
> Example: 'Astramentis' currencies are not allowed to drop below the alchemy tier

----

### Addendum:

- (*A) Early and late league mode are currently hardcoded dates per league. Early league starts at a league start and last about 2-3 days. Lateleague start 2-3 weeks in. Certain aspects only work during specific dates.
- (*B) To sensibly tier items at the beginning of a new league multiple tricks are used. The tiering from around 1 month before the league end is taken and is merged with the economy data from the LAST league, around 1-2 weeks in. Additionally a low divine orb threshhold to simulate the early league state is used. This results in new meta discoveries still being highlighted as expensive, but lower and end and early league items have a tiering advantage. This is a *very* general explanation and there's a lot more details per tierlist. This process is the result of many years of experimentation. It's not perfect, but gets the job done quite well.
- (*C) Divination card price data on low end divination cards is highly unstable. It often presents cards at the price of 2-5c, even if noone in the world would ever buy the card. An example would be Flora's Gift. A card that grants a 5link staff. Since many players just list the whole div-tab with a 5c price, this often results in the cards being priced at 5c as well, which is absolutely wrong. To combat that I employ a wide range of techniques, such as the aspects themself and price resolution. Price resolution is an algorithm that evaluates the result of the divination card and looks it up in the economy data (offset by card stack size and reward quantity).
