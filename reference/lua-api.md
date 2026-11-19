# Lua function reference

Every `yoke.*` function a script can call, generated from the same list the editor
completes from. Each one does exactly what the node of the same name does in a graph.

How to call them, in short (the [Lua scripting guide](../06-lua-scripting.md#calling-the-panel-yoke)
has the long version):

- Pass values in order, `yoke.server.command("say hi")`, or by name in one table,
  `yoke.server.command{ command = "say hi" }`. Settings can only be passed by name.
- A function with one result returns it. One with several returns a table:
  `local file = yoke.file.read("a.txt")` then `file.content`, `file.size`.
- A function that fails raises a Lua error. Catch it with `pcall` if you want to carry on.
- The permission listed is the one the person who last saved the script needs.

The helpers `yoke.vars`, `yoke.wait`, `yoke.stop` and `yoke.log` are in the
[Lua language reference](../07-lua-reference.md#the-yoke-helpers).

## Contents

- [Server: yoke.server](#server-yokeserver)
- [Startup variables: yoke.startup](#startup-variables-yokestartup)
- [Schedules: yoke.schedule](#schedules-yokeschedule)
- [Files: yoke.file](#files-yokefile)
- [Databases: yoke.db](#databases-yokedb)
- [Data and kept values: yoke.data](#data-and-kept-values-yokedata)
- [Text: yoke.text](#text-yoketext)
- [Math: yoke.math](#math-yokemath)
- [Lists: yoke.list](#lists-yokelist)
- [JSON: yoke.json](#json-yokejson)
- [Time: yoke.time](#time-yoketime)
- [Logic: yoke.logic](#logic-yokelogic)
- [Web: yoke.web](#web-yokeweb)
- [Discord: yoke.discord](#discord-yokediscord)
- [Pavlov VR: yoke.pavlov](#pavlov-vr-yokepavlov)

## Server: yoke.server

### yoke.server.allocations

**List allocations.** The server's network allocations, the primary one first.

```lua
local result = yoke.server.allocations()
```

Returns a table with:

- `allocations` (list): Each: ip, port, alias, address, primary.
- `count` (number): Count.

Needs the `allocation.read` permission.

### yoke.server.backup

**Make backup.** Starts a backup, as the Backups tab does. Fails when the server is at its backup limit.

```lua
local result = yoke.server.backup(name, ignored)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | Blank for the panel's dated name. |
| `ignored` | string | One path per line to leave out. |
| `replace_oldest` (by name only) | setting | Replace the oldest when full. Default: false. |

Returns a table with:

- `uuid` (string): Backup UUID.
- `name` (string): Name.

Needs the `backup.create` permission.

### yoke.server.backups

**List backups.** The server's backups, newest first.

```lua
local result = yoke.server.backups()
```

Returns a table with:

- `backups` (list): Each: uuid, name, successful, locked, bytes, completed_at.
- `count` (number): Count.

Needs the `backup.read` permission.

### yoke.server.command

**Send command.** Types a command into the server console. The server has to be running.

```lua
yoke.server.command(command)
```

| Argument | Type | Notes |
|---|---|---|
| `command` | string | Command. |

Returns nothing.

Needs the `control.console` permission.

### yoke.server.info

**Server info.** What the panel knows about this server: name, address, limits, egg, status.

```lua
local result = yoke.server.info()
```

Returns a table with:

- `name` (string): Name.
- `uuid` (string): UUID.
- `identifier` (string): Short id.
- `description` (string): Description.
- `address` (string): ip:port of the primary allocation.
- `ip` (string): IP.
- `port` (number): Port.
- `memory` (number): Memory (MB)
- `disk` (number): Disk (MB)
- `cpu` (number): CPU (%)
- `egg` (string): Egg.
- `node` (string): Node.
- `owner` (string): Owner.
- `status` (string): Blank when installed and not suspended.
- `data` (table): Everything.

### yoke.server.kill

**Kill server.** Kills the server process at once. Unsaved game state is lost.

```lua
yoke.server.kill()
```

Returns nothing.

Needs the `control.stop` permission.

### yoke.server.note

**Activity note.** Writes a line to the server's Activity tab, so people can see what the graph did.

```lua
yoke.server.note(message)
```

| Argument | Type | Notes |
|---|---|---|
| `message` | string | Message. |

Returns nothing.

### yoke.server.restart

**Restart server.** Restarts the server, as the Restart button does.

```lua
yoke.server.restart()
```

Returns nothing.

Needs the `control.restart` permission.

### yoke.server.start

**Start server.** Starts the server, as the Start button does.

```lua
yoke.server.start()
```

Returns nothing.

Needs the `control.start` permission.

### yoke.server.status

**Server status.** Asks Wings how the server is doing right now: state, CPU, memory, disk, uptime.

```lua
local result = yoke.server.status()
```

Returns a table with:

- `state` (string): running, offline, starting or stopping.
- `online` (boolean): Online.
- `cpu` (number): CPU (%)
- `memory` (number): Memory (MB)
- `disk` (number): Disk (MB)
- `uptime` (number): Uptime (s)
- `network_in` (number): Network in (bytes)
- `network_out` (number): Network out (bytes)

### yoke.server.stop

**Stop server.** Stops the server gracefully, as the Stop button does.

```lua
yoke.server.stop()
```

Returns nothing.

Needs the `control.stop` permission.

### yoke.server.users

**List users.** The people with access to this server: the owner and every subuser.

```lua
local result = yoke.server.users()
```

Returns a table with:

- `users` (list): Each: username, email, owner.
- `count` (number): Count.

Needs the `user.read` permission.

## Startup variables: yoke.startup

### yoke.startup.get

**Get startup variable.** The value of one of the server's startup variables, by its environment name (SERVER_NAME, MAX_PLAYERS).

```lua
local result = yoke.startup.get(variable)
```

| Argument | Type | Notes |
|---|---|---|
| `variable` | string | Variable. |

Returns a table with:

- `value` (string): Value.
- `default` (string): Default.
- `exists` (boolean): Exists.

Needs the `startup.read` permission.

### yoke.startup.set

**Set startup variable.** Changes one of the server's startup variables. Takes effect on the next start. Only variables the Startup tab lets users edit.

```lua
local old = yoke.startup.set(variable, value)
```

| Argument | Type | Notes |
|---|---|---|
| `variable` | string | Variable. |
| `value` | string | Value. |

Returns `old` (string): Old value.

Needs the `startup.update` permission.

## Schedules: yoke.schedule

### yoke.schedule.run

**Run schedule.** Runs one of the server's schedules now, by name, as its Run now button does.

```lua
yoke.schedule.run(name)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | Schedule. |

Returns nothing.

Needs the `schedule.update` permission.

## Files: yoke.file

### yoke.file.copy

**Copy file.** Makes a copy of a file next to it, as the Files tab does ("name copy").

```lua
yoke.file.copy(path)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Path. |

Returns nothing.

Needs the `file.create` permission.

### yoke.file.delete

**Delete files.** Deletes files or folders. One path per line, or a list.

```lua
local count = yoke.file.delete(paths)
```

| Argument | Type | Notes |
|---|---|---|
| `paths` | list | Paths. |

Returns `count` (number): Deleted.

Needs the `file.delete` permission.

### yoke.file.exists

**File exists.** Whether a file or folder is there, and its size and last change when it is.

```lua
local result = yoke.file.exists(path)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Path. |

Returns a table with:

- `exists` (boolean): Exists.
- `size` (number): Size (bytes)
- `is_directory` (boolean): Is folder.
- `modified` (string): Modified.

Needs the `file.read` permission.

### yoke.file.list

**List folder.** What is in a folder.

```lua
local result = yoke.file.list(path)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Folder. Default: "/". |
| `files_only` (by name only) | setting | Files only. Default: false. |

Returns a table with:

- `entries` (list): Each: name, path, size, directory, modified.
- `names` (list): Names.
- `count` (number): Count.

Needs the `file.read` permission.

### yoke.file.mkdir

**Make folder.** Makes a folder, and the folders on the way to it.

```lua
yoke.file.mkdir(path)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Path. |

Returns nothing.

Needs the `file.create` permission.

### yoke.file.pull

**Download to server.** Has Wings download a file from a URL into a folder on the server. Same rules as the Web request node: https, public hosts, the panel's allow list.

```lua
yoke.file.pull(url, directory, filename)
```

| Argument | Type | Notes |
|---|---|---|
| `url` | string | URL. |
| `directory` | string | Folder. Default: "/". |
| `filename` | string | Blank keeps the name from the URL. |

Returns nothing.

Needs the `file.create` permission.

### yoke.file.read

**Read file.** Reads a file and, with a parser chosen, turns it into data: JSON, one item per line, key=value pairs, an INI file, or CSV rows.

```lua
local result = yoke.file.read(path)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | From the server root, like /config/server.json. |
| `parser` (by name only) | setting | Parser. One of `"text"` (Text), `"json"` (JSON), `"lines"` (Lines), `"keyvalue"` (key=value), `"ini"` (INI), `"csv"` (CSV). Default: "text". |

Returns a table with:

- `content` (string): Content.
- `data` (table): What the parser made of it; the text again for "Text".
- `size` (number): Size (bytes)

Needs the `file.read-content` permission.

### yoke.file.rename

**Rename or move.** Renames a file or folder, or moves it when the new path is somewhere else.

```lua
yoke.file.rename(from, to)
```

| Argument | Type | Notes |
|---|---|---|
| `from` | string | From. |
| `to` | string | To. |

Returns nothing.

Needs the `file.update` permission.

### yoke.file.write

**Write file.** Writes text to a file, making it when it does not exist. "Append" adds to the end instead.

```lua
local bytes = yoke.file.write(path, content)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Path. |
| `content` | string | Content. |
| `mode` (by name only) | setting | Mode. One of `"replace"` (Replace), `"append"` (Append), `"create"` (Only if missing). Default: "replace". |

Returns `bytes` (number): Bytes written.

Needs the `file.update` permission.

### yoke.file.write_json

**Write JSON file.** Writes data to a file as JSON.

```lua
yoke.file.write_json(path, data)
```

| Argument | Type | Notes |
|---|---|---|
| `path` | string | Path. |
| `data` | table | Data. |
| `pretty` (by name only) | setting | Indented. Default: true. |

Returns nothing.

Needs the `file.update` permission.

## Databases: yoke.db

### yoke.db.create

**Create database.** Makes a database for the server, as the Databases tab does, within the server's limit. The name gets the server's prefix.

```lua
local result = yoke.db.create(name, remote)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | 3 to 48 letters, numbers, dashes, underscores. |
| `remote` | string | % for anywhere, or an address. Default: "%". |

Returns a table with:

- `name` (string): Name.
- `username` (string): Username.
- `password` (string): Password.
- `host` (string): Host.
- `port` (number): Port.

Needs the `database.create` permission.

### yoke.db.credentials

**Database credentials.** Everything needed to connect to one database, password included. For writing a config file or handing to another service.

```lua
local result = yoke.db.credentials(name)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `host` (string): Host.
- `port` (number): Port.
- `database` (string): Database.
- `username` (string): Username.
- `password` (string): Password.
- `url` (string): mysql://user:password@host:port/database.
- `jdbc` (string): JDBC URL.

Needs the `database.view_password` permission.

### yoke.db.delete

**Delete database.** Drops a database and its user. Everything in it is gone.

```lua
yoke.db.delete(name)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | The database name. Never blank: this one is not guessed. |

Returns nothing.

Needs the `database.delete` permission.

### yoke.db.info

**Database details.** Where one database lives and who its user is. No password here; that is "Database credentials".

```lua
local result = yoke.db.info(name)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `found` (boolean): Found.
- `name` (string): Name.
- `host` (string): Host.
- `port` (number): Port.
- `username` (string): Username.
- `remote` (string): Allowed from.
- `max_connections` (number): Max connections.

Needs the `database.read` permission.

### yoke.db.list

**Databases.** The databases this server has, as the Databases tab lists them.

```lua
local result = yoke.db.list()
```

Returns a table with:

- `databases` (list): name, host, port, username, max_connections for each.
- `names` (list): Names.
- `count` (number): Count.
- `limit` (number): How many the server may have; blank for no limit.

Needs the `database.read` permission.

### yoke.db.query

**Run SQL.** Runs one SQL statement on a database as its own user and gives back the rows (SELECT) or how many rows changed (INSERT, UPDATE, DELETE). Use ? for values and link them as Parameters so nothing is pasted into the SQL.

```lua
local result = yoke.db.query(sql, params)
```

| Argument | Type | Notes |
|---|---|---|
| `sql` | string | One statement. ? marks a value from Parameters, in order. |
| `params` | list | The values for the ? marks, in order. From a Make list node. |
| `database` (by name only) | setting | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `rows` (list): One object per row, column names as keys.
- `count` (number): Row count.
- `columns` (list): Columns.
- `affected` (number): Rows changed.
- `insert_id` (string): The auto increment id of an INSERT, else blank.
- `truncated` (boolean): True when more rows matched than the cap allows.

Needs the `database.view_password` permission.

### yoke.db.rotate

**Rotate database password.** Gives a database user a new random password and returns it.

```lua
local result = yoke.db.rotate(name)
```

| Argument | Type | Notes |
|---|---|---|
| `name` | string | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `password` (string): New password.
- `username` (string): Username.

Needs the `database.update` permission.

### yoke.db.tables

**List tables.** The tables in a database, with how many rows each has (as MySQL estimates it).

```lua
local result = yoke.db.tables()
```

| Argument | Type | Notes |
|---|---|---|
| `database` (by name only) | setting | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `tables` (list): name and rows for each.
- `names` (list): Names.
- `count` (number): Count.

Needs the `database.view_password` permission.

### yoke.db.value

**Query one value.** Runs a SELECT and gives back the first column of the first row: a count, a setting, a name.

```lua
local result = yoke.db.value(sql, params)
```

| Argument | Type | Notes |
|---|---|---|
| `sql` | string | For example SELECT COUNT(*) FROM players WHERE banned = ? |
| `params` | list | Parameters. |
| `database` (by name only) | setting | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

Returns a table with:

- `value` (any): Value.
- `found` (boolean): False when no row came back.

Needs the `database.view_password` permission.

## Data and kept values: yoke.data

### yoke.data.add_to_variable

**Add to variable.** Adds a number to a kept value, starting from zero when there is none. Safe when two runs do it at once.

```lua
local value = yoke.data.add_to_variable(key, by)
```

| Argument | Type | Notes |
|---|---|---|
| `key` | string | Name. |
| `by` | number | By. Default: 1. |
| `scope` (by name only) | setting | Kept for. One of `"graph"` (This graph), `"server"` (Whole server). Default: "graph". |

Returns `value` (number): New value.

### yoke.data.boolean

**True or false.** A yes or no to feed into other nodes.

In Lua, write true or false.

```lua
local value = yoke.data.boolean(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | boolean | Value. Default: false. |

Returns `value` (boolean): Value.

### yoke.data.convert

**Convert.** Reads a value as another type: "12" as a number, a list as JSON text, and so on.

```lua
local value = yoke.data.convert(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | any | Value. |
| `to` (by name only) | setting | To. One of `"string"` (Text), `"number"` (Number), `"boolean"` (True or false), `"json"` (Data), `"list"` (List). Default: "string". |

Returns `value` (any): Value.

### yoke.data.delete_variable

**Delete variable.** Forgets a kept value.

```lua
local existed = yoke.data.delete_variable(key)
```

| Argument | Type | Notes |
|---|---|---|
| `key` | string | Name. |
| `scope` (by name only) | setting | Kept for. One of `"graph"` (This graph), `"server"` (Whole server). Default: "graph". |

Returns `existed` (boolean): Existed.

### yoke.data.get_variable

**Get variable.** Reads a value kept between runs. "Whole server" values are shared by every graph on this server.

```lua
local result = yoke.data.get_variable(key, default)
```

| Argument | Type | Notes |
|---|---|---|
| `key` | string | Name. |
| `default` | any | Given when nothing is kept under that name. |
| `scope` (by name only) | setting | Kept for. One of `"graph"` (This graph), `"server"` (Whole server). Default: "graph". |

Returns a table with:

- `value` (any): Value.
- `exists` (boolean): Exists.

### yoke.data.json

**JSON.** Structured data written as JSON, parsed for other nodes.

In Lua, write a table.

```lua
local data = yoke.data.json(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | string | JSON. Default: "{}". |

Returns `data` (table): Data.

### yoke.data.number

**Number.** A number to feed into other nodes.

In Lua, write the number itself.

```lua
local value = yoke.data.number(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | number | Value. Default: 0. |

Returns `value` (number): Value.

### yoke.data.payload

**Trigger data.** Everything the trigger produced, as one object. Handy with "Get from JSON".

In a handler, the function's argument already holds this.

```lua
local data = yoke.data.payload()
```

Returns `data` (table): Data.

### yoke.data.set_variable

**Set variable.** Keeps a value between runs under a name.

```lua
local value = yoke.data.set_variable(key, value)
```

| Argument | Type | Notes |
|---|---|---|
| `key` | string | Name. |
| `value` | any | Value. |
| `scope` (by name only) | setting | Kept for. One of `"graph"` (This graph), `"server"` (Whole server). Default: "graph". |

Returns `value` (any): Value.

### yoke.data.template

**Template.** Fills placeholders in text: {{a}} to {{d}} for the inputs, {{trigger.name}} for what set the run off, {{var.name}} and {{shared.name}} for kept values, {{server.name}}, {{server.address}}, {{now}}.

```lua
local text = yoke.data.template(template, a, b, c, d)
```

| Argument | Type | Notes |
|---|---|---|
| `template` | string | Template. |
| `a` | any | A. |
| `b` | any | B. |
| `c` | any | C. |
| `d` | any | D. |

Returns `text` (string): Text.

### yoke.data.text

**Text.** A piece of text to feed into other nodes.

In Lua, write the text itself.

```lua
local value = yoke.data.text(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | string | Value. |

Returns `value` (string): Value.

### yoke.data.type_of

**Type of.** Says what kind of value this is: string, number, boolean, list or json.

```lua
local type = yoke.data.type_of(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | any | Value. |

Returns `type` (string): Type.

## Text: yoke.text

### yoke.text.case

**Change case.** Upper, lower, Title Case or Sentence case.

```lua
local text = yoke.text.case(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `mode` (by name only) | setting | Mode. One of `"upper"` (UPPER), `"lower"`, `"title"` (Title Case), `"sentence"` (Sentence case). Default: "lower". |

Returns `text` (string): Text.

### yoke.text.contains

**Contains.** Whether the text holds the search text, case ignored.

```lua
local result = yoke.text.contains(text, search)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `search` | string | Search. |

Returns `result` (boolean): Result.

### yoke.text.join

**Join text.** Puts up to four pieces of text together, with a separator between the ones that are set.

```lua
local text = yoke.text.join(a, b, c, d)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | string | A. |
| `b` | string | B. |
| `c` | string | C. |
| `d` | string | D. |
| `separator` (by name only) | setting | Separator. |

Returns `text` (string): Text.

### yoke.text.length

**Length.** How many characters the text has.

```lua
local length = yoke.text.length(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |

Returns `length` (number): Length.

### yoke.text.lines

**Lines.** Cuts text into a list of lines, blank ones dropped.

```lua
local result = yoke.text.lines(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |

Returns a table with:

- `list` (list): Lines.
- `count` (number): Count.

### yoke.text.pad

**Pad.** Pads text to a width, on the left or the right.

```lua
local text = yoke.text.pad(text, width)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `width` | number | Width. Default: 10. |
| `with` (by name only) | setting | Pad with. Default: " ". |
| `side` (by name only) | setting | Side. One of `"right"` (Right), `"left"` (Left). Default: "right". |

Returns `text` (string): Text.

### yoke.text.regex

**Regex match.** Finds the first match of a regular expression. Groups come out as a list, named groups as data.

```lua
local result = yoke.text.regex(text, pattern)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `pattern` | string | Pattern. |

Returns a table with:

- `matched` (boolean): Matched.
- `match` (string): Match.
- `groups` (list): Groups.
- `named` (table): Named groups.

### yoke.text.replace

**Replace.** Replaces every occurrence of a search text, or of a regular expression when that is switched on.

```lua
local result = yoke.text.replace(text, search, replace)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `search` | string | Search. |
| `replace` | string | Replace with. |
| `regex` (by name only) | setting | Regular expression. Default: false. |

Returns a table with:

- `text` (string): Text.
- `count` (number): Replacements.

### yoke.text.split

**Split.** Cuts text into a list at every separator. Use \n for a line break, \t for a tab.

```lua
local result = yoke.text.split(text, separator)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `separator` | string | Separator. Default: ",". |
| `trim` (by name only) | setting | Trim each piece. Default: true. |
| `skip_empty` (by name only) | setting | Skip empty pieces. Default: true. |

Returns a table with:

- `list` (list): List.
- `count` (number): Count.

### yoke.text.substring

**Substring.** A slice of the text. Start counts from 0; a negative start counts from the end; length 0 means to the end.

```lua
local text = yoke.text.substring(text, start, length)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |
| `start` | number | Start. Default: 0. |
| `length` | number | Length. Default: 0. |

Returns `text` (string): Text.

### yoke.text.trim

**Trim.** Removes spaces and line breaks from both ends.

```lua
local text = yoke.text.trim(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |

Returns `text` (string): Text.

## Math: yoke.math

### yoke.math.calc

**Calculate.** A and B through one operation.

```lua
local result = yoke.math.calc(a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | number | A. Default: 0. |
| `b` | number | B. Default: 0. |
| `operation` (by name only) | setting | Operation. One of `"add"` (A + B), `"subtract"` (A - B), `"multiply"` (A x B), `"divide"` (A / B), `"modulo"` (A mod B), `"power"` (A to the power B), `"min"` (Smaller of A and B), `"max"` (Larger of A and B). Default: "add". |

Returns `result` (number): Result.

### yoke.math.clamp

**Clamp.** Keeps a number between two bounds.

```lua
local result = yoke.math.clamp(value, min, max)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | number | Value. Default: 0. |
| `min` | number | Min. Default: 0. |
| `max` | number | Max. Default: 100. |

Returns `result` (number): Result.

### yoke.math.percent

**Percentage.** Part of whole as a percentage, rounded to the decimals given.

```lua
local result = yoke.math.percent(part, whole, decimals)
```

| Argument | Type | Notes |
|---|---|---|
| `part` | number | Part. Default: 0. |
| `whole` | number | Whole. Default: 100. |
| `decimals` | number | Decimals. Default: 0. |

Returns `result` (number): Percent.

### yoke.math.random

**Random number.** A random number between the bounds, both included.

```lua
local result = yoke.math.random(min, max)
```

| Argument | Type | Notes |
|---|---|---|
| `min` | number | Min. Default: 0. |
| `max` | number | Max. Default: 100. |
| `integer` (by name only) | setting | Whole number. Default: true. |

Returns `result` (number): Result.

### yoke.math.round

**Round.** Rounds to a number of decimals, or always down or up.

```lua
local result = yoke.math.round(value, decimals)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | number | Value. Default: 0. |
| `decimals` | number | Decimals. Default: 0. |
| `mode` (by name only) | setting | Mode. One of `"round"` (Nearest), `"floor"` (Down), `"ceil"` (Up). Default: "round". |

Returns `result` (number): Result.

## Lists: yoke.list

### yoke.list.append

**Append.** A copy of the list with the item added at the end.

```lua
local list = yoke.list.append(list, item)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `item` | any | Item. |

Returns `list` (list): List.

### yoke.list.contains

**List contains.** Whether the list holds the item, compared as text with case ignored.

```lua
local result = yoke.list.contains(list, item)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `item` | any | Item. |

Returns a table with:

- `result` (boolean): Result.
- `index` (number): -1 when not found.

### yoke.list.first

**First item.**

```lua
local item = yoke.list.first(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |

Returns `item` (any): Item.

### yoke.list.get

**Get item.** The item at an index. 0 is the first; -1 is the last.

```lua
local result = yoke.list.get(list, index)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `index` | number | Index. Default: 0. |

Returns a table with:

- `item` (any): Item.
- `exists` (boolean): Exists.

### yoke.list.join

**Join list.** The items as one text with a separator between them. Use \n for a line break.

```lua
local text = yoke.list.join(list, separator)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `separator` | string | Separator. Default: ", ". |

Returns `text` (string): Text.

### yoke.list.last

**Last item.**

```lua
local item = yoke.list.last(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |

Returns `item` (any): Item.

### yoke.list.length

**List length.** How many items the list has.

```lua
local length = yoke.list.length(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |

Returns `length` (number): Length.

### yoke.list.make

**Make list.** A list of up to four values, in order, the unset ones left out.

```lua
local list = yoke.list.make(a, b, c, d)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | any | A. |
| `b` | any | B. |
| `c` | any | C. |
| `d` | any | D. |

Returns `list` (list): List.

### yoke.list.pluck

**Pluck.** From a list of objects, one field of each as a new list.

```lua
local list = yoke.list.pluck(list, key)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `key` | string | Key. |

Returns `list` (list): List.

### yoke.list.remove

**Remove item.** A copy of the list without every item equal to the one given.

```lua
local result = yoke.list.remove(list, item)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `item` | any | Item. |

Returns a table with:

- `list` (list): List.
- `removed` (number): Removed.

### yoke.list.reverse

**Reverse.**

```lua
local list = yoke.list.reverse(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |

Returns `list` (list): List.

### yoke.list.slice

**Slice.** Part of the list. Start counts from 0; length 0 means to the end.

```lua
local list = yoke.list.slice(list, start, length)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `start` | number | Start. Default: 0. |
| `length` | number | Length. Default: 0. |

Returns `list` (list): List.

### yoke.list.sort

**Sort.** The list in order. Numbers sort as numbers when every item is one.

```lua
local list = yoke.list.sort(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `order` (by name only) | setting | Order. One of `"asc"` (Ascending), `"desc"` (Descending). Default: "asc". |
| `key` (by name only) | setting | For a list of objects: the key to sort by. |

Returns `list` (list): List.

### yoke.list.sum

**Sum.** Adds up a list of numbers, or one field of a list of objects.

```lua
local result = yoke.list.sum(list, key)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `key` | string | Key. |

Returns a table with:

- `sum` (number): Sum.
- `average` (number): Average.

### yoke.list.unique

**Unique.** The list with repeats dropped, first occurrence kept.

```lua
local list = yoke.list.unique(list)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |

Returns `list` (list): List.

### yoke.list.where

**Where.** Keeps the objects whose field compares true against a value, using the Compare operations.

```lua
local result = yoke.list.where(list, key, value)
```

| Argument | Type | Notes |
|---|---|---|
| `list` | list | List. |
| `key` | string | Key. |
| `value` | any | Value. |
| `operation` (by name only) | setting | Operation. One of `"equals"`, `"not_equals"` (does not equal), `"greater"` (is greater than), `"less"` (is less than), `"contains"`, `"starts_with"` (starts with), `"matches"` (matches (regex)). Default: "equals". |

Returns a table with:

- `list` (list): List.
- `count` (number): Count.

## JSON: yoke.json

### yoke.json.get

**Get from JSON.** The value at a path like players.0.name or settings.max.

```lua
local result = yoke.json.get(data, path, default)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | table | Data. |
| `path` | string | Path. |
| `default` | any | Given when there is nothing at the path. |

Returns a table with:

- `value` (any): Value.
- `exists` (boolean): Exists.

### yoke.json.has

**Has field.** Whether there is something at the path.

```lua
local result = yoke.json.has(data, path)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | table | Data. |
| `path` | string | Path. |

Returns `result` (boolean): Result.

### yoke.json.keys

**Keys.** The field names of an object as a list.

```lua
local result = yoke.json.keys(data)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | table | Data. |

Returns a table with:

- `keys` (list): Keys.
- `values` (list): Values.

### yoke.json.merge

**Merge.** B laid over A: fields in both take B's value.

```lua
local data = yoke.json.merge(a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | table | A. |
| `b` | table | B. |
| `deep` (by name only) | setting | Merge nested objects too. Default: true. |

Returns `data` (table): Data.

### yoke.json.parse

**Parse JSON.** Reads JSON text into data. Fails when the text is not JSON.

```lua
local data = yoke.json.parse(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. |

Returns `data` (table): Data.

### yoke.json.remove

**Remove from JSON.** A copy of the data without the field at the path.

```lua
local data = yoke.json.remove(data, path)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | table | Data. |
| `path` | string | Path. |

Returns `data` (table): Data.

### yoke.json.set

**Set in JSON.** A copy of the data with the value put at the path, making the objects on the way.

```lua
local data = yoke.json.set(data, path, value)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | table | Data. |
| `path` | string | Path. |
| `value` | any | Value. |

Returns `data` (table): Data.

### yoke.json.stringify

**To JSON text.** Writes data out as JSON text.

```lua
local text = yoke.json.stringify(data)
```

| Argument | Type | Notes |
|---|---|---|
| `data` | any | Data. |
| `pretty` (by name only) | setting | Indented. Default: true. |

Returns `text` (string): Text.

## Time: yoke.time

### yoke.time.add

**Add time.** A time moved by an amount. Negative amounts go back.

```lua
local result = yoke.time.add(value, amount)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | any | Time. |
| `amount` | number | Amount. Default: 1. |
| `unit` (by name only) | setting | Unit. One of `"seconds"` (Seconds), `"minutes"` (Minutes), `"hours"` (Hours), `"days"` (Days), `"weeks"` (Weeks). Default: "minutes". |

Returns a table with:

- `unix` (number): Unix.
- `iso` (string): ISO 8601.

### yoke.time.difference

**Time between.** How far B is after A, in the unit chosen. Negative when B is earlier.

```lua
local result = yoke.time.difference(a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | any | A. |
| `b` | any | Blank means now. |
| `unit` (by name only) | setting | Unit. One of `"seconds"` (Seconds), `"minutes"` (Minutes), `"hours"` (Hours), `"days"` (Days). Default: "seconds". |

Returns a table with:

- `amount` (number): Amount.
- `human` (string): In words.

### yoke.time.format

**Format time.** A unix timestamp or any date text, written in a format.

```lua
local result = yoke.time.format(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | any | Time. |
| `format` (by name only) | setting | Format. Default: "Y-m-d H:i:s". |
| `timezone` (by name only) | setting | Blank for the panel's own. |

Returns a table with:

- `text` (string): Text.
- `unix` (number): Unix.

### yoke.time.now

**Now.** The current time, formatted, plus the unix timestamp and the ISO form.

```lua
local result = yoke.time.now()
```

| Argument | Type | Notes |
|---|---|---|
| `format` (by name only) | setting | Format. Default: "Y-m-d H:i:s". |
| `timezone` (by name only) | setting | Blank for the panel's own. |

Returns a table with:

- `text` (string): Text.
- `unix` (number): Unix.
- `iso` (string): ISO 8601.
- `hour` (number): Hour.
- `weekday` (string): Weekday.

### yoke.time.parse

**Parse time.** Reads date text ("2026-03-01 18:00", "next friday", "+2 hours") into a timestamp.

```lua
local result = yoke.time.parse(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | Text. Default: "now". |
| `timezone` (by name only) | setting | Blank for the panel's own. |

Returns a table with:

- `unix` (number): Unix.
- `iso` (string): ISO 8601.

## Logic: yoke.logic

### yoke.logic.and

**And.** True when both are true.

`and` is a Lua keyword, so this one is written with brackets. In Lua you would usually just use the `and` operator.

```lua
local result = yoke.logic["and"](a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | boolean | A. Default: false. |
| `b` | boolean | B. Default: false. |

Returns `result` (boolean): Result.

### yoke.logic.choose

**Choose.** Gives one value when the condition is true and the other when it is not.

```lua
local value = yoke.logic.choose(condition, if_true, if_false)
```

| Argument | Type | Notes |
|---|---|---|
| `condition` | boolean | Condition. Default: false. |
| `if_true` | any | If true. |
| `if_false` | any | If false. |

Returns `value` (any): Value.

### yoke.logic.compare

**Compare.** Compares two values. Numbers compare as numbers, everything else as text. "Matches" takes a regular expression.

```lua
local result = yoke.logic.compare(a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | any | A. |
| `b` | any | B. |
| `operation` (by name only) | setting | Operation. One of `"equals"`, `"not_equals"` (does not equal), `"greater"` (is greater than), `"greater_equal"` (is at least), `"less"` (is less than), `"less_equal"` (is at most), `"contains"`, `"starts_with"` (starts with), `"ends_with"` (ends with), `"matches"` (matches (regex)). Default: "equals". |

Returns `result` (boolean): Result.

### yoke.logic.is_empty

**Is empty.** True for nothing, blank text, zero, an empty list or an empty object.

```lua
local result = yoke.logic.is_empty(value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | any | Value. |

Returns `result` (boolean): Result.

### yoke.logic.not

**Not.** Flips a true to false and back.

`not` is a Lua keyword, so this one is written with brackets. In Lua you would usually just use the `not` operator.

```lua
local result = yoke.logic["not"](value)
```

| Argument | Type | Notes |
|---|---|---|
| `value` | boolean | Value. Default: false. |

Returns `result` (boolean): Result.

### yoke.logic.or

**Or.** True when either is true.

`or` is a Lua keyword, so this one is written with brackets. In Lua you would usually just use the `or` operator.

```lua
local result = yoke.logic["or"](a, b)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | boolean | A. Default: false. |
| `b` | boolean | B. Default: false. |

Returns `result` (boolean): Result.

## Web: yoke.web

### yoke.web.discord

**Discord message.** Posts a message to a Discord channel through a webhook URL.

```lua
local ok = yoke.web.discord(webhook, message, title, username)
```

| Argument | Type | Notes |
|---|---|---|
| `webhook` | string | Webhook URL. |
| `message` | string | Message. |
| `title` | string | Set to send as an embed with this title. |
| `username` | string | Sender name. |

Returns `ok` (boolean): OK.

Needs the `nodes.notify` permission.

### yoke.web.request

**Web request.** Calls an https URL and gives back the status and body. With the JSON parser the body comes out as data too.

```lua
local result = yoke.web.request(url, body, headers)
```

| Argument | Type | Notes |
|---|---|---|
| `url` | string | URL. |
| `body` | string | Sent as is for POST, PUT, PATCH and DELETE. |
| `headers` | table | An object of header names to values. |
| `method` (by name only) | setting | Method. One of `"GET"`, `"POST"`, `"PUT"`, `"PATCH"`, `"DELETE"`. Default: "GET". |
| `parser` (by name only) | setting | Read body as. One of `"text"` (Text), `"json"` (JSON). Default: "text". |

Returns a table with:

- `status` (number): Status.
- `ok` (boolean): Status 200 to 299.
- `body` (string): Body.
- `data` (table): Data.

Needs the `nodes.http` permission.

## Discord: yoke.discord

> Needs a Discord bot on the server. See [Discord bots](../05-discord-bots.md).

### yoke.discord.add_role

**Give role.** Gives a member a role. The bot's own role must sit above it.

```lua
local roles = yoke.discord.add_role(guild, user_id, role)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |
| `role` | string | A role name or id. |

Returns `roles` (list): Roles now.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.ban

**Ban member.** Bans a user from a server, deleting their recent messages if asked.

```lua
yoke.discord.ban(guild, user_id, reason, delete_days)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | They need not be in the server. |
| `reason` | string | Reason. |
| `delete_days` | number | 0 to 7 days of their messages to delete. Default: 0. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.bot

**Bot info.** The bot on this server as the panel knows it: name, id, which Discord servers it is in.

```lua
local result = yoke.discord.bot()
```

Returns a table with:

- `name` (string): Name.
- `bot_id` (string): Bot user id.
- `status` (string): online, offline, error or pending.
- `guilds` (list): id, name, members for each.
- `guild_count` (number): Server count.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.button

**Build button.** A button for a message. When pressed, the "Button pressed" event fires with its id. A link button opens a URL instead and fires nothing.

```lua
local button = yoke.discord.button(custom_id, label, emoji, url, disabled)
```

| Argument | Type | Notes |
|---|---|---|
| `custom_id` | string | What "Button pressed" reports. Letters, numbers, dashes, underscores, colons. |
| `label` | string | Label. Default: "Press me". |
| `emoji` | string | Emoji. |
| `url` | string | For the Link style only. |
| `disabled` | boolean | Disabled. Default: false. |
| `style` (by name only) | setting | Style. One of `"primary"` (Blue), `"secondary"` (Grey), `"success"` (Green), `"danger"` (Red), `"link"` (Link). Default: "primary". |

Returns `button` (table): Feed into Build row, or straight into a Send node for a row of one.

Needs a Discord bot on the server.

### yoke.discord.create_channel

**Create channel.** Makes a text, voice or announcement channel, or a category.

```lua
local result = yoke.discord.create_channel(guild, name, parent, topic)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `name` | string | Name. |
| `parent` | string | Optional: a category name or id. |
| `topic` | string | Topic. |
| `type` (by name only) | setting | Type. One of `"text"` (Text), `"voice"` (Voice), `"category"` (Category), `"news"` (Announcement), `"forum"` (Forum). Default: "text". |

Returns a table with:

- `channel_id` (string): Channel id.
- `name` (string): Name.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.delete_channel

**Delete channel.** Deletes a channel and everything in it.

```lua
yoke.discord.delete_channel(channel)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.delete_message

**Delete message.** Deletes a message.

```lua
yoke.discord.delete_message(channel, message_id)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `message_id` | string | Message id. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.dm

**Send direct message.** Sends a user a direct message. They must share a server with the bot and allow DMs.

```lua
local message_id = yoke.discord.dm(user_id, content, embed, components)
```

| Argument | Type | Notes |
|---|---|---|
| `user_id` | string | A user id, usually linked from an event. |
| `content` | string | Content. |
| `embed` | table | Embed. |
| `components` | table | Components. |

Returns `message_id` (string): Message id.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.edit

**Edit message.** Changes a message the bot sent.

```lua
local message_id = yoke.discord.edit(channel, message_id, content, embed, components)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `message_id` | string | Message id. |
| `content` | string | Blank leaves the text as it is when an embed or components are given. |
| `embed` | table | Embed. |
| `components` | table | An empty list removes the buttons. |

Returns `message_id` (string): Message id.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.edit_channel

**Edit channel.** Renames a channel, sets its topic or slow mode.

```lua
yoke.discord.edit_channel(channel, name, topic, slowmode)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `name` | string | Blank keeps it. |
| `topic` | string | Blank keeps it. |
| `slowmode` | number | -1 keeps it, 0 turns it off. Default: -1. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.embed

**Build embed.** A rich embed: title, text, colour, image, fields. Feed it into a Send, Reply or DM node.

```lua
local embed = yoke.discord.embed(title, description, colour, url, image, thumbnail, footer, author, fields, timestamp)
```

| Argument | Type | Notes |
|---|---|---|
| `title` | string | Title. |
| `description` | string | Up to 4096 characters. Markdown works. |
| `colour` | string | A hex colour like #5865F2. Default: "#5865F2". |
| `url` | string | Title link. |
| `image` | string | Image URL. |
| `thumbnail` | string | Thumbnail URL. |
| `footer` | string | Footer. |
| `author` | string | Author line. |
| `fields` | list | A list of "Name: value" strings, or objects with name, value, inline. |
| `timestamp` | boolean | Stamp it with now. Default: false. |

Returns `embed` (table): Embed.

Needs a Discord bot on the server.

### yoke.discord.guild

**Get server.** A Discord server the bot is in: its channels, roles and size.

```lua
local result = yoke.discord.guild(guild)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |

Returns a table with:

- `name` (string): Name.
- `guild_id` (string): Server id.
- `member_count` (number): Members.
- `owner_id` (string): Owner id.
- `channels` (list): id, name, type, parent for each.
- `roles` (list): id, name, members for each.
- `icon` (string): Icon URL.
- `data` (table): Everything.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.invite

**Make invite link.** Makes an invite link to a channel.

```lua
local result = yoke.discord.invite(channel, max_age, max_uses)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `max_age` | number | 0 for never expires. Default: 86400. |
| `max_uses` | number | 0 for unlimited. Default: 0. |

Returns a table with:

- `url` (string): Link.
- `code` (string): Code.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.kick

**Kick member.** Removes a member from a server. They can come back with an invite.

```lua
yoke.discord.kick(guild, user_id, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |
| `reason` | string | Reason. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.member

**Get member.** What the bot knows about a member of a server: names, roles, when they joined, whether they are in voice.

```lua
local result = yoke.discord.member(guild, user_id)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |

Returns a table with:

- `found` (boolean): Found.
- `user` (string): Username.
- `display_name` (string): Display name.
- `nickname` (string): Nickname.
- `roles` (list): Role names.
- `role_ids` (list): Role ids.
- `joined_at` (string): Joined.
- `avatar` (string): Avatar URL.
- `bot` (boolean): Is a bot.
- `voice_channel` (string): Blank when not in voice.
- `timed_out` (boolean): Timed out.
- `data` (table): Everything.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.mention

**Mention.** The text that pings a user, a channel or a role: <@id>, <#id>, <@&id>.

```lua
local text = yoke.discord.mention(id)
```

| Argument | Type | Notes |
|---|---|---|
| `id` | string | Id. |
| `kind` (by name only) | setting | Kind. One of `"user"` (User), `"channel"` (Channel), `"role"` (Role). Default: "user". |

Returns `text` (string): Text.

Needs a Discord bot on the server.

### yoke.discord.messages

**Fetch messages.** Reads the last messages in a channel.

```lua
local result = yoke.discord.messages(channel, limit, before)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `limit` | number | Up to 100. Default: 20. |
| `before` | string | Optional: a message id to read back from. |

Returns a table with:

- `messages` (list): Newest first: message_id, content, author, author_id, at, attachments.
- `count` (number): Count.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.nickname

**Set nickname.** Changes a member's nickname; blank clears it.

```lua
yoke.discord.nickname(guild, user_id, nickname)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |
| `nickname` | string | Up to 32 characters. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.pin

**Pin message.** Pins a message in its channel.

```lua
yoke.discord.pin(channel, message_id)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `message_id` | string | Message id. |
| `unpin` (by name only) | setting | Unpin instead. Default: false. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.presence

**Set bot status.** What the bot shows as doing, and whether it looks online, idle or busy.

```lua
yoke.discord.presence(text)
```

| Argument | Type | Notes |
|---|---|---|
| `text` | string | What it is playing, watching and so on. |
| `status` (by name only) | setting | Status. One of `"online"` (Online), `"idle"` (Idle), `"dnd"` (Do not disturb), `"invisible"` (Invisible). Default: "online". |
| `activity` (by name only) | setting | Activity. One of `""` (None), `"playing"` (Playing), `"watching"` (Watching), `"listening"` (Listening to), `"competing"` (Competing in), `"custom"` (Custom text). Default: "playing". |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.purge

**Delete recent messages.** Deletes the last N messages in a channel (up to 100, none older than two weeks; Discord's rule).

```lua
local deleted = yoke.discord.purge(channel, count)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `count` | number | How many. Default: 10. |

Returns `deleted` (number): Deleted.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.react

**Add reaction.** Reacts to a message with an emoji.

```lua
yoke.discord.react(channel, message_id, emoji)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `message_id` | string | Message id. |
| `emoji` | string | A character, or a custom emoji as name:id. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.remove_role

**Take role.** Takes a role off a member.

```lua
local roles = yoke.discord.remove_role(guild, user_id, role)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |
| `role` | string | A role name or id. |

Returns `roles` (list): Roles now.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.reply

**Reply to command.** Answers a slash command, button or menu choice. The interaction comes from the event that started the graph and stays valid for 15 minutes; the first reply edits the "thinking" placeholder, later ones follow up.

```lua
local message_id = yoke.discord.reply(interaction_id, content, embed, components)
```

| Argument | Type | Notes |
|---|---|---|
| `interaction_id` | string | Link from "Slash command", "Button pressed" or "Menu choice made". |
| `content` | string | Content. |
| `embed` | table | Embed. |
| `components` | table | Components. |
| `ephemeral` (by name only) | setting | For follow ups on a command; the first reply keeps the command's own setting. Default: false. |

Returns `message_id` (string): Message id.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.row

**Build row.** Up to five buttons side by side. A message takes up to five rows.

```lua
local row = yoke.discord.row(a, b, c, d, e)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | table | Button 1. |
| `b` | table | Button 2. |
| `c` | table | Button 3. |
| `d` | table | Button 4. |
| `e` | table | Button 5. |

Returns `row` (table): Row.

Needs a Discord bot on the server.

### yoke.discord.rows

**Combine rows.** Puts rows and menus together for one message, up to five.

```lua
local components = yoke.discord.rows(a, b, c, d, e)
```

| Argument | Type | Notes |
|---|---|---|
| `a` | table | Row 1. |
| `b` | table | Row 2. |
| `c` | table | Row 3. |
| `d` | table | Row 4. |
| `e` | table | Row 5. |

Returns `components` (table): Components.

Needs a Discord bot on the server.

### yoke.discord.select

**Build select menu.** A dropdown for a message. When somebody chooses, the "Menu choice made" event fires with the values.

```lua
local menu = yoke.discord.select(custom_id, placeholder, options, min, max)
```

| Argument | Type | Notes |
|---|---|---|
| `custom_id` | string | Menu id. |
| `placeholder` | string | Placeholder. Default: "Choose". |
| `options` | list | A list of "value:Label" strings (or "value:Label:description"), or objects with value, label, description. |
| `min` | number | Min choices. Default: 1. |
| `max` | number | Max choices. Default: 1. |

Returns `menu` (table): A row of its own; feed into a Send node.

Needs a Discord bot on the server.

### yoke.discord.send

**Send message.** Posts a message in a channel: text, an embed, buttons or a menu.

```lua
local result = yoke.discord.send(channel, content, embed, components, reply_to)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `content` | string | Up to 2000 characters. Markdown works. |
| `embed` | table | From a Build embed node. |
| `components` | table | From Build row, Build button or Build select menu. |
| `reply_to` | string | Optional: a message id to reply to. |

Returns a table with:

- `message_id` (string): Message id.
- `channel_id` (string): Channel id.
- `url` (string): Link.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.thread

**Create thread.** Starts a thread in a channel, on a message or on its own.

```lua
local thread_id = yoke.discord.thread(channel, name, message_id)
```

| Argument | Type | Notes |
|---|---|---|
| `channel` | string | A channel by name (with or without #) or id. Usually linked from an event. |
| `name` | string | Name. |
| `message_id` | string | Optional: the message the thread hangs off. |
| `archive` (by name only) | setting | Auto archive after. One of `"60"` (An hour), `"1440"` (A day), `"4320"` (Three days), `"10080"` (A week). Default: "1440". |

Returns `thread_id` (string): A thread is a channel: send to it with Send message.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.timeout

**Time out member.** Puts a member in timeout for a while (they cannot talk or react), or ends one with 0.

```lua
yoke.discord.timeout(guild, user_id, seconds, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | A user id, usually linked from an event. |
| `seconds` | number | Up to 28 days. 0 lifts a timeout. Default: 600. |
| `reason` | string | Shown in the audit log. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.timestamp

**Discord timestamp.** A time as Discord renders it in everyone's own zone: <t:unix:style>. Relative reads "in 5 minutes".

```lua
local text = yoke.discord.timestamp(unix)
```

| Argument | Type | Notes |
|---|---|---|
| `unix` | number | 0 for now. Default: 0. |
| `style` (by name only) | setting | Style. One of `"R"` (Relative), `"f"` (Date and time), `"F"` (Long date and time), `"d"` (Date), `"D"` (Long date), `"t"` (Time), `"T"` (Time with seconds). Default: "R". |

Returns `text` (string): Text.

Needs a Discord bot on the server.

### yoke.discord.unban

**Unban user.** Lifts a ban.

```lua
yoke.discord.unban(guild, user_id, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `guild` | string | A Discord server by name or id. Blank when the bot is in only one. |
| `user_id` | string | User id. |
| `reason` | string | Reason. |

Returns nothing.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

### yoke.discord.user

**Get user.** A Discord user by id, wherever they are.

```lua
local result = yoke.discord.user(user_id)
```

| Argument | Type | Notes |
|---|---|---|
| `user_id` | string | A user id, usually linked from an event. |

Returns a table with:

- `user` (string): Username.
- `display_name` (string): Display name.
- `avatar` (string): Avatar URL.
- `bot` (boolean): Is a bot.
- `created_at` (string): Account created.

Needs the `nodes.discord` permission. Needs a Discord bot on the server.

## Pavlov VR: yoke.pavlov

> Only on Pavlov VR servers, with the Server Manager switched on.

### Game and players

#### yoke.pavlov.actions

**Recent admin actions.** What admins did lately, from the tab, in game through RCON Plus, or from graphs: kicks, bans, gags, items given and the rest.

```lua
local result = yoke.pavlov.actions(player, limit)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Optional: only actions on this player. |
| `limit` | number | How many. Default: 20. |

Returns a table with:

- `actions` (list): Newest first: action, uid, name, reason, user_name, source, created_at.
- `count` (number): Count.

Needs the `pavlov.read` permission.

#### yoke.pavlov.add_moderator

**Make moderator.** Makes a player an in game moderator. The game only takes AddMod for somebody on the server; anyone else is written into mods.txt and made real when they next join.

```lua
local result = yoke.pavlov.add_moderator(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.moderator` permission.

#### yoke.pavlov.ban

**Ban player.** Bans a player, for a time or until lifted. The poller lifts a timed ban when it runs out. A player who is not on the server can be banned by id.

```lua
local result = yoke.pavlov.ban(player, duration, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `duration` | number | 0 for until lifted. 3600 is an hour, 86400 a day. Default: 0. |
| `reason` | string | Reason. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.ban` permission.

#### yoke.pavlov.ban_list

**Ban list.** Who is banned, as the game lists them right now or as blacklist.txt reads.

```lua
local result = yoke.pavlov.ban_list()
```

| Argument | Type | Notes |
|---|---|---|
| `source` (by name only) | setting | From. One of `"game"` (The game), `"file"` (The file). Default: "game". |

Returns a table with:

- `uids` (list): Player ids.
- `players` (list): uid and name for each, where the name is known.
- `count` (number): Count.

Needs the `pavlov.read` permission.

#### yoke.pavlov.clear_vehicles

**Clear empty vehicles.** Removes vehicles nobody is in.

```lua
local message = yoke.pavlov.clear_vehicles()
```

Returns `message` (string): Message.

Needs the `pavlov.vehicles` permission.

#### yoke.pavlov.command

**RCON command.** Sends one line to the game's RCON as typed and gives back its reply. For whatever the other nodes do not cover. Needs a permission of its own, since it is not bound by the caps on the buttons.

```lua
local result = yoke.pavlov.command(command)
```

| Argument | Type | Notes |
|---|---|---|
| `command` | string | For example ServerInfo, or SetLimitedAmmoType 2. |

Returns a table with:

- `ok` (boolean): The reply's Successful field.
- `reply` (table): The whole reply.

Needs the `pavlov.rcon` permission.

#### yoke.pavlov.events

**Recent game events.** What the game log reported lately: kills, bomb plants and defuses, team switches, round and map ends. Needs verbose logging on the server.

```lua
local result = yoke.pavlov.events(player, limit)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Optional: only events this player was part of. |
| `limit` | number | How many. Default: 20. |
| `kind` (by name only) | setting | Kind. One of `""` (Anything), `"kill"` (Kills), `"bomb"` (Bomb), `"switch_team"` (Team switches), `"round_end"` (Round ends), `"map_end"` (Map ends). |

Returns a table with:

- `events` (list): Newest first: kind, actor, target, details, occurred_at.
- `count` (number): Count.

Needs the `pavlov.read` permission.

#### yoke.pavlov.gag

**Gag player.** Mutes a player in game, for a time or until lifted. The poller keeps a timed gag on them if they reconnect, and lifts it when it runs out.

```lua
local result = yoke.pavlov.gag(player, duration, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `duration` | number | 0 for until lifted. Default: 0. |
| `reason` | string | Reason. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.gag` permission.

#### yoke.pavlov.give_cash

**Give cash.** Gives a player cash.

```lua
local result = yoke.pavlov.give_cash(player, amount)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `amount` | number | 1 to 16000. Default: 1000. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.give-cash` permission.

#### yoke.pavlov.give_item

**Give item.** Gives a player an item the current map offers ("Item list" says which). Limited by the item lists on the account the graph runs as.

```lua
local result = yoke.pavlov.give_item(player, item)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `item` | string | For example ak47, m4, grenade. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.give-item` permission.

#### yoke.pavlov.give_vehicle

**Give vehicle.** Spawns a vehicle for a player. Limited by the vehicle lists on the account the graph runs as.

```lua
local result = yoke.pavlov.give_vehicle(player, vehicle)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `vehicle` | string | For example atv, kubel, sherman. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.give-vehicle` permission.

#### yoke.pavlov.item_list

**Item list.** The items the current map offers, which is what "Give item" accepts.

```lua
local result = yoke.pavlov.item_list()
```

Returns a table with:

- `items` (list): Item ids.
- `names` (list): Names.
- `count` (number): Count.

Needs the `pavlov.read` permission.

#### yoke.pavlov.item_name

**Item name.** The name of an item from its id.

```lua
local name = yoke.pavlov.item_name(item)
```

| Argument | Type | Notes |
|---|---|---|
| `item` | string | Item id. |

Returns `name` (string): Name.

#### yoke.pavlov.kick

**Kick player.** Kicks a player off the server.

```lua
local result = yoke.pavlov.kick(player, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `reason` | string | Kept in the action log. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.kick` permission.

#### yoke.pavlov.kill

**Kill player.** Kills a player where they stand.

```lua
local result = yoke.pavlov.kill(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.kill` permission.

#### yoke.pavlov.map_list

**Map rotation.** The maps in rotation, from the game (what it is running with) or from Game.ini (what it will run with after a restart).

```lua
local result = yoke.pavlov.map_list()
```

| Argument | Type | Notes |
|---|---|---|
| `source` (by name only) | setting | From. One of `"game"` (The game (MapList)), `"file"` (Game.ini). Default: "game". |

Returns a table with:

- `maps` (list): In order: map, map_name, mode.
- `count` (number): Count.
- `ids` (list): Map ids.

Needs the `pavlov.read` permission.

#### yoke.pavlov.map_name

**Map name.** The name of a map from its id: a stock map from the panel's list, a Workshop map from what the mod manager has already looked up.

```lua
local name = yoke.pavlov.map_name(map)
```

| Argument | Type | Notes |
|---|---|---|
| `map` | string | Map id. |

Returns `name` (string): Name.

#### yoke.pavlov.mod_add

**Add mod.** Adds a mod.io mod to AdditionalMods in Game.ini. The server downloads it when it next restarts.

```lua
local result = yoke.pavlov.mod_add(mod)
```

| Argument | Type | Notes |
|---|---|---|
| `mod` | string | The mod.io number, with or without UGC in front. |

Returns a table with:

- `changed` (boolean): False when the list already read that way.
- `count` (number): Mods listed.

Needs the `file.update` permission.

#### yoke.pavlov.mod_list

**Installed mods.** The mod.io mods in Game.ini, and the ones the server has actually downloaded.

```lua
local result = yoke.pavlov.mod_list()
```

Returns a table with:

- `ids` (list): The numbers in AdditionalMods, in order.
- `count` (number): Count.
- `downloaded` (list): Downloaded ids.
- `missing` (list): Listed in Game.ini but not on disk: the server has not restarted since they were added.

Needs the `pavlov.read` permission.

#### yoke.pavlov.mod_remove

**Remove mod.** Takes a mod.io mod out of AdditionalMods in Game.ini. Its files stay on disk until the Mods tab deletes them.

```lua
local result = yoke.pavlov.mod_remove(mod)
```

| Argument | Type | Notes |
|---|---|---|
| `mod` | string | The mod.io number, with or without UGC in front. |

Returns a table with:

- `changed` (boolean): False when the list already read that way.
- `count` (number): Mods listed.

Needs the `file.update` permission.

#### yoke.pavlov.mode_name

**Mode name.** The full name of a game mode code: SND is Search and destroy.

```lua
local name = yoke.pavlov.mode_name(mode)
```

| Argument | Type | Notes |
|---|---|---|
| `mode` | string | Mode id. |

Returns `name` (string): Name.

#### yoke.pavlov.moderator_list

**Moderator list.** Who is a moderator, as the game lists them right now or as mods.txt reads.

```lua
local result = yoke.pavlov.moderator_list()
```

| Argument | Type | Notes |
|---|---|---|
| `source` (by name only) | setting | From. One of `"game"` (The game), `"file"` (The file). Default: "game". |

Returns a table with:

- `uids` (list): Player ids.
- `players` (list): uid and name for each, where the name is known.
- `count` (number): Count.

Needs the `pavlov.read` permission.

#### yoke.pavlov.player

**Find player.** One player by id or name, from everyone this server has ever seen. Not found is an output, not a failure, so a graph can branch on it.

```lua
local result = yoke.pavlov.player(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. |
| `live` (by name only) | setting | Inspect the player over RCON for their live score, cash and ping. They must be online for that. Default: false. |

Returns a table with:

- `found` (boolean): Found.
- `uid` (string): Player id.
- `name` (string): Name.
- `online` (boolean): Online.
- `team` (number): Team.
- `team_name` (string): Team name.
- `kills` (number): Kills.
- `deaths` (number): Deaths.
- `assists` (number): Assists.
- `score` (number): Score.
- `cash` (number): Cash.
- `ping` (number): Ping.
- `gagged` (boolean): Gagged.
- `moderator` (boolean): Moderator.
- `banned` (boolean): Banned.
- `playtime` (number): Including the session in progress.
- `sessions` (number): Visits.
- `first_seen` (string): First seen.
- `last_seen` (string): Last seen.
- `data` (table): Everything.

Needs the `pavlov.read` permission.

#### yoke.pavlov.player_history

**Player history.** When a player tends to be on and what they played: their recent sessions, the hours of the day they play, the maps and modes, and what admins did to them.

```lua
local result = yoke.pavlov.player_history(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Player. |
| `days` (by name only) | setting | Days back. Default: 30. |

Returns a table with:

- `found` (boolean): Found.
- `playtime` (number): Playtime (s)
- `sessions` (list): Most recent first: started_at, ended_at, seconds, map, mode.
- `hours` (list): 24 numbers: seconds played in each hour, panel time.
- `days` (list): One entry per day: date, seconds.
- `maps` (list): Most played first: label, sessions.
- `modes` (list): Modes.
- `actions` (list): The last 50 things done to them, newest first.

Needs the `pavlov.read` permission.

#### yoke.pavlov.players

**Players online.** Everyone on the server right now, with their team, score and kills.

```lua
local result = yoke.pavlov.players()
```

| Argument | Type | Notes |
|---|---|---|
| `live` (by name only) | setting | Ask the game now over RCON rather than use the poll's last reading (up to a minute old). Default: false. |
| `team` (by name only) | setting | Only one team, or everyone. One of `""` (Any), `"0"` (Blue), `"1"` (Red). |

Returns a table with:

- `players` (list): One entry per player: uid, name, team, kills, deaths, score, cash, ping and more.
- `count` (number): Count.
- `uids` (list): Player ids.
- `names` (list): Names.

Needs the `pavlov.read` permission.

#### yoke.pavlov.punishments

**Active punishments.** Timed bans and gags still running, with when each ends.

```lua
local result = yoke.pavlov.punishments(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Optional: only this player. |

Returns a table with:

- `punishments` (list): action, uid, name, reason, expires_at, user_name.
- `count` (number): Count.
- `banned` (boolean): With a player given: whether a timed ban is running on them.
- `gagged` (boolean): Gagged.

Needs the `pavlov.read` permission.

#### yoke.pavlov.remove_moderator

**Remove moderator.** Takes a player off the moderator list.

```lua
local result = yoke.pavlov.remove_moderator(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.moderator` permission.

#### yoke.pavlov.remove_pin

**Remove the pin.** Unlocks the server. Anyone can join.

```lua
local message = yoke.pavlov.remove_pin()
```

Returns `message` (string): Message.

Needs the `pavlov.pin` permission.

#### yoke.pavlov.reset_snd

**Reset match.** Resets a Search and Destroy match: everyone back to starting cash with no score.

```lua
local message = yoke.pavlov.reset_snd()
```

Returns `message` (string): Message.

Needs the `pavlov.reset-snd` permission.

#### yoke.pavlov.rotate_map

**Next map.** Moves the server on to the next map in its rotation.

```lua
local message = yoke.pavlov.rotate_map()
```

Returns `message` (string): Message.

Needs the `pavlov.rotate-map` permission.

#### yoke.pavlov.rotation_add

**Add to rotation.** Adds a map and mode to MapRotation in Game.ini. The game reads the file when it restarts or rotates to it.

```lua
local count = yoke.pavlov.rotation_add(map, mode)
```

| Argument | Type | Notes |
|---|---|---|
| `map` | string | Map. |
| `mode` | string | Mode. Default: "SND". |
| `where` (by name only) | setting | Where. One of `"end"` (At the end), `"start"` (At the start). Default: "end". |

Returns `count` (number): Maps in rotation.

Needs the `file.update` permission.

#### yoke.pavlov.rotation_remove

**Remove from rotation.** Takes a map out of MapRotation in Game.ini: every entry for the map, or only one mode of it.

```lua
local result = yoke.pavlov.rotation_remove(map, mode)
```

| Argument | Type | Notes |
|---|---|---|
| `map` | string | Map. |
| `mode` | string | Blank removes the map in every mode. |

Returns a table with:

- `count` (number): Maps in rotation.
- `removed` (number): Removed.

Needs the `file.update` permission.

#### yoke.pavlov.rotation_set

**Set rotation.** Replaces MapRotation in Game.ini with a list. Each entry is "map mode" text, or an object with map and mode.

```lua
local count = yoke.pavlov.rotation_set(maps)
```

| Argument | Type | Notes |
|---|---|---|
| `maps` | list | From a Make list, a Split, or a Map rotation node's Maps. |

Returns `count` (number): Maps in rotation.

Needs the `file.update` permission.

#### yoke.pavlov.set_cash

**Set cash.** Sets a player's cash to an amount.

```lua
local result = yoke.pavlov.set_cash(player, amount)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `amount` | number | 0 to 16000. Default: 0. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.set-cash` permission.

#### yoke.pavlov.set_pin

**Lock with a pin.** Locks the server behind a pin, 1 to 9999. The pin is not written to any log.

```lua
local message = yoke.pavlov.set_pin(pin)
```

| Argument | Type | Notes |
|---|---|---|
| `pin` | number | Pin. Default: 1234. |

Returns `message` (string): Message.

Needs the `pavlov.pin` permission.

#### yoke.pavlov.set_skin

**Set skin.** Changes a player's skin until they leave or the map changes. Limited by the skin lists on the account the graph runs as.

```lua
local result = yoke.pavlov.set_skin(player, skin)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `skin` | string | For example clown, prisoner, naked, farmer, russian, nato, german, us, soviet. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.set-skin` permission.

#### yoke.pavlov.skin_name

**Skin name.** The name of a skin from its id.

```lua
local name = yoke.pavlov.skin_name(skin)
```

| Argument | Type | Notes |
|---|---|---|
| `skin` | string | Skin id. |

Returns `name` (string): Name.

#### yoke.pavlov.slap

**Slap player.** Slaps a player for damage.

```lua
local result = yoke.pavlov.slap(player, damage)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `damage` | number | 1 to 1000. Default: 10. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.slap` permission.

#### yoke.pavlov.status

**Game status.** How the match stands: map, mode, round, scores, player count, and whether the game is answering. Never fails: an unreachable game reads as online false with the error.

```lua
local result = yoke.pavlov.status()
```

| Argument | Type | Notes |
|---|---|---|
| `live` (by name only) | setting | Ask the game now over RCON rather than use the poll's last reading (up to a minute old). Default: false. |

Returns a table with:

- `online` (boolean): Whether RCON answered.
- `server_name` (string): Server name.
- `map` (string): Map.
- `map_name` (string): Map name.
- `mode` (string): Mode.
- `mode_name` (string): Mode name.
- `players` (number): Players online.
- `max_players` (number): Max players.
- `round` (number): Round.
- `round_state` (string): Round state.
- `teams` (boolean): Team game.
- `team0_score` (number): Blue score.
- `team1_score` (number): Red score.
- `error` (string): Why the game did not answer, else blank.
- `data` (table): Everything.

Needs the `pavlov.read` permission.

#### yoke.pavlov.switch_map

**Switch map.** Switches the server to a map and mode now. A stock map by name (sand, datacenter), a Workshop map as UGC followed by its number.

```lua
local message = yoke.pavlov.switch_map(map, mode)
```

| Argument | Type | Notes |
|---|---|---|
| `map` | string | sand, datacenter, UGC1234567 and so on. |
| `mode` | string | SND, TDM, DM, GUN, KOTH, OITC, PUSH, TTT, ZWV, HIDE, INFECTION, PH, CUSTOM and the rest. Default: "SND". |

Returns `message` (string): Message.

Needs the `pavlov.switch-map` permission.

#### yoke.pavlov.switch_team

**Switch team.** Moves a player to a team, or to the other one.

```lua
local result = yoke.pavlov.switch_team(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `team` (by name only) | setting | Team. One of `""` (The other team), `"0"` (Blue), `"1"` (Red). |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.switch-team` permission.

#### yoke.pavlov.team_name

**Team name.** Blue or Red from a team number.

```lua
local name = yoke.pavlov.team_name(team)
```

| Argument | Type | Notes |
|---|---|---|
| `team` | number | Team. Default: 0. |

Returns `name` (string): Name.

#### yoke.pavlov.teleport

**Teleport player.** Moves a player to where another player is.

```lua
local result = yoke.pavlov.teleport(player, to)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `to` | string | The player to move them to, by id or name. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.teleport` permission.

#### yoke.pavlov.ttt_credits

**Give TTT credits.** Gives a player credits in Trouble in Terrorist Town.

```lua
local result = yoke.pavlov.ttt_credits(player, amount)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `amount` | number | 1 to 1000. Default: 1. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.ttt-credits` permission.

#### yoke.pavlov.ttt_end_round

**End TTT round.** Ends the round in Trouble in Terrorist Town in favour of a team.

```lua
local message = yoke.pavlov.ttt_end_round()
```

| Argument | Type | Notes |
|---|---|---|
| `team` (by name only) | setting | Winner. One of `"0"` (Blue), `"1"` (Red). Default: "0". |

Returns `message` (string): Message.

Needs the `pavlov.ttt-end-round` permission.

#### yoke.pavlov.unban

**Unban player.** Lifts a ban, and ends any timed ban the panel was keeping.

```lua
local result = yoke.pavlov.unban(player, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `reason` | string | Reason. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.unban` permission.

#### yoke.pavlov.ungag

**Ungag player.** Lets a gagged player talk again.

```lua
local result = yoke.pavlov.ungag(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `message` (string): What was done, as the tab would word it.
- `uid` (string): Player id.
- `name` (string): Player.

Needs the `pavlov.gag` permission.

#### yoke.pavlov.vehicle_name

**Vehicle name.** The name of a vehicle from its id.

```lua
local name = yoke.pavlov.vehicle_name(vehicle)
```

| Argument | Type | Notes |
|---|---|---|
| `vehicle` | string | Vehicle id. |

Returns `name` (string): Name.

### RCON Plus: yoke.pavlov.plus

These need the Pavlov RCON Plus mod on the server, and fail without it.

#### yoke.pavlov.plus.add_bots

**Add bots.** Adds bots, to a team or to whichever needs them.

```lua
local message = yoke.pavlov.plus.add_bots(amount)
```

| Argument | Type | Notes |
|---|---|---|
| `amount` | number | How many. Default: 1. |
| `team` (by name only) | setting | Team. One of `""` (Either), `"0"` (Blue), `"1"` (Red). |

Returns `message` (string): Message.

Needs the `rconplus.add-bots` permission.

#### yoke.pavlov.plus.attachment_mode

**Attachment mode.** Turns attachment mode on or off.

```lua
local message = yoke.pavlov.plus.attachment_mode(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.attachment-mode` permission.

#### yoke.pavlov.plus.cleanup

**Clean up.** Removes idle bodies, items and vehicles from the map.

```lua
local message = yoke.pavlov.plus.cleanup()
```

| Argument | Type | Notes |
|---|---|---|
| `object` (by name only) | setting | What. One of `"All"` (Everything), `"Bodies"`, `"Items"`, `"Vehicles"`. Default: "All". |

Returns `message` (string): Message.

Needs the `rconplus.cleanup` permission.

#### yoke.pavlov.plus.detonate

**Detonate.** Blows a player up.

```lua
local message = yoke.pavlov.plus.detonate(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.detonate` permission.

#### yoke.pavlov.plus.disable_items

**Disable items.** Stops a player using items, or lets them again.

```lua
local message = yoke.pavlov.plus.disable_items(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.disable-items` permission.

#### yoke.pavlov.plus.disable_pickup

**Disable pickup.** Stops a player picking up items, or lets them again.

```lua
local message = yoke.pavlov.plus.disable_pickup(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.disable-pickup` permission.

#### yoke.pavlov.plus.disable_voting

**Voting.** Turns the map vote off or back on. On means voting is disabled.

```lua
local message = yoke.pavlov.plus.disable_voting(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.disable-voting` permission.

#### yoke.pavlov.plus.drop_items

**Drop items.** Makes a player drop everything they hold.

```lua
local message = yoke.pavlov.plus.drop_items(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.drop-items` permission.

#### yoke.pavlov.plus.enable_buy_menu

**Buy menu.** Gives a player the buy menu, or takes it away.

```lua
local message = yoke.pavlov.plus.enable_buy_menu(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.enable-buy-menu` permission.

#### yoke.pavlov.plus.enable_prone

**Prone.** Lets players go prone, or not.

```lua
local message = yoke.pavlov.plus.enable_prone(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.enable-prone` permission.

#### yoke.pavlov.plus.fall_damage

**Fall damage.** Turns fall damage on or off.

```lua
local message = yoke.pavlov.plus.fall_damage(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.fall-damage` permission.

#### yoke.pavlov.plus.game_speed

**Game speed.** Speeds the whole game up or slows it down: 1 is normal.

```lua
local message = yoke.pavlov.plus.game_speed(multiplier)
```

| Argument | Type | Notes |
|---|---|---|
| `multiplier` | number | 0.05 to 20. 1 is normal. Default: 1. |

Returns `message` (string): Message.

Needs the `rconplus.game-speed` permission.

#### yoke.pavlov.plus.godmode

**Godmode.** Makes a player immune to all damage, or mortal again.

```lua
local message = yoke.pavlov.plus.godmode(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.godmode` permission.

#### yoke.pavlov.plus.gravity

**Gravity.** Sets gravity: 1 is normal, 0.2 is the moon.

```lua
local message = yoke.pavlov.plus.gravity(multiplier)
```

| Argument | Type | Notes |
|---|---|---|
| `multiplier` | number | 0.05 to 20. 1 is normal. Default: 1. |

Returns `message` (string): Message.

Needs the `rconplus.gravity` permission.

#### yoke.pavlov.plus.ignite

**Ignite.** Sets a player on fire.

```lua
local message = yoke.pavlov.plus.ignite(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.ignite` permission.

#### yoke.pavlov.plus.kill_feedback

**Kill feedback.** Turns kill feedback on or off.

```lua
local message = yoke.pavlov.plus.kill_feedback(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.kill-feedback` permission.

#### yoke.pavlov.plus.movement_speed

**Movement speed.** Sets how fast a player moves: 1 is normal, 2 twice as fast, 0.5 half.

```lua
local message = yoke.pavlov.plus.movement_speed(player, multiplier)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `multiplier` | number | 0.05 to 20. Default: 1. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.movement-speed` permission.

#### yoke.pavlov.plus.noclip

**Noclip.** Lets a player fly through walls, or puts them back on the ground.

```lua
local message = yoke.pavlov.plus.noclip(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.noclip` permission.

#### yoke.pavlov.plus.notify

**Show a message.** Shows a message on a player's screen, or everyone's. The only way to talk to players in Pavlov. Mind the mod's terms: be respectful, no soliciting.

```lua
local message = yoke.pavlov.plus.notify(player, message)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `message` | string | One line. {{ }} from a Template node works well here. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.notify` permission.

#### yoke.pavlov.plus.remove_bots

**Remove bots.** Removes bots: a number of them, or every one with 0.

```lua
local message = yoke.pavlov.plus.remove_bots(amount)
```

| Argument | Type | Notes |
|---|---|---|
| `amount` | number | 0 removes them all. Default: 0. |
| `team` (by name only) | setting | Team. One of `""` (Either), `"0"` (Blue), `"1"` (Red). |

Returns `message` (string): Message.

Needs the `rconplus.remove-bots` permission.

#### yoke.pavlov.plus.remove_zombies

**Remove zombies.** Removes every zombie from the map.

```lua
local message = yoke.pavlov.plus.remove_zombies()
```

Returns `message` (string): Message.

Needs the `rconplus.remove-zombies` permission.

#### yoke.pavlov.plus.revive

**Revive.** Brings a dead player back.

```lua
local message = yoke.pavlov.plus.revive(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.revive` permission.

#### yoke.pavlov.plus.set_team_skin

**Team skin.** Makes a team spawn with a skin, or back to its default with a blank skin. Limited by the skin lists on the account the graph runs as.

```lua
local message = yoke.pavlov.plus.set_team_skin(skin)
```

| Argument | Type | Notes |
|---|---|---|
| `skin` | string | Blank for the team's default. |
| `team` (by name only) | setting | Team. One of `"0"` (Blue), `"1"` (Red). Default: "0". |

Returns `message` (string): Message.

Needs the `rconplus.set-team-skin` permission.

#### yoke.pavlov.plus.set_vitality

**Set health.** Sets a player's health, armour and helmet. -1 leaves one as it is.

```lua
local message = yoke.pavlov.plus.set_vitality(player, health, armor, helmet)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `health` | number | -1 to 1000. Default: 100. |
| `armor` | number | Armour. Default: -1. |
| `helmet` | number | Helmet. Default: -1. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.set-vitality` permission.

#### yoke.pavlov.plus.spawn_chickens

**Spawn chickens.** Spawns chickens at random spawn points.

```lua
local message = yoke.pavlov.plus.spawn_chickens(amount)
```

| Argument | Type | Notes |
|---|---|---|
| `amount` | number | How many. Default: 5. |

Returns `message` (string): Message.

Needs the `rconplus.spawn-chickens` permission.

#### yoke.pavlov.plus.spawn_loot_crate

**Drop a loot crate.** Drops a loot crate somewhere on the map.

```lua
local message = yoke.pavlov.plus.spawn_loot_crate(crate)
```

| Argument | Type | Notes |
|---|---|---|
| `crate` | string | Optional: a particular crate. |

Returns `message` (string): Message.

Needs the `rconplus.spawn-loot-crate` permission.

#### yoke.pavlov.plus.spawn_zombies

**Spawn zombies.** Spawns zombies at random spawn points.

```lua
local message = yoke.pavlov.plus.spawn_zombies(amount)
```

| Argument | Type | Notes |
|---|---|---|
| `amount` | number | How many. Default: 5. |

Returns `message` (string): Message.

Needs the `rconplus.spawn-zombies` permission.

#### yoke.pavlov.plus.supply

**Endless supply.** Gives a player an endless supply of an item, or clears every supply. Limited by the item lists on the account the graph runs as.

```lua
local message = yoke.pavlov.plus.supply(player, item)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `item` | string | An item id, or Clear to take every supply away. Default: "Clear". |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.supply` permission.

#### yoke.pavlov.plus.team_switching

**Team switching.** Lets players switch team themselves, or not.

```lua
local message = yoke.pavlov.plus.team_switching(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.team-switching` permission.

#### yoke.pavlov.plus.utility_trails

**Utility trails.** Shows trails behind thrown utility, or hides them.

```lua
local message = yoke.pavlov.plus.utility_trails(enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `enabled` | boolean | On. Default: true. |

Returns `message` (string): Message.

Needs the `rconplus.utility-trails` permission.

#### yoke.pavlov.plus.visibility

**Visibility.** Makes a player visible to others, or invisible.

```lua
local message = yoke.pavlov.plus.visibility(player, enabled)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `enabled` | boolean | On. Default: true. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.visibility` permission.

#### yoke.pavlov.plus.warp

**Warp.** Warps a player, a team or everyone to where another player is.

```lua
local message = yoke.pavlov.plus.warp(player, to)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | Used when Target is one player: a player id or exact name. |
| `to` | string | By id or name. |
| `target` (by name only) | setting | Target. One of `"player"` (One player (the Player input)), `"All"` (Everyone), `"BlueTeam"` (Blue team), `"RedTeam"` (Red team), `"Random"` (A random player), `"Innocent"` (TTT innocents), `"Traitor"` (TTT traitors), `"Detective"` (TTT detectives), `"Jester"` (TTT jesters). Default: "player". |

Returns `message` (string): Message.

Needs the `rconplus.warp` permission.

### Security: yoke.pavlov.security

#### yoke.pavlov.security.ban_address

**Ban address.** Bans an address, or a range written like 203.0.113.0/24, on this server. Anybody joining from it is kicked, or banned, as the Security tab is set.

```lua
local result = yoke.pavlov.security.ban_address(address, duration, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `address` | string | An IP address, or a CIDR range no wider than the panel allows. |
| `duration` | number | 0 for until lifted. Default: 0. |
| `reason` | string | Reason. |

Returns a table with:

- `ban_id` (number): Ban id.
- `message` (string): Message.

Needs the `pavlov-security.ip-ban` permission.

#### yoke.pavlov.security.ban_player_address

**Ban player's address.** Bans the address a player last connected from, or the /24 range around it, without the graph ever seeing it. Only an address the panel is sure was theirs is used.

```lua
local result = yoke.pavlov.security.ban_player_address(player, duration, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `duration` | number | 0 for until lifted. Default: 0. |
| `reason` | string | Reason. |
| `what` (by name only) | setting | Ban. One of `"address"` (Their address), `"range"` (The /24 range around it). Default: "address". |

Returns a table with:

- `ban_id` (number): Ban id.
- `masked_address` (string): Address (hidden end)
- `message` (string): Message.

Needs the `pavlov-security.ip-ban` permission.

#### yoke.pavlov.security.exempt_player

**Exempt player.** Puts a player on, or takes them off, the list the VPN, country and ban evasion checks leave alone. Bans still apply to them.

```lua
local message = yoke.pavlov.security.exempt_player(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| `mode` (by name only) | setting | Do. One of `"add"` (Exempt them), `"remove"` (Stop exempting them). Default: "add". |

Returns `message` (string): Message.

Needs the `pavlov-security.settings` permission.

#### yoke.pavlov.security.global_ban

**Global ban.** Bans a player, an address or a range on every Pavlov server the graph's editor can place global bans on. A player is put into each server's own ban list; an address or range acts when somebody joins from it.

```lua
local result = yoke.pavlov.security.global_ban(target, duration, reason)
```

| Argument | Type | Notes |
|---|---|---|
| `target` | string | A player id or name, an IP address, or a CIDR range. |
| `duration` | number | 0 for until lifted. Default: 0. |
| `reason` | string | Reason. |
| `later` (by name only) | setting | Also reaches servers the editor gets the global ban permission on later. Default: true. |

Returns a table with:

- `ban_id` (number): Ban id.
- `servers` (number): How many servers it was placed on.
- `message` (string): Message.

Needs the `pavlov-security.global-ban` permission.

#### yoke.pavlov.security.is_banned

**Is banned.** Whether a player or an address is banned on this server, and by what: the game's own ban, a global ban, or an address or range ban.

```lua
local result = yoke.pavlov.security.is_banned(target)
```

| Argument | Type | Notes |
|---|---|---|
| `target` | string | A player id or name, or an IP address. |

Returns a table with:

- `banned` (boolean): Banned.
- `by` (string): game, global, ip or range; blank when not banned.
- `ban_id` (number): 0 for the game's own ban.
- `reason` (string): Reason.

Needs the `pavlov-security.read` permission.

#### yoke.pavlov.security.lift_address_ban

**Lift address ban.** Lifts this server's ban on an address or range, written as it was banned.

```lua
local message, branch = yoke.pavlov.security.lift_address_ban(address)
```

| Argument | Type | Notes |
|---|---|---|
| `address` | string | Address or range. |

Returns `message` (string): Message.

Also returns which way it went: `"then"` or `"none"`.

Needs the `pavlov-security.ip-ban` permission.

#### yoke.pavlov.security.lift_global_ban

**Lift global ban.** Lifts one of the editor's own global bans everywhere it reached, found by its id or by what it banned.

```lua
local message, branch = yoke.pavlov.security.lift_global_ban(target)
```

| Argument | Type | Notes |
|---|---|---|
| `target` | string | Ban id, player, address or range. |

Returns `message` (string): Message.

Also returns which way it went: `"then"` or `"none"`.

Needs the `pavlov-security.global-ban` permission.

#### yoke.pavlov.security.lookup_address

**Look up address.** What is known about an address: VPN, hosting company, country and network. Answers are cached for a day across the panel.

```lua
local result = yoke.pavlov.security.lookup_address(address)
```

| Argument | Type | Notes |
|---|---|---|
| `address` | string | Address. |

Returns a table with:

- `vpn` (boolean): VPN.
- `hosting` (boolean): Hosting address.
- `country` (string): Country.
- `isp` (string): Network.
- `asn` (string): ASN.
- `banned` (boolean): Banned here.
- `error` (string): Why nothing could be found out, else blank.

Needs the `pavlov-security.ips` permission.

#### yoke.pavlov.security.player_addresses

**Player addresses.** The addresses a player has connected to this server from, newest first, with where each is and whether it is a VPN.

```lua
local result = yoke.pavlov.security.player_addresses(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `latest` (string): Latest address.
- `addresses` (list): Addresses.
- `countries` (list): Every country code seen, once each.
- `vpn_seen` (boolean): Used a VPN.
- `count` (number): Count.

Needs the `pavlov-security.ips` permission.

#### yoke.pavlov.security.set_rules

**Set protection rules.** Changes the VPN and ban evasion rules, for example to tighten them for an event and loosen them after. Anything left on "Leave as it is" is not touched.

```lua
local message = yoke.pavlov.security.set_rules()
```

| Argument | Type | Notes |
|---|---|---|
| `vpn` (by name only) | setting | VPN and hosting. One of `"keep"` (Leave as it is), `"off"` (Off), `"flag"` (Flag), `"kick"` (Kick). Default: "keep". |
| `evasion` (by name only) | setting | Ban evasion. One of `"keep"` (Leave as it is), `"off"` (Off), `"flag"` (Flag), `"kick"` (Kick), `"ban"` (Ban). Default: "keep". |
| `ip_ban_action` (by name only) | setting | Banned address. One of `"keep"` (Leave as it is), `"kick"` (Kick), `"ban"` (Ban the account). Default: "keep". |

Returns `message` (string): Message.

Needs the `pavlov-security.settings` permission.

#### yoke.pavlov.security.shared_accounts

**Accounts sharing an address.** Other accounts seen on this server from any address the player used: likely alts, family or a shared connection. No addresses are given out.

```lua
local result = yoke.pavlov.security.shared_accounts(player)
```

| Argument | Type | Notes |
|---|---|---|
| `player` | string | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |

Returns a table with:

- `accounts` (list): Player ids.
- `names` (list): Names.
- `banned` (number): Banned among them.
- `count` (number): Count.

Needs the `pavlov-security.read` permission.
