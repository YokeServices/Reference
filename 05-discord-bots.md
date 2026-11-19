# Discord bots

There are two ways for your graphs to talk to Discord.

| | Discord message node | Your own Discord bot |
|---|---|---|
| What it is | Posts into one channel through a webhook | A full bot account, run by your graphs |
| Can post messages | Yes, to its channel | Yes, anywhere the bot can see |
| Can react to Discord | No | Yes: messages, slash commands, buttons, reactions, members joining |
| Can manage people | No | Yes: roles, nicknames, timeouts, kicks, bans |
| Set up | Copy a webhook URL from the channel settings | Make a bot in the Discord Developer Portal |

For alerts ("the server crashed", "a backup failed"), the **Discord message**
node in the Web group is all you need; [Your first graph](01-first-graph.md#walkthrough-2-a-discord-alert-when-the-server-crashes)
shows how. Read on for a bot.

## Adding a bot

Each server can have one bot. The bot does not have to have anything to do
with your game: a server's graphs can run a community bot that never touches
the game at all.

### 1. Make the bot on Discord

1. Go to the [Discord Developer Portal](https://discord.com/developers/applications)
   and press **New Application**. Give it a name.
2. Open **Bot** in the menu on the left.
3. Under **Privileged Gateway Intents**, switch on **Message Content Intent**
   and **Server Members Intent**, and save. Without these the bot cannot read
   messages or see members joining and leaving.
4. Press **Reset Token** and copy the token. Treat it like a password:
   anyone with it controls your bot.

### 2. Give it to the panel

1. On your server's **Visual Scripting** tab, open **Graphs**. On the
   **Discord bot** card, press **Add a Discord bot**.
2. Paste the token and press **Add bot**. The panel checks it with Discord
   first, then keeps it encrypted. It is never shown again, not even to you.
3. The card now shows your bot's name and its status: **Starting**, then
   **Online**.

### 3. Invite it to your Discord server

Press **Invite** on the card. Discord asks which of your servers to add it
to, with the permissions the Discord nodes need (not administrator). Once it
has joined, the card stops saying it is in no Discord server.

The **Discord** and **Discord events** groups now appear in the node
browser. They stay hidden on servers without a bot.

### Changing or removing the bot

- **Replace token** swaps in a new token, for example after you reset it on
  Discord.
- **Remove** takes the bot off the server. Graphs with Discord nodes fail
  until a bot is added again.

Only people with the **Discord bot** permission can add, replace or remove
the bot, or put Discord nodes in graphs, because those graphs act as the bot.

### Bot status

| Status | Meaning |
|---|---|
| Online | Connected and listening. |
| Starting | Connecting. Give it a few seconds. |
| Offline | Not connected. |
| Error | Discord refused it: usually a reset token (use Replace token) or a missing intent (step 1.3). |

## Events: what the bot hears

Put these "On ..." nodes from **Discord events** at the start of a graph:

| Event | When |
|---|---|
| Message, Message edited, Message deleted | Someone posts, edits or deletes a message the bot can see |
| Slash command | Someone runs one of your bot's slash commands |
| Button pressed, Menu choice made | Someone uses a button or menu your bot sent |
| Reaction added, Reaction removed | Someone reacts to a message |
| Member joined, Member left, Member changed | Someone joins, leaves, or has their nickname or roles changed |
| Voice channel change | Someone joins, leaves or moves between voice channels |
| Bot added to a server, Bot removed from a server | Your bot is invited or removed |
| Bot online, Bot error | Your bot connects, or loses its connection |

Each carries the details: who, where, what. The
[Events reference](reference/events.md#discord) lists every field. Filters
narrow things down; for example, On Message with Content `!help*` only runs
for messages starting with `!help`, and **From a bot** set to No ignores
other bots (and your own).

## Nodes: what the bot can do

| Group | Nodes |
|---|---|
| Messages | Send message, Reply to command, Edit message, Delete message, Delete recent messages, Add reaction, Fetch messages, Send direct message, Pin message |
| People | Get member, Get user, Give role, Take role, Set nickname, Time out member, Kick member, Ban member, Unban user |
| Server | Get server (its channels and roles), Create, Edit and Delete channel, Create thread, Make invite link |
| The bot | Set bot status, Bot info |
| Building blocks | Build embed, Build button, Build select menu, Build row, Combine rows, Mention, Discord timestamp |

How to point at things:

- **Channels and roles**: by name (`general`, `#general`, `Moderator`) or by id.
- **People**: by user id. Events give you ids (for example **User id** or
  **Author id**); link those in.
- **Discord server** (on nodes that ask for one): leave it blank when the bot
  is in only one server.

If Discord refuses something (the bot's role sits below the role it is
trying to give, or it lacks a permission in that channel), the node fails
with Discord's reason. Wrap it in **Try** to handle that yourself.

## Slash commands

You define a slash command right on its **On Slash command** event node:

- **Command name**: what people type after the slash, in lower case letters,
  numbers and dashes, such as `status`.
- **Description**: shown next to the command in Discord.
- **Options**: what the command asks for, one per line, as
  `name:type:description`. Types: `string`, `integer`, `number`, `boolean`,
  `user`, `channel`, `role`, `mentionable`, `attachment`. Put `?` after the
  type to make it optional: `reason:string?:Why`.
- **Reply privately**: only the person who ran it sees the answer.

Save the graph and the command is registered with Discord. A brand new
command can take up to an hour to show up in Discord; changes to an
existing one are usually quicker.

What people typed comes out of the event's **Options** pin, as JSON keyed by
option name: use **Get from JSON** with the option's name to read one.

To answer, use **Reply to command** and link the event's **Interaction**
pin into it. Discord shows "thinking..." the moment the command is used, and
your first reply replaces it; more replies follow on. You have 15 minutes to
reply.

### Example: `/status`

1. **On Slash command**: name `status`, description `How is the server doing?`
2. **Server, Server status** after it.
3. **Data, Template**: `The server is {{a}}, using {{b}} MB of memory.` Link
   Server status's **State** into A and **Memory (MB)** into B.
4. **Discord, Reply to command**: link the event's **Interaction** into
   Interaction, and the Template's **Text** into Content.

## Buttons and menus

1. Make a button with **Build button**: a **Button id** (such as
   `restart-yes`), a **Label** and a **Style**. The Link style opens a URL
   instead and fires nothing.
2. Put up to five buttons in a **Build row**, and send the row through the
   **Components** input of Send message or Reply to command.
3. In a graph with **On Button pressed** (Button id filter `restart-yes`),
   do the work and answer with **Reply to command**, linking that event's
   Interaction.

Select menus work the same way with **Build select menu** and **On Menu
choice made**. Menu options are a list like `eu:Europe` and
`us:North America:Our US node`, as `value:Label` or
`value:Label:description`.

## Handy building blocks

- **Mention** turns an id into a ping: a user (`<@id>`), a channel (`<#id>`)
  or a role (`<@&id>`).
- **Discord timestamp** shows a time in everyone's own time zone, such as
  "in 5 minutes" with the Relative style.
- **Build embed** makes a rich message: title, text, colour (`#5865F2`),
  image, fields (a list of `Name: value` lines).
- **Set bot status** sets what the bot is "Playing" or "Watching", and
  whether it shows as online, idle or do not disturb.

## In Lua

Every Discord node is also a Lua function under `yoke.discord`, and every
Discord event can start a script with `---@on discord.<event>`. See
[Lua scripting](06-lua-scripting.md) and the
[Lua function reference](reference/lua-api.md#discord-yokediscord).
