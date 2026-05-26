# Leaderboard Recipe

This recipe creates a simple Roblox `leaderstats` leaderboard with `Coins` and `Level`.

## Type Of Leaderboard

This is the built-in Roblox player list leaderboard. It shows values beside player names in the default Roblox player list.

It is not a global ordered leaderboard.

## Built-In leaderstats vs Global Ordered Leaderboards

`leaderstats`:

- Simple.
- Shows stats in the player list.
- Good for Coins, Level, Wins, Kills, and similar stats.
- Exists only while the player is in the server unless connected to DataStore.

Global ordered leaderboard:

- Uses `OrderedDataStore`.
- Shows top players across servers.
- Needs careful request budgeting.
- Better for top coins, fastest times, most wins, or weekly rankings.

For the starter recipe, use simple `leaderstats`.

## Placement

Create a Script in:

```text
ServerScriptService/
`-- Leaderstats (Script)
```

## Code

```lua
local Players = game:GetService("Players")

local DEFAULT_COINS = 0
local DEFAULT_LEVEL = 1

local function createLeaderstats(player: Player)
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player

    local coins = Instance.new("IntValue")
    coins.Name = "Coins"
    coins.Value = DEFAULT_COINS
    coins.Parent = leaderstats

    local level = Instance.new("IntValue")
    level.Name = "Level"
    level.Value = DEFAULT_LEVEL
    level.Parent = leaderstats
end

Players.PlayerAdded:Connect(createLeaderstats)
```

## Connecting With DataStore Later

When you add DataStore support:

- Load profile data when the player joins.
- Set `Coins.Value` and `Level.Value` from the loaded profile.
- Let server systems change the leaderstats values.
- On save, copy leaderstats values back into the profile.
- Do not let the client change leaderstats directly.

The `deep/datastore.md` guide shows a safe profile pattern that creates leaderstats from loaded data.

## Testing Steps

- Press Play in Roblox Studio.
- Confirm `Coins` and `Level` show in the player list.
- Open the Explorer during play and inspect `Players/<YourPlayer>/leaderstats`.
- Change `Coins.Value` on the server and confirm the player list updates.
- Test with two players to confirm each player gets separate values.

## Mistakes To Avoid

- Creating leaderstats in a LocalScript.
- Naming the folder anything other than `leaderstats`.
- Using `StringValue` for numbers.
- Letting the client set coins.
- Assuming leaderstats saves automatically.
- Building a global leaderboard before the basic stats are stable.
