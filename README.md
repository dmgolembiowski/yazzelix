# 🧰 Project: `yazelix`

> A Tokio-powered command center that listens to `yazi`, reacts with floating `helix` panes in `zellij`, and manages async coordination with style.

---

## 📸 Architecture Diagram
How it works:
Rustacean in Alacritty spawns a Zellij multiplexer. They pin a floating pane to roughly fit a helix TUI ide dimension, or a
similar window size to what we often expect to see on Neovim.
Below that, there is a shell where the Rustacean runs `yazi`, a popular file explorer TUI.
Now imagine: a software harness that overrides the behavior for opening the editor within yazi
and instead sends a message to zellij to open it elsewhere.
Now, the floating pane _receives_ the selected file, and opens it in the terminal IDE.
`yazelix` is a specialized MITM proxy or puppeteer loaded by Zellij at runtime for facilitating this quality of life improvement.

```text
╭────────────────────────────────────────────────────────────╮
│                     yazelix `yaz`                          │
│                      |                                     │
│  ┌────────────┐      ├───────────────┐       ┌──────────┐  │
│  │ yazi_hook  │──────▶ relay socket ├─┬─┐   | helix 🔥 │  │
│  └────────────┘      |└──────────────┘ └-|-▶└──────────┘  │
│                      |      │            |                 │
│                      ├──────▼──────┐  ┌──▼───────────┐     │
│                      │  ZellijExe  ─▶| YazelixPlug  |     │
│                      └─────────────┘  └──────────────┘     │
╰────────────────────────────────────────────────────────────╯
```
It's a bit convoluted, but my initial design generally should follow the flow:
1. Invoke `yaz` within an Alacritty terminal session (or kitty)
2. `yaz` performs the following sequence in parallel:
> i. Daemonize a relay socket / message bus
> ii. Spawn `zellij` then tell it to make two additional floating pane, one of them hidden (P2), the other pinned to the foreground (P1). P1 runs a PTY of the current shell from the parent process. P2 is a standby subscriber to the relay socket, waiting for a message to become the active foreground pane. Next, once P2 receives the signal to open `helix` it sends three synchronous messages to the zellij socket. Send a message to Zellij to send P1 to the background (or make it's width and height both zero, and toggle any existing selected focus toward P2), apply the TUI process corresponding to the yazi hook onto P2's PTY region with a `process::Command`, such that when the process exits a signal is sent to the relay socket to ressurect P1.
> iii. Apply P2's window location to the P1 pane and resume focus there.
3. TODO: Figure out how to actually leave the process. Perhaps with `,` somewhere on path as a binary, or a transient executable created at runtime and destroyed upon invocation and process closure.
---

## ✅ Roadmap
- [ ] 🧙 Listen to `yazi` file selections and react in some programmatic fashion
- [ ] 🧠 Coordinate PTY state between integrated development tools
- [ ] X  Update this readme to accommodate the actual state of what makes things function
- [ ] 📬 Exchange messages to propagate changes beyond the process boundary
- [ ] 💫 Remotely control Zellij to spawn TUI programs like `helix` or `nvim` in existing floating panes
- [ ] 🧪 Nuclear strike the codebase and address absurd levels of technical debt
