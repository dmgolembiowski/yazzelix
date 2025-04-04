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
│                       zellij-puppetmaster                  │
│                                                            │
│  ┌────────────┐       ┌──────────────┐       ┌──────────┐  │
│  │ yazi_hook  │──────▶│ mpsc channel │──────▶│ helix 🔥 │  │
│  └────────────┘       └──────────────┘       └──────────┘  │
│                             │                              │
│                      ┌──────▼──────┐                       │
│                      │  App State  │                       │
│                      └─────────────┘                       │
╰────────────────────────────────────────────────────────────╯
```

---

## ✅ Roadmap
- [ ] 🧙 Listen to `yazi` file selections and react in some programmatic fashion
- [ ] 🧠 Coordinate PTY state between integrated development tools
- [ ] 📬 Exchange messages to propagate changes beyond the process boundary
- [ ] 💫 Remotely control Zellij to spawn TUI programs like `helix` or `nvim` in existing floating panes
- [ ] 🧪 Nuclear strike the codebase and address absurd levels of technical debt
