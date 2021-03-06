# Bring a real terminal to Sublime Text

The first cross platform terminal for Sublime Text.

<table>
    <tr>
        <th>Unix shell</th>
        <th>Cmd.exe</th>
    </tr>
    <tr>
        <td width="50%">
            <a href="https://user-images.githubusercontent.com/1690993/41784539-03534fdc-760e-11e8-845d-3d133a559df5.gif">
                <img src="https://user-images.githubusercontent.com/1690993/41784539-03534fdc-760e-11e8-845d-3d133a559df5.gif" width="100%">
            </a>
        </td>
        <td width="50%">
            <a href="https://user-images.githubusercontent.com/1690993/41786131-a625d870-7612-11e8-882d-f1574184faba.gif">
                <img src="https://user-images.githubusercontent.com/1690993/41786131-a625d870-7612-11e8-882d-f1574184faba.gif" width="100%">
            </a>
        </td>
    </tr>
    <tr>
        <th>Terminal in panel</th>
        <th></th>
    </tr>
    <tr>
        <td width="50%">
            <a href="https://user-images.githubusercontent.com/1690993/41784748-a7ed9d90-760e-11e8-8979-dd341933f1bb.gif">
                <img src="https://user-images.githubusercontent.com/1690993/41784748-a7ed9d90-760e-11e8-8979-dd341933f1bb.gif" width="100%">
            </a>
        </td>
        <td width="50%">
        </td>
    </tr>
</table>

This package is heavily inspired by [TerminalView](https://github.com/Wramberg/TerminalView). Compare with TerminalView, this has

- Windows support
- continuous history
- easily customizable themes
- unicode support
- 256 colors support
- better xterm support
- terminal panel
- [imgcat](https://www.iterm2.com/documentation-images.html) support (PS: it also works on Linux / WSL)

## Installation

Package Control.

### Getting started

- run `Terminus: Open Default Shell in View`


## User Key Bindings

You may find these key bindings useful. To edit, run `Preferences: Terminus Key Bindings`.

- toggle terminal panel
```json
{ 
    "keys": ["alt+`"], "command": "toggle_terminus_panel" 
}
```

- open a terminal view at current file directory
```json
{ 
    "keys": ["ctrl+alt+t"], "command": "terminus_open", "args": {
        "config_name": "Default",
        "cwd": "${file_path:${folder}}"
    }
}
```
or by passing a custom `cmd`
```json
{ 
    "keys": ["ctrl+alt+t"], "command": "terminus_open", "args": {
        "cmd": "ipython",
        "cwd": "${file_path:${folder}}"
    }
}
```

(check the details for the arguments of `terminus_open` below)

### Alt-Left/Right to move between words (Unix)

- Bash: add the following in `.bash_profile` or `.bashrc`
```
bind '"\e[1;3C": forward-word'
bind '"\e[1;3D": backward-word'
```

- Zsh: add the following in `.zshrc`
```
bindkey "\e[1;3C" forward-word
bindkey "\e[1;3D" backward-word
```

Some programs, such as julia, does not recognize the standard keycodes for `alt+left` and `alt+right`. You could
bind them to `alt+b` and `alt+f` respectively
```json
[
    { "keys": ["alt+left"], "command": "terminus_keypress", "args": {"key": "b", "alt": true}, "context": [{"key": "terminus_view"}] },
    { "keys": ["alt+right"], "command": "terminus_keypress", "args": {"key": "f", "alt": true}, "context": [{"key": "terminus_view"}] }
]
```


## User Commands in Palette

- run `Preferences: Terminus Command Palette` and add, for example

```json
    {
        "caption": "Terminus: Open Default Shell at Current Location",
        "command": "terminus_open",
        "args"   : {
            "config_name": "Default",
            "cwd": "${file_path:${folder}}"
        }
    }
```
or by passing custom `cmd`

```json
    {
        "caption": "Terminus: Open iPython",
        "command": "terminus_open",
        "args"   : {
            "cmd": "ipython",
            "cwd": "${file_path:${folder}}",
            "title": "iPython"
        }
    }
```

(check the details for the arguments of `terminus_open` below)


## Note to advance users

- A terminal could be opened using the command `terminus_open` with

```py
window.run_command(
    "terminus_open", {
        "config_name": None,  # the shell config name, use "Default" for the default config
        "cmd": None,          # the cmd to execute if config_name is None
        "cwd": None,          # the working directory
        "working_dir": None,  # alias of "cwd"
        "env": {},            # extra environmental variables
        "title": None,        # title of the view
        "panel_name": None,   # the name of the panel if terminal should be opened in panel
        "tag": None           # a tag to identify the terminal
    }
)
```

The fields `cmd` and `cwd` understand Sublime Text build system [variables](https://www.sublimetext.com/docs/3/build_systems.html#variables).


- the setting `view.settings().get("terminus_view.tag")` can be used to identify the terminal and 

- keybind can be binded with specific tagged terminal

```json
    {
        "keys": ["ctrl+alt+w"], "command": "terminus_close", "context": [
            { "key": "terminus_view.tag", "operator": "equal", "operand": "YOUR_TAG"}
        ]
    }
```

- text can be sent to the terminal with

```py
window.run_command(
    "terminus_send_string", 
    {
        "string": "ls\n",
        "tag": None        # or the tag which is passed to "terminus_open"
    }
)
```

If `tag` is not provided, the text will be sent to the first terminal found in the current window.

- `Terminus` as a build system. For example, the following can be added to your project settings to allow
"SSH to Remote" build system.

```json
{
    "build_systems":
    [
        {
            "cmd":
            [
                "ssh", "user@example.com"
            ],
            "name": "SSH to Remote",
            "target": "terminus_open",
            "working_dir": "$folder"
        }
    ]
}
```

## FAQ

### Terminal panel background issue

If you are using DA UI and your terminal panel has weired background color,
try playing with the setting `panel_background_color` in `DA UI: Theme
Settings`.

<img src="https://user-images.githubusercontent.com/1690993/41728204-31a9a2a2-7544-11e8-9fb6-a37b59da852a.png" width="50%" />

```json
{
    "panel_background_color": "$background_color"
}
```

### Acknowledgments

This package won't be possible without [pyte](https://github.com/selectel/pyte), [pywinpty](https://github.com/spyder-ide/pywinpty) and [ptyprocess](https://github.com/pexpect/ptyprocess).
