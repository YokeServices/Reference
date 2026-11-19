# Graph functions

> **Beta.** Graph functions are part of the Scripts beta. If the **A
> function** choice or the **Graph functions** group is missing, they are not
> on your account yet.

A graph function is a graph you can use like a node. Build something once,
such as "announce a message in the console and on Discord", and every other
graph can drop it in as a single node, give it values and get values back.
Lua scripts can call it too.

## Making one

1. On the Graphs tab, press **New graph**, choose **A function**, give it a
   name and press **Create and open**.
2. The new graph has two nodes already linked: **Function** (where a call
   starts) and **Return** (where it ends).
3. Select the **Function** node and fill in its settings:
   - **Name**: what it is called by, such as `announce`. Letters, numbers
     and `_`. Each name can be used once per server.
   - **Parameters**: the values it takes, one per line.
   - **Returns**: the values it hands back, one per line.
4. Build what it does between the two, save, and keep it switched **On**.

A line in Parameters or Returns is `name type label`:

```text
player string Player name
amount number
tags list
```

- **name**: letters, numbers and `_`, starting with a letter (and not a word
  Lua keeps for itself, like `end` or `function`).
- **type**: `string`, `number`, `boolean`, `json`, `list` or `any`. Left out,
  it is `any`.
- **label**: what the pin is called on the node. Left out, it is made from
  the name.

A function can have up to 12 parameters and 12 returns.

Each parameter becomes an output pin on the **Function** node, so the nodes
inside can use it. Each return becomes an input pin on the **Return** node:
link the values to hand back into them.

## Using one in another graph

Open the node browser in any other graph. Your function is in the **Graph
functions** group under its name. Its inputs are the parameters, its outputs
are the returns, and it has arrow pins like any other node.

When the run reaches it, the function graph runs right away, inside the
calling run, from its Function node to its Return node. Then the calling
graph carries on with the returned values. Its steps and time count towards
the calling run's limits, and anything it logs shows on the calling node in
the run log.

If a path inside the function ends without reaching a Return node, the call
still finishes, with nothing returned.

## Calling one from Lua

In a script, a graph function is `graphs.<name>`:

```lua
graphs.announce("Server restarts in 5 minutes")

-- Several returns come back as several values, in the order of Returns.
local online, count = graphs.player_report()

-- Or pass the parameters by name, in one table.
graphs.give_reward{ player = "Ash", amount = 500 }
```

The [Exports tab](06-lua-scripting.md#the-exports-tab) lists every graph
function with the Lua to call it.

## Rules

- The function graph must be **switched on** to be called.
- Functions can call other functions, up to 8 deep in one run.
- A function acts with the permissions of the person who last saved **it**.
  Whoever last saved the graph or script that **calls** it must also hold
  every permission the function needs. A function can never let someone do
  something they could not do themselves.
- Renaming a function changes the node other graphs use; open them and
  check them after a rename.

## Graph functions or Run another graph?

| | Graph function | Run another graph |
|---|---|---|
| Waits for it to finish | Yes, right away, inside the same run | No, queues a separate run |
| Hands values back | Yes, through Return | No |
| Shows in the run log | On the calling node | As its own run |
| Good for | Reusable steps, calculations, lookups | Kicking off a long job in the background |
