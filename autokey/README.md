# Emacs Keybindings in Chrome under Awesome

Configure [Awesome WM](https://awesomewm.org)
with [AutoKey](https://github.com/autokey/autokey)
for some Emacs Keybindings in Chrome.

I used to start Chrome with something like this:
```
Exec=env GTK_KEY_THEME=Emacs google-chrome-stable %U
```
Unfortunately Chromium 116+ no longer respects gtk-key-theme-name
setting:

  https://issues.chromium.org/issues/40279679

I workaround this using AutoKey:

  https://github.com/autokey/autokey

AutoKey intercepts the key strokes at the input layer and can 
prevent Chrome from ever seeing them. It can also do this based
on a window filter so I can have it translate a key combination
to soemthing else only for Chrome windows.

E.g. I'm not going to configure Chrome to handle ctrl+k differently
(I was unable to with [Shortkeys](https://chromewebstore.google.com/detail/shortkeys-custom-keyboard/logpjaacgmcbpdkdchjiaagddngobkck?hl=en)).
Instead, I'll prevent ctrl+k from reaching Chrome
and send it a different set of keys instead.

## Keybindings

I don't need all Emacs keybindings to work. Just the following.
```
GNU      CUA
--------------------------------
ctrl+a   home
ctrl+e   end
alt+f    ctrl-right
alt+b    ctrl-left
ctrl+k   select-all in front of cursor and cut
```
The key bindings on the left are from GNU and I have them in my muscle
memory. The keybindings on the right are the GNU equivalent in CUA.
The term CUA bindings comes from IBM's Common User Access standard,
which was later adopted by Microsoft.

On Fedora, Chrome uses
[aura](https://www.chromium.org/developers/design-documents/aura-desktop-window-manager)
which dropped GTK support.

In macOS, Chrome inherits system text field behaviors from Cocoa where
Emacs-like bindings are still part of the native NSTextView
implementation.

## Autokey

```
sudo dnf install autokey-gtk
```

Per the bottom of my [rc.lua](../rc.lua),
a [script](../autorun.sh) starts autokey-gtk
when awesome starts. Restart aweseome.
```
echo 'awesome.restart()' | awesome-client
```
I then see an `A` icon in the tray. When I click
the icon I can use the GUI to create the following
new scripts.

### ctrl+a

- Name: `chrome_ctrl_a_to_home`
- Hotkey: `<ctrl>+a`
- WindowFilter: `google-chrome-beta.Google-chrome-beta`
- Body:
```python
keyboard.send_keys("<home>")
```

### ctrl+e

- Name: `chrome_ctrl_e_to_end`
- Hotkey: `<ctrl>+e`
- WindowFilter: `google-chrome-beta.Google-chrome-beta`
- Body:
```python
keyboard.send_keys("<end>")
```

### alt+f

- Name: `chrome_alt_f_word_forward`
- Hotkey: `<alt>+f`
- WindowFilter: `google-chrome-beta.Google-chrome-beta`
- Body:
```python
keyboard.send_keys("<ctrl>+<right>")
```
### alt+b

- Name: `chrome_alt_b_word_back`
- Hotkey: `<alt>+b`
- WindowFilter: `google-chrome-beta.Google-chrome-beta`
- Body:
```python
keyboard.send_keys("<ctrl>+<left>")
```

### ctrl+k

- Name: `swallow_ctrl_k_in_chrome`
- Hotkey: `<ctrl>+k`
- WindowFilter: `google-chrome-beta.Google-chrome-beta`
- Body:
```python
keyboard.send_keys("<shift>+<end>")
keyboard.send_keys("<ctrl>+c")   # copy into clipboard
keyboard.send_keys("<delete>")
```
