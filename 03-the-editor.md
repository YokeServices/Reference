# Working in the editor

Everything on the Visual Scripting tab, button by button.

## The Graphs tab

Each graph is a card showing:

- its **name**, and an **On/Off** switch;
- the **events** it listens for (a graph with none says "No event yet");
- a **function** badge if it is a [graph function](04-graph-functions.md);
- how its **last run** went, and how many times it has run;
- **dropped** runs, if events came faster than the graph may run (30 runs a
  minute per graph);
- **why the panel switched it off**, if it did.

The buttons on a card:

| Button | What it does |
|---|---|
| Play (**Run now**) | Runs the graph from its **On Run button** event. Only on graphs that have one. |
| **Webhook** | Makes or shows the graph's webhook URL. Only on graphs with an **On Webhook received** event. See [Webhooks](#webhooks). |
| **Convert to Lua** | Writes a Lua script that does what the graph does. *Beta*, see [Lua scripting](06-lua-scripting.md#turning-a-graph-into-a-script). |
| **Duplicate** | Makes a copy. The copy starts switched off, so it does not run twice. |
| **Delete** | Deletes the graph and its run history. |
| **Open** | Opens it in the editor. |

### Making a graph

Press **New graph**:

- **Name**: what you will recognise it by, such as `Crash alert`.
- **Starts on an event** (most graphs) or **A function** (a graph other
  graphs call; *beta*, see [Graph functions](04-graph-functions.md)).
- **What it is for**: optional notes for whoever reads it later.

Then **Create and open**. You can have up to 25 graphs on a server.

### The Discord bot card

The Graphs tab also has the **Discord bot** card, where you add a bot of
your own for your graphs to run. See [Discord bots](05-discord-bots.md).

## The editor

Along the top: **back** to the graphs, the graph's **name** (click to change
it), its **On/Off** badge, **Unsaved** when you have changes, then **Add
node**, **undo** and **redo**, the zoom buttons, **Check**, **Run** (when the
graph has an On Run button event) and **Save**.

Below is the **canvas** with your nodes, and on the right a side panel: the
**node browser**, or the **selected node's** settings. On a phone the side
panel slides up from the bottom.

### Moving around

- **Pan**: drag the empty background.
- **Zoom**: the mouse wheel, a pinch on a touch screen, or the + and -
  buttons.
- **Fit everything in view**: the fit button.

### Adding nodes

- **Add node** (or Ctrl+K) opens the browser. Every group starts closed;
  open one, or type in **Search nodes** to search all of them at once.
- **Click** a node to put it in the middle of the view, or **drag** it onto
  the canvas where you want it.
- **Right click** the canvas (long press on a phone) to open the browser and
  have the new node land on that spot.
- **Drag a line out of any pin and let go on empty space**: the browser opens
  with only the nodes that can take that line, and the node you pick arrives
  already linked. This is the fastest way to build.

The browser only offers what your server can use. Discord nodes appear once
the server has a bot; game nodes appear on servers of that game.

### Linking

- Drag from one pin to another and let go. White diamonds (arrow pins) link
  to white diamonds, round pins to round pins.
- A value input holds one line, and so does an arrow output. Linking to one
  that already has a line replaces the old line.
- To remove a line, click it and press Delete.

### Typing values

An input with no line can be typed into right on the node. Long values, and
every setting, are in the side panel when the node is selected. The side
panel also shows the node's description, and **Delete node**.

Some inputs take one item per line (a list), and some show a dropdown of
choices. A node that needs something you have not given it shows a red
outline when you press Check.

### Keyboard shortcuts

Use Cmd instead of Ctrl on a Mac.

| Keys | Does |
|---|---|
| Ctrl+S | Save |
| Ctrl+Z | Undo |
| Ctrl+Y or Ctrl+Shift+Z | Redo |
| Ctrl+D | Duplicate the selected node |
| Ctrl+K | Open the node browser |
| Delete or Backspace | Delete the selected node or line |
| Escape | Close the browser, or clear the selection |

### Check and Save

- **Check** asks the panel to look over the graph without saving: every node
  is one your server has, every line joins pins that fit, nothing loops back
  on itself, schedules are valid cron, and you hold every permission the
  nodes need. Problems are marked on the nodes (a red outline and a number;
  hover or tap to read them).
- **Save** does the same checks and refuses with the same marks if anything
  is wrong. Nothing changes until a save succeeds.
- If someone else saved the graph while you were editing it, your save is
  refused with a clear message instead of quietly overwriting their work.
  Reload to see their version.
- Leaving the editor with unsaved changes asks first.

## Running by hand

A graph with an **On Run button** event can be run from the editor's **Run**
button or the play button on its card. Save first: Run uses the saved graph.

The Run window has one box, the **input**. Whatever you type there comes out
of the event's **Input** pin. JSON is read as data (`{"player": "Ash"}` gives
an object whose `player` field you can read with **Get from JSON**);
anything else is passed as text. Leave it empty if the graph does not use
it.

## Webhooks

A webhook lets another website or program start your graph, by sending a web
request to a secret address.

1. Add an **On Webhook received** event to the graph and save.
2. On the graph's card, press **Webhook**, then **Make a URL**.
3. Copy the URL straight away. It is **shown once**: the panel keeps only a
   fingerprint of it, so it cannot show it again. If you lose it, **Make a
   new URL** (the old one stops working) or **Remove** it.

To use it, send a `POST` request to the URL. The request's body comes out of
the event's **Body** pin (as JSON when it is JSON), the query string out of
**Query**, and the method out of **Method**:

For example, with `curl` (use the URL you copied):

```bash
curl -X POST "https://panel.example.com/api/nodes/hooks/your-secret-token" \
  -H "Content-Type: application/json" \
  -d '{"player": "Ash", "reason": "vote kick"}'
```

The answer is `{"ok": true, "runs": 1}` (how many runs it started), or
`{"ok": false, ...}` when it did not start anything.

You can add a name to the end of the URL, such as `.../your-secret-token/restart`.
Set the same name in the event's **Name** filter and that event only runs
for that name, so one graph can have several webhook events that each do
something different.

Webhooks take up to 30 calls a minute per graph, and bodies up to 64 KB.

## The Runs tab

Every run of every graph, newest first. Use **Every graph** to show one
graph only, and **Older runs** to go further back.

Open a run to see:

- the **log**: each node the run went through, with the time, a note and how
  many milliseconds it took;
- **Trigger data**: exactly what the event carried;
- the error, if it failed, and **Show the failing node** to open the editor
  on it (or **Open the graph**, or **Open the script** for a script's run).

## The Variables tab

Every value your graphs keep between runs. A **Server** badge means the
value is kept for the whole server; otherwise the badge names the graph it
belongs to.

- **Add** makes a new value: a **Name**, what it is **Kept for** (the whole
  server, or one graph) and a **Value**. A value written as JSON is kept as
  data (`[1, 2, 3]`, `{"max": 20}`); anything else is kept as text.
- **Edit** and **Delete** do what they say.

Changing a value here takes effect on the next run that reads it.
