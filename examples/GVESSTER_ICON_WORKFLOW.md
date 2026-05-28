# Gvesster Icon Workflow For Roblox UI

This workflow shows how to use Gvesster Free Icon Pack with DPFTS without bundling the icon files into this repository.

Source pack:

`https://gvesster.itch.io/free-icon-pack`

Local example zip:

`Free Icon Pack v3.1 (Basic).zip`

## Rule

Do not commit the PNG icons into DPFTS.

Use the pack locally, upload selected icons to Roblox, then put the real Roblox image IDs into your game config.

## What The Pack Contains

The Basic v3.1 zip uses category folders like:

```text
Free Icon Pack v3.1 (Basic)/
|-- Animal/
|-- Currency/
|-- Exclusive/
|-- Food/
|-- Item/
|-- Main/
|-- Nature/
|-- Player/
|-- Social/
`-- UI/
```

Most icons include `64px` and `256px` versions. Many also include `Outline` variants.

## Which Size To Use

Use `64px` for:

- Small HUD icons.
- Currency icons beside prices.
- Compact shop rows.
- Settings or close buttons.
- Quest objective rows.

Use `256px` for:

- Shop item cards.
- Reward previews.
- Pet inventory cards.
- Badge previews.
- Large modal art.
- Thumbnail-like UI inside the game.

If an icon appears blurry in Roblox, use the `256px` version and let Roblox scale it down.

## Which Category To Use

Good defaults:

- Coins, gems, cash: `Currency/`
- Shop, settings, close, arrows: `UI/` or `Main/`
- Pets: `Animal/`
- Tools, boosts, chests, keys: `Item/`
- Player stats, rank, profile: `Player/`
- Friends, invite, group, trade: `Social/`
- Trees, leaves, natural resources: `Nature/`
- Food rewards or cooking systems: `Food/`

## Style Choice

Pick one style per screen:

- Regular: detailed, good for shops and inventory.
- Outline: more readable on busy backgrounds.
- Flat/simple-looking icons: better for small HUD controls.
- Golden variants: use only for premium, rare, featured, or high-value items.

Do not mix regular, outline, flat, golden, and recolor variants randomly on one screen.

## Roblox Upload Workflow

1. Download the pack from itch.io.
2. Unzip it locally.
3. Pick the exact PNG files needed for your UI.
4. Upload them to Roblox as image assets.
5. Copy the uploaded asset IDs.
6. Put the IDs in a shared config ModuleScript.
7. Reference the config from LocalScripts that build UI.

## Naming Convention

When uploading icons to Roblox, use clear names:

```text
DPFTS_Coin_GreenCash1_64
DPFTS_Shop_Cart_256
DPFTS_Pet_Cat_256
DPFTS_UI_Close_64
DPFTS_Reward_Chest_256
```

Good names make it easier to replace icons later.

## Icon Config Module

Place this ModuleScript in:

`ReplicatedStorage/Config/IconConfig`

```lua
-- ReplicatedStorage/Config/IconConfig.lua
local IconConfig = {
    Currency = {
        Coins = "rbxassetid://COIN_ICON_ID",
        Gems = "rbxassetid://GEM_ICON_ID",
    },

    UI = {
        Shop = "rbxassetid://SHOP_ICON_ID",
        Close = "rbxassetid://CLOSE_ICON_ID",
        Settings = "rbxassetid://SETTINGS_ICON_ID",
    },

    Items = {
        Chest = "rbxassetid://CHEST_ICON_ID",
        Sword = "rbxassetid://SWORD_ICON_ID",
    },

    Pets = {
        Cat = "rbxassetid://CAT_ICON_ID",
        Dog = "rbxassetid://DOG_ICON_ID",
    },
}

return IconConfig
```

Replace placeholders after uploading icons to Roblox.

Do not invent fake asset IDs.

## Shop Button Example

Placement:

- `ReplicatedStorage/Config/IconConfig`
- `StarterGui/ShopGui/LocalScript`

```lua
-- StarterGui/ShopGui/LocalScript
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local IconConfig = require(ReplicatedStorage:WaitForChild("Config"):WaitForChild("IconConfig"))

local buyButton = script.Parent:WaitForChild("BuyButton")
local coinIcon = buyButton:WaitForChild("CoinIcon")
local priceLabel = buyButton:WaitForChild("PriceLabel")

coinIcon.Image = IconConfig.Currency.Coins
priceLabel.Text = "250"
```

The LocalScript displays the icon. The server still validates whether the player can buy the item.

## Recommended Shop Icon Set

For a simple shop UI:

- Coin/cash icon from `Currency/`.
- Shop/cart icon from `UI/` or `Main/`.
- Lock icon for unavailable items.
- Check icon for owned items.
- Chest or item icon for reward previews.
- Close icon for modal close button.

## Recommended HUD Icon Set

For a simple HUD:

- Coin icon.
- Gem icon if the game really needs a second currency.
- Quest icon.
- Shop icon.
- Settings icon.

If the HUD has more than five major buttons, DPFTS should review the hierarchy before adding more icons.

## Recommended Pet UI Icon Set

For pet systems:

- Cat/dog/bunny icons from `Animal/`.
- Star or rarity icon.
- Equip icon.
- Lock icon.
- Favorite icon if needed.

Pet UI should show ownership and equip state clearly. Icons should support that, not replace labels entirely.

## DPFTS Prompt

Use this prompt when asking an assistant to design UI:

```text
Use DPFTS to design my Roblox shop UI using Gvesster Free Icon Pack.

I will upload the icons myself, so use placeholder asset IDs.
Include:
- required icon list
- suggested pack categories
- 64px vs 256px choice
- ReplicatedStorage/Config/IconConfig
- StarterGui placement
- mobile button rules
- server/client separation
```

## Mistakes To Avoid

- Uploading every icon when the UI needs five.
- Mixing multiple icon styles on one screen.
- Using icons without labels for unclear actions.
- Letting the client decide purchases because the UI looks polished.
- Putting real third-party PNG files into a public repo without checking redistribution rights.
- Using the pack for AI training.
