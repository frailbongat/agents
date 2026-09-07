# Project icon

`agents-hub.png` is the Paseo project icon for `~/.agents`. One amber hub, three
slate satellites: one library, many agents.

- Structure: slate `#7E8794`, links at 0.6 opacity
- Accent: amber `#E0A02E`, hub only
- 1024x1024 RGBA, transparent, glyph spans 80% wide and 71% tall

The app theme is auto, so the icon has to hold on dark and light. No pure white,
no pure black, no background plate.

## Re-render

Edit `agents-hub.html`, then run this from this directory.

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --hide-scrollbars \
  --default-background-color=00000000 --window-size=1024,1024 \
  --screenshot=agents-hub.png "file://$PWD/agents-hub.html"
```

After any edit, view the PNG at 40px on `#15161A` and on `#FFFFFF`. The three
satellites have to stay separate dots and the amber hub has to stay the loudest
element. Keep every stroke at 40px or thicker at this size or it disappears when
downscaled.
