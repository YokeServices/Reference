# Visual Scripting

Visual Scripting lets your server look after itself. You decide what should
happen when something happens: warn players before a restart, start the server
again after a crash, post to Discord when a backup fails, run a Discord bot
for your community, tidy up log files every night. No programming needed:
you connect blocks on a canvas. If you like to write code, you can do the
same things in Lua.

You will find it on every server, in the **Visual Scripting** tab.

## What you can build

A few ideas to get going:

- **Daily restart with a warning.** Every morning at 6, tell players the
  server restarts in 5 minutes, wait, then restart it.
- **Crash alerts.** When the server goes down without anyone stopping it,
  post a message in your Discord channel and start it again.
- **Backup watcher.** When a backup fails, ping your staff on Discord.
- **A Discord bot.** Slash commands that show who is online, buttons that
  restart the server, roles handed out when people join.
- **Config switcher.** Change a startup setting or a config file from a Run
  button, so nobody has to edit files by hand.
- **Game events** (on supported games, such as Pavlov VR). Welcome new
  players, announce the top player at the end of a map, kick people who
  team kill.

## The tabs

| Tab | What it is for |
|---|---|
| **Graphs** | Your graphs: the automations you build on the canvas. Make, edit, switch on and off, run by hand. |
| **Runs** | Every time a graph ran: when, what started it, whether it worked, and a step by step log. |
| **Variables** | Values your graphs keep between runs, such as a counter or the last player who joined. |
| **Scripts** | Lua scripts, in a full code editor. *Beta.* |
| **Exports** | What your scripts and graphs offer each other, and the Lua to call it. *Beta.* |

> **Scripts and Exports are in beta.** If you do not see them, they are not
> on your account yet. Everything on the Graphs, Runs and Variables tabs
> works without them.

## Words you will see

| Word | Meaning |
|---|---|
| **Graph** | One automation: a set of connected nodes on a canvas. |
| **Node** | One block in a graph. Each does one thing: "Send command", "Wait", "Read file". |
| **Event** | Something that happens and starts a graph: a schedule, the server crashing, a Discord message. Event nodes are the ones named "On ...". |
| **Pin** | A dot on the side of a node. Links go from pin to pin. |
| **Link** | A line between two pins. Arrow links say what happens next; value links carry information. |
| **Run** | One go of a graph, from its event to the end. |
| **Variable** | A value kept between runs. |
| **Script** | A Lua file on the Scripts tab. |

## The guide

1. [Your first graph](01-first-graph.md): two short walkthroughs, start here.
2. [How graphs work](02-how-graphs-work.md): events, nodes, links, values, runs.
3. [Working in the editor](03-the-editor.md): every button, shortcut and tab.
4. [Graph functions](04-graph-functions.md): graphs other graphs (and Lua) can call. *Beta.*
5. [Discord bots](05-discord-bots.md): a bot of your own, run by your graphs.
6. [Lua scripting](06-lua-scripting.md): writing scripts, from the basics up. *Beta.*
7. [Lua language reference](07-lua-reference.md): every rule, keyword and built in.
8. [Recipes](08-recipes.md): ready made graphs and scripts to copy.
9. [Limits and troubleshooting](09-limits-and-troubleshooting.md): what to do when something does not work.

Reference, for looking things up:

- [Events reference](reference/events.md): every event and what it carries.
- [Nodes reference](reference/nodes.md): every node, its inputs, outputs and settings.
- [Lua function reference](reference/lua-api.md): every `yoke.*` function.

## Who can use it

The server owner can do everything. To let someone else in, add them as a
subuser and tick what they need in the **Nodes** permission group: open the
tab, create, edit, delete, run, edit variables, web requests, Discord
messages, the Discord bot, and Lua scripts.

One rule keeps your server safe: **a graph can never do more than the person
who last saved it.** A node that restarts the server needs the restart
permission, a node that deletes files needs the delete permission, and so on.
If a subuser loses a permission, the graphs they last saved stop running and
say why on their card. Anyone with the right permissions can open the graph
and save it again to bring it back.
