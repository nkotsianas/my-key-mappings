# my-keymaps
Describes the keyboard remappings that I use for productivity (for both X and TTY).

OS is Ubuntu 16.04 LTS.


## For the virtual console / TTY

### Included files:

`defkeymap.kmap`
- Output of `dumpkeys` before any changes.

`custom-tty-kbd-changes.kmap`
- Only the *changes* that I would like to make for the keyboard.
- Does not constitute an entire keyboard keymap binding.
- My changes:
    - Swap the Shift behavior of the number row (ie. special characters are easier to type, numbers are harder).
    - Remap CapsLock to Escape.
    - Swap the Shift behavior of semi-colon/colon.

`nickkeymap.kmap`
- Output of `dumpkeys` after the changes.
- (Not really needed, but just for reference.)


### Usage:

In a virtual console, `loadkeys`, `dumpkeys`, and `showkey` are the way to find and remap keys.

See: https://superuser.com/questions/290115/how-to-change-console-keymap-in-linux


Steps to create custom keymap:

1. Save the current keyboard layout:
    - `dumpkeys > backup.kmap`
    - In case something goes horribly wrong, reload it with:
    - `sudo loadkeys backup.kmap`
    - Or just reboot. But **do things atomically!!**

2. Check which keycodes are assigned to your keys:
    - Run `showkey`.

3. Note the output of `dumpkeys | head -n1`:
    - For me this is: `keymaps 0-127`.

4. Create your own keymap for (say) remapping CapsLock to Esc:
    - Put it in `/etc/` so that it can be loaded automatically at startup. (See next steps for details.)
```
    +--------------------------+
    | /etc/remap-capslock.kmap |
    |--------------------------|
    | keymaps 0-127            |
    | keycode 58 = Escape      |
    +--------------------------+
```

5. Load the keymap:
    - `sudo loadkeys /etc/remap-capslock.kmap`
    - To revert, see (1).

6. To have this load on every boot:
    - Add the following line to `/etc/rc.local` just before `exit 0`.
    - For safety, you may want to wrap the instruction in `if [ -f /etc/remap-capslock.kmap ]; then ... fi`
```
    +--------------------------------------------+
    | /etc/rc.local                              |
    |--------------------------------------------|
    | ...                                        |
    | /usr/bin/loadkeys /etc/remap-capslock.kmap |
    | exit 0                                     |
    +--------------------------------------------+
```

## For applications under X window system

### Included files:

`us.nick`
- Modified version of `/usr/share/X11/xkb/symbols/us`.
- Defines the keymaps for the United States keyboard layout.
- Here we can make the switches for the number row and semi-/colon (same as for the TTYs).

`pc.nick`
- Modified version of `/usr/share/X11/xkb/symbols/pc`.
- Handles a lot of modifier keys.
- Here we can make capslock map to escape instead.

### Usage:
- Pretty self explanatory, just swap the order of the columns on the keys you want.
- **Backup** your `/usr/share/X11/xkb/symbols/xx` file, then replace it with the new one.
- (Don't forget to do this **atomically!**)

### Note:
- There appear to be *many* `xkb` remapping solutions (just google it), but it took me a while to find this simple one.
- For more info, see: https://wiki.archlinux.org/index.php/X_KeyBoard_extension
- `Xmodmap` is no longer the recommended way to modify keys on X, use `xkb` as here.


