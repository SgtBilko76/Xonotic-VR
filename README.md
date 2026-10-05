# Xonotic VR

[![Sponsor](https://img.shields.io/badge/Sponsor-SgtBilko76-ea4aaa?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/SgtBilko76)

**[Xonotic](https://xonotic.org/) — "The Free and Fast Arena Shooter" — standalone in VR on Meta Quest and Pico.**

Xonotic is a fast-paced open-source arena shooter: crisp movement, in-your-face action, and an
armory of 9 core weapons and 16 full weapons, each with a primary and a UT-like secondary fire. Deathmatch, Capture The
Flag, Clan Arena, Nexball, Freeze Tag and Multiplayer Race are all here — easy to learn, hard to
master. This port runs the real game natively in the headset, with room-scale head tracking,
a weapon held in your hand, and full online multiplayer.

👉 **[Download the latest APK from Releases](https://github.com/SgtBilko76/Xonotic-VR/releases)**

---

## Install

The release APK is self-contained — the Xonotic 0.8.6 game data is bundled, so there is nothing
else to download.

| | |
|---|---|
| Headsets | Meta Quest 2 / 3 / 3S / Pro, Pico (developed and tested on Quest 3) |
| Download | ~1.2 GB |
| Free space needed | ~3 GB (the data is unpacked to `/sdcard/XonoticVR`, ~1.8 GB) |

**With SideQuest:** connect the headset, open SideQuest and drag the `.apk` onto it.

**With adb:**

```bash
adb install -r Xonotic-VR-full-release.apk
```

On first launch the app asks for storage permission and unpacks the game data — this takes a few
minutes and only happens once. The game then starts by itself. Find it under
**Apps → Unknown Sources → Xonotic VR**.

## Controls

Defaults for right-handed play (set `cl_righthanded 0` to swap hands).

| Aim hand (right) | |
|---|---|
| Trigger | Fire |
| Grip | Secondary fire |
| Thumbstick ↑ | Zoom |
| Thumbstick ↓ | Crouch |

| Off hand (left) | |
|---|---|
| Thumbstick | Move |
| Trigger | Jump |
| Grip | Grappling hook |
| Thumbstick click | Free to bind (`JOY2`) |

| Buttons | |
|---|---|
| A | Next weapon |
| B | Previous weapon |
| X | Use |
| Y | Scoreboard |
| Right thumbstick click | Recenter the view |
| Left menu button | Menu / Escape |

Aiming is done with the controller — the weapon points where your hand points, and the game's own
crosshair is drawn along that aim. Turning is snap by default (45°); see below for smooth turning.

**In menus** the thumbsticks move the cursor, A confirms and B goes back. For typing, set
`vr_keyboard 1` to get a grid keyboard on Y (right stick click = space, left stick click =
backspace).

## Settings

Everything is a normal Xonotic/DarkPlaces console variable. The VR defaults live in
`/sdcard/XonoticVR/data/vr.cfg` on the headset.

### Comfort and feel

| Cvar | Default | What it does |
|---|---|---|
| `vr_yawmode` | `0` | `0` snap turning, `1` smooth turning |
| `cl_comfort` | `45` | Snap turn angle in degrees |
| `vr_turnspeed` | `120` | Smooth turn speed (degrees/second) |
| `cl_walkdirection` | `0` | `0` move relative to your head, `1` relative to the off hand |
| `vr_worldscale` | `39` | Game units per metre — raise to feel smaller, lower to feel bigger |
| `vr_6dof` | `1` | Positional head tracking (lean and duck for real) |
| `vr_viewkick` | `0` | Weapon/damage kick applied to the view; `0` is the comfortable choice |
| `cl_righthanded` | `1` | `0` aims with the left controller |
| `vr_weaponpitchadjust` | `-20` | Tilt of the weapon in your hand, in degrees |
| `vr_hudscale` | `0.55` | Size of the HUD |

### Performance

| Cvar | Default | What it does |
|---|---|---|
| `vr_supersampling` | `0.9` | Render resolution multiplier (applied at startup) |
| `vr_foveation` | `1` | Fixed foveated rendering: `0` off … `3` high. Higher = more GPU headroom, blurrier edges |
| `vr_refreshrate` | `72` | Display refresh rate in Hz |
| `vr_multiview` | `1` | Draws both eyes in one pass — leave this on, it is a large win |
| `vr_novis` | `0` | `1` ignores the map's precomputed visibility and draws everything (much slower) |

Quest Games Optimizer users: the app clamps the eye buffer to the panel-native baseline on
purpose, so QGO's resolution setting has no effect — use `vr_supersampling` instead.

### Making your own settings stick

`vr.cfg` is rewritten by the app whenever a release ships new VR defaults, so personal edits there
can be overwritten. `commandline.txt` is only ever written once, so put your own file in the chain
instead:

```bash
adb shell "echo 'vr_supersampling 1.1' >> /sdcard/XonoticVR/data/myconfig.cfg"
adb shell "echo '-xonotic -basedir /sdcard/XonoticVR -nohome +exec vr.cfg +exec myconfig.cfg' > /sdcard/XonoticVR/commandline.txt"
```

## Multiplayer

The server browser works as it does on desktop, and the port uses the standard Xonotic 0.8.6 data,
so you can join public servers and play against desktop players. Bots work offline too — start a
match from the menu.

## Troubleshooting

**The sky or background shows through walls.** Set `vr_novis 1` (costs framerate, but is a sure
workaround) and please open an issue with the map name.

**A weapon looks washed out / too white.** Lower `r_reflectcube_neutral` (try `0.1`, or `0` for no
reflection at all). Raise it toward `1` if weapons look flat instead.

**The framerate is poor.** Try `vr_foveation 2`, then `vr_supersampling 0.8`. If you are running
other apps in the background, close them — the headset shares one GPU.

**It starts into a black screen or cannot find the game data.** Check that
`/sdcard/XonoticVR/data` contains the `.pk3` files; if it is empty, reinstall the full APK.

## Building from source

The Android app lives on `master`; the VR engine (a fork of
[DarkPlaces](https://gitlab.com/xonotic/darkplaces)) lives on the **`darkplaces-quest-vr`** branch
of this repository, and is expected at `../xonotic/darkplaces` relative to the project.

Requirements: Android SDK with NDK 27 and CMake 3.22, and a JDK.

```bash
./gradlew assembleRelease          # engine-only APK (~11 MB, needs game data on the headset)
scripts/build-full-apk.sh          # self-contained APK with the game data bundled
```

For an engine-only build, copy the data from the official
[Xonotic 0.8.6 release](https://dl.xonotic.org/xonotic-0.8.6.zip) onto the headset:

```bash
adb shell mkdir -p /sdcard/XonoticVR/data
adb push Xonotic/data/*.pk3 /sdcard/XonoticVR/data/
adb push Xonotic/key_0.d0pk /sdcard/XonoticVR/
```

`scripts/profile-round.sh` and `scripts/thread-sampler.sh` capture CPU profiles from the headset.

## Credits and licence

- **[Xonotic](https://xonotic.org/)** by the Xonotic team — "free to play and modify under the
  copyleft GPLv3+ license"
- **DarkPlaces** engine by Ashley "LadyHavoc" Hale and contributors
- **QuakeQuest / OpenXR groundwork** by Simon "DrBeef" Brown
- **VR port** by Sgt.Bilko

Released under the GPL, like the game and engine it builds on.
