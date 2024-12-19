---
sort: 5
---

# Tmux

## Introduction

Tmux (Terminal Multiplexer) is a powerful tool that allows you to create multiple terminal sessions within a single window.
This is particularly useful when working on HPC clusters through interactive sessions on a compute node.

It is very common to need multiple terminal windows, e.g. you have your task running in one terminal, and monitor its activity from another.

### Keywords to know

Before diving in, let's understand some essential Tmux terminology:

- **Session**: A group of windows that can be attached (viewed) or detached (running in background)
- **Window**: Like tabs in your browser, each containing one or more panes
- **Pane**: Split sections within a window
- **Prefix**: The key combination that tells Tmux you're about to enter a command (default: `Ctrl+b`)

## Getting Started

To create a new Tmux session:
```bash
tmux new -s my_session
```

When you create a new session, you'll automatically be attached to it. You'll notice a green status bar appear at the bottom of your terminal. The bottom-left corner shows your session name (in this case, "my_session"), which helps you keep track of where you are.

To list existing sessions:
```bash
tmux ls
```

To attach to an existing session:
```bash
tmux attach -t my_session
```

## Understanding the Interface

![tmux]({{ site.baseurl }}/images/tmux-images/1.png "tmux1")

Looking at the image above, we can see how Tmux organizes your workspace. The window is divided into multiple panes (numbered 0, 1, and 2), and each pane operates as an independent terminal. The green dividing lines help you visually distinguish between panes, while the status bar at the bottom provides important session information.

### Basic Navigation and Control

The prefix key (`Ctrl+b`) is your gateway to controlling Tmux. Here's how to use it:
1. Press `Ctrl+b`
2. Release both keys
3. Press your desired command key

For example, to detach from your current session:
1. Press `Ctrl+b`
2. Release both keys
3. Press `d`

This detaches you from the session while leaving everything running in the background. Once detached, if you want to completely remove a session, you can use:

```bash
tmux kill-session -t my_session
```

## Working with Panes

![tmux]({{ site.baseurl }}/images/tmux-images/2.png "tmux2")

As shown in the image, each pane can run its own commands independently. This is incredibly useful when you need to monitor multiple processes or work with different directories simultaneously.

### Creating and Managing Panes

To split your window:
- Press `Ctrl+b`, release, then press `"` for a horizontal split
- Press `Ctrl+b`, release, then press `%` for a vertical split

### Navigating Between Panes

Moving between panes is straightforward:
1. Press `Ctrl+b`, release
2. Use arrow keys to move in that direction

To quickly identify panes:
1. Press `Ctrl+b`, release
2. Press `q`
This will briefly display large numbers in the middle of each pane, as shown in the image above (containg the numbers)

### Resizing Panes

Resizing panes is done using a continuous key combination:
1. Press `Ctrl+b`, release
2. Hold down Ctrl
3. While holding Ctrl, press arrow keys to resize the current pane:
   - Up arrow: Expand upward
   - Down arrow: Expand downward
   - Left arrow: Expand to the left
   - Right arrow: Expand to the right
4. Release Ctrl when done

![tmux]({{ site.baseurl }}/images/tmux-images/3.png "tmux3")

### Command Mode and Advanced Operations

Tmux provides a command mode for more complex operations. To enter it:
1. Press `Ctrl+b`, release
2. Press `:`

You'll see a yellow command bar appear at the bottom of your screen, as shown in the image above.

![tmux]({{ site.baseurl }}/images/tmux-images/4.png "tmux4")

The image demonstrates entering a command to swap two panes. This is just one example of what you can do in command mode - from renaming windows to adjusting layouts. The yellow command bar is where you'll type these commands after pressing `Ctrl+b` followed by `:`.

### Scrolling Through Pane History

To view previous output in a pane:
1. Press `Ctrl+b`, release
2. Press `[` to enter scroll mode
3. Use arrow keys or PgUp/PgDn to navigate
4. Press `q` to exit scroll mode and return to normal operation

## Working with Windows

Windows in Tmux work similar to browser tabs. Here's how to manage them:

- New window: Press `Ctrl+b`, release, then press `c`
- Switch windows:
  - Next window: `Ctrl+b`, release, then `n`
  - Previous window: `Ctrl+b`, release, then `p`
  - Specific window: `Ctrl+b`, release, then type the window number
- Rename current window: `Ctrl+b`, release, then press `,`

## Important Notes

1. Commands that start with `:` must be entered in command mode (Prefix then `:`, look for yellow bar)
2. All Tmux commands start with the prefix (`Ctrl+b`) unless you're in command mode
3. If you get disconnected, your Tmux session continues running! Just reconnect to your HPC node and reattach to your session

To cleanly exit Tmux, you can either:
- Exit all shells in all panes
- Enter command mode and type:

```
kill-session
```
