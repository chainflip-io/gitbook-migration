---
description: The official way to get devs to do something.
---

# Submitting an Issue

First output your logs to the terminal. Copy the output to the channel below.

```bash
journalctl -u "chainflip-*" --since "2 days ago" > /tmp/logs
```

Then you can very easily copy that log file to your local machine, and include it in your issue description in Discord.

```
scp -i /path/to/ssh/key root@<YOUR NODE IP>:/tmp/logs $HOME/
```

For general help, the [`💬-node-support`](https://discord.com/channels/824147014140952596/1096065647361925161) channel in the Perseverance Discord category is a good place to start.\
\
If you have hit a more serious issue or have a formal request, use this:

[https://github.com/chainflip-io/support](https://github.com/chainflip-io/support)
