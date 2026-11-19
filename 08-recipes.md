# Recipes

Ready made automations. Each one shows the graph (as the nodes to add, in
order) and, where it helps, the same thing as a Lua script. Copy, change the
names and values to fit your server, and save.

In the graph steps, "A → B" means "link A's arrow output to B". Values in
quotes go into the node's boxes.

## Contents

Server care:

- [Daily restart with a warning](#daily-restart-with-a-warning)
- [Crash alert with a restart limit](#crash-alert-with-a-restart-limit)
- [Nightly backup](#nightly-backup)
- [Tell Discord when a backup fails](#tell-discord-when-a-backup-fails)
- [Hear about any graph that fails](#hear-about-any-graph-that-fails)
- [Clean out old log files](#clean-out-old-log-files)
- [Change a config value from a Run button](#change-a-config-value-from-a-run-button)
- [Weekend settings](#weekend-settings)

Discord:

- [Welcome new members](#welcome-new-members)
- [A /status command](#a-status-command)
- [A /restart command for admins only](#a-restart-command-for-admins-only)

Data and the web:

- [Count something across runs](#count-something-across-runs)
- [Read data from a website](#read-data-from-a-website)
- [A reusable function for graphs](#a-reusable-function-for-graphs)

Pavlov VR:

- [Welcome first time players](#welcome-first-time-players)
- [Kick repeat team killers](#kick-repeat-team-killers)
- [Announce the top player when a map ends](#announce-the-top-player-when-a-map-ends)

---

## Daily restart with a warning

Every day at 06:00: warn players, wait, restart. (The step by step version
is [Your first graph](01-first-graph.md).)

**Graph:** On Schedule (`0 6 * * *`) → Send command (`say Restarting in 5 minutes`)
→ Wait (`240`) → Send command (`say Restarting in 1 minute`) → Wait (`60`)
→ Restart server.

**Lua:**

```lua
---@on schedule cron="0 6 * * *"
function morning_restart(event)
    yoke.server.command("say Restarting in 5 minutes")
    yoke.wait(240)
    yoke.server.command("say Restarting in 1 minute")
    yoke.wait(60)
    yoke.server.restart()
end
```

The `say` command is the usual one for Minecraft and many other games; use
whatever your game's console uses to broadcast.

## Crash alert with a restart limit

When the server crashes, post to Discord and start it again, but stop
restarting after three crashes in a day, so a broken server does not loop.

**Lua** (the graph version is the same idea with Get, Add to and Branch):

```lua
local WEBHOOK = "https://discord.com/api/webhooks/your/webhook"

---@on server.crashed
function on_crash(event)
    local crashes = yoke.vars.get("crashes_today", 0, true) + 1
    yoke.vars.set("crashes_today", crashes, true)

    if crashes > 3 then
        yoke.web.discord(WEBHOOK, "Crashed again (" .. crashes .. " today). Leaving it off; please take a look.", "Crash")
        return
    end

    yoke.web.discord(WEBHOOK, "The server crashed (" .. crashes .. " today). Starting it again.", "Crash")
    yoke.server.start()
end

---@on schedule cron="0 0 * * *"
function reset_count(event)
    yoke.vars.set("crashes_today", 0, true)
end
```

## Nightly backup

A backup every night at 03:00 that makes room for itself.

**Graph:** On Schedule (`0 3 * * *`) → Make backup (Name `nightly`, setting
**Replace the oldest when full** on).

**Lua:**

```lua
---@on schedule cron="0 3 * * *"
function nightly_backup(event)
    yoke.server.backup{ name = "nightly", replace_oldest = true }
end
```

## Tell Discord when a backup fails

**Graph:**

1. On Backup failed.
2. Data, Template: `Backup "{{trigger.name}}" on {{server.name}} failed.`
3. Web, Discord message: your webhook URL; link the Template's Text into
   Message; Title `Backup failed`.

On Backup failed → Discord message.

## Hear about any graph that fails

One graph that watches all the others.

**Graph:**

1. On A graph failed (leave Graph name blank for all of them).
2. Data, Template: `{{trigger.graph}} failed: {{trigger.error}}`
3. Web, Discord message with the Template's Text as Message.

It never fires for its own failures, so it cannot loop.

## Clean out old log files

Every night, delete compressed logs older than a week from `/logs`.

**Lua:**

```lua
-- "2026-11-17T04:30:00Z" (how file times arrive) as seconds since 1970.
local function to_time(text)
    local y, mo, d, h, mi, s = tostring(text):match("^(%d+)-(%d+)-(%d+)T(%d+):(%d+):(%d+)")

    if y == nil then
        return nil
    end

    return os.time{ year = tonumber(y), month = tonumber(mo), day = tonumber(d),
                    hour = tonumber(h), min = tonumber(mi), sec = tonumber(s) }
end

---@on schedule cron="30 4 * * *"
function clean_logs(event)
    local folder = yoke.file.list{ path = "/logs", files_only = true }
    local week_ago = os.time() - 7 * 24 * 60 * 60
    local old = {}

    for _, entry in ipairs(folder.entries) do
        local modified = to_time(entry.modified)

        if entry.name:match("%.gz$") and modified ~= nil and modified < week_ago then
            table.insert(old, entry.path)
        end
    end

    if #old > 0 then
        yoke.file.delete(old)
    end

    print("Deleted " .. #old .. " old logs")
end
```

The script makes just two `yoke` calls however many files there are: one
to list, one to delete. Deleting needs the file delete permission.

## Change a config value from a Run button

Let anyone with Run permission change the player limit without touching
files. This one is for a Minecraft style `server.properties`; adapt the file
and the key for your game.

**Lua:**

```lua
---@on manual
function set_max_players(event)
    local max = tonumber(event.input)

    if max == nil or max < 1 or max > 200 then
        yoke.stop("Type a number from 1 to 200 in the Run window.")
    end

    local file = yoke.file.read("/server.properties")
    local updated = file.content:gsub("max%-players=%d+", "max-players=" .. max)

    yoke.file.write("/server.properties", updated)
    yoke.server.command("say Player limit is now " .. max .. " after the next restart")
end
```

Press **Run**, type `40`, press Run.

## Weekend settings

Switch a startup setting on Friday evening and back on Monday morning, with
a restart each time. Name the variable by its environment name, the one in
capitals on your server's **Startup** tab (such as `MAX_PLAYERS`); only
settings you can change there can be changed this way.

**Lua:**

```lua
local function switch(value, message)
    yoke.startup.set("MAX_PLAYERS", value)
    yoke.server.command("say " .. message)
    yoke.wait(60)
    yoke.server.restart()
end

---@on schedule cron="0 18 * * 5"
function weekend_on(event)
    switch("64", "Weekend mode in 1 minute: 64 slots!")
end

---@on schedule cron="0 8 * * 1"
function weekend_off(event)
    switch("32", "Back to normal in 1 minute.")
end
```

## Welcome new members

Needs a [Discord bot](05-discord-bots.md) with the Server Members intent.

**Graph:**

1. On Member joined.
2. Discord, Mention (Kind: user): link the event's **User id** into Id.
3. Data, Template: `Welcome {{a}}! Have a look at the rules first.`, with
   the Mention's Text into A.
4. Discord, Send message: Channel `welcome`, Template's Text into Content.

On Member joined → Send message.

**Lua:**

```lua
---@on discord.member_joined
function welcome(event)
    yoke.discord.send("welcome", "Welcome <@" .. event.user_id .. ">! Have a look at the rules first.")
end
```

## A /status command

**Lua:**

```lua
---@on discord.command name=status description="How is the server doing?"
function status_command(event)
    local s = yoke.server.status()
    local text = string.format("The server is %s, using %d MB of memory and %.1f%% CPU.",
        s.state, math.floor(s.memory), s.cpu)

    yoke.discord.reply(event.interaction_id, text)
end
```

The graph version is in [Discord bots](05-discord-bots.md#example-status).

## A /restart command for admins only

Checks the person's Discord roles before doing anything. Change `Admin` to
your own role's name.

**Lua:**

```lua
local function has_role(roles, wanted)
    for _, role in ipairs(roles or {}) do
        if role == wanted then
            return true
        end
    end

    return false
end

---@on discord.command name=restart description="Restart the game server"
function restart_command(event)
    if not has_role(event.roles, "Admin") then
        yoke.discord.reply{ interaction_id = event.interaction_id, content = "Only admins can do that.", ephemeral = true }
        return
    end

    yoke.discord.reply(event.interaction_id, "Restarting the server now.")
    yoke.server.command("say Restarting now, back in a minute")
    yoke.server.restart()
end
```

## Count something across runs

Count how many times the server has started, and say so.

**Graph:**

1. On Server online.
2. Data, Add to variable: Name `starts`, By `1`, Kept for: Whole server.
3. Data, Template: `say Server start number {{a}}`, with Add to variable's
   **New value** into A.
4. Server, Send command, with the Template's Text into Command.

On Server online → Add to variable → Send command.

**Lua:**

```lua
---@on server.online
function count_start(event)
    local starts = yoke.data.add_to_variable{ key = "starts", by = 1, scope = "server" }
    yoke.wait(20)   -- give the game a moment to finish loading
    yoke.server.command("say Server start number " .. starts)
end
```

## Read data from a website

Fetch JSON from a web API and use a field. Needs the **Web requests**
permission. Only `https` addresses on the public internet can be reached.

**Lua:**

```lua
---@on manual
function check_api(event)
    local ok, response = pcall(yoke.web.request, {
        url = "https://api.example.com/status",
        parser = "json",
    })

    if not ok then
        yoke.stop("Could not reach the site: " .. tostring(response))
    end

    if response.ok then
        print("The site says: " .. tostring(response.data.status))
    else
        print("The site answered " .. response.status)
    end
end
```

**Graph:** On Run button → Web request (URL, settings Method `GET`, Read
body as `JSON`) → Branch on the request's **OK** → Log or Discord message.

## A reusable function for graphs

Turn seconds into readable text, as a node every graph can use.

```lua
--- Turns a number of seconds into text like "2 h 5 min".
---@export
---@title Readable duration
---@param seconds number
---@return string text
function readable_duration(seconds)
    seconds = math.floor(seconds or 0)

    local hours = math.floor(seconds / 3600)
    local minutes = math.floor((seconds % 3600) / 60)

    if hours > 0 then
        return hours .. " h " .. minutes .. " min"
    elseif minutes > 0 then
        return minutes .. " min"
    end

    return seconds .. " s"
end
```

Save it, and **Readable duration** is in the **Scripts** group of the node
browser, with a Seconds input and a Text output. Link Server status's
**Uptime (s)** into it, for example.

## Welcome first time players

Pavlov VR servers, with the RCON Plus mod (which is how messages reach
players' screens).

**Lua:**

```lua
---@on pavlov.player_joined first_time=true
function welcome_new(event)
    yoke.pavlov.plus.notify(event.uid, "Welcome to the server, " .. event.name .. "! Be nice and have fun.")
end
```

**Graph:** On Player joined (filter **First time** Yes) → Template
(`Welcome to the server, {{trigger.name}}!`) → RCON Plus, Show a message
(Player from the event's **Uid**, Message from the Template).

## Kick repeat team killers

Count team kills per player for the current map and kick on the third. The
counts live in one kept value, cleared whenever the map changes.

**Lua:**

```lua
---@on pavlov.kill kind=team_kill
function on_team_kill(event)
    local counts = yoke.vars.get("team_kills", {}, true)
    -- "id" in front keeps a numeric player id a text field name.
    local key = "id" .. event.killer_uid

    counts[key] = (counts[key] or 0) + 1
    yoke.vars.set("team_kills", counts, true)

    if counts[key] >= 3 then
        yoke.pavlov.kick(event.killer_uid, "Team killing")
    else
        yoke.pavlov.plus.notify(event.killer_uid, "Team kill " .. counts[key] .. " of 3. Watch your fire!")
    end
end

---@on pavlov.map_changed
function reset_team_kills(event)
    yoke.vars.delete("team_kills", true)
end
```

Kill events come from the game log, which needs verbose logging switched on
for the server.

## Announce the top player when a map ends

**Lua:**

```lua
---@on pavlov.map_ended
function top_player(event)
    if event.top_player ~= nil and event.top_player ~= "" then
        yoke.pavlov.plus.notify{
            message = "Top player this map: " .. event.top_player .. "!",
            target = "All",
        }
    end
end
```
