# Your first graph

Two short walkthroughs. The first restarts your server every morning with a
warning for players; the second tells you on Discord when your server
crashes. Each takes about five minutes.

## Walkthrough 1: a daily restart with a warning

What it will do: every day at 06:00, say in the console that the server
restarts in 5 minutes, wait, say it again a minute before, then restart.

### 1. Make the graph

1. Open your server and go to **Visual Scripting**, then **Graphs**.
2. Press **New graph**.
3. Name it `Morning restart`. Leave **Starts on an event** selected.
4. Press **Create and open**. You are now in the editor, looking at an empty
   canvas.

### 2. Add the event

Every graph starts with an event: the thing that sets it off.

1. Press **Add node**. The node browser opens on the right (on a phone it
   slides up from the bottom).
2. Open **Events** and pick **On Schedule**. The node appears in the middle
   of the canvas.
3. Click the node. The side panel now shows its settings. In **Cron**, type
   `0 6 * * *`.

`0 6 * * *` means "minute 0 of hour 6, every day". The five parts are
minute, hour, day of the month, month and day of the week. A few more:

| Cron | When |
|---|---|
| `0 6 * * *` | Every day at 06:00 |
| `*/15 * * * *` | Every 15 minutes |
| `0 */4 * * *` | Every 4 hours, on the hour |
| `30 18 * * 5` | Fridays at 18:30 |
| `0 0 1 * *` | Midnight on the first of every month |

Times are in the panel's time zone.

### 3. Add the steps

1. Press **Add node** again, open **Server**, and pick **Send command**.
2. Drag the node a little to the right of the event. You move a node by its
   header (the coloured top).
3. Connect them: press on the white diamond on the right of **On Schedule**
   (its "Then" pin) and drag to the white diamond on the left of **Send
   command**. Let go. A line joins them.
4. On **Send command**, click the **Command** box and type
   `say The server restarts in 5 minutes`.

The white diamonds are **arrow pins**. A line between them means "and then
do this". Your graph now says: at 06:00, send that command.

Now the wait and the restart:

1. Add **Flow, Wait**. Link Send command's "Then" diamond to Wait's left
   diamond. Set **Seconds** to `240`.
2. Add another **Send command** after it, with `say Restarting in 1 minute`.
3. Add another **Wait** of `60` seconds.
4. Add **Server, Restart server** at the end.

Tip: instead of pressing Add node each time, drag a line out of a "Then"
pin and let go on an empty spot. The browser opens showing only the nodes
that can go there, and the new node arrives already linked.

### 4. Check and save

1. Press **Check**. The panel looks for problems without saving. A node with
   a problem gets a red outline and a number; hover over it (or tap it) to
   read what to fix.
2. Press **Save** (or Ctrl+S).
3. Make sure the badge next to the graph's name says **On**. New graphs are
   switched on; the switch on the graph's card (Graphs tab) turns it off and
   on again.

That is it. Tomorrow at 06:00 it runs on its own.

### 5. Try it now

You do not have to wait until 06:00. Add a second event, **Events, On Run
button**, and link its "Then" pin to the same first Send command. An arrow
pin on the left can take any number of lines, so both events lead into the
same steps. Save, then press **Run** at the top of the editor (or the play
button on the graph's card). A small window asks for an input; leave it
empty and press **Run**.

Open **Runs** to watch it: the run shows as Running, then OK. Click it to
see each step it took and how long each took.

> A run can wait 300 seconds (5 minutes) in total. That is why the waits
> above add up to exactly 300. For longer gaps, use a second schedule
> instead of a longer wait.

## Walkthrough 2: a Discord alert when the server crashes

What it will do: when the server goes offline without anyone stopping it,
post in a Discord channel, then start the server again.

You need a Discord **webhook** for the channel (not a bot; that is for
[Discord bots](05-discord-bots.md)):

1. In Discord, open the channel's settings (the cog), then **Integrations**,
   then **Webhooks**, then **New Webhook**.
2. Press **Copy Webhook URL**.

Keep that URL private: anyone with it can post in the channel.

Now the graph:

1. **New graph**, name it `Crash alert`, **Create and open**.
2. Add **Events, On Server crashed**.
3. Add **Web, Discord message** and link the event's "Then" to it.
4. Fill in **Webhook URL** (paste it), **Message** (`The server crashed. Starting it again.`)
   and, if you like, **Title** (`Crash`) to send it as a neat embed, and
   **Sender name** (`Server watch`).
5. Add **Server, Start server** after it.
6. Check, then Save.

"Crashed" means the server went offline when nobody asked it to stop in the
minutes before. A normal stop or restart from the panel does not count, so
this will not fire when you restart on purpose.

To make the message say which server crashed, add **Data, Template** and
type `{{server.name}} crashed at {{now}}.` in it, then drag a value line from
the Template's **Text** output (the round pin on the right) to the Discord
message's **Message** input. The round pins are **value pins**: they carry
information instead of "what happens next".

## Where to go next

- [How graphs work](02-how-graphs-work.md) explains arrow pins and value pins
  properly, and everything else you just used.
- [Recipes](08-recipes.md) has more graphs to build.
