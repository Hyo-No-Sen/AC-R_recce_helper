🚗 [English](README.md) | [日本語](README_ja.md) 🚗
# ACR Recce Helper Manual

This tool lets you register "memos" at positions along a stage in Assetto Corsa Rally (ACR). When you pass a registered position, the memo is shown on screen and read aloud.

## 1. Requirements and Setup

- Windows 11, on the same PC as ACR
- Launch: run `AC-R_recce_helper.exe`
- If the settings file does not exist at first launch, `ac_telemetry_config.toml` is created automatically.

## 2. Basic Usage

1. Start ACR and enter a stage (start driving).
2. Launch the tool. The progress bar (a horizontal bar) and the memo display window appear. You can move them by dragging with the left mouse button.
3. As you drive, the vertical marker on the progress bar moves to the right.
4. **While paused** (it also works while driving), right-click the progress bar and choose "Add Memo" to register a memo at your current position.
5. On later runs, when you pass a registered position, the memo is shown and read aloud.

## 3. Right-Click Menu (Progress Bar and Memo Display Window)

| Item | Description |
|---|---|
| Add Memo | Opens the memo input window, with your current position as the initial value |
| End Race (Save Stage Distance) | Saves the distance of the stage (the `E` key also works). You need to do this once for each stage. |
| Open Memo Window on Pause | Automatically opens the memo input window when a pause is detected (not recommended, as the behavior is unstable) |
| Continuous Screenshots | Takes screenshots at regular distance intervals (used for positioning memos) |
| Read Aloud | Choose a voice. Select "No Voice" to turn off reading aloud. |
| Language / 言語 | Switches the display language (日本語 / English) |
| Exit | Quits the tool |

## 4. Registering and Editing Memos

- **Memo input window**: Set the memo text, display start position (m), display duration, font size, and target cars, then click OK to save.
  - You can adjust the display start position while looking at the captured images with "Pick Position from Screenshots".
  - The font size ranges from 0 to 128. 0 or a blank field uses the default size from the settings file.
  - Specify cars with "Select Cars". The memo is shown and read aloud only if it includes `all` or matches your current car.
- **Triangle icons on the progress bar**: These show the positions of registered memos.
  - Memos that match your current car are shown pointing up; memos that do not match are shown pointing down in gray.
  - Click an icon to edit it, right-click it for an Edit / Delete menu, or hover over it to see its contents.
- Memos are saved in `memo/<stage name>.jsonl`.

## 5. Showing and Reading Memos

- When you pass a registered position, the memo text is shown in the semi-transparent memo display window (drag to move it; drag its edges to resize it).
- If the content does not fit, the window expands only while the memo is shown (the direction can be changed in the settings file).
- At the same time, the text is read aloud with the selected voice.
- Emoji are shown in color (requires Pillow). When reading aloud, emoji are replaced with the readings in the dictionary (see the next section).

## 6. Emoji Reading Dictionary

- Define it in `emoji_dic/emoji_dic_<2 letters>.toml`. The 2 letters in the file name are only for people to tell the files apart.
- The dictionary that is used is the one whose `language` in `[general]` matches the current display language (the `language` value in `lang/<code>.toml`).

```toml
[general]
language = "English"

[dict]
"⬅️" = "left"
"➡️" = "right"
```

- Wrap values in quotation marks. Emoji that are not in the dictionary are passed to the speech engine as they are.
- A dictionary that cannot be loaded is ignored.

## 7. Loop Stages

For loop (circuit) stages, write the stage name (the name shown after "[コース]" in the console) in the settings file.
Note: As of ACR Ver0.6, the only loop stage is Rebinho, and it is already written in the settings file, so you can skip this section.

```toml
[stage]
loop_names = ["Stage name (Layout name)"]
```

- The tool automatically detects that you have completed a lap, and resets the progress bar and the record of passed memos.
- If the stage distance has been saved, the triangle icons of memos located before the start position are shown at the correct positions.
- Save the stage distance by driving one full lap and then clicking "End Race (Save Stage Distance)".

## 8. Main Settings (`ac_telemetry_config.toml`)

Edit and save the values; they take effect the next time the tool starts. The main items are as follows.

| Section | Key | Description |
|---|---|---|
| `[bar]` | `length_px` / `thickness_px` | Length and thickness of the progress bar (making it the same length as ACR's progress bar and placing it on top of it is also recommended) |
| `[language]` | `code` | Display language (`en` / `ja`). The default is English. |
| `[message_window]` | `render_mode` | `emoji` (default) / `classic` |
| | `expand_horizontal` / `expand_vertical` | Direction in which the window expands when the content does not fit (`left`/`right`, `up`/`down`) |
| | `width_px` / `height_px` / `transparency` | Size and transparency of the memo display window |
| `[message_window.emoji]` | `font_size` / `text_color` / `outline_color` / `outline_width_px` | Text settings for the emoji-compatible mode |
| `[tts]` | `voice` / `speaking_rate` / `audio_pitch` / `audio_volume` | Voice, speed, pitch, and volume for reading aloud |
| `[screenshot]` | `step_m` / `max_files`, etc. | Interval and number of files kept for continuous screenshots |
| `[stage]` | `loop_names` | List of loop stage names |

## 9. Folder Structure

You can easily create a language file for another language with a translation tool.
```
main.py / ac_telemetry_config.toml
lang/          Display language files (put a <code>.toml file here to add a language).
emoji_dic/     Emoji reading dictionaries
memo/          Memos (one .jsonl file per stage)
track_info/    Stage distance for each stage
screenshot/    Where continuous screenshots are saved
carModels.lst  List of cars
```

Place `lang/`, `emoji_dic/`, and the other folders in the same folder as the exe.

## 10. Troubleshooting

- **Memos are not shown or read aloud**: Check that the car matches (memos with a downward-pointing triangle are not targeted), and that the voice is not set to "No Voice".
- **The dictionary has no effect**: Check that the folder name `emoji_dic/`, the file name, and the `language` value match the language file in `lang/`.
- **Emoji look wrong in the memo input window**: This is by design. If you save the memo as it is, it should display correctly in the memo display window. However, some emoji may not be supported.
