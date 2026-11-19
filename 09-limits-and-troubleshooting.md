# Limits and troubleshooting

## Limits

Visual Scripting runs on shared machines, so every server gets the same fair
allowance. These are the current limits.

### Graphs and runs

| Limit | Value |
|---|---|
| Graphs per server | 25 |
| Nodes per graph | 200 |
| Steps per run (each node a run goes through) | 1,000 |
| Working time per run (waits not counted) | 60 seconds |
| Waiting per run (Wait nodes and `yoke.wait` together) | 300 seconds |
| Runs per graph per minute (more are dropped, and counted on the card) | 30 |
| Runs started by events, per server per hour | 3,000 |
| Lines kept in one run's log | 500 |
| Graphs starting graphs with Run another graph, in a chain | 3 |
| Graph functions calling each other, in one run | 8 deep |
| Runs kept on the Runs tab | 14 days, and the last 200 per graph |
| Kept values (variables) per server | 200 |
| Size of one value | 1 MB |
| Items in one list | 5,000 |

### Files, web and databases

| Limit | Value |
|---|---|
| Largest file a graph reads or writes | 1 MB |
| Web requests | `https` only, public addresses only, 10 second timeout, 256 KB answer, redirects not followed |
| Webhook calls | 30 a minute per graph, bodies up to 64 KB |
| Database query | 1,000 rows back, 20 seconds per statement |
| Discord: messages fetched or deleted at once | 100 |

### Fair use, per server per hour

| Kind of action | Allowance per hour |
|---|---|
| Web requests | 3,000 |
| Discord actions | 6,000 |
| Console commands | 6,000 |
| File changes | 6,000 |
| Database queries | 10,000 |

Past the allowance, those nodes fail with a message (which a **Try** node
can catch) until the hour turns over. Normal use never comes close.

### Lua scripts

See [Lua language reference, Limits](07-lua-reference.md#limits): 25
scripts of up to 64 KB, and per call 100 ms of processor time, 16 MB of
memory and 100 `yoke` calls.

## What is not allowed

Graphs and scripts are checked when they are saved and when they run.
These are refused outright. If one turns up while a graph runs, the graph is
paused, and our team may lock Visual Scripting on the server:

- cryptocurrency mining of any kind;
- remote shells, or downloading something and running it as a program;
- attack tools, or using graphs to attack or probe other systems.

These are flagged for our team to look at:

- IP logger links, and other ways of collecting people's addresses;
- Discord bot tokens typed into nodes (add your bot on the Discord bot card
  instead; the panel keeps it encrypted there);
- downloads of programs, and links to paste and tunnel services.

If Visual Scripting is locked on your server, the tab says so and why:
saves and runs are refused until it is lifted. Contact support if you think
it was a mistake.

## Troubleshooting

### My graph does not run

1. **Is it on?** The card and the editor show On or Off.
2. **Has it got an event?** A card with "No event yet" never starts.
3. **Are the filters too narrow?** Clear them and try again; then add them
   back one at a time. Remember `*` for "anything here".
4. **Look at the Runs tab.** A run marked **Skipped** says why it did not
   start.
5. **Server online, offline and crashed** events can be up to 15 seconds
   late, and the first check after you switch a graph on only notes the
   current state.
6. **The card says the panel switched it off?** Usually the person who last
   saved it lost a permission. Open it, save it as someone who has them
   (the owner always does), and switch it on.

### My graph ran but failed

Open the run on the **Runs** tab. The error says what went wrong, and
**Show the failing node** opens the editor on it. Common causes:

| Error mentions | Try |
|---|---|
| A file not found | Paths start from your server's main folder: `/config/server.json`, not `config/server.json` or a full path from your computer. |
| Wings, or the server not answering | The game server's machine is busy or restarting. Wrap the node in **Try**, or run again later. |
| A web request refused | Use `https://`. Addresses on private networks (`192.168...`, `10...`, `localhost`) cannot be reached. |
| A Discord refusal | The bot's role must sit above roles it gives or takes, and it needs permission in that channel. |
| The step or time limit | A loop goes through too many items, or the graph does too much in one run. Split the work, or narrow the list first. |
| "The server is being moved to another node" | Our team is moving your server. Power actions wait until it is done; the server comes back by itself. |

### Dropped runs on the card

Events arrived faster than 30 a minute for that graph, so some were dropped.
Usually a filter is missing (such as reacting to every file change instead
of one folder), or two graphs are setting each other off (see the next
question).

### Two graphs keep setting each other off

A graph never hears about its own actions, but two graphs can start each
other in turn: one writes a file and the other reacts to file changes and
sends a command the first one listens for. Set the event's **By graph**
filter to `-` (people only) on at least one of them.

### The Discord nodes are missing

They only appear once your server has a bot: add one on the **Discord bot**
card on the Graphs tab. See [Discord bots](05-discord-bots.md).

### My bot says Error or Offline

Most often the token was reset on Discord (use **Replace token**) or an
intent is off (switch on **Message Content** and **Server Members** on the
Bot page of the Developer Portal).

### My slash command does not show up

- Save the graph (or script), and check it is on.
- The name must be lower case letters, numbers and dashes.
- A brand new command can take up to an hour to appear in Discord.
- Make sure the bot was invited with its **Invite** button, which asks for
  the right to add commands.

### A variable does not seem to change

Check **Kept for**: a value kept for **This graph** is separate from one
with the same name kept for the **Whole server**. The Variables tab shows
which is which.

### My script function is not in the node browser

- The script must be **On** and saved.
- `---@export` must be right above `function name(...)`, with no blank line,
  at the top level of the script, and not `local`.
- The Problems panel lists anything the panel did not accept.

### Lua says "is not a function on this server"

The name is misspelled, or it belongs to something your server does not
have (Discord functions need a bot, Pavlov functions a Pavlov server). The
editor's completions only offer what exists.

### Still stuck?

Open a support ticket with the graph's or script's name, the time of a run
that went wrong, and the error from its run log.
