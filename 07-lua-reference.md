# Lua language reference

Everything about the Lua that runs on the Scripts tab, in one place. For a
gentler start, read [Lua scripting](06-lua-scripting.md) first.

## Contents

- [The version](#the-version)
- [How a script is laid out](#how-a-script-is-laid-out)
- [Doc comment tags](#doc-comment-tags)
- [What the panel adds](#what-the-panel-adds)
- [The yoke helpers](#the-yoke-helpers)
- [Calling yoke functions](#calling-yoke-functions)
- [Values between Lua and the panel](#values-between-lua-and-the-panel)
- [The language](#the-language)
- [String patterns](#string-patterns)
- [Built in functions](#built-in-functions)
- [What is not available](#what-is-not-available)
- [Limits](#limits)

## The version

Scripts run **Lua 5.1** in a sandbox. Code written for Lua 5.2, 5.3 or 5.4
mostly works, except for the newer features listed under
[What is not available](#what-is-not-available). The editor marks those for
you.

## How a script is laid out

A script is a **module**: a file of Lua with a name. Each script has its own
global variables; other scripts only see what it exports.

```lua
-- 1. Load other scripts you need.
local util = require("util")

-- 2. Constants and local helpers.
local MAX_WARNINGS = 3

local function clean(name)
    return (name:gsub("%s+", " "))
end

-- 3. Exported functions (nodes in graphs) and event handlers.

--- Warns a player, and kicks them after three warnings.
---@export
---@param player string
---@return number warnings
function warn(player)
    local key = "warnings:" .. clean(player)
    local count = yoke.vars.get(key, 0, true) + 1
    yoke.vars.set(key, count, true)

    return count
end

---@on manual
function run(event)
    print(warn("Ash"))
end
```

Code at the top level (outside functions) runs each time the panel loads the
script to call one of its functions. Keep it to `require`, constants and
local helper functions. Nothing it sets is kept between runs; use
`yoke.vars` for that.

## Doc comment tags

A doc comment is a block of lines starting with three dashes, `---`, right
above a function, with no blank line between. Plain lines are the
description; lines starting with `@` are tags.

```lua
--- Gives a player a reward.
--- Shown as the node's description.
---@export
---@title Give reward
---@param player string The player's name or id
---@param amount? number How much, 100 if left out
---@return boolean ok Whether it worked
---@branch then, refused
function give_reward(player, amount)
    -- ...
    return true
end
```

| Tag | Form | Meaning |
|---|---|---|
| `@export` | `---@export` | Makes the function a node in every graph on the server (Scripts group), and a field of `require("this_script")`. |
| `@on` | `---@on event key=value ...` | Runs the function when the event happens. See [@on](#on). |
| `@param` | `---@param name type description` | One parameter, which becomes an input pin. `name?` makes it optional. |
| `@return` | `---@return type name description` | One return value, which becomes an output pin, in order. The name is optional. |
| `@branch` | `---@branch name, name` | The node's arrow outputs. Names separated by commas or spaces; may be repeated over several lines. |
| `@title` | `---@title Text` | The node's title, up to 60 characters. The function's name when left out. |
| `@type` | `---@type string` | For the editor: the type of the variable declared below. |
| `@deprecated` | `---@deprecated` | For the editor: marks the function as one not to use any more. |
| `@see` | `---@see name` | For the editor: points at something related. |

### Types

Used by `@param`, `@return` and `@type`:

| Write | Pin type |
|---|---|
| `string` | Text |
| `number`, `integer` | Number |
| `boolean` | Yes/no |
| `table`, `object`, `json`, `table<string, number>`, `{ name: string }` | JSON |
| `list`, `string[]`, `number[]`, `list<string>` | List |
| `any`, `unknown`, anything with a `\|` such as `string\|number` | Anything |

Adding `?` or `|nil` (`string?`, `string|nil`) means the value may be missing.

### Rules for exported functions and handlers

- Declared at the **top level** of the script, as `function name(...)`.
  Not `local function`, not `t.name = function`, not inside another block.
- Every parameter named; no `...`.
- Up to 12 parameters and 12 returns.
- Up to 32 exported functions and 10 handlers per script.
- Function names are up to 32 letters, numbers and `_`.

### @on

```lua
---@on <event> [key=value] [key="value with spaces"] ...
```

- `<event>` is an event's name from the [Events reference](reference/events.md),
  such as `server.crashed`, `file.changed`, `discord.message` or
  `pavlov.player_joined`.
- Each `key=value` is one of that event's filters. Blank means any; `*`
  matches any run of characters. Quote a value that has spaces.
- The function gets one argument: a table of what the event carries.

Special cases:

| Event | Notes |
|---|---|
| `manual` | Runs from the **Run** button on the Scripts tab (F5). The argument has `input` (what was typed in the Run window, as data if it was JSON) and `by` (who pressed it). |
| `schedule` | Needs a cron: `---@on schedule cron="0 6 * * *"`. |
| `discord.command` | Defines the slash command too: `---@on discord.command name=kick description="Kick a player" options="player:string:Who, reason:string?:Why"`. Options are separated by commas here. |
| `webhook`, `called` | Not useful in scripts: start a graph from them and have it call your exported function. |

## What the panel adds

On top of standard Lua, every script can use:

| Name | What it is |
|---|---|
| `yoke` | Every node on your server as a function: `yoke.server.command(...)`. Plus the helpers below. [Every function](reference/lua-api.md). |
| `graphs` | Your [graph functions](04-graph-functions.md): `graphs.name(...)`. |
| `require(name)` | Another script's exported functions, as a table. The script must be on. |
| `branch(name)` | In an exported function with `---@branch`: which arrow output the node leaves by. Must be one of the declared names. The first one is used if you never call it. |
| `print(...)` | A line in the run log. |
| `event` | In a handler, the same table as its argument. Empty in exported functions. |

## The yoke helpers

| Helper | Returns | Does |
|---|---|---|
| `yoke.log(...)` | nothing | The same as `print`. |
| `yoke.wait(seconds)` | nothing | Pauses the run. Waits add up to at most 300 seconds per run. |
| `yoke.stop(reason)` | does not return | Ends the run as **Stopped**. `pcall` cannot catch it. |
| `yoke.vars.get(name, default, shared)` | value, exists | A value kept between runs. `default` when there is none; `exists` says whether there was one. |
| `yoke.vars.set(name, value, shared)` | nothing | Keeps a value: text, a number, a boolean or a table. |
| `yoke.vars.delete(name, shared)` | nothing | Forgets it. |

`shared = true` keeps the value for the whole server, shared by every graph
and script. Without it, the value belongs to the graph the run is in (for a
handler, the script's handlers). Values are visible and editable on the
**Variables** tab.

## Calling yoke functions

```lua
-- In order:
yoke.file.write("/motd.txt", "Welcome!")

-- By name, in one table (note the braces). Settings only work this way:
yoke.file.write{ path = "/log.txt", content = "line\n", mode = "append" }
```

- **Arguments** go in the order the reference lists them, or by name in one
  table. Missing values, `nil` and `""` get the node's default.
- **Settings** (the choices on a node's side panel) can only be given by
  name.
- **A single record** (a table with named fields) is always read as values by
  name, so pass a record by name: `yoke.json.stringify{ data = record }`.
- **Results**: one result comes back as a value; several as one table with a
  field per result; none as nothing.
- **Branches**: the two functions that choose a path (Pavlov's lift ban
  functions) return the path's name as a second value.
- **Errors**: a call that fails raises a Lua error starting with the
  function's name, then the reason (`yoke.file.read: ...`). Catch it with
  `pcall`.
- **Permissions**: each call needs its node's permission, held by whoever
  last saved the script.

## Values between Lua and the panel

| Panel value | In Lua |
|---|---|
| Text | string |
| Number | number |
| Yes/no | boolean |
| Nothing (null) | `nil` |
| List | table counted from 1: `{ "a", "b" }` |
| JSON object | table with named fields: `{ name = "Ash" }` |

Going the other way, a table whose keys are exactly 1, 2, 3 ... becomes a
list, and any other table an object. An empty table `{}` becomes an empty
list. Functions, and numbers that are not numbers (`0/0`), cannot leave Lua
and become nothing. Tables nested more than 32 deep are cut off.

One catch: a field name made only of digits, such as a player's SteamID used
as a key (`counts["76561198000000000"]`), comes back from the panel as a
**number** key, so looking it up with the text no longer finds it. Put a
letter in front (`counts["id" .. uid]`) when you key a kept table by ids.

When a value goes into a typed pin (an exported function's output, a `yoke`
argument) it is converted the same forgiving way as in graphs: the text
`"12"` into a number pin becomes 12.

## The language

### Keywords

```text
and       break     do        else      elseif    end
false     for       function  if        in        local
nil       not       or        repeat    return    then
true      until     while
```

These cannot be used as names.

### Types

| Type | Examples |
|---|---|
| nil | `nil` |
| boolean | `true`, `false` |
| number | `12`, `3.5`, `-1`, `1e6`, `0xff` (one number type for all) |
| string | `"text"`, `'text'`, `[[long text, many lines]]` |
| table | `{ 1, 2, 3 }`, `{ name = "Ash" }`, `{ ["max-players"] = 20 }` |
| function | `function(a) return a * 2 end` |

`type(value)` gives the type's name as text.

Only `nil` and `false` are false in a condition. `0`, `""` and `{}` are true.

### Strings

Escapes in quoted strings: `\n` new line, `\t` tab, `\\` backslash, `\"`
and `\'` quotes, `\ddd` a character by its decimal code. Long strings
`[[ ... ]]` (or `[==[ ... ]==]`) take everything as written, including new
lines.

Every string function also works as a method: `name:upper()` is
`string.upper(name)`. Positions start at 1, and negative positions count
from the end: `("hello"):sub(-3)` is `"llo"`.

### Operators

From lowest to highest precedence:

| Operators | Meaning |
|---|---|
| `or` | either (returns the first true value, or the last) |
| `and` | both (returns the first false value, or the last) |
| `<` `>` `<=` `>=` `~=` `==` | comparison (`~=` is "not equal") |
| `..` | join text (numbers are turned into text) |
| `+` `-` | add, subtract |
| `*` `/` `%` | multiply, divide, remainder |
| `not` `#` `-` | not, length, negative |
| `^` | power |

`a or b` is handy for defaults: `local amount = amount or 100`.
`/` always gives a decimal: use `math.floor(a / b)` for whole numbers.

### Variables and scope

```lua
local a, b = 1, 2          -- several at once
a, b = b, a                -- swap
local x                    -- nil until set
```

A `local` is visible from where it is declared to the end of its block
(`do ... end`, a function, a loop, an `if` branch). Without `local`, a
variable is global for the whole script. Prefer `local`.

### Control structures

```lua
if score > 10 then
    -- ...
elseif score > 5 then
    -- ...
else
    -- ...
end

while count < 10 do count = count + 1 end

repeat count = count - 1 until count == 0   -- runs at least once

for i = 1, 10 do print(i) end               -- 1 to 10
for i = 10, 1, -1 do print(i) end           -- counting down
for i, v in ipairs(list) do print(i, v) end -- a list, in order
for k, v in pairs(tbl) do print(k, v) end   -- every field, in no set order

do local x = 1 end                          -- a block on its own
```

`break` leaves the innermost loop. `return` must be the last statement in
its block (wrap it as `do return end` to return from the middle).

### Functions

```lua
local function area(w, h)
    return w * h
end

local function stats(list)
    local sum = 0
    for _, v in ipairs(list) do sum = sum + v end
    return sum, sum / #list               -- several results
end

local total, average = stats({ 3, 4, 5 })

local function log_all(...)              -- any number of arguments
    local count = select("#", ...)
    local args = { ... }
end

local square = function(x) return x * x end   -- functions are values
```

Functions can use the local variables around them (closures). Calling a
function with one string or table needs no brackets: `print "hi"`,
`f{ a = 1 }`.

### Tables

```lua
local t = {}
t.name = "Ash"             -- same as t["name"] = "Ash"
t[1] = "first"
t["max-players"] = 20      -- keys with dashes need brackets
t.name = nil               -- removes the field

local n = #list            -- length of a list (with no gaps)
table.insert(list, value)  -- add to the end
table.insert(list, 1, value) -- add at the start
table.remove(list)         -- remove the last
table.remove(list, 1)      -- remove the first
table.concat(list, ", ")   -- join into text
table.sort(list)           -- sort in place
table.sort(list, function(a, b) return a.score > b.score end)
```

Metatables (`setmetatable`, `getmetatable`) work as in standard Lua.

## String patterns

`string.find`, `match`, `gmatch` and `gsub` use **Lua patterns**, which look
like regular expressions but are simpler. (For real regular expressions, use
`yoke.text.regex` or `yoke.text.replace{ ..., regex = true }`.)

| Pattern | Matches |
|---|---|
| `.` | any character |
| `%a` `%d` `%s` `%w` | a letter, a digit, a space, a letter or digit |
| `%l` `%u` `%p` `%x` `%c` | lower case, upper case, punctuation, hex digit, control character |
| `%A` `%D` `%S` ... | the opposite of the lower case version |
| `[abc]` `[^abc]` `[a-z]` | one of, none of, a range |
| `*` `+` `-` `?` | 0 or more (longest), 1 or more, 0 or more (shortest), optional |
| `^` `$` | start, end of the text |
| `( )` | a capture: what `match` returns |
| `%b()` | a balanced pair, such as `(...)` |
| `%.` `%%` | a literal `.` or `%` (any punctuation is escaped with `%`) |

```lua
local name, score = ("Ash: 12"):match("^(%w+):%s*(%d+)$")    -- "Ash", "12"
for word in text:gmatch("%a+") do print(word) end
local tidy = text:gsub("%s+", " ")                             -- squeeze spaces
local found = text:find("1.2.3", 1, true)                      -- plain search, no pattern
```

## Built in functions

### Base

| Function | Does |
|---|---|
| `assert(v, message)` | Fails with `message` when `v` is false or nil, otherwise returns its arguments. |
| `error(message, level)` | Fails with a message. |
| `pcall(f, ...)` | Calls `f`, catching failures: returns `true` and results, or `false` and the error. |
| `xpcall(f, handler)` | Like `pcall`, with `handler(err)` building the error. |
| `print(...)` | A line in the run log. |
| `type(v)` | The type's name as text. |
| `tostring(v)`, `tonumber(v, base)` | Convert. `tonumber` gives `nil` when it cannot. |
| `pairs(t)`, `ipairs(t)`, `next(t, k)` | Go through tables. `next(t) == nil` means `t` is empty. |
| `select(n, ...)`, `select("#", ...)` | Values from the nth on; the count. |
| `unpack(list, i, j)` | A list's values as separate values. |
| `rawget`, `rawset`, `rawequal` | Table access without metatables. |
| `setmetatable(t, mt)`, `getmetatable(t)` | Metatables. |
| `_VERSION` | `"Lua 5.1"` |

### string

`byte`, `char`, `find`, `format`, `gmatch`, `gsub`, `len`, `lower`, `match`,
`rep`, `reverse`, `sub`, `upper`.

`string.format` placeholders: `%s` text, `%d` whole number, `%.2f` two
decimals, `%5d` padded, `%q` quoted, `%x` hex, `%%` a percent sign.

### table

`concat`, `insert`, `maxn`, `remove`, `sort`.

### math

`abs`, `acos`, `asin`, `atan`, `atan2`, `ceil`, `cos`, `cosh`, `deg`, `exp`,
`floor`, `fmod`, `frexp`, `ldexp`, `log` (natural only), `log10`, `max`,
`min`, `modf`, `pow`, `rad`, `random`, `randomseed`, `sin`, `sinh`, `sqrt`,
`tan`, `tanh`, and the values `math.pi` and `math.huge`.

`math.random()` gives 0 up to 1, `math.random(6)` 1 to 6,
`math.random(5, 10)` 5 to 10.

### os

Only time:

| Function | Does |
|---|---|
| `os.time(table)` | Now as seconds since 1970, or the time of a date table. |
| `os.date(format, time)` | A date as text in the server's time zone: `os.date("%Y-%m-%d %H:%M")`. `"*t"` gives a table (year, month, day, hour, min, sec). Start the format with `!` for UTC. |
| `os.clock()` | Processor time the script has used, in seconds. |
| `os.difftime(t2, t1)` | Seconds between two times. |

For more time handling (time zones, adding days, "5 minutes ago") use
`yoke.time.*`.

## What is not available

| Not here | Instead |
|---|---|
| `io`, `os.remove`, `os.rename`, `dofile`, `loadfile` | `yoke.file.*` for files in your server's folder; `require` for other scripts |
| `os.execute`, `os.getenv`, `os.exit`, `os.tmpname` | `yoke.server.command` for the console; `yoke.stop` to end a run |
| `load`, `loadstring`, `string.dump` | Not available |
| `package`, `module` | A script is already a module: use `---@export` and `require` |
| `coroutine`, `debug`, `collectgarbage`, `setfenv`, `getfenv` | Not available |
| `goto`, `//`, `&`, `\|`, `~`, `<<`, `>>` (Lua 5.2+) | Loops with `break`; `math.floor(a / b)`; arithmetic |
| `table.unpack`, `table.pack`, `table.move` | `unpack(list)`; `{ ... }` with `select("#", ...)` |
| `math.type`, `math.tointeger`, `math.maxinteger` | `math.floor` |
| `string.pack`, `utf8`, `bit32`, `rawlen` | Not available; `#` for length |

The web is reachable only through `yoke.web.request` (https, public sites)
and the Discord functions.

## Limits

| Limit | Value |
|---|---|
| Scripts per server | 25 |
| Size of a script | 64 KB |
| Processor time each time the panel calls into a script | 100 ms (time inside `yoke` calls not counted) |
| Memory each time the panel calls into a script | 16 MB |
| `yoke` calls each time the panel calls into a script | 100 |
| `print` lines kept each time | 50 |
| Steps per run (each `yoke` call counts) | 1,000 |
| Working time per run | 60 seconds |
| Waiting per run (`yoke.wait`) | 300 seconds |
| Graph functions calling each other | 8 deep |

"Each time the panel calls into a script" means one handler run, or one
time a graph's run reaches your function's node.
