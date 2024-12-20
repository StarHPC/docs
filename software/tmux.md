---
sort: 5
---

# Tmux

## Introduction

Tmux (Terminal Multiplexer) allows you to create multiple terminal sessions within a single window.
This functionality can come in handy when working on the compute nodes, through an interactive session.

Users commonly need multiple terminal windows, e.g. you have your task running in one terminal, and monitor its activity from another.

Tmux allows you to achieve this functionality, through a single interactive session on a node.

### Keywords to know

Before getting started, let's understand some common Tmux terminology:

- **Prefix** (like a escape key): The key combination that tells Tmux you're about to enter a command (default: `Ctrl+b`)
- **Session**: A group of windows that can be attached (viewed) or detached (run in background)
- **Window**: Like tabs in your browser, each containing one or more panes
- **Pane**: Split sections within a window

We will further clarify these terms through the image attached below.

## Getting Started

Tmux is already available on the compute nodes, so you can go ahead and create a new **Tmux session** using:

```bash
tmux new -s your_session_name
```

When you create a new session, you'll automatically be attached to it. You'll notice a green status bar appear at the bottom of your terminal. The bottom-left corner shows your session name (in this case, "your_session_name"), which helps you keep track of where you are.

To list existing sessions:
```bash
tmux ls
```

To attach to an existing session:
```bash
tmux attach -t your_session_name
```

## Understanding the Interface

![tmux]({{ site.baseurl }}/images/tmux-images/1.png "tmux1")

Looking at the image above, we can see how Tmux organizes your workspace. The window is divided into multiple panes (numbered 0, 1, and 2), and each pane operates as an independent terminal.
The green dividing lines help you visually distinguish between panes, while the status bar at the bottom provides some session information.

### Basic Navigation and Control

The prefix key (`Ctrl+b`) is your gateway to controlling Tmux. Here's how to use it:
1. Press `Ctrl+b`
2. Release both keys
3. Press your desired command key

For example, to detach from your current session:
1. Press `Ctrl+b`
2. Release both keys
3. Press `d`

This detaches you from the session while leaving everything running in the background.

This is one of the most beautiful features about terminal multiplexers, as you can detach from the session 
and even disconnect your SSH connection, but your tasks running within the Tmux session in the background.

Once detached, if you want to completely remove a session (which also terminates running tasks through that session), you can use:

```bash
tmux kill-session -t your_session_name
```

## Working with Panes

![tmux]({{ site.baseurl }}/images/tmux-images/2.png "tmux2")

As shown in the image, each pane can run its own commands independently. 
This is useful when you need to monitor multiple processes or work with different directories simultaneously.

The numbers on each pane where shown using `Ctrl+b` then `q`, which flashes the number of each pane.
We will further use these numbesr to demonstrate an example of swapping the placement of panes 0 and 2.

### Creating and Managing Panes

To split your window:
- Press `Ctrl+b`, release, then press `"` for a horizontal split
- Press `Ctrl+b`, release, then press `%` for a vertical split

To kill a pane:
- Press `Ctrl+b`, release, then press `x` (This kills the running children tasks within that pane as well)

Zooming in/out of a pane:
- Press `Ctrl+b`, release, then press `z` (Toggles between zooming in/out of a pane)

### Navigating Between Panes

Moving between panes is straightforward:
1. Press `Ctrl+b`, release
2. Use arrow keys to move in that direction

To quickly identify panes:
1. Press `Ctrl+b`, release
2. Press `q`
This will briefly display large numbers in the middle of each pane, indicating their pane number.

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

### Command Mode and Advanced Operations

Tmux provides a command mode for more complex operations. To enter it:
1. Press `Ctrl+b`, release
2. Press `:`

![tmux]({{ site.baseurl }}/images/tmux-images/3.png "tmux3")

You'll see a yellow command bar appear at the bottom of your screen, as shown in the image above.

The image below demonstrates entering a command to swap two panes:
```bash
swap-pane -s num_first_pane -t num_second_pane
```

This is just one example of what you can do in command mode.

![tmux]({{ site.baseurl }}/images/tmux-images/4.png "tmux4")

The yellow command bar is where you'll type these commands after pressing `Ctrl+b` followed by `:`. Once you hit enter, your command will be executed.

### Mouse mode

Now that you know about command mode, you can enter command mode (`Ctrl+b`, release, `:`), and then run:

```
set mouse on
```

to enable clicking between panes, or using your mouse in general to navigate things.

To turn it off:

```
set mouse off
```

### Scrolling Through Pane History

To view previous output in a pane:
1. Press `Ctrl+b`, elease
2. Press `[` to enter scroll mode
3. Use arrow keys, PgUp/PgDn, trackpad, etc to navigate
4. Press `q` or `Ctrl+c` to exit scroll mode and return to normal operation

## Working with Windows

Each window in Tmux is like a canvas that can be subdivided.

- New window: Press `Ctrl+b`, release, then press `c`. This **c**reates a new window and switches to it.
- Switch windows:
  - Next window: `Ctrl+b`, release, then `n`
  - Previous window: `Ctrl+b`, release, then `p`
  - Specific window: `Ctrl+b`, release, then type the window number
- Rename current window: `Ctrl+b`, release, then press `,`
- Delete a window (This kills the tasks running within its pane(s)):
  - `Ctrl+b`, release, then `&`, then press `y` to confirm when prompted.

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

## Fun little things
- You can do Prefix + `t` to turn a pane into a digital clock
- You can sync all panes in a window to send the same command to them all simultaneously. In tmux's command mode (indicated below using `:`), run:
   ```
   :setw synchronize-panes
   ```

   To turn it off, do:
   ```
   :setw synchronize-panes off
   ```
