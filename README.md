# Manage and sync all your passwords and one-time-passwords on Linux

## Requirements

**Debian**

```bash
apt install git gnupg pass rofi pass-extension-otp zbar-tools
```

**Fedora**

```bash
dnf install git gnupg pass pass-otp rofi zbar
```

**Arch**

```bash
pacman -S git gnupg pass pass-otp rofi zbar
```

## Setup `pass`

1. You need a GPG key

   ```bash
   gpg --list-keys
   gpg --full-gen-key
   ```

2. Backup your GPG key

   ```bash
   # backup
   gpg -o private.gpg --export-options backup --export-secret-keys <gpg_key_fingerprint>

   # restore
   gpg --import-options restore --import private.gpg
   ```

3. Initialize password store

   ```bash
   pass init <gpg_key_fingerprint>
   pass git init
   ```

4. Manage passwords

   ```bash
   pass help
   ```

## Setup `passmenu`

1. Clone this repository

   ```bash
   git clone https://github.com/flolu/pass
   cd pass
   ```

2. Install `passmenu` script

   ```bash
   sudo cp ./passmenu /usr/bin/
   ```

3. Assign hotkey to `passmenu`

   > In GNOME it can be done like this:

   - Settings 🠖 Keyboard 🠖 Shortcuts 🠖 Custom 🠖 Add
   - Enter `passmenu` as the "Command"
   - And set a "Shortcut" (e.g. `Ctrl` + `Alt` + `P`)

4. Fix menu not focusing

   > If you start the menu and it won't be focused, you need to disable Wayland and switch to X11. But don't worry it's very easy:

   - Edit `/etc/gdm/custom.conf`
   - Change `#WaylandEnable=false` to `WaylandEnable=false`
   - Reboot your computer
   - If you have a laptop and your touch gestures are broken afterwards, just install [X11 Gestures](https://extensions.gnome.org/extension/4033/x11-gestures) extension and [touchegg](https://github.com/JoseExposito/touchegg#installation)

## Synchronization

I recommend syncing your passwords through an encrypted Git repository. You can read more about the reasoning in my [blog post](https://flolu.de/blog/linux-password-manager-and-sync).

1. Install [git-remote-gcrypt](https://spwhitton.name/tech/code/git-remote-gcrypt)

   - Debian: `apt install git-remote-gcrypt`
   - Fedora: `dnf install git-remote-gcrypt`
   - Arch: `pacman -S git-remote-gcrypt`

2. Add encrypted remote

   ```bash
   pass git remote add <remote_name> gcrypt::<remote_url>
   pass git config remote.<remote_name>.gcrypt-participants "<key_fingerprint>"
   pass git config remote.<remote_name>.gcrypt-signingkey "<key_fingerprint>"
   ```

3. Push changes

   ```bash
   pass git push origin master
   ```

4. Pull changes

   ```bash
   git clone gcrypt::<remote_url>
   git pull origin master
   ```
