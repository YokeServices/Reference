# Nodes reference

Every node in the node browser, group by group. "In" pins take values, "Out" pins give
them, and "Setting" is set on the node itself (in the side panel). Nodes with no arrow
pins are worked out whenever another node reads them; see
[How graphs work](../02-how-graphs-work.md#two-kinds-of-link).

A node that changes something on your server needs the same permission the panel asks
for the matching button. The owner of a server has all of them.

The event nodes ("On ...") are listed in the [Events reference](events.md).

## Contents

- [Discord](#discord)
- [Flow](#flow)
- [Logic](#logic)
- [Server](#server)
- [Files](#files)
- [Databases](#databases)
- [Data](#data)
- [Text](#text)
- [Math](#math)
- [Lists](#lists)
- [JSON](#json)
- [Time](#time)
- [Web](#web)
- [Graph functions](#graph-functions)
- [Pavlov VR](#pavlov-vr)

## Discord

> Needs a Discord bot on the server. See [Discord bots](../05-discord-bots.md).

### Send message

Posts a message in a channel: text, an embed, buttons or a menu.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Content | Text | Up to 2000 characters. Markdown works. |
| In | Embed | JSON | From a Build embed node. |
| In | Components | JSON | From Build row, Build button or Build select menu. |
| In | Reply to message | Text | Optional: a message id to reply to. |
| Out | Message id | Text |  |
| Out | Channel id | Text |  |
| Out | Link | Text |  |

### Reply to command

Answers a slash command, button or menu choice. The interaction comes from the event that started the graph and stays valid for 15 minutes; the first reply edits the "thinking" placeholder, later ones follow up.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Interaction | Text | Link from "Slash command", "Button pressed" or "Menu choice made". |
| In | Content | Text |  |
| In | Embed | JSON |  |
| In | Components | JSON |  |
| Out | Message id | Text |  |
| Setting | Only they see it |  | For follow ups on a command; the first reply keeps the command's own setting. |

### Edit message

Changes a message the bot sent.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Message id | Text |  |
| In | Content | Text | Blank leaves the text as it is when an embed or components are given. |
| In | Embed | JSON |  |
| In | Components | JSON | An empty list removes the buttons. |
| Out | Message id | Text |  |

### Delete message

Deletes a message.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Message id | Text |  |

### Delete recent messages

Deletes the last N messages in a channel (up to 100, none older than two weeks; Discord's rule).

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | How many | Number |  |
| Out | Deleted | Number |  |

### Add reaction

Reacts to a message with an emoji.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Message id | Text |  |
| In | Emoji | Text | A character, or a custom emoji as name:id. |

### Fetch messages

Reads the last messages in a channel.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | How many | Number | Up to 100. |
| In | Before message | Text | Optional: a message id to read back from. |
| Out | Messages | List | Newest first: message_id, content, author, author_id, at, attachments. |
| Out | Count | Number |  |

### Send direct message

Sends a user a direct message. They must share a server with the bot and allow DMs.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | User id | Text | A user id, usually linked from an event. |
| In | Content | Text |  |
| In | Embed | JSON |  |
| In | Components | JSON |  |
| Out | Message id | Text |  |

### Pin message

Pins a message in its channel.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Message id | Text |  |
| Setting | Unpin instead |  |  |

### Get member

What the bot knows about a member of a server: names, roles, when they joined, whether they are in voice.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| Out | Found | Yes/no |  |
| Out | Username | Text |  |
| Out | Display name | Text |  |
| Out | Nickname | Text |  |
| Out | Roles | List | Role names. |
| Out | Role ids | List |  |
| Out | Joined | Text |  |
| Out | Avatar URL | Text |  |
| Out | Is a bot | Yes/no |  |
| Out | Voice channel | Text | Blank when not in voice. |
| Out | Timed out | Yes/no |  |
| Out | Everything | JSON |  |

### Get user

A Discord user by id, wherever they are.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | User id | Text | A user id, usually linked from an event. |
| Out | Username | Text |  |
| Out | Display name | Text |  |
| Out | Avatar URL | Text |  |
| Out | Is a bot | Yes/no |  |
| Out | Account created | Text |  |

### Give role

Gives a member a role. The bot's own role must sit above it.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| In | Role | Text | A role name or id. |
| Out | Roles now | List |  |

### Take role

Takes a role off a member.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| In | Role | Text | A role name or id. |
| Out | Roles now | List |  |

### Set nickname

Changes a member's nickname; blank clears it.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| In | Nickname | Text | Up to 32 characters. |

### Time out member

Puts a member in timeout for a while (they cannot talk or react), or ends one with 0.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| In | Seconds | Number | Up to 28 days. 0 lifts a timeout. |
| In | Reason | Text | Shown in the audit log. |

### Kick member

Removes a member from a server. They can come back with an invite.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | A user id, usually linked from an event. |
| In | Reason | Text |  |

### Ban member

Bans a user from a server, deleting their recent messages if asked.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text | They need not be in the server. |
| In | Reason | Text |  |
| In | Delete messages (days) | Number | 0 to 7 days of their messages to delete. |

### Unban user

Lifts a ban.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | User id | Text |  |
| In | Reason | Text |  |

### Get server

A Discord server the bot is in: its channels, roles and size.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| Out | Name | Text |  |
| Out | Server id | Text |  |
| Out | Members | Number |  |
| Out | Owner id | Text |  |
| Out | Channels | List | id, name, type, parent for each. |
| Out | Roles | List | id, name, members for each. |
| Out | Icon URL | Text |  |
| Out | Everything | JSON |  |

### Create channel

Makes a text, voice or announcement channel, or a category.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Server | Text | A Discord server by name or id. Blank when the bot is in only one. |
| In | Name | Text |  |
| In | Under category | Text | Optional: a category name or id. |
| In | Topic | Text |  |
| Out | Channel id | Text |  |
| Out | Name | Text |  |
| Setting | Type |  | One of: Text, Voice, Category, Announcement, Forum. |

### Edit channel

Renames a channel, sets its topic or slow mode.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | New name | Text | Blank keeps it. |
| In | Topic | Text | Blank keeps it. |
| In | Slow mode (s) | Number | -1 keeps it, 0 turns it off. |

### Delete channel

Deletes a channel and everything in it.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |

### Create thread

Starts a thread in a channel, on a message or on its own.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Name | Text |  |
| In | From message | Text | Optional: the message the thread hangs off. |
| Out | Thread id | Text | A thread is a channel: send to it with Send message. |
| Setting | Auto archive after |  | One of: An hour, A day, Three days, A week. |

### Make invite link

Makes an invite link to a channel.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Channel | Text | A channel by name (with or without #) or id. Usually linked from an event. |
| In | Valid for (s) | Number | 0 for never expires. |
| In | Max uses | Number | 0 for unlimited. |
| Out | Link | Text |  |
| Out | Code | Text |  |

### Set bot status

What the bot shows as doing, and whether it looks online, idle or busy.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text | What it is playing, watching and so on. |
| Setting | Status |  | One of: Online, Idle, Do not disturb, Invisible. |
| Setting | Activity |  | One of: None, Playing, Watching, Listening to, Competing in, Custom text. |

### Bot info

The bot on this server as the panel knows it: name, id, which Discord servers it is in.

- Needs the `nodes.discord` permission.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Name | Text |  |
| Out | Bot user id | Text |  |
| Out | Status | Text | online, offline, error or pending. |
| Out | Servers | List | id, name, members for each. |
| Out | Server count | Number |  |

### Build embed

A rich embed: title, text, colour, image, fields. Feed it into a Send, Reply or DM node.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Title | Text |  |
| In | Text | Text | Up to 4096 characters. Markdown works. |
| In | Colour | Text | A hex colour like #5865F2. |
| In | Title link | Text |  |
| In | Image URL | Text |  |
| In | Thumbnail URL | Text |  |
| In | Footer | Text |  |
| In | Author line | Text |  |
| In | Fields | List | A list of "Name: value" strings, or objects with name, value, inline. |
| In | Timestamp | Yes/no | Stamp it with now. |
| Out | Embed | JSON |  |

### Build button

A button for a message. When pressed, the "Button pressed" event fires with its id. A link button opens a URL instead and fires nothing.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Button id | Text | What "Button pressed" reports. Letters, numbers, dashes, underscores, colons. |
| In | Label | Text |  |
| In | Emoji | Text |  |
| In | Link URL | Text | For the Link style only. |
| In | Disabled | Yes/no |  |
| Out | Button | JSON | Feed into Build row, or straight into a Send node for a row of one. |
| Setting | Style |  | One of: Blue, Grey, Green, Red, Link. |

### Build select menu

A dropdown for a message. When somebody chooses, the "Menu choice made" event fires with the values.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Menu id | Text |  |
| In | Placeholder | Text |  |
| In | Options | List | A list of "value:Label" strings (or "value:Label:description"), or objects with value, label, description. |
| In | Min choices | Number |  |
| In | Max choices | Number |  |
| Out | Menu | JSON | A row of its own; feed into a Send node. |

### Build row

Up to five buttons side by side. A message takes up to five rows.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Button 1 | JSON |  |
| In | Button 2 | JSON |  |
| In | Button 3 | JSON |  |
| In | Button 4 | JSON |  |
| In | Button 5 | JSON |  |
| Out | Row | JSON |  |

### Combine rows

Puts rows and menus together for one message, up to five.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Row 1 | JSON |  |
| In | Row 2 | JSON |  |
| In | Row 3 | JSON |  |
| In | Row 4 | JSON |  |
| In | Row 5 | JSON |  |
| Out | Components | JSON |  |

### Mention

The text that pings a user, a channel or a role: <@id>, <#id>, <@&id>.

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Id | Text |  |
| Out | Text | Text |  |
| Setting | Kind |  | One of: User, Channel, Role. |

### Discord timestamp

A time as Discord renders it in everyone's own zone: <t:unix:style>. Relative reads "in 5 minutes".

- No arrow pins: works its value out whenever a node reads it.
- Needs a Discord bot on the server.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Unix time | Number | 0 for now. |
| Out | Text | Text |  |
| Setting | Style |  | One of: Relative, Date and time, Long date and time, Date, Long date, Time, Time with seconds. |

## Flow

### Branch

Goes one way when the condition is true and the other when it is not.

- Arrow outputs: "True", "False".

| | Pin | Type | Notes |
|---|---|---|---|
| In | Condition | Yes/no |  |

### Sequence

Runs each branch in turn, top to bottom.

- Arrow outputs: "First", "Second", "Third", "Fourth".

### For each

Runs "Each" once per item in the list, then "Done".

- Arrow outputs: "Each", "Done".

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List | Any list, or one item per line. |
| Out | Item | Anything |  |
| Out | Index | Number | Starts at 0. |
| Out | Count | Number |  |

### Repeat

Runs "Each" the given number of times, then "Done".

- Arrow outputs: "Each", "Done".

| | Pin | Type | Notes |
|---|---|---|---|
| In | Times | Number |  |
| Out | Index | Number | Starts at 0. |

### Wait

Pauses the run. A run may wait 300 seconds in total.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Seconds | Number |  |

### Stop

Ends the run here. Not a failure.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Reason | Text |  |

### Log

Writes a line to the run log, with a value after it when one is linked.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Message | Text |  |
| In | Value | Anything |  |
| Setting | Level |  | One of: Note, Info, Warning. |

### Try

Runs "Try". If a node in there fails, runs "Failed" with the error instead of ending the run. "Then" runs after either.

- Arrow outputs: "Try", "Failed", "Then".

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Error | Text |  |

### Switch

Follows the case whose text equals the value, or "Default" when none does.

- Arrow outputs: "Case 1", "Case 2", "Case 3", "Case 4", "Default".

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Text |  |
| Setting | Case 1 |  |  |
| Setting | Case 2 |  |  |
| Setting | Case 3 |  |  |
| Setting | Case 4 |  |  |

### Run another graph

Queues a run of another graph on this server, starting at its "Called by another graph" node. Does not wait for it.

- Needs the `nodes.run` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Input | Anything | Handed to the other graph as its input. |
| Out | Run id | Number |  |
| Setting | Graph |  | Which graph to run. |

## Logic

### Compare

Compares two values. Numbers compare as numbers, everything else as text. "Matches" takes a regular expression.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Anything |  |
| In | B | Anything |  |
| Out | Result | Yes/no |  |
| Setting | Operation |  | One of: equals, does not equal, is greater than, is at least, is less than, is at most, contains, starts with, ends with, matches (regex). |

### And

True when both are true.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Yes/no |  |
| In | B | Yes/no |  |
| Out | Result | Yes/no |  |

### Or

True when either is true.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Yes/no |  |
| In | B | Yes/no |  |
| Out | Result | Yes/no |  |

### Not

Flips a true to false and back.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Yes/no |  |
| Out | Result | Yes/no |  |

### Is empty

True for nothing, blank text, zero, an empty list or an empty object.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Anything |  |
| Out | Result | Yes/no |  |

### Choose

Gives one value when the condition is true and the other when it is not.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Condition | Yes/no |  |
| In | If true | Anything |  |
| In | If false | Anything |  |
| Out | Value | Anything |  |

## Server

### Start server

Starts the server, as the Start button does.

- Needs the `control.start` permission.

### Stop server

Stops the server gracefully, as the Stop button does.

- Needs the `control.stop` permission.

### Restart server

Restarts the server, as the Restart button does.

- Needs the `control.restart` permission.

### Kill server

Kills the server process at once. Unsaved game state is lost.

- Needs the `control.stop` permission.

### Send command

Types a command into the server console. The server has to be running.

- Needs the `control.console` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Command | Text |  |

### Server info

What the panel knows about this server: name, address, limits, egg, status.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Name | Text |  |
| Out | UUID | Text |  |
| Out | Short id | Text |  |
| Out | Description | Text |  |
| Out | Address | Text | ip:port of the primary allocation. |
| Out | IP | Text |  |
| Out | Port | Number |  |
| Out | Memory (MB) | Number |  |
| Out | Disk (MB) | Number |  |
| Out | CPU (%) | Number |  |
| Out | Egg | Text |  |
| Out | Node | Text |  |
| Out | Owner | Text |  |
| Out | Status | Text | Blank when installed and not suspended. |
| Out | Everything | JSON |  |

### Server status

Asks Wings how the server is doing right now: state, CPU, memory, disk, uptime.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | State | Text | running, offline, starting or stopping. |
| Out | Online | Yes/no |  |
| Out | CPU (%) | Number |  |
| Out | Memory (MB) | Number |  |
| Out | Disk (MB) | Number |  |
| Out | Uptime (s) | Number |  |
| Out | Network in (bytes) | Number |  |
| Out | Network out (bytes) | Number |  |

### Make backup

Starts a backup, as the Backups tab does. Fails when the server is at its backup limit.

- Needs the `backup.create` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text | Blank for the panel's dated name. |
| In | Ignored | Text | One path per line to leave out. |
| Out | Backup UUID | Text |  |
| Out | Name | Text |  |
| Setting | Replace the oldest when full |  |  |

### List backups

The server's backups, newest first.

- Needs the `backup.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Backups | List | Each: uuid, name, successful, locked, bytes, completed_at. |
| Out | Count | Number |  |

### Get startup variable

The value of one of the server's startup variables, by its environment name (SERVER_NAME, MAX_PLAYERS).

- No arrow pins: works its value out whenever a node reads it.
- Needs the `startup.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Variable | Text |  |
| Out | Value | Text |  |
| Out | Default | Text |  |
| Out | Exists | Yes/no |  |

### Set startup variable

Changes one of the server's startup variables. Takes effect on the next start. Only variables the Startup tab lets users edit.

- Needs the `startup.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Variable | Text |  |
| In | Value | Text |  |
| Out | Old value | Text |  |

### Run schedule

Runs one of the server's schedules now, by name, as its Run now button does.

- Needs the `schedule.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Schedule | Text |  |

### List users

The people with access to this server: the owner and every subuser.

- Needs the `user.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Users | List | Each: username, email, owner. |
| Out | Count | Number |  |

### List allocations

The server's network allocations, the primary one first.

- Needs the `allocation.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Allocations | List | Each: ip, port, alias, address, primary. |
| Out | Count | Number |  |

### Activity note

Writes a line to the server's Activity tab, so people can see what the graph did.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Message | Text |  |

## Files

### Read file

Reads a file and, with a parser chosen, turns it into data: JSON, one item per line, key=value pairs, an INI file, or CSV rows.

- Needs the `file.read-content` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text | From the server root, like /config/server.json. |
| Out | Content | Text |  |
| Out | Data | JSON | What the parser made of it; the text again for "Text". |
| Out | Size (bytes) | Number |  |
| Setting | Parser |  | One of: Text, JSON, Lines, key=value, INI, CSV. |

### Write file

Writes text to a file, making it when it does not exist. "Append" adds to the end instead.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text |  |
| In | Content | Text |  |
| Out | Bytes written | Number |  |
| Setting | Mode |  | One of: Replace, Append, Only if missing. |

### Write JSON file

Writes data to a file as JSON.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text |  |
| In | Data | JSON |  |
| Setting | Indented |  |  |

### File exists

Whether a file or folder is there, and its size and last change when it is.

- Needs the `file.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text |  |
| Out | Exists | Yes/no |  |
| Out | Size (bytes) | Number |  |
| Out | Is folder | Yes/no |  |
| Out | Modified | Text |  |

### List folder

What is in a folder.

- Needs the `file.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Folder | Text |  |
| Out | Entries | List | Each: name, path, size, directory, modified. |
| Out | Names | List |  |
| Out | Count | Number |  |
| Setting | Files only |  |  |

### Delete files

Deletes files or folders. One path per line, or a list.

- Needs the `file.delete` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Paths | List |  |
| Out | Deleted | Number |  |

### Rename or move

Renames a file or folder, or moves it when the new path is somewhere else.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | From | Text |  |
| In | To | Text |  |

### Copy file

Makes a copy of a file next to it, as the Files tab does ("name copy").

- Needs the `file.create` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text |  |

### Make folder

Makes a folder, and the folders on the way to it.

- Needs the `file.create` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Path | Text |  |

### Download to server

Has Wings download a file from a URL into a folder on the server. Same rules as the Web request node: https, public hosts, the panel's allow list.

- Needs the `file.create` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | URL | Text |  |
| In | Folder | Text |  |
| In | Save as | Text | Blank keeps the name from the URL. |

## Databases

### Databases

The databases this server has, as the Databases tab lists them.

- Needs the `database.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Databases | List | name, host, port, username, max_connections for each. |
| Out | Names | List |  |
| Out | Count | Number |  |
| Out | Limit | Number | How many the server may have; blank for no limit. |

### Database details

Where one database lives and who its user is. No password here; that is "Database credentials".

- Needs the `database.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Database | Text | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |
| Out | Found | Yes/no |  |
| Out | Name | Text |  |
| Out | Host | Text |  |
| Out | Port | Number |  |
| Out | Username | Text |  |
| Out | Allowed from | Text |  |
| Out | Max connections | Number |  |

### Database credentials

Everything needed to connect to one database, password included. For writing a config file or handing to another service.

- Needs the `database.view_password` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Database | Text | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |
| Out | Host | Text |  |
| Out | Port | Number |  |
| Out | Database | Text |  |
| Out | Username | Text |  |
| Out | Password | Text |  |
| Out | Connection URL | Text | mysql://user:password@host:port/database. |
| Out | JDBC URL | Text |  |

### Create database

Makes a database for the server, as the Databases tab does, within the server's limit. The name gets the server's prefix.

- Needs the `database.create` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text | 3 to 48 letters, numbers, dashes, underscores. |
| In | Allowed from | Text | % for anywhere, or an address. |
| Out | Name | Text |  |
| Out | Username | Text |  |
| Out | Password | Text |  |
| Out | Host | Text |  |
| Out | Port | Number |  |

### Delete database

Drops a database and its user. Everything in it is gone.

- Needs the `database.delete` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Database | Text | The database name. Never blank: this one is not guessed. |

### Rotate database password

Gives a database user a new random password and returns it.

- Needs the `database.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Database | Text | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |
| Out | New password | Text |  |
| Out | Username | Text |  |

### Run SQL

Runs one SQL statement on a database as its own user and gives back the rows (SELECT) or how many rows changed (INSERT, UPDATE, DELETE). Use ? for values and link them as Parameters so nothing is pasted into the SQL.

- Needs the `database.view_password` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | SQL | Text | One statement. ? marks a value from Parameters, in order. |
| In | Parameters | List | The values for the ? marks, in order. From a Make list node. |
| Out | Rows | List | One object per row, column names as keys. |
| Out | Row count | Number |  |
| Out | Columns | List |  |
| Out | Rows changed | Number |  |
| Out | Insert id | Text | The auto increment id of an INSERT, else blank. |
| Out | Truncated | Yes/no | True when more rows matched than the cap allows. |
| Setting | Database |  | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

### Query one value

Runs a SELECT and gives back the first column of the first row: a count, a setting, a name.

- Needs the `database.view_password` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | SQL | Text | For example SELECT COUNT(*) FROM players WHERE banned = ? |
| In | Parameters | List |  |
| Out | Value | Anything |  |
| Out | Found | Yes/no | False when no row came back. |
| Setting | Database |  | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

### List tables

The tables in a database, with how many rows each has (as MySQL estimates it).

- Needs the `database.view_password` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Tables | List | name and rows for each. |
| Out | Names | List |  |
| Out | Count | Number |  |
| Setting | Database |  | The database name as the Databases tab shows it (with or without the s123_ prefix). Blank for the server's first database. |

## Data

### Text

A piece of text to feed into other nodes.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Text |  |
| Out | Value | Text |  |

### Number

A number to feed into other nodes.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Number |  |
| Out | Value | Number |  |

### True or false

A yes or no to feed into other nodes.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Yes/no |  |
| Out | Value | Yes/no |  |

### JSON

Structured data written as JSON, parsed for other nodes.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | JSON | Text |  |
| Out | Data | JSON |  |

### Trigger data

Everything the trigger produced, as one object. Handy with "Get from JSON".

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Data | JSON |  |

### Get variable

Reads a value kept between runs. "Whole server" values are shared by every graph on this server.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text |  |
| In | Default | Anything | Given when nothing is kept under that name. |
| Out | Value | Anything |  |
| Out | Exists | Yes/no |  |
| Setting | Kept for |  | One of: This graph, Whole server. |

### Set variable

Keeps a value between runs under a name.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text |  |
| In | Value | Anything |  |
| Out | Value | Anything |  |
| Setting | Kept for |  | One of: This graph, Whole server. |

### Add to variable

Adds a number to a kept value, starting from zero when there is none. Safe when two runs do it at once.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text |  |
| In | By | Number |  |
| Out | New value | Number |  |
| Setting | Kept for |  | One of: This graph, Whole server. |

### Delete variable

Forgets a kept value.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Name | Text |  |
| Out | Existed | Yes/no |  |
| Setting | Kept for |  | One of: This graph, Whole server. |

### Template

Fills placeholders in text: {{a}} to {{d}} for the inputs, {{trigger.name}} for what set the run off, {{var.name}} and {{shared.name}} for kept values, {{server.name}}, {{server.address}}, {{now}}.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Template | Text |  |
| In | A | Anything |  |
| In | B | Anything |  |
| In | C | Anything |  |
| In | D | Anything |  |
| Out | Text | Text |  |

### Convert

Reads a value as another type: "12" as a number, a list as JSON text, and so on.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Anything |  |
| Out | Value | Anything |  |
| Setting | To |  | One of: Text, Number, True or false, Data, List. |

### Type of

Says what kind of value this is: string, number, boolean, list or json.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Anything |  |
| Out | Type | Text |  |

## Text

### Join text

Puts up to four pieces of text together, with a separator between the ones that are set.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Text |  |
| In | B | Text |  |
| In | C | Text |  |
| In | D | Text |  |
| Out | Text | Text |  |
| Setting | Separator |  |  |

### Change case

Upper, lower, Title Case or Sentence case.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Text | Text |  |
| Setting | Mode |  | One of: UPPER, lower, Title Case, Sentence case. |

### Trim

Removes spaces and line breaks from both ends.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Text | Text |  |

### Replace

Replaces every occurrence of a search text, or of a regular expression when that is switched on.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Search | Text |  |
| In | Replace with | Text |  |
| Out | Text | Text |  |
| Out | Replacements | Number |  |
| Setting | Regular expression |  |  |

### Split

Cuts text into a list at every separator. Use \n for a line break, \t for a tab.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Separator | Text |  |
| Out | List | List |  |
| Out | Count | Number |  |
| Setting | Trim each piece |  |  |
| Setting | Skip empty pieces |  |  |

### Lines

Cuts text into a list of lines, blank ones dropped.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Lines | List |  |
| Out | Count | Number |  |

### Length

How many characters the text has.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Length | Number |  |

### Substring

A slice of the text. Start counts from 0; a negative start counts from the end; length 0 means to the end.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Start | Number |  |
| In | Length | Number |  |
| Out | Text | Text |  |

### Regex match

Finds the first match of a regular expression. Groups come out as a list, named groups as data.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Pattern | Text |  |
| Out | Matched | Yes/no |  |
| Out | Match | Text |  |
| Out | Groups | List |  |
| Out | Named groups | JSON |  |

### Contains

Whether the text holds the search text, case ignored.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Search | Text |  |
| Out | Result | Yes/no |  |

### Pad

Pads text to a width, on the left or the right.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| In | Width | Number |  |
| Out | Text | Text |  |
| Setting | Pad with |  |  |
| Setting | Side |  | One of: Right, Left. |

## Math

### Calculate

A and B through one operation.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Number |  |
| In | B | Number |  |
| Out | Result | Number |  |
| Setting | Operation |  | One of: A + B, A - B, A x B, A / B, A mod B, A to the power B, Smaller of A and B, Larger of A and B. |

### Round

Rounds to a number of decimals, or always down or up.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Number |  |
| In | Decimals | Number |  |
| Out | Result | Number |  |
| Setting | Mode |  | One of: Nearest, Down, Up. |

### Random number

A random number between the bounds, both included.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Min | Number |  |
| In | Max | Number |  |
| Out | Result | Number |  |
| Setting | Whole number |  |  |

### Clamp

Keeps a number between two bounds.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Value | Number |  |
| In | Min | Number |  |
| In | Max | Number |  |
| Out | Result | Number |  |

### Percentage

Part of whole as a percentage, rounded to the decimals given.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Part | Number |  |
| In | Whole | Number |  |
| In | Decimals | Number |  |
| Out | Percent | Number |  |

## Lists

### Make list

A list of up to four values, in order, the unset ones left out.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Anything |  |
| In | B | Anything |  |
| In | C | Anything |  |
| In | D | Anything |  |
| Out | List | List |  |

### List length

How many items the list has.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | Length | Number |  |

### Get item

The item at an index. 0 is the first; -1 is the last.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Index | Number |  |
| Out | Item | Anything |  |
| Out | Exists | Yes/no |  |

### First item

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | Item | Anything |  |

### Last item

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | Item | Anything |  |

### Append

A copy of the list with the item added at the end.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Item | Anything |  |
| Out | List | List |  |

### Remove item

A copy of the list without every item equal to the one given.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Item | Anything |  |
| Out | List | List |  |
| Out | Removed | Number |  |

### List contains

Whether the list holds the item, compared as text with case ignored.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Item | Anything |  |
| Out | Result | Yes/no |  |
| Out | Index | Number | -1 when not found. |

### Join list

The items as one text with a separator between them. Use \n for a line break.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Separator | Text |  |
| Out | Text | Text |  |

### Slice

Part of the list. Start counts from 0; length 0 means to the end.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Start | Number |  |
| In | Length | Number |  |
| Out | List | List |  |

### Sort

The list in order. Numbers sort as numbers when every item is one.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | List | List |  |
| Setting | Order |  | One of: Ascending, Descending. |
| Setting | By key |  | For a list of objects: the key to sort by. |

### Unique

The list with repeats dropped, first occurrence kept.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | List | List |  |

### Reverse

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| Out | List | List |  |

### Pluck

From a list of objects, one field of each as a new list.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Key | Text |  |
| Out | List | List |  |

### Where

Keeps the objects whose field compares true against a value, using the Compare operations.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Key | Text |  |
| In | Value | Anything |  |
| Out | List | List |  |
| Out | Count | Number |  |
| Setting | Operation |  | One of: equals, does not equal, is greater than, is less than, contains, starts with, matches (regex). |

### Sum

Adds up a list of numbers, or one field of a list of objects.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | List | List |  |
| In | Key | Text |  |
| Out | Sum | Number |  |
| Out | Average | Number |  |

## JSON

### Parse JSON

Reads JSON text into data. Fails when the text is not JSON.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Data | JSON |  |

### To JSON text

Writes data out as JSON text.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | Anything |  |
| Out | Text | Text |  |
| Setting | Indented |  |  |

### Get from JSON

The value at a path like players.0.name or settings.max.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | JSON |  |
| In | Path | Text |  |
| In | Default | Anything | Given when there is nothing at the path. |
| Out | Value | Anything |  |
| Out | Exists | Yes/no |  |

### Set in JSON

A copy of the data with the value put at the path, making the objects on the way.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | JSON |  |
| In | Path | Text |  |
| In | Value | Anything |  |
| Out | Data | JSON |  |

### Remove from JSON

A copy of the data without the field at the path.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | JSON |  |
| In | Path | Text |  |
| Out | Data | JSON |  |

### Keys

The field names of an object as a list.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | JSON |  |
| Out | Keys | List |  |
| Out | Values | List |  |

### Merge

B laid over A: fields in both take B's value.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | JSON |  |
| In | B | JSON |  |
| Out | Data | JSON |  |
| Setting | Deep |  | Merge nested objects too. |

### Has field

Whether there is something at the path.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Data | JSON |  |
| In | Path | Text |  |
| Out | Result | Yes/no |  |

## Time

### Now

The current time, formatted, plus the unix timestamp and the ISO form.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Text | Text |  |
| Out | Unix | Number |  |
| Out | ISO 8601 | Text |  |
| Out | Hour | Number |  |
| Out | Weekday | Text |  |
| Setting | Format |  |  |
| Setting | Timezone |  | Blank for the panel's own. |

### Format time

A unix timestamp or any date text, written in a format.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Time | Anything |  |
| Out | Text | Text |  |
| Out | Unix | Number |  |
| Setting | Format |  |  |
| Setting | Timezone |  | Blank for the panel's own. |

### Parse time

Reads date text ("2026-03-01 18:00", "next friday", "+2 hours") into a timestamp.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Text | Text |  |
| Out | Unix | Number |  |
| Out | ISO 8601 | Text |  |
| Setting | Timezone |  | Blank for the panel's own. |

### Add time

A time moved by an amount. Negative amounts go back.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Time | Anything |  |
| In | Amount | Number |  |
| Out | Unix | Number |  |
| Out | ISO 8601 | Text |  |
| Setting | Unit |  | One of: Seconds, Minutes, Hours, Days, Weeks. |

### Time between

How far B is after A, in the unit chosen. Negative when B is earlier.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | A | Anything |  |
| In | B | Anything | Blank means now. |
| Out | Amount | Number |  |
| Out | In words | Text |  |
| Setting | Unit |  | One of: Seconds, Minutes, Hours, Days. |

## Web

### Web request

Calls an https URL and gives back the status and body. With the JSON parser the body comes out as data too.

- Needs the `nodes.http` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | URL | Text |  |
| In | Body | Text | Sent as is for POST, PUT, PATCH and DELETE. |
| In | Headers | JSON | An object of header names to values. |
| Out | Status | Number |  |
| Out | OK | Yes/no | Status 200 to 299. |
| Out | Body | Text |  |
| Out | Data | JSON |  |
| Setting | Method |  | One of: GET, POST, PUT, PATCH, DELETE. |
| Setting | Read body as |  | One of: Text, JSON. |

### Discord message

Posts a message to a Discord channel through a webhook URL.

- Needs the `nodes.notify` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Webhook URL | Text |  |
| In | Message | Text |  |
| In | Title | Text | Set to send as an embed with this title. |
| In | Sender name | Text |  |
| Out | OK | Yes/no |  |

## Graph functions

> Part of the Scripts beta. See [Graph functions](../04-graph-functions.md).

### Function

Makes this graph a function: other graphs use it as a node and Lua calls it as graphs.<name>(...). The call ends at a Return node.

- Needs the `nodes.lua` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Setting | Name |  | What it is called by. Letters, numbers and _. |
| Setting | Parameters |  | One per line: name, type, label. As in "player string Player name". |
| Setting | Returns |  | What the Return node hands back, one per line the same way. |

### Return

Ends the function and hands back these values. Its inputs are the Returns of the Function node.

- Needs the `nodes.lua` permission.

## Pavlov VR

> Only on Pavlov VR servers, with the Server Manager switched on.

### Pavlov

#### Game status

How the match stands: map, mode, round, scores, player count, and whether the game is answering. Never fails: an unreachable game reads as online false with the error.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Online | Yes/no | Whether RCON answered. |
| Out | Server name | Text |  |
| Out | Map | Text |  |
| Out | Map name | Text |  |
| Out | Mode | Text |  |
| Out | Mode name | Text |  |
| Out | Players online | Number |  |
| Out | Max players | Number |  |
| Out | Round | Number |  |
| Out | Round state | Text |  |
| Out | Team game | Yes/no |  |
| Out | Blue score | Number |  |
| Out | Red score | Number |  |
| Out | Error | Text | Why the game did not answer, else blank. |
| Out | Everything | JSON |  |
| Setting | Ask the game now |  | Ask the game now over RCON rather than use the poll's last reading (up to a minute old). |

#### Players online

Everyone on the server right now, with their team, score and kills.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Players | List | One entry per player: uid, name, team, kills, deaths, score, cash, ping and more. |
| Out | Count | Number |  |
| Out | Player ids | List |  |
| Out | Names | List |  |
| Setting | Ask the game now |  | Ask the game now over RCON rather than use the poll's last reading (up to a minute old). |
| Setting | Team |  | Only one team, or everyone. One of: Any, Blue, Red. |

#### Find player

One player by id or name, from everyone this server has ever seen. Not found is an output, not a failure, so a graph can branch on it.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. |
| Out | Found | Yes/no |  |
| Out | Player id | Text |  |
| Out | Name | Text |  |
| Out | Online | Yes/no |  |
| Out | Team | Number |  |
| Out | Team name | Text |  |
| Out | Kills | Number |  |
| Out | Deaths | Number |  |
| Out | Assists | Number |  |
| Out | Score | Number |  |
| Out | Cash | Number |  |
| Out | Ping | Number |  |
| Out | Gagged | Yes/no |  |
| Out | Moderator | Yes/no |  |
| Out | Banned | Yes/no |  |
| Out | Playtime (s) | Number | Including the session in progress. |
| Out | Visits | Number |  |
| Out | First seen | Text |  |
| Out | Last seen | Text |  |
| Out | Everything | JSON |  |
| Setting | Ask the game now |  | Inspect the player over RCON for their live score, cash and ping. They must be online for that. |

#### Player history

When a player tends to be on and what they played: their recent sessions, the hours of the day they play, the maps and modes, and what admins did to them.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text |  |
| Out | Found | Yes/no |  |
| Out | Playtime (s) | Number |  |
| Out | Sessions | List | Most recent first: started_at, ended_at, seconds, map, mode. |
| Out | Hours of day | List | 24 numbers: seconds played in each hour, panel time. |
| Out | Days | List | One entry per day: date, seconds. |
| Out | Maps | List | Most played first: label, sessions. |
| Out | Modes | List |  |
| Out | Admin actions | List | The last 50 things done to them, newest first. |
| Setting | Days back |  |  |

#### Recent game events

What the game log reported lately: kills, bomb plants and defuses, team switches, round and map ends. Needs verbose logging on the server.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Optional: only events this player was part of. |
| In | How many | Number |  |
| Out | Events | List | Newest first: kind, actor, target, details, occurred_at. |
| Out | Count | Number |  |
| Setting | Kind |  | One of: Anything, Kills, Bomb, Team switches, Round ends, Map ends. |

#### Recent admin actions

What admins did lately, from the tab, in game through RCON Plus, or from graphs: kicks, bans, gags, items given and the rest.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Optional: only actions on this player. |
| In | How many | Number |  |
| Out | Actions | List | Newest first: action, uid, name, reason, user_name, source, created_at. |
| Out | Count | Number |  |

#### Active punishments

Timed bans and gags still running, with when each ends.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Optional: only this player. |
| Out | Punishments | List | action, uid, name, reason, expires_at, user_name. |
| Out | Count | Number |  |
| Out | Banned | Yes/no | With a player given: whether a timed ban is running on them. |
| Out | Gagged | Yes/no |  |

#### Map rotation

The maps in rotation, from the game (what it is running with) or from Game.ini (what it will run with after a restart).

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Maps | List | In order: map, map_name, mode. |
| Out | Count | Number |  |
| Out | Map ids | List |  |
| Setting | From |  | One of: The game (MapList), Game.ini. |

#### Item list

The items the current map offers, which is what "Give item" accepts.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Item ids | List |  |
| Out | Names | List |  |
| Out | Count | Number |  |

#### Ban list

Who is banned, as the game lists them right now or as blacklist.txt reads.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Player ids | List |  |
| Out | Players | List | uid and name for each, where the name is known. |
| Out | Count | Number |  |
| Setting | From |  | One of: The game, The file. |

#### Moderator list

Who is a moderator, as the game lists them right now or as mods.txt reads.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Player ids | List |  |
| Out | Players | List | uid and name for each, where the name is known. |
| Out | Count | Number |  |
| Setting | From |  | One of: The game, The file. |

#### Installed mods

The mod.io mods in Game.ini, and the ones the server has actually downloaded.

- Needs the `pavlov.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Mod ids | List | The numbers in AdditionalMods, in order. |
| Out | Count | Number |  |
| Out | Downloaded ids | List |  |
| Out | Not downloaded yet | List | Listed in Game.ini but not on disk: the server has not restarted since they were added. |

#### Map name

The name of a map from its id: a stock map from the panel's list, a Workshop map from what the mod manager has already looked up.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Map id | Text |  |
| Out | Name | Text |  |

#### Mode name

The full name of a game mode code: SND is Search and destroy.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Mode id | Text |  |
| Out | Name | Text |  |

#### Item name

The name of an item from its id.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Item id | Text |  |
| Out | Name | Text |  |

#### Skin name

The name of a skin from its id.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Skin id | Text |  |
| Out | Name | Text |  |

#### Vehicle name

The name of a vehicle from its id.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Vehicle id | Text |  |
| Out | Name | Text |  |

#### Team name

Blue or Red from a team number.

- No arrow pins: works its value out whenever a node reads it.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Team | Number |  |
| Out | Name | Text |  |

#### Kick player

Kicks a player off the server.

- Needs the `pavlov.kick` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Reason | Text | Kept in the action log. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Ban player

Bans a player, for a time or until lifted. The poller lifts a timed ban when it runs out. A player who is not on the server can be banned by id.

- Needs the `pavlov.ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Duration (s) | Number | 0 for until lifted. 3600 is an hour, 86400 a day. |
| In | Reason | Text |  |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Unban player

Lifts a ban, and ends any timed ban the panel was keeping.

- Needs the `pavlov.unban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Reason | Text |  |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Gag player

Mutes a player in game, for a time or until lifted. The poller keeps a timed gag on them if they reconnect, and lifts it when it runs out.

- Needs the `pavlov.gag` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Duration (s) | Number | 0 for until lifted. |
| In | Reason | Text |  |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Ungag player

Lets a gagged player talk again.

- Needs the `pavlov.gag` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Kill player

Kills a player where they stand.

- Needs the `pavlov.kill` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Slap player

Slaps a player for damage.

- Needs the `pavlov.slap` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Damage | Number | 1 to 1000. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Teleport player

Moves a player to where another player is.

- Needs the `pavlov.teleport` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | To player | Text | The player to move them to, by id or name. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Switch team

Moves a player to a team, or to the other one.

- Needs the `pavlov.switch-team` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |
| Setting | Team |  | One of: The other team, Blue, Red. |

#### Give cash

Gives a player cash.

- Needs the `pavlov.give-cash` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Amount | Number | 1 to 16000. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Set cash

Sets a player's cash to an amount.

- Needs the `pavlov.set-cash` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Amount | Number | 0 to 16000. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Give item

Gives a player an item the current map offers ("Item list" says which). Limited by the item lists on the account the graph runs as.

- Needs the `pavlov.give-item` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Item id | Text | For example ak47, m4, grenade. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Give vehicle

Spawns a vehicle for a player. Limited by the vehicle lists on the account the graph runs as.

- Needs the `pavlov.give-vehicle` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Vehicle id | Text | For example atv, kubel, sherman. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Set skin

Changes a player's skin until they leave or the map changes. Limited by the skin lists on the account the graph runs as.

- Needs the `pavlov.set-skin` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Skin id | Text | For example clown, prisoner, naked, farmer, russian, nato, german, us, soviet. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Give TTT credits

Gives a player credits in Trouble in Terrorist Town.

- Needs the `pavlov.ttt-credits` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Amount | Number | 1 to 1000. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Make moderator

Makes a player an in game moderator. The game only takes AddMod for somebody on the server; anyone else is written into mods.txt and made real when they next join.

- Needs the `pavlov.moderator` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Remove moderator

Takes a player off the moderator list.

- Needs the `pavlov.moderator` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text | What was done, as the tab would word it. |
| Out | Player id | Text |  |
| Out | Player | Text |  |

#### Switch map

Switches the server to a map and mode now. A stock map by name (sand, datacenter), a Workshop map as UGC followed by its number.

- Needs the `pavlov.switch-map` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Map | Text | sand, datacenter, UGC1234567 and so on. |
| In | Mode | Text | SND, TDM, DM, GUN, KOTH, OITC, PUSH, TTT, ZWV, HIDE, INFECTION, PH, CUSTOM and the rest. |
| Out | Message | Text |  |

#### Next map

Moves the server on to the next map in its rotation.

- Needs the `pavlov.rotate-map` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |

#### Reset match

Resets a Search and Destroy match: everyone back to starting cash with no score.

- Needs the `pavlov.reset-snd` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |

#### End TTT round

Ends the round in Trouble in Terrorist Town in favour of a team.

- Needs the `pavlov.ttt-end-round` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |
| Setting | Winner |  | One of: Blue, Red. |

#### Lock with a pin

Locks the server behind a pin, 1 to 9999. The pin is not written to any log.

- Needs the `pavlov.pin` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Pin | Number |  |
| Out | Message | Text |  |

#### Remove the pin

Unlocks the server. Anyone can join.

- Needs the `pavlov.pin` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |

#### Clear empty vehicles

Removes vehicles nobody is in.

- Needs the `pavlov.vehicles` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |

#### Add to rotation

Adds a map and mode to MapRotation in Game.ini. The game reads the file when it restarts or rotates to it.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Map | Text |  |
| In | Mode | Text |  |
| Out | Maps in rotation | Number |  |
| Setting | Where |  | One of: At the end, At the start. |

#### Remove from rotation

Takes a map out of MapRotation in Game.ini: every entry for the map, or only one mode of it.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Map | Text |  |
| In | Mode | Text | Blank removes the map in every mode. |
| Out | Maps in rotation | Number |  |
| Out | Removed | Number |  |

#### Set rotation

Replaces MapRotation in Game.ini with a list. Each entry is "map mode" text, or an object with map and mode.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Maps | List | From a Make list, a Split, or a Map rotation node's Maps. |
| Out | Maps in rotation | Number |  |

#### Add mod

Adds a mod.io mod to AdditionalMods in Game.ini. The server downloads it when it next restarts.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Mod id | Text | The mod.io number, with or without UGC in front. |
| Out | Changed | Yes/no | False when the list already read that way. |
| Out | Mods listed | Number |  |

#### Remove mod

Takes a mod.io mod out of AdditionalMods in Game.ini. Its files stay on disk until the Mods tab deletes them.

- Needs the `file.update` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Mod id | Text | The mod.io number, with or without UGC in front. |
| Out | Changed | Yes/no | False when the list already read that way. |
| Out | Mods listed | Number |  |

#### RCON command

Sends one line to the game's RCON as typed and gives back its reply. For whatever the other nodes do not cover. Needs a permission of its own, since it is not bound by the caps on the buttons.

- Needs the `pavlov.rcon` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Command | Text | For example ServerInfo, or SetLimitedAmmoType 2. |
| Out | Successful | Yes/no | The reply's Successful field. |
| Out | Reply | JSON | The whole reply. |

### Pavlov RCON Plus

#### Show a message

Shows a message on a player's screen, or everyone's. The only way to talk to players in Pavlov. Mind the mod's terms: be respectful, no soliciting.

- Needs the `rconplus.notify` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | Message | Text | One line. {{ }} from a Template node works well here. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Drop items

Makes a player drop everything they hold.

- Needs the `rconplus.drop-items` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Disable pickup

Stops a player picking up items, or lets them again.

- Needs the `rconplus.disable-pickup` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Godmode

Makes a player immune to all damage, or mortal again.

- Needs the `rconplus.godmode` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Disable items

Stops a player using items, or lets them again.

- Needs the `rconplus.disable-items` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Buy menu

Gives a player the buy menu, or takes it away.

- Needs the `rconplus.enable-buy-menu` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Noclip

Lets a player fly through walls, or puts them back on the ground.

- Needs the `rconplus.noclip` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Visibility

Makes a player visible to others, or invisible.

- Needs the `rconplus.visibility` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | On | Yes/no |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Movement speed

Sets how fast a player moves: 1 is normal, 2 twice as fast, 0.5 half.

- Needs the `rconplus.movement-speed` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | Multiplier | Number | 0.05 to 20. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Warp

Warps a player, a team or everyone to where another player is.

- Needs the `rconplus.warp` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | To player | Text | By id or name. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Ignite

Sets a player on fire.

- Needs the `rconplus.ignite` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Detonate

Blows a player up.

- Needs the `rconplus.detonate` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Endless supply

Gives a player an endless supply of an item, or clears every supply. Limited by the item lists on the account the graph runs as.

- Needs the `rconplus.supply` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | Item id | Text | An item id, or Clear to take every supply away. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Revive

Brings a dead player back.

- Needs the `rconplus.revive` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Set health

Sets a player's health, armour and helmet. -1 leaves one as it is.

- Needs the `rconplus.set-vitality` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | Used when Target is one player: a player id or exact name. |
| In | Health | Number | -1 to 1000. |
| In | Armour | Number |  |
| In | Helmet | Number |  |
| Out | Message | Text |  |
| Setting | Target |  | One of: One player (the Player input), Everyone, Blue team, Red team, A random player, TTT innocents, TTT traitors, TTT detectives, TTT jesters. |

#### Clean up

Removes idle bodies, items and vehicles from the map.

- Needs the `rconplus.cleanup` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |
| Setting | What |  | One of: Everything, Bodies, Items, Vehicles. |

#### Add bots

Adds bots, to a team or to whichever needs them.

- Needs the `rconplus.add-bots` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | How many | Number |  |
| Out | Message | Text |  |
| Setting | Team |  | One of: Either, Blue, Red. |

#### Remove bots

Removes bots: a number of them, or every one with 0.

- Needs the `rconplus.remove-bots` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | How many | Number | 0 removes them all. |
| Out | Message | Text |  |
| Setting | Team |  | One of: Either, Blue, Red. |

#### Game speed

Speeds the whole game up or slows it down: 1 is normal.

- Needs the `rconplus.game-speed` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Multiplier | Number | 0.05 to 20. 1 is normal. |
| Out | Message | Text |  |

#### Gravity

Sets gravity: 1 is normal, 0.2 is the moon.

- Needs the `rconplus.gravity` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Multiplier | Number | 0.05 to 20. 1 is normal. |
| Out | Message | Text |  |

#### Prone

Lets players go prone, or not.

- Needs the `rconplus.enable-prone` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Fall damage

Turns fall damage on or off.

- Needs the `rconplus.fall-damage` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Voting

Turns the map vote off or back on. On means voting is disabled.

- Needs the `rconplus.disable-voting` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Attachment mode

Turns attachment mode on or off.

- Needs the `rconplus.attachment-mode` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Utility trails

Shows trails behind thrown utility, or hides them.

- Needs the `rconplus.utility-trails` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Kill feedback

Turns kill feedback on or off.

- Needs the `rconplus.kill-feedback` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Team switching

Lets players switch team themselves, or not.

- Needs the `rconplus.team-switching` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | On | Yes/no |  |
| Out | Message | Text |  |

#### Team skin

Makes a team spawn with a skin, or back to its default with a blank skin. Limited by the skin lists on the account the graph runs as.

- Needs the `rconplus.set-team-skin` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Skin id | Text | Blank for the team's default. |
| Out | Message | Text |  |
| Setting | Team |  | One of: Blue, Red. |

#### Drop a loot crate

Drops a loot crate somewhere on the map.

- Needs the `rconplus.spawn-loot-crate` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Crate id | Text | Optional: a particular crate. |
| Out | Message | Text |  |

#### Spawn chickens

Spawns chickens at random spawn points.

- Needs the `rconplus.spawn-chickens` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | How many | Number |  |
| Out | Message | Text |  |

#### Spawn zombies

Spawns zombies at random spawn points.

- Needs the `rconplus.spawn-zombies` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | How many | Number |  |
| Out | Message | Text |  |

#### Remove zombies

Removes every zombie from the map.

- Needs the `rconplus.remove-zombies` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |

### Pavlov security

#### Ban address

Bans an address, or a range written like 203.0.113.0/24, on this server. Anybody joining from it is kicked, or banned, as the Security tab is set.

- Needs the `pavlov-security.ip-ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Address or range | Text | An IP address, or a CIDR range no wider than the panel allows. |
| In | Duration (s) | Number | 0 for until lifted. |
| In | Reason | Text |  |
| Out | Ban id | Number |  |
| Out | Message | Text |  |

#### Ban player's address

Bans the address a player last connected from, or the /24 range around it, without the graph ever seeing it. Only an address the panel is sure was theirs is used.

- Needs the `pavlov-security.ip-ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| In | Duration (s) | Number | 0 for until lifted. |
| In | Reason | Text |  |
| Out | Ban id | Number |  |
| Out | Address (hidden end) | Text |  |
| Out | Message | Text |  |
| Setting | Ban |  | One of: Their address, The /24 range around it. |

#### Lift address ban

Lifts this server's ban on an address or range, written as it was banned.

- Arrow outputs: "Then", "None".
- Needs the `pavlov-security.ip-ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Address or range | Text |  |
| Out | Message | Text |  |

#### Global ban

Bans a player, an address or a range on every Pavlov server the graph's editor can place global bans on. A player is put into each server's own ban list; an address or range acts when somebody joins from it.

- Needs the `pavlov-security.global-ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player, address or range | Text | A player id or name, an IP address, or a CIDR range. |
| In | Duration (s) | Number | 0 for until lifted. |
| In | Reason | Text |  |
| Out | Ban id | Number |  |
| Out | Servers | Number | How many servers it was placed on. |
| Out | Message | Text |  |
| Setting | Servers gained later too |  | Also reaches servers the editor gets the global ban permission on later. |

#### Lift global ban

Lifts one of the editor's own global bans everywhere it reached, found by its id or by what it banned.

- Arrow outputs: "Then", "None".
- Needs the `pavlov-security.global-ban` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Ban id, player, address or range | Text |  |
| Out | Message | Text |  |

#### Player addresses

The addresses a player has connected to this server from, newest first, with where each is and whether it is a VPN.

- No arrow pins: works its value out whenever a node reads it.
- Needs the `pavlov-security.ips` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Latest address | Text |  |
| Out | Addresses | List |  |
| Out | Countries | List | Every country code seen, once each. |
| Out | Used a VPN | Yes/no |  |
| Out | Count | Number |  |

#### Accounts sharing an address

Other accounts seen on this server from any address the player used: likely alts, family or a shared connection. No addresses are given out.

- No arrow pins: works its value out whenever a node reads it.
- Needs the `pavlov-security.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Accounts | List | Player ids. |
| Out | Names | List |  |
| Out | Banned among them | Number |  |
| Out | Count | Number |  |

#### Look up address

What is known about an address: VPN, hosting company, country and network. Answers are cached for a day across the panel.

- Needs the `pavlov-security.ips` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Address | Text |  |
| Out | VPN | Yes/no |  |
| Out | Hosting address | Yes/no |  |
| Out | Country | Text |  |
| Out | Network | Text |  |
| Out | ASN | Text |  |
| Out | Banned here | Yes/no |  |
| Out | Error | Text | Why nothing could be found out, else blank. |

#### Is banned

Whether a player or an address is banned on this server, and by what: the game's own ban, a global ban, or an address or range ban.

- No arrow pins: works its value out whenever a node reads it.
- Needs the `pavlov-security.read` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player or address | Text | A player id or name, or an IP address. |
| Out | Banned | Yes/no |  |
| Out | By | Text | game, global, ip or range; blank when not banned. |
| Out | Ban id | Number | 0 for the game's own ban. |
| Out | Reason | Text |  |

#### Exempt player

Puts a player on, or takes them off, the list the VPN, country and ban evasion checks leave alone. Bans still apply to them.

- Needs the `pavlov-security.settings` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| In | Player | Text | A player id (SteamID64 or Oculus name) or their exact name. Usually linked from an event. |
| Out | Message | Text |  |
| Setting | Do |  | One of: Exempt them, Stop exempting them. |

#### Set protection rules

Changes the VPN and ban evasion rules, for example to tighten them for an event and loosen them after. Anything left on "Leave as it is" is not touched.

- Needs the `pavlov-security.settings` permission.

| | Pin | Type | Notes |
|---|---|---|---|
| Out | Message | Text |  |
| Setting | VPN and hosting |  | One of: Leave as it is, Off, Flag, Kick. |
| Setting | Ban evasion |  | One of: Leave as it is, Off, Flag, Kick, Ban. |
| Setting | Banned address |  | One of: Leave as it is, Kick, Ban the account. |
