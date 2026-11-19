# How graphs work

A graph is a picture of "when this happens, do these things". It has two
parts: an **event** that starts it, and **nodes** joined by **links** that
say what to do. When the event happens, the panel starts a **run**: it
begins at the event node and follows the links, one node at a time, until
there is nothing left to do.

## Events

Every graph needs at least one event node, the ones named "On ...". You
find them in the **Events** group of the node browser (and in **Discord
events**, or a game's own group, when those apply to your server).

| Group | Events |
|---|---|
| Start | Run button, Schedule, Webhook received, Called by another graph |
| Server | Power action, Server online, offline, crashed, starting, stopping, State changed, Console command, Reinstall requested, Install finished, Suspended, Unsuspended, Settings changed, Any activity |
| Files | File changed, File opened |
| Backups | Backup started, completed, failed, deleted; Restore started, completed, failed |
| Schedules | Schedule run by hand, Schedule changed |
| Users and network | Subuser added, changed, removed; Allocation changed; Database changed |
| Visual Scripting | A graph failed |
| Discord | Messages, slash commands, buttons, reactions, members, voice and more, once you have a [bot](05-discord-bots.md) |

The [Events reference](reference/events.md) lists all of them with what each
one carries.

A graph may have **several events**. Each starts its own run from its own
node, so a graph can, for example, do the same thing on a schedule and from
a Run button.

### What an event carries

An event node's outputs (the round pins on its right) are the details of
what happened. **On Console command** gives you the command that was typed
and who typed it; **On File changed** gives the path and what happened to
it; **On Message** (Discord) gives the message text, its author and channel.
Link those outputs into later nodes to use them.

### Filters: only run when it matters

Most events have filters, set on the event node in the side panel. A graph
only runs when the event matches them:

- Blank means "anything".
- In text filters, `*` matches any run of characters. On **On Console
  command**, a Command filter of `say *` runs for every `say` command; on
  **On File changed**, a Path of `/config/*.json` runs for JSON files in
  `/config`.
- Choice filters (like the Action on **On Power action**) pick one value.

Events that something can cause have a **By graph** filter: blank for anyone,
`-` for people only (not graphs), a graph's name for that graph, `*` for any
graph. A graph never hears about its own actions, so it cannot start itself
again, but two graphs can set each other off: a graph that writes a file and
another that reacts to file changes, for example. Set `-` on the second to
have it react to people only.

### How quickly events arrive

Most events start a run within a second or two. A few are found by checking:

- **Server online, offline, crashed, starting, stopping, State changed**: the
  panel asks your server's state every 15 seconds, so these can be up to 15
  seconds late. When a graph is first switched on, the first check only
  notes the current state; changes after that start runs.
- **Server crashed** means the server went offline when nobody had asked
  for a stop, kill or restart in the five minutes before.
- **Schedule** fires within the minute it is due.
- Game events (such as Pavlov's) come from the game itself and can take up
  to a minute or two, as each says.

## Nodes

A node does one job. They are grouped in the browser:

| Group | For example |
|---|---|
| Flow | Branch, Sequence, For each, Repeat, Wait, Stop, Log, Try, Switch, Run another graph |
| Logic | Compare, And, Or, Not, Is empty, Choose |
| Server | Start, Stop, Restart, Kill, Send command, Server info, Server status, Make backup, Startup variables, Run schedule |
| Files | Read, Write, Write JSON, Exists, List folder, Delete, Rename or move, Copy, Make folder, Download to server |
| Databases | Your server's MySQL databases: list, create, delete, run SQL |
| Data | Text, Number, True or false, JSON, Trigger data, variables, Template, Convert |
| Text, Math, Lists, JSON, Time | Working with values |
| Web | Web request, Discord message |
| Discord | Everything a bot can do, once you have one |

The [Nodes reference](reference/nodes.md) describes every one.

## Two kinds of link

Look closely at a node and you will see two kinds of pin.

### Arrow pins: what happens next

The **white diamonds** are arrow pins. A line from one node's arrow output to
another node's arrow input means "when this is done, do that". Following the
arrows is how the run moves through your graph.

- An arrow output leads to one place. To do several things in a row, chain
  them, or use **Sequence**, which has First, Second, Third and Fourth
  outputs that run in that order.
- An arrow input can take many lines, so different paths can join up again.
- Some nodes have more than one arrow output. **Branch** has True and False;
  **For each** has Each (once per item) and Done (after the last).
- A node with nothing linked to its arrow output is where that path ends.

### Value pins: information

The **round, coloured pins** carry values. A line from an output (right side)
to an input (left side) hands a value over.

| Colour | Type | Holds |
|---|---|---|
| Amber | Text | Any text: `hello`, `/config/server.json` |
| Green | Number | `12`, `3.5`, `-1` |
| Red | Yes/no | True or false |
| Blue | JSON | Structured data: objects and lists from files, web requests, events |
| Purple | List | A list of values |
| Grey | Anything | Any of the above |

- An input takes one line. An output can feed as many inputs as you like.
- An input with no line can be typed into directly, on the node or in the
  side panel. Typed values and linked values do the same job.
- Types are forgiving. Text `12` going into a Number input is read as 12; a
  list going into a Text input becomes JSON text. The editor only refuses a
  line when the two sides cannot read each other at all. **Convert** turns a
  value into another type on purpose.

### Nodes without arrow pins

Some nodes have only value pins: **Template**, **Compare**, **Calculate**,
**Join text** and most of the Text, Math, Lists, JSON and Time groups. These
do not sit in the chain of arrows. Instead, they work out their value at the
moment another node reads it. You can place them anywhere and just link
their output to where it is needed.

Nodes that talk to your server (Read file, Server status, Send command) or
change something always have arrow pins, so you decide exactly when they
happen. Their outputs hold what they found when they ran; a later node in
the same run can read them.

## Making decisions

| You want to | Use |
|---|---|
| Do one thing or another | **Branch**, with a Yes/no value in Condition. Pair it with **Compare** (equals, not equals, greater, less, contains, starts with, ends with, matches a regular expression). |
| Pick between many cases | **Switch**: up to four cases of text, plus Default. |
| Combine conditions | **And**, **Or**, **Not** |
| Check for nothing | **Is empty**: true for nothing at all, blank text, zero, an empty list or an empty object |
| Pick a value, not a path | **Choose**: one value when true, another when false |

## Repeating

- **For each** runs its Each output once per item of a list, with the Item,
  its Index (counting from 0) and the Count. Done runs after the last.
- **Repeat** runs Each a set number of times.
- Every node a run goes through counts as a step, and a run may take up to
  1,000 steps, so loops over very long lists will stop with a message.

## Waiting and stopping

- **Wait** pauses the run for a number of seconds. All the waits in one run
  may add up to 300 seconds.
- **Stop** ends the run straight away, with an optional reason. The run is
  shown as **Stopped**, not Failed.

## When something fails

If a node cannot do its job (a file is missing, a web site does not answer,
Discord refuses), the run ends as **Failed**, and the run log points at the
node and says why.

To handle failures yourself, wrap the risky part in **Try**: link the nodes
that might fail after its **Try** output. If one of them fails, the run
carries on from **Failed** instead, with the error text on the Try node's
**Error** pin. **Then** runs after either.

To hear about failures, make a separate graph with **On A graph failed**. It
fires when any other graph on the server fails (never for itself), with the
graph's name and the error. Link it to a Discord message and you will know
straight away.

## Text with values in it

**Template** fills placeholders in text:

| Placeholder | Becomes |
|---|---|
| `{{a}}` to `{{d}}` | Whatever is linked to the Template's A to D inputs |
| `{{trigger.name}}` | A field of the event that started the run, by name: `{{trigger.command}}`, `{{trigger.path}}` |
| `{{var.name}}` | A value this graph keeps (see below) |
| `{{shared.name}}` | A value kept for the whole server |
| `{{server.name}}`, `{{server.address}}` | Your server's name and address |
| `{{now}}` | The current date and time |

**Trigger data** gives you everything the event carried as one JSON object;
**Get from JSON** reads a value out of it (or out of any JSON) by a path such
as `players.0.name` or `settings.max`. Lists count from 0 in paths.

## Keeping values between runs

A run forgets everything when it ends, unless you keep it in a **variable**:

- **Set variable** keeps a value under a name.
- **Get variable** reads it back (with a default for when there is none).
- **Add to variable** adds a number, starting from 0. It is safe when two
  runs do it at the same moment, so use it for counters.
- **Delete variable** forgets it.

Each has a **Kept for** setting: **This graph** (only this graph sees it) or
**Whole server** (every graph on the server shares it). The **Variables** tab
shows every kept value, and lets you add, edit and delete them by hand.

## Runs

Every time an event starts a graph, a run is queued and done in the
background, usually within a second. Nothing you do on the panel waits for
it.

| Status | Meaning |
|---|---|
| Queued | Waiting its turn. |
| Running | Going now. |
| OK | Finished. |
| Failed | A node failed, or the run went over a limit. The log says which. |
| Stopped | A Stop node (or a script) ended it on purpose. |
| Skipped | It was not allowed to start: the graph was switched off, Visual Scripting was locked on the server, or the graph is no longer allowed to do what it does. The reason is shown. |

The **Runs** tab lists them newest first. Open one to see each node it went
through, with a note and how many milliseconds it took, plus **Trigger
data** (what the event carried). **Show the failing node** opens the editor
right on the node that failed. Runs are kept for 14 days, and the last 200
per graph.

Everything a graph changes on your server (files, power, commands, backups,
startup settings) also appears on your server's **Activity** tab, with the
graph's name, just as if a person had done it.

## Graphs calling graphs

- **Run another graph** starts a different graph of yours, at its **On
  Called by another graph** event, handing it an Input. The other graph runs
  on its own, separately.
- A **graph function** is a graph you call like a node, and which hands
  values back. See [Graph functions](04-graph-functions.md).

## Why a graph switched itself off

The panel switches a graph off, and says why on its card, when running it
would be unsafe or impossible. The usual reason: the person who last saved
it no longer has a permission one of its nodes needs. Open it, and save it
again as someone who does (the owner always does), then switch it back on.
