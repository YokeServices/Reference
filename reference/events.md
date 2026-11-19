# Events reference

Every event a graph or a script can start from. In a graph, each one is an "On ..." node
in the **Events** group of the node browser; its fields are the node's outputs. In Lua,
put `---@on <name>` above a function and the fields arrive in the function's one argument
(see [Lua scripting](../06-lua-scripting.md#event-handlers)).

Filters narrow an event down, so the graph only runs when it matters: blank means "any",
and in text filters `*` matches any run of characters (`say *`, `/config/*.json`).

Fields called `actor` say who did it, and `by_graph` names the graph that did it
(blank when a person did). A graph never hears about its own actions; to ignore what
other graphs do as well, set the `by_graph` filter to `-` (people only).

## Contents

- [Start](#start)
- [Server](#server)
- [Files](#files)
- [Backups](#backups)
- [Schedules](#schedules)
- [Users](#users)
- [Network](#network)
- [Visual Scripting](#visual-scripting)
- [Discord](#discord)
- [Pavlov VR](#pavlov-vr)

## Start

### Run button

Starts when somebody presses Run on this graph, or calls it from the API.

In Lua: `---@on manual`

| Field | Type | What it is |
|---|---|---|
| `input` | JSON | Whatever was typed into the Run dialog. |
| `by` | Text | Who. |

### Schedule

Starts on a cron schedule: "0 6 * * *" is every day at 06:00, "*/15 * * * *" every 15 minutes. Panel time.

In Lua: `---@on schedule`

| Field | Type | What it is |
|---|---|---|
| `time` | Text | Time. |
| `unix` | Number | Unix. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `cron` (Cron): minute hour day month weekday

### Webhook received

Starts when something POSTs to this graph's webhook URL (made on the graph's page). The name lets one graph have several.

In Lua: `---@on webhook`

| Field | Type | What it is |
|---|---|---|
| `body` | JSON | The JSON or form body, or the raw text under "text". |
| `query` | JSON | The URL parameters. |
| `method` | Text | Method. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Name): Optional. Matched against the last part of the URL.

### Called by another graph

Starts when another graph on this server uses "Run another graph" on this one.

In Lua: `---@on called`

| Field | Type | What it is |
|---|---|---|
| `input` | Anything | Input. |
| `from` | Text | The calling graph's name. |

## Server

### Power action

Somebody asked for a start, stop, restart or kill, from the panel or the API. For the moment the server actually comes up or goes down, use "Server online" and "Server offline".

In Lua: `---@on server.power`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `start`, `stop`, `restart`, `kill`.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Server online

The server reached "running". Found by asking Wings every few seconds, so up to a short delay after the fact.

In Lua: `---@on server.online`

| Field | Type | What it is |
|---|---|---|
| `previous` | Text | Previous state. |

### Server offline

The server reached "offline", for any reason.

In Lua: `---@on server.offline`

| Field | Type | What it is |
|---|---|---|
| `expected` | Yes/no | True when a stop or kill was asked for shortly before. |
| `previous` | Text | Previous state. |

### Server crashed

The server went offline without anybody asking it to stop in the minutes before. The best the panel can tell from the outside.

In Lua: `---@on server.crashed`

| Field | Type | What it is |
|---|---|---|
| `previous` | Text | Previous state. |

### Server starting

The server entered "starting".

In Lua: `---@on server.starting`

Carries no fields.

### Server stopping

The server entered "stopping".

In Lua: `---@on server.stopping`

Carries no fields.

### State changed

Any change of state: running, offline, starting, stopping.

In Lua: `---@on server.state`

| Field | Type | What it is |
|---|---|---|
| `state` | Text | State. |
| `previous` | Text | Previous state. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `state` (New state): blank for any, `running`, `offline`, `starting`, `stopping`.

### Console command

A command was typed into the console, from the panel or the API.

In Lua: `---@on server.command`

| Field | Type | What it is |
|---|---|---|
| `command` | Text | Command. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `command` (Command): Blank matches any command. * matches anything: "say *".
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Reinstall requested

Somebody asked for the server to be reinstalled.

In Lua: `---@on server.reinstall`

| Field | Type | What it is |
|---|---|---|
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Install finished

The install or reinstall script finished.

In Lua: `---@on server.installed`

| Field | Type | What it is |
|---|---|---|
| `successful` | Yes/no | Successful. |

### Server suspended

An admin, or the store, suspended the server.

In Lua: `---@on server.suspended`

Carries no fields.

### Server unsuspended

The server was unsuspended.

In Lua: `---@on server.unsuspended`

Carries no fields.

### Settings changed

The name, description, Docker image, egg or a startup variable changed.

In Lua: `---@on server.settings`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | What. |
| `variable` | Text | Variable. |
| `old` | Text | Old. |
| `new` | Text | New. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (What): blank for any, `rename`, `description`, `startup`, `image`, `egg`.
- `variable` (Variable): For startup changes: the environment name, blank for any.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Any activity

Every line the Activity tab gets, with its raw key (server:file.write, server:power.start, server:minecraft.mod.install and so on) and properties. The catch all for anything above and anything other addons log.

In Lua: `---@on activity.any`

| Field | Type | What it is |
|---|---|---|
| `event` | Text | Event key. |
| `properties` | JSON | Properties. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `event` (Event key): Blank for every line. * matches anything: "server:file.*".
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Files

### File changed

A file or folder was written, uploaded, deleted, renamed, copied, compressed, decompressed, pulled or made, from the panel, the API or SFTP.

In Lua: `---@on file.changed`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `path` | Text | The first path involved. |
| `paths` | List | Every path involved. |
| `directory` | Text | Folder. |
| `url` | Text | For downloads from a URL. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `write`, `upload`, `delete`, `rename`, `copy`, `compress`, `decompress`, `create-directory`, `pull`, `chmod`, `sftp-write`, `sftp-create`, `sftp-create-directory`, `sftp-rename`, `sftp-delete`.
- `paths` (Path): Blank for any path. * matches anything: "/config/*.json", "*.properties".
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### File opened

A file was opened in the editor or downloaded.

In Lua: `---@on file.read`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `path` | Text | Path. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `read`, `download`.
- `paths` (Path): Blank matches anything. * matches any run of characters.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Backups

### Backup started

In Lua: `---@on backup.started`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Backup completed

In Lua: `---@on backup.completed`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Backup failed

In Lua: `---@on backup.failed`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Backup deleted

In Lua: `---@on backup.deleted`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Restore started

In Lua: `---@on backup.restore_started`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Restore completed

In Lua: `---@on backup.restored`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Restore failed

In Lua: `---@on backup.restore_failed`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Schedules

### Schedule run by hand

Somebody pressed "Run now" on a schedule. Automatic runs are not logged by the panel.

In Lua: `---@on schedule.ran`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Schedule): Blank matches anything. * matches any run of characters.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Schedule changed

A schedule or one of its tasks was made, changed or deleted.

In Lua: `---@on schedule.changed`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `schedule_create`, `schedule_update`, `schedule_delete`, `task_create`, `task_update`, `task_delete`.
- `name` (Schedule): Blank matches anything. * matches any run of characters.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Users

### Subuser added

In Lua: `---@on subuser.added`

| Field | Type | What it is |
|---|---|---|
| `email` | Text | Email. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Subuser changed

In Lua: `---@on subuser.updated`

| Field | Type | What it is |
|---|---|---|
| `email` | Text | Email. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Subuser removed

In Lua: `---@on subuser.removed`

| Field | Type | What it is |
|---|---|---|
| `email` | Text | Email. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Network

### Allocation changed

A port was added, removed, made primary or had its note changed.

In Lua: `---@on allocation.changed`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `allocation` | Text | ip:port. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `create`, `delete`, `primary`, `notes`.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### Database changed

In Lua: `---@on database.changed`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `name` | Text | Name. |
| `actor` | Text | The username, or "system". |
| `by_graph` | Text | The graph that did it, blank for a person. A graph never hears about its own actions. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `create`, `delete`, `rotate_password`.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

## Visual Scripting

### A graph failed

Another graph on this server ended in failure. Never fires for the graph that listens, so an alert graph cannot loop on itself.

In Lua: `---@on nodes.run_failed`

| Field | Type | What it is |
|---|---|---|
| `graph` | Text | Graph. |
| `error` | Text | Error. |
| `run_id` | Number | Run id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `graph` (Graph name): Blank matches anything. * matches any run of characters.

## Discord

> Needs a Discord bot on the server. See [Discord bots](../05-discord-bots.md).

### Message

Somebody posted a message the bot can see. Needs the Message Content intent on the Bot page of the Developer Portal to see what it says.

In Lua: `---@on discord.message`

| Field | Type | What it is |
|---|---|---|
| `message_id` | Text | Message id. |
| `content` | Text | Content. |
| `author` | Text | Author. |
| `author_id` | Text | Author id. |
| `author_bot` | Yes/no | From a bot. |
| `channel_type` | Text | text, dm, thread, voice, news. |
| `mentions` | List | Mentioned user ids. |
| `mentions_bot` | Yes/no | Mentions the bot. |
| `attachments` | List | name, url, size for each. |
| `reply_to` | Text | The message id replied to, or blank. |
| `url` | Text | Link. |
| `at` | Text | When. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `content` (Content): Glob: "!help*" matches messages starting with !help. Blank for any.
- `author` (Author): Blank matches anything. * matches any run of characters.
- `author_bot` (From a bot): blank for any, `true`, `false`.
- `mentions_bot` (Mentions the bot): blank for any, `true`, `false`.
- `channel_type` (Channel type): blank for any, `text`, `dm`, `thread`, `voice`, `news`.
- `channel` (Channel): A channel name or id. Blank for any.

### Message edited

A message was edited.

In Lua: `---@on discord.message_edited`

| Field | Type | What it is |
|---|---|---|
| `old_content` | Text | Content before. |
| `message_id` | Text | Message id. |
| `content` | Text | Content. |
| `author` | Text | Author. |
| `author_id` | Text | Author id. |
| `author_bot` | Yes/no | From a bot. |
| `channel_type` | Text | text, dm, thread, voice, news. |
| `mentions` | List | Mentioned user ids. |
| `mentions_bot` | Yes/no | Mentions the bot. |
| `attachments` | List | name, url, size for each. |
| `reply_to` | Text | The message id replied to, or blank. |
| `url` | Text | Link. |
| `at` | Text | When. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `content` (Content): Glob: "!help*" matches messages starting with !help. Blank for any.
- `author` (Author): Blank matches anything. * matches any run of characters.
- `author_bot` (From a bot): blank for any, `true`, `false`.
- `mentions_bot` (Mentions the bot): blank for any, `true`, `false`.
- `channel_type` (Channel type): blank for any, `text`, `dm`, `thread`, `voice`, `news`.
- `channel` (Channel): A channel name or id. Blank for any.

### Message deleted

A message was deleted. The content is only known when the bot saw the message before.

In Lua: `---@on discord.message_deleted`

| Field | Type | What it is |
|---|---|---|
| `message_id` | Text | Message id. |
| `author` | Text | Author. |
| `author_id` | Text | Author id. |
| `content` | Text | Content. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `channel` (Channel): A channel name or id. Blank for any.

### Slash command

Somebody ran a slash command of this bot. The command is defined right here: its name, description and options are registered with Discord when the graph is saved (allow up to an hour for Discord to show a new one). Reply with "Reply to command".

In Lua: `---@on discord.command`

| Field | Type | What it is |
|---|---|---|
| `interaction_id` | Text | Link this into "Reply to command". Valid for 15 minutes. |
| `command` | Text | Command. |
| `options` | JSON | The option values by name; users, channels and roles as ids. |
| `roles` | List | The names of the roles the user holds. |
| `at` | Text | When. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Command name): Lower case letters, numbers, dashes. The part after the slash.
- `description` (Description): Shown next to the command in Discord.
- `options` (Options): One per line: name:type:description. Types: string, integer, number, boolean, user, channel, role, mentionable, attachment. Put ? after the type for an optional one, e.g. reason:string?:Why
- `ephemeral` (Reply privately): Only the person who ran the command sees the reply.
- `user` (User): Blank matches anything. * matches any run of characters.
- `channel` (Channel): A channel name or id. Blank for any.

### Button pressed

Somebody pressed a button the bot sent (made with "Build button"). Reply with "Reply to command" or edit the message.

In Lua: `---@on discord.button`

| Field | Type | What it is |
|---|---|---|
| `interaction_id` | Text | Interaction. |
| `custom_id` | Text | The custom id the button was built with. |
| `label` | Text | Label. |
| `message_id` | Text | Message id. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `custom_id` (Button id): Glob. Blank for any button.
- `user` (User): Blank matches anything. * matches any run of characters.
- `channel` (Channel): A channel name or id. Blank for any.

### Menu choice made

Somebody chose from a select menu the bot sent (made with "Build select menu").

In Lua: `---@on discord.select`

| Field | Type | What it is |
|---|---|---|
| `interaction_id` | Text | Interaction. |
| `custom_id` | Text | Menu id. |
| `values` | List | Chosen values. |
| `value` | Text | First value. |
| `message_id` | Text | Message id. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `custom_id` (Menu id): Blank matches anything. * matches any run of characters.
- `user` (User): Blank matches anything. * matches any run of characters.
- `channel` (Channel): A channel name or id. Blank for any.

### Reaction added

Somebody reacted to a message.

In Lua: `---@on discord.reaction_added`

| Field | Type | What it is |
|---|---|---|
| `emoji` | Text | The character, or the name of a custom emoji. |
| `emoji_id` | Text | For a custom emoji. |
| `message_id` | Text | Message id. |
| `message_author_id` | Text | Message author id. |
| `count` | Number | How many of that reaction the message has now. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `emoji` (Emoji): Blank matches anything. * matches any run of characters.
- `user` (User): Blank matches anything. * matches any run of characters.
- `channel` (Channel): A channel name or id. Blank for any.

### Reaction removed

Somebody took their reaction off a message.

In Lua: `---@on discord.reaction_removed`

| Field | Type | What it is |
|---|---|---|
| `emoji` | Text | Emoji. |
| `emoji_id` | Text | Emoji id. |
| `message_id` | Text | Message id. |
| `count` | Number | Count. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `guild` | Text | The Discord server (guild). |
| `guild_id` | Text | Server id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `emoji` (Emoji): Blank matches anything. * matches any run of characters.
- `user` (User): Blank matches anything. * matches any run of characters.
- `channel` (Channel): A channel name or id. Blank for any.

### Member joined

Somebody joined a Discord server the bot is in. Needs the Server Members intent on the Bot page.

In Lua: `---@on discord.member_joined`

| Field | Type | What it is |
|---|---|---|
| `tag` | Text | Username with discriminator where there is one. |
| `avatar` | Text | Avatar URL. |
| `guild` | Text | Server. |
| `guild_id` | Text | Server id. |
| `member_count` | Number | Members now. |
| `account_created` | Text | Account created. |
| `bot` | Yes/no | Is a bot. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `user` (User): Blank matches anything. * matches any run of characters.

### Member left

Somebody left, was kicked or was banned. Needs the Server Members intent.

In Lua: `---@on discord.member_left`

| Field | Type | What it is |
|---|---|---|
| `guild` | Text | Server. |
| `guild_id` | Text | Server id. |
| `member_count` | Number | Members now. |
| `roles` | List | Roles they had. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `user` (User): Blank matches anything. * matches any run of characters.

### Member changed

A member's nickname or roles changed. Needs the Server Members intent.

In Lua: `---@on discord.member_updated`

| Field | Type | What it is |
|---|---|---|
| `guild_id` | Text | Server id. |
| `nickname` | Text | Nickname. |
| `old_nickname` | Text | Nickname before. |
| `roles` | List | Roles. |
| `roles_added` | List | Roles added. |
| `roles_removed` | List | Roles removed. |
| `timed_out` | Yes/no | Timed out. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `roles_added` (Role added): A role name; matches when that role was added.
- `roles_removed` (Role removed): Blank matches anything. * matches any run of characters.
- `user` (User): Blank matches anything. * matches any run of characters.

### Voice channel change

Somebody joined, left or moved between voice channels.

In Lua: `---@on discord.voice`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | joined, left or moved. |
| `channel` | Text | Channel. |
| `channel_id` | Text | Channel id. |
| `old_channel` | Text | Channel before. |
| `old_channel_id` | Text | Channel id before. |
| `guild_id` | Text | Server id. |
| `muted` | Yes/no | Muted. |
| `deafened` | Yes/no | Deafened. |
| `user` | Text | The username. |
| `user_id` | Text | User id. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `joined`, `left`, `moved`.
- `channel` (Channel): Blank matches anything. * matches any run of characters.
- `user` (User): Blank matches anything. * matches any run of characters.

### Bot added to a server

The bot was invited into a Discord server.

In Lua: `---@on discord.guild_joined`

| Field | Type | What it is |
|---|---|---|
| `guild` | Text | Server. |
| `guild_id` | Text | Server id. |
| `member_count` | Number | Members. |

### Bot removed from a server

The bot was kicked from, or the bot left, a Discord server.

In Lua: `---@on discord.guild_left`

| Field | Type | What it is |
|---|---|---|
| `guild` | Text | Server. |
| `guild_id` | Text | Server id. |

### Bot online

The bot connected to Discord: after the bridge starts, the token is set, or a reconnect.

In Lua: `---@on discord.ready`

| Field | Type | What it is |
|---|---|---|
| `bot` | Text | Bot name. |
| `bot_id` | Text | Bot id. |
| `guilds` | Number | How many Discord servers it is in. |

### Bot error

The bot lost its connection or Discord refused it (a bad token, a missing intent).

In Lua: `---@on discord.error`

| Field | Type | What it is |
|---|---|---|
| `error` | Text | Error. |

## Pavlov VR

> Only on Pavlov VR servers, with the Server Manager switched on.

### Player joined

Somebody connected. Seen by the poll, so up to a minute after the fact.

In Lua: `---@on pavlov.player_joined`

| Field | Type | What it is |
|---|---|---|
| `team` | Number | 0 blue, 1 red, blank without teams. |
| `team_name` | Text | Team name. |
| `first_time` | Yes/no | True for somebody this server has never seen before. |
| `playtime` | Number | Seconds played on this server before this visit. |
| `max_players` | Number | Max players. |
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |
| `players` | Number | Players online. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `first_time` (First visit): blank for any, `true`, `false`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Player left

Somebody disconnected. The poll closes a session a couple of minutes after the last time it saw them, so this arrives a little late.

In Lua: `---@on pavlov.player_left`

| Field | Type | What it is |
|---|---|---|
| `seconds` | Number | Session length (s) |
| `playtime` | Number | Playtime total (s) |
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |
| `players` | Number | Players online. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### First player on

The server went from empty to somebody on it. A good moment to warm things up.

In Lua: `---@on pavlov.first_player`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |
| `players` | Number | Players online. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Server empty

The last player left.

In Lua: `---@on pavlov.server_empty`

| Field | Type | What it is |
|---|---|---|
| `last_player` | Text | Last player. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |

### Server full

The player count reached the server's maximum.

In Lua: `---@on pavlov.server_full`

| Field | Type | What it is |
|---|---|---|
| `players` | Number | Players online. |
| `max_players` | Number | Max players. |
| `map` | Text | Map. |

### Player count changed

The number of players on the server went up or down, as the poll counts them.

In Lua: `---@on pavlov.players_changed`

| Field | Type | What it is |
|---|---|---|
| `players` | Number | Players online. |
| `previous` | Number | Previous count. |
| `max_players` | Number | Max players. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |

### Map changed

The server moved to another map or mode, as the poll saw it.

In Lua: `---@on pavlov.map_changed`

| Field | Type | What it is |
|---|---|---|
| `map` | Text | The map id, a stock name or UGC number. |
| `map_name` | Text | Map name. |
| `mode` | Text | SND, TDM, TTT and the rest. |
| `mode_name` | Text | Mode name. |
| `previous_map` | Text | Previous map. |
| `previous_mode` | Text | Previous mode. |
| `players` | Number | Players online. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `map` (Map): Blank matches anything. * matches any run of characters.
- `mode` (Mode): Blank matches anything. * matches any run of characters.

### Round state changed

The round moved between the game's states (Started, Ended, StandBy and the like), as the poll saw it. Once a minute, so short rounds can be missed; the log driven "Round ended" is exact.

In Lua: `---@on pavlov.round_state`

| Field | Type | What it is |
|---|---|---|
| `state` | Text | State. |
| `previous` | Text | Previous state. |
| `round` | Number | Round. |
| `team0_score` | Number | Blue score. |
| `team1_score` | Number | Red score. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `state` (State): Blank matches anything. * matches any run of characters.

### Score changed

A team's score moved, as the poll saw it.

In Lua: `---@on pavlov.score_changed`

| Field | Type | What it is |
|---|---|---|
| `team0_score` | Number | Blue score. |
| `team1_score` | Number | Red score. |
| `previous_team0` | Number | Blue before. |
| `previous_team1` | Number | Red before. |
| `leader` | Number | 0 blue, 1 red, -1 for a tie. |
| `round` | Number | Round. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |

### Player changed team

A player is on the other team from the last poll, whether they switched or were moved.

In Lua: `---@on pavlov.team_changed`

| Field | Type | What it is |
|---|---|---|
| `team` | Number | Team. |
| `team_name` | Text | Team name. |
| `previous` | Number | Previous team. |
| `previous_name` | Text | Previous team name. |
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `team` (Team): blank for any, `0`, `1`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Kill

One player killed another (or themselves), as the game wrote it in its log. Needs verbose logging on the server; read every couple of minutes, with the exact time of the kill.

In Lua: `---@on pavlov.kill`

| Field | Type | What it is |
|---|---|---|
| `killer_uid` | Text | Killer id. |
| `killer` | Text | Killer. |
| `killed_uid` | Text | Victim id. |
| `killed` | Text | Victim. |
| `weapon` | Text | Weapon. |
| `headshot` | Yes/no | Headshot. |
| `killer_team` | Number | Killer team. |
| `killed_team` | Number | Victim team. |
| `kind` | Text | kill, team_kill or suicide. |
| `at` | Text | When. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `weapon` (Weapon): Blank matches anything. * matches any run of characters.
- `killer` (Killer name): Blank matches anything. * matches any run of characters.
- `killed` (Victim name): Blank matches anything. * matches any run of characters.
- `kind` (Kind): blank for any, `kill`, `team_kill`, `suicide`.
- `headshot` (Headshot): blank for any, `true`, `false`.

### Bomb planted or defused

A bomb interaction in Search and Destroy, from the log. Needs verbose logging.

In Lua: `---@on pavlov.bomb`

| Field | Type | What it is |
|---|---|---|
| `interaction` | Text | As the game words it, for example BombPlanted or BombDefused. |
| `at` | Text | When. |
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `interaction` (Interaction): Blank matches anything. * matches any run of characters.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Round ended

A round finished, from the log, with the winner. Needs verbose logging.

In Lua: `---@on pavlov.round_ended`

| Field | Type | What it is |
|---|---|---|
| `round` | Number | Round. |
| `winning_team` | Number | Winning team. |
| `winning_team_name` | Text | Winning team name. |
| `at` | Text | When. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `winning_team` (Winning team): blank for any, `0`, `1`.

### Map ended

A map finished, from the log, with the final scoreboard. Needs verbose logging.

In Lua: `---@on pavlov.map_ended`

| Field | Type | What it is |
|---|---|---|
| `map` | Text | Map. |
| `map_name` | Text | Map name. |
| `mode` | Text | Mode. |
| `players` | Number | Players. |
| `team0_score` | Number | Blue score. |
| `team1_score` | Number | Red score. |
| `winning_team` | Number | 0 blue, 1 red, -1 for a tie or no teams. |
| `top_uid` | Text | Top player id. |
| `top_player` | Text | Top player. |
| `scoreboard` | List | One entry per player: uid, name, team, stats. |
| `at` | Text | When. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `map` (Map): Blank matches anything. * matches any run of characters.
- `mode` (Mode): Blank matches anything. * matches any run of characters.

### Player banned

A player appeared on the ban list, whoever put them there.

In Lua: `---@on pavlov.player_banned`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Player unbanned

A player came off the ban list.

In Lua: `---@on pavlov.player_unbanned`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Player gagged

A player was muted in game.

In Lua: `---@on pavlov.player_gagged`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Player ungagged

A player can talk again.

In Lua: `---@on pavlov.player_ungagged`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Moderator added

A player appeared on the moderator list.

In Lua: `---@on pavlov.moderator_added`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Moderator removed

A player came off the moderator list.

In Lua: `---@on pavlov.moderator_removed`

| Field | Type | What it is |
|---|---|---|
| `uid` | Text | SteamID64 or Oculus name, the id every player node takes. |
| `name` | Text | Player. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Admin action

Something was done to a player or the server: from the Server Manager tab, from RCON Plus's in game menu, by the poller lifting a timed punishment, or by a graph. A graph never hears about its own actions.

In Lua: `---@on pavlov.action`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | Action. |
| `uid` | Text | Player id. |
| `name` | Text | Player. |
| `by` | Text | The panel username or the in game admin's name; blank for the poller. |
| `source` | Text | panel or game. |
| `reason` | Text | Reason. |
| `duration` | Number | For a timed ban or gag, else blank. |
| `details` | JSON | Details. |
| `by_graph` | Text | The graph that did it, blank for a person. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `action` (Action): blank for any, `add_access_manager`, `add_blacklisted_command`, `add_bots`, `add_webhook`, `ban`, `cleanup`, `clear_access_managers`, `clear_blacklisted_commands`, `clear_menu_access`, `clear_vehicles`, `clear_webhooks`, `detonate`, `disable_items`, `disable_pickup`, `drop_items`, `enable_buy_menu`, `gag`, `game_speed`, `give_cash`, `give_item`, `give_menu`, `give_vehicle`, `godmode`, `gravity`, `ignite`, `kick`, `kill`, `mod_add`, `mod_remove`, `movement_speed`, `noclip`, `notify`, `remove_access_manager`, `remove_blacklisted_command`, `remove_bots`, `remove_menu`, `remove_pin`, `remove_webhook`, `remove_zombies`, `reset_snd`, `revive`, `rotate_map`, `set_cash`, `set_pin`, `set_skin`, `set_team_skin`, `set_vitality`, `slap`, `spawn_chickens`, `spawn_loot_crate`, `spawn_zombies`, `supply`, `switch_map`, `switch_team`, `teleport`, `ttt_credits`, `ttt_end_round`, `unban`, `ungag`, `visibility`, `warp`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `by` (By): Blank matches anything. * matches any run of characters.
- `by_graph` (By graph): Blank for anyone. "-" for people only, a graph name for that graph, * for any graph.

### RCON reachable

The poll could talk to the game again: it is up and answering.

In Lua: `---@on pavlov.rcon_online`

| Field | Type | What it is |
|---|---|---|
| `server_name` | Text | Server name. |
| `map` | Text | Map. |
| `mode` | Text | Mode. |
| `players` | Number | Players online. |

### RCON unreachable

The poll could not talk to the game. Usually the server is down; the error says what happened.

In Lua: `---@on pavlov.rcon_offline`

| Field | Type | What it is |
|---|---|---|
| `error` | Text | Error. |
| `online_for` | Number | Was up for (s) |

### Server name changed

The game reports a different server name than last poll.

In Lua: `---@on pavlov.name_changed`

| Field | Type | What it is |
|---|---|---|
| `name` | Text | Name. |
| `previous` | Text | Previous name. |

### Player connected (with address)

Somebody joined and the panel has checked them. Carries the full address they connected from, so using it needs the permission to see addresses.

In Lua: `---@on pavlov.security_connection`

| Field | Type | What it is |
|---|---|---|
| `address` | Text | The full address. |
| `verdict` | Text | allowed, exempt, flagged, unsure, kicked or banned. |
| `uid` | Text | The id every player node takes. |
| `name` | Text | Player. |
| `platform` | Text | What the game client said it runs on. |
| `masked_address` | Text | Like 203.0.x.x. |
| `country` | Text | Two letter code, when the lookup knows it. |
| `vpn` | Yes/no | VPN. |
| `hosting` | Yes/no | An address that belongs to a hosting company, usually a VPN. |
| `isp` | Text | Who the address belongs to, when the lookup knows. |
| `match` | Text | How sure the panel is the address was theirs: definite, certain or likely. |
| `rule` | Text | ip_ban, global_ban, evasion, vpn or country; blank when nothing matched. |
| `reason` | Text | Reason. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `verdict` (Verdict): blank for any, `allowed`, `exempt`, `flagged`, `unsure`, `kicked`, `banned`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Join blocked

A rule on the Security tab kicked or banned somebody who joined.

In Lua: `---@on pavlov.security_blocked`

| Field | Type | What it is |
|---|---|---|
| `action` | Text | kick or ban. |
| `uid` | Text | The id every player node takes. |
| `name` | Text | Player. |
| `platform` | Text | What the game client said it runs on. |
| `masked_address` | Text | Like 203.0.x.x. |
| `country` | Text | Two letter code, when the lookup knows it. |
| `vpn` | Yes/no | VPN. |
| `hosting` | Yes/no | An address that belongs to a hosting company, usually a VPN. |
| `isp` | Text | Who the address belongs to, when the lookup knows. |
| `match` | Text | How sure the panel is the address was theirs: definite, certain or likely. |
| `rule` | Text | ip_ban, global_ban, evasion, vpn or country; blank when nothing matched. |
| `reason` | Text | Reason. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `rule` (Rule): blank for any, `ip_ban`, `global_ban`, `evasion`, `vpn`, `country`.
- `action` (Action): blank for any, `kick`, `ban`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Join flagged

Somebody joined that a rule set to flag noticed, or the panel could not be sure whose address was whose. Nothing was done to them.

In Lua: `---@on pavlov.security_flagged`

| Field | Type | What it is |
|---|---|---|
| `unsure` | Yes/no | True when several players joined together and the address could have been any of theirs. |
| `uid` | Text | The id every player node takes. |
| `name` | Text | Player. |
| `platform` | Text | What the game client said it runs on. |
| `masked_address` | Text | Like 203.0.x.x. |
| `country` | Text | Two letter code, when the lookup knows it. |
| `vpn` | Yes/no | VPN. |
| `hosting` | Yes/no | An address that belongs to a hosting company, usually a VPN. |
| `isp` | Text | Who the address belongs to, when the lookup knows. |
| `match` | Text | How sure the panel is the address was theirs: definite, certain or likely. |
| `rule` | Text | ip_ban, global_ban, evasion, vpn or country; blank when nothing matched. |
| `reason` | Text | Reason. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `rule` (Rule): blank for any, `ip_ban`, `global_ban`, `evasion`, `vpn`, `country`.
- `name` (Player name): Blank matches anything. * matches any run of characters.
- `uid` (Player id): Blank matches anything. * matches any run of characters.

### Security ban placed

An address, range or global ban was placed that reaches this server, from the tab or a graph.

In Lua: `---@on pavlov.security_ban_added`

| Field | Type | What it is |
|---|---|---|
| `ban_id` | Number | Ban id. |
| `scope` | Text | server or global. |
| `kind` | Text | player, ip or range. |
| `target` | Text | The player id, or the address with its end hidden. |
| `name` | Text | Player. |
| `reason` | Text | Reason. |
| `by` | Text | By. |
| `duration` | Number | 0 for until lifted. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `scope` (Scope): blank for any, `server`, `global`.
- `kind` (Kind): blank for any, `player`, `ip`, `range`.

### Security ban lifted

An address, range or global ban that reached this server was lifted.

In Lua: `---@on pavlov.security_ban_lifted`

| Field | Type | What it is |
|---|---|---|
| `ban_id` | Number | Ban id. |
| `scope` | Text | server or global. |
| `kind` | Text | player, ip or range. |
| `target` | Text | The player id, or the address with its end hidden. |
| `name` | Text | Player. |
| `reason` | Text | Reason. |
| `by` | Text | By. |
| `duration` | Number | 0 for until lifted. |

Filters (set on the event node, or as `key=value` after `---@on`):

- `scope` (Scope): blank for any, `server`, `global`.
- `kind` (Kind): blank for any, `player`, `ip`, `range`.
