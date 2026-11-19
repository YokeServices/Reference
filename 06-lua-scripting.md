# Lua scripting

> **Beta.** The Scripts and Exports tabs are in beta. If you do not see them,
> they are not on your account yet.

Graphs are great for "when this happens, do that". When the logic gets
bigger (lots of conditions, loops over players, text to pick apart, maths),
it can be easier to write it as code. Scripts let you do that in **Lua**, a
small, friendly language used by many games for exactly this.

Scripts and graphs work together:

- A script can do everything a graph can: every node is a Lua function.
- A script function can become a **node** that your graphs use.
- A script can **react to events** on its own, without any graph.
- A script can call your [graph functions](04-graph-functions.md), and
  graphs can call your script functions.

New to Lua? Read [Lua in ten minutes](#lua-in-ten-minutes) first. Already
know it? Jump to [Event handlers](#event-handlers) and
[Calling the panel: yoke](#calling-the-panel-yoke).

## The Scripts tab

On the left is the **script list** (Ctrl+B shows and hides it). Each script
has an **On** checkbox: when it is on, its event handlers run and its
functions are offered to graphs. Under it you can see which graphs use it.

### Making a script

Press **New script**, give it a name and pick a starting point:

| Template | Gives you |
|---|---|
| **Function for graphs** | A function marked `---@export`, which becomes a node in your graphs |
| **Event handler** | A function marked `---@on manual`, which runs when you press Run |
| **Library** | Helper functions other scripts can `require()` |
| **Empty** | Just a comment |

A script's name is up to 32 lower case letters, numbers and `_`, starting
with a letter, such as `greetings` or `player_tools`. It is the name other
scripts use to load it.

A server can have up to 25 scripts, of up to 64 KB each.

### Renaming and deleting

Use the **Rename** and **Delete** buttons for the open script. Renaming a script
keeps the graphs that use its functions working: they are pointed at the
new name for you. Other scripts that load it with `require("old_name")` are
changed in the editor for you to check and save.

### The editor

The editor is the one VS Code is built on, set up for this panel's Lua. It
knows your server: every `yoke` function and the permission it needs, every
event and what it carries, your graph functions, and the functions your
other scripts export.

- **Completions as you type**, including the fields of `event` in a handler,
  the names after `---@on`, the scripts you can `require`, and the names a
  `yoke.x.y{ ... }` table takes. Ctrl+Space asks for them at any time.
- **Documentation on hover**, and the parameters of a function as you call it.
- **Mistakes underlined as you type**: red for errors, yellow for warnings.
  It catches typos in names ("did you mean"), Lua features that do not exist
  in this version, events and filters that do not exist, a branch you have
  not declared, a missing `require`, a global variable made by accident,
  unused variables, the wrong number of arguments and clear type mix ups.
- **Quick fixes**: put the cursor on an underline and press Ctrl+. (or click
  the light bulb) for a fix.
- **Go to definition** (F12), **find references** (Shift+F12) and **rename**
  (F2), across all your scripts.
- **Outline** of the script's functions, breadcrumbs, folding, and labels
  over exported functions showing how many graphs use them.
- **Format** the script (Shift+Alt+F, or on save if you turn it on in the
  editor settings).
- **Unsaved changes are kept in your browser**, so closing the tab loses
  nothing. They come back when you return.

| Keys | Does |
|---|---|
| Ctrl+S | Save this script |
| Ctrl+Alt+S | Save every script |
| F5 | Run the script's `---@on manual` handler |
| Ctrl+Shift+B | Ask the panel to check the script (without saving) |
| F1 | Every command |
| Ctrl+P | Go to a script |
| Ctrl+Alt+O | Go to a function in any script |
| Ctrl+Shift+F | Find in every script |
| Ctrl+F, Ctrl+H | Find, replace in this script |
| Ctrl+G | Go to a line |
| Ctrl+/ | Comment or uncomment the line |
| Alt+Up, Alt+Down | Move the line up or down |
| Ctrl+D | Select the next match too (several cursors) |
| Ctrl+B | Show or hide the script list |
| Ctrl+J | Show or hide the bottom panel |
| Alt+Z | Word wrap on or off |

Use Cmd instead of Ctrl on a Mac.

The **bottom panel** has **Problems** (every problem in every script; click
one to jump to it), **Output** (what saves, checks and runs said) and
**Find** (search and replace across scripts).

**Saving** also has the panel check the script: that it compiles, that the
person saving holds every permission its `yoke` calls need, and that it does
not contain anything that is not allowed. If something is wrong, the save is
refused and the problems are marked in the editor.

## Lua in ten minutes

Lua is small. This is most of what you need. The panel runs **Lua 5.1**.

### Comments

```lua
-- A comment runs to the end of the line.
--[[ A long comment
     can span lines. ]]
```

### Variables and values

```lua
local name = "Ash"        -- text (a "string")
local score = 12          -- a number
local online = true       -- true or false (a "boolean")
local nothing = nil       -- no value at all
```

Always write `local` when you make a variable. Without it the variable is
global to the whole script, which leads to surprises; the editor warns you.

### Text

```lua
local greeting = "Hello, " .. name .. "!"   -- .. joins text
local length = #greeting                     -- # is the length
local shout = greeting:upper()               -- "HELLO, ASH!"
local line = string.format("%s has %d points", name, score)
```

### Maths

```lua
local total = 10 + 5 * 2       -- 20
local half = 7 / 2             -- 3.5 (always a decimal)
local whole = math.floor(7 / 2) -- 3
local rest = 7 % 2             -- 1
local power = 2 ^ 10           -- 1024
```

### Comparing and deciding

```lua
if score >= 10 then
    print("Well done")
elseif score > 0 then
    print("Keep going")
else
    print("No points yet")
end
```

`==` equal, `~=` not equal, `<`, `>`, `<=`, `>=`. Combine with `and`, `or`,
`not`.

Only `nil` and `false` count as false. **`0` and `""` count as true**, unlike
some other languages. To check for empty text, compare: `if text == "" then`.

### Tables: lists and records

A table holds many values. As a **list**, it counts from **1**:

```lua
local maps = { "Dust", "Bridge", "Harbour" }
print(maps[1])          -- Dust
print(#maps)            -- 3
table.insert(maps, "Station")
```

As a **record**, it has named fields:

```lua
local player = { name = "Ash", score = 12 }
print(player.name)      -- Ash
player.score = player.score + 1
```

### Loops

```lua
for i = 1, 5 do                       -- 1, 2, 3, 4, 5
    print(i)
end

for index, map in ipairs(maps) do     -- every item of a list, in order
    print(index, map)
end

for key, value in pairs(player) do    -- every field of a table
    print(key, value)
end

while score < 100 do
    score = score + 10
end
```

`break` leaves a loop early.

### Functions

```lua
local function add(a, b)
    return a + b
end

local function split_name(full)
    local first, last = full:match("(%S+)%s+(%S+)")
    return first, last                  -- a function can return several values
end

local first, last = split_name("Ash Ketchum")
```

That is enough to read every example in this guide. The
[Lua language reference](07-lua-reference.md) has the rest.

## Your first script

1. Press **New script**, call it `hello`, pick **Event handler**.
2. You get:

```lua
--- Runs when the Run button on the Scripts tab is pressed.
---@on manual
function run(event)
    print("Run by " .. tostring(event.by))
    yoke.server.command("say Hello from Lua")
end
```

3. Make sure **On** is ticked, press **Save**, then **Run** (F5). Leave the
   input empty and press Run.
4. Your server's console says "Hello from Lua". The **Runs** tab shows the
   run as "Script hello"; open it to see the `print` line in its log.

The lines starting with `---` are **doc comments**. They are how you tell the
panel what a function is for: `---@on manual` says "run this when Run is
pressed".

## Event handlers

A handler is a function that runs when something happens on your server,
just like a graph's event node. Put `---@on` and the event's name right
above it, with no blank line between:

```lua
---@on server.crashed
function on_crash(event)
    yoke.web.discord("https://discord.com/api/webhooks/...", "The server crashed.")
    yoke.server.start()
end
```

The function gets one argument (call it `event`) holding everything the
event carries, by field name: for `server.command` that is `event.command`,
`event.actor` and `event.by_graph`. The [Events reference](reference/events.md)
lists every event and its fields; the editor completes them too.

### Filters

Add `key=value` after the event name to only run when they match, exactly
like the filters on a graph's event node. Quote values that have spaces:

```lua
---@on server.command command="say *" by_graph=-
function on_say(event)
    print("Someone said: " .. event.command)
end

---@on file.changed paths=/config/*.json action=write
function on_config(event)
    print(event.path .. " was saved")
end
```

### The special ones

| Write | Runs |
|---|---|
| `---@on manual` | When you press **Run** (F5) on the Scripts tab. `event.input` holds what you typed in the Run window (read as JSON when it is JSON), `event.by` who pressed it. |
| `---@on schedule cron="*/5 * * * *"` | On a schedule, in the same cron format as graphs: here every 5 minutes. |
| `webhook`, `called` | Use a graph for these. A script has no card to make a webhook URL from, and **Run another graph** only picks graphs. A graph can start from them and call your script's function. |

### Things to know

- A script can have up to 10 handlers. Several handlers can listen for the
  same event; each runs.
- The script must be **On** for its handlers to run.
- Handler runs are listed on the **Runs** tab as "Script" and the script's
  name, with the same limits as a graph's runs. **Open the script** jumps
  from a run to the code.
- When a handler fails, the run is Failed and the log shows the script and
  line.

## Functions for graphs

Mark a function `---@export` and it becomes a node in every graph on your
server, in the **Scripts** group of the node browser:

```lua
--- Works out a player's rank from their score.
---@export
---@title Rank from score
---@param score number The player's score
---@param bonus? number Extra points, if any
---@return string rank The rank's name
---@return number next Points needed for the next rank
function rank_for(score, bonus)
    local total = score + (bonus or 0)

    if total >= 1000 then
        return "Gold", 0
    elseif total >= 500 then
        return "Silver", 1000 - total
    end

    return "Bronze", 500 - total
end
```

In a graph, that is a node titled **Rank from score** with two inputs
(Score, Bonus) and two outputs (Rank, Next). The text above the tags becomes
the node's description.

### The tags

| Tag | Meaning |
|---|---|
| `---@export` | Make this function a node, and available to other scripts through `require`. |
| `---@param name type description` | One input. Put `?` after the name for an optional one: `---@param bonus? number`. |
| `---@return type name description` | One output, in the order you return them. The name is optional (`result`, `result2` and so on when left out). |
| `---@branch name, name` | Arrow outputs. See [Branches](#branches). |
| `---@title Text` | The node's title (up to 60 characters). Without it, the function's name. |

Types: `string`, `number` (or `integer`), `boolean`, `table` (or `object`,
`json`), `list` (or `string[]` and the like), `any`. Something like
`string|number` means `any`.

### The rules

- The doc comment goes **right above** the `function` line, no blank line.
- The function must be at the **top level** of the script (not inside
  another function or an `if`), and **not `local`**: write
  `function rank_for(...)`, not `local function rank_for(...)`.
- Name every parameter; `...` cannot become pins.
- Up to 12 parameters and 12 returns per function, and 32 exported
  functions per script.

### What comes out

Return values fill the outputs **in order**. Or return one table whose fields
are named like the outputs:

```lua
return { rank = "Gold", next = 0 }
```

### Branches

By default the node has one arrow output, "Then". To make it choose a path,
list its arrow outputs with `---@branch`, and pick one with `branch("name")`:

```lua
--- Checks a player against the ban list file.
---@export
---@param name string
---@branch allowed, banned
function check_player(name)
    local file = yoke.file.read{ path = "/banned.txt", parser = "lines" }

    for _, banned in ipairs(file.data) do
        if banned == name then
            branch("banned")
            return
        end
    end

    branch("allowed")
end
```

If the function never calls `branch`, the graph follows the first one
listed. `branch` does not end the function by itself; `return` does.

### Script functions in graphs

- The node runs your function each time the run reaches it. Inside it,
  `event` is empty: the inputs are your parameters.
- Save the script and its node is in the browser straight away. If you
  change its parameters or returns, open the graphs that use it and check
  them.
- Switch a script off and its functions disappear from the browser; graphs
  that use them fail until it is back on.

## Calling the panel: yoke

Everything a graph can do, a script does through `yoke`. Every node is a
function named after its group and its job:

```lua
yoke.server.command("say Restarting soon")
yoke.server.restart()

local status = yoke.server.status()
print(status.state, status.cpu, status.memory)

local file = yoke.file.read{ path = "/server.properties", parser = "keyvalue" }
print(file.data["max-players"])
```

The [Lua function reference](reference/lua-api.md) lists every one, with
its arguments, settings and results.

### Passing values

Pass values **in order**, or **by name** in one table (use `{ }` instead of
`( )`). Settings (the choices you would make in a node's side panel) can
only be passed by name:

```lua
yoke.file.write("/motd.txt", "Welcome!")                          -- in order
yoke.file.write{ path = "/log.txt", content = "line\n", mode = "append" } -- by name
```

Values you leave out get the node's default.

One thing to watch: a single table with named fields is always read as
"values by name". So when a function's value is itself a record, such as
the data for `yoke.json.stringify`, wrap it by name:

```lua
local record = { name = "Ash", score = 12 }

yoke.json.stringify(record)            -- wrong: read as fields "name" and "score"
yoke.json.stringify{ data = record }   -- right
```

Lists (`{ "a", "b" }`) are not affected.

### What comes back

- A function with **one** result returns it: `local ok = yoke.web.discord(url, "Hi")`.
- A function with **several** returns them as **one table**:
  `local file = yoke.file.read("/a.txt")` then `file.content`, `file.size`.
- A function with **no** results returns nothing.
- Data from JSON comes back as tables; lists count from 1 in Lua.

### When it fails

A `yoke` call that cannot do its job raises a Lua error, which ends the run
as Failed with the reason. To carry on instead, use `pcall`:

```lua
local ok, err = pcall(yoke.server.backup, "before-update")

if not ok then
    print("Backup failed: " .. tostring(err))
end
```

### Permissions

A `yoke` call needs the same permission as its node, and it is checked
against **whoever last saved the script**. Saving tells you if you are
missing one. Calls are checked again when they run.

## Helpers

| Helper | Does |
|---|---|
| `print(...)` | Writes a line to the run log. Values are separated by tabs. Up to 50 lines each time the panel calls into your script. |
| `yoke.log(...)` | The same as `print`. |
| `yoke.wait(seconds)` | Pauses. All waits in one run add up to 300 seconds at most. |
| `yoke.stop(reason)` | Ends the run now, as **Stopped**, with the reason. |
| `yoke.vars.get(name, default, shared)` | A value kept between runs, or `default` when there is none. Also returns whether it existed. |
| `yoke.vars.set(name, value, shared)` | Keeps a value between runs. |
| `yoke.vars.delete(name, shared)` | Forgets it. |
| `branch(name)` | Picks the arrow output an exported function leaves by. |

For `yoke.vars`, `shared` decides who sees the value:

- Pass `true` to keep it for the **whole server**: every graph and script
  sees the same value. This is usually what you want.
- Leave it out to keep it for the graph the run belongs to: for a handler,
  the script's own handlers; for an exported function, the graph that used
  the node. The same function called from two graphs then keeps two values.

```lua

local count = yoke.vars.get("restarts", 0, true)
yoke.vars.set("restarts", count + 1, true)
```

To add to a number safely when two runs might do it at once, use
`yoke.data.add_to_variable{ key = "restarts", by = 1, scope = "server" }`.

## Sharing code between scripts

Every exported function of a script can be used by your other scripts with
`require`:

```lua
-- In a script called "util":
---@export
---@param seconds number
---@return string
function duration(seconds)
    local minutes = math.floor(seconds / 60)
    return minutes .. " min " .. (seconds % 60) .. " s"
end
```

```lua
-- In any other script:
local util = require("util")

---@on manual
function run(event)
    print(util.duration(135))   -- 2 min 15 s
end
```

- Each script has its own globals. Another script only sees what it exports.
- The script you require must be **On**.
- Two scripts cannot require each other in a circle.

## Calling graph functions

Your [graph functions](04-graph-functions.md) are under `graphs`:

```lua
local online, count = graphs.player_report()
graphs.announce("Event starting!")
graphs.give_reward{ player = "Ash", amount = 500 }
```

Pass the parameters in order, or by name in one table when the function
takes more than one. Values come back in the order the function's Returns
list them.

## Errors and debugging

Errors say where they happened: `greetings, line 12: attempt to index a nil
value`. The usual causes:

| Message | Usually means |
|---|---|
| `attempt to index a nil value` | You read `x.field` but `x` is `nil`. Check the name, or that a file or event field exists. |
| `attempt to concatenate a nil value` | You joined text with `..` and one side was `nil`. Wrap it: `tostring(value)`. |
| `attempt to call a nil value` | A function name is misspelled, or `require` was not done. |
| `yoke.x.y is not a function on this server` | That node does not exist here (a typo, or a game or Discord function on a server without it). |
| `... has no "name"` | You passed a field by name that the function does not take. The message lists what it does take. |

Tips:

- `print` anything you want to see; it lands in the run's log.
- `error("message")` fails on purpose. `pcall` catches errors from `error`
  and from `yoke` calls.
- `pcall` cannot catch `yoke.stop()` or a run going over a limit. Those
  always end the run.
- **Check** (Ctrl+Shift+B) asks the panel about problems without saving.

## What scripts cannot do

Scripts run in a sandbox, so they cannot harm the panel or other servers:

- No files except through `yoke.file.*`, which works inside your server's
  folder.
- No network except `yoke.web.request` (https to public sites) and the
  Discord functions.
- No `io`, `os.execute`, `load`, `loadstring`, `dofile`, `package`,
  `coroutine` or `debug`. `os` has only `time`, `date`, `clock` and
  `difftime`.
- Nothing is remembered between runs except what you keep with
  `yoke.vars` (or files you write).
- Each time the panel calls into your script it gets 100 milliseconds of
  processor time (time spent inside `yoke` calls does not count), 16 MB of
  memory and up to 100 `yoke` calls. Runs also have the usual run limits:
  1,000 steps, 60 seconds of work and 300 seconds of waiting.

The [limits page](09-limits-and-troubleshooting.md) has all the numbers.

## Turning a graph into a script

On a graph's card, **Convert to Lua** writes a script that does the same
thing:

- each event becomes a `---@on` handler, and a Function node an exported
  function;
- Branch, Switch, For each, Repeat and Try become Lua's own `if`, `for` and
  `pcall`;
- every other node becomes its `yoke.*` call (or `graphs.*`, or a
  `require`).

Before it makes the script, it lists anything that has no Lua form (such as
a webhook event or Run another graph); those are left as comments. The graph
is not changed. A converted script with handlers starts **switched off**, so
the script and the graph do not both run: check it, switch the graph off,
then switch the script on.

## The Exports tab

Everything your scripts and graphs offer each other, in one place:

- **Script functions**: every exported function, which graphs use it, and
  the Lua to call it from another script.
- **Graph functions**: every graph function, who calls it, and its
  `graphs.name(...)` call. **Convert** turns one into a script.
- **Handlers**: every `---@on` handler and the event it listens for.

Click any Lua line to copy it. The search box filters all three lists.
