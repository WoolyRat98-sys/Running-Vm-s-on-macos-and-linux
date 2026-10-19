# Homebrew — Installation & Quick Guide (macOS)

This file contains a complete, copy-paste-friendly set of instructions to install Homebrew on a MacBook (Intel or Apple Silicon), plus verification, common post-install tweaks, troubleshooting, and useful commands.

> NOTE: These instructions follow Homebrew's official installer flow. Always review any script you download before running it.

---

## Quick summary (one-liners)

- Install Homebrew (official installer):
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
****Prerequisites****

1. A Mac running a supported version of macOS (see Homebrew docs for exact compatibility).

2. (optional) Command Line Tools for Xcode (developer tools) are recommended — required for building some formulae.

3. Internet connection for downloading Homebrew and packages.

4. Sufficient privileges to install to the system prefix (/opt/homebrew on Apple Silicon, /usr/local on Intel).

---

****Post-install: add Homebrew to your shell environment
The installer prints the lines you need to add. Common, recommended options:****

Apple Silicon (default prefix /opt/homebrew)

```sh
# Apply now in current session:
eval "$(/opt/homebrew/bin/brew shellenv)"

# Persist for future logins (zsh is default on modern macOS):
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```
Intel macs (default prefix /usr/local)

```sh
eval "$(/usr/local/bin/brew shellenv)"
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
If you use bash or another shell, add the same line to ~/.bash_profile, ~/.profile, or the shell-specific profile.
```
To verify the path is correct for the current shell:

```sh
which brew
brew --prefix
```

---

****Verify the installation
Run:****

```sh
brew --version
brew doctor
brew update
```
brew --version shows the installed Homebrew version.

brew doctor gives suggestions and warns about possible problems.

brew update updates Homebrew metadata.

Install a test package to confirm everything works:

```sh
brew install wget
wget --version
```
Remove the test package if you like:

```sh
brew uninstall wget
```

---

**Troubleshooting -- common errors & fixes**

"brew: command not found" after install:

Ensure you added ```eval "$(.../brew shellenv)"``` to your shell profile and reloaded your shell or log out/log in.
Run ```eval "$(/opt/homebrew/bin/brew shellenv)"``` (or ```/usr/local/bin)``` manually to test.

Permission errors while installing / writing to prefix:

The installer generally sets correct permissions. Avoid changing ownership of system directories unless you understand consequences.

If permissions are wrong, re-run the installer or follow Homebrew instructions printed by the installer.

"xcrun: error: invalid active developer path" or similar:

Install Command Line Tools: xcode-select --install

Slow brew update or network errors:

Check network/proxy settings. Use GIT_TRACE=1 git -C "$(brew --repo)" fetch to see detailed git network activity.

brew doctor shows warnings:

Read and resolve the warnings. Many are benign; some indicate conflicting tools.

"Permission denied" cloning Homebrew repos:

Ensure you are not running brew as root. Run as a normal user.

When in doubt, consult:

```sh
brew help
man brew
and the official docs: https://docs.brew.sh
```


****Uninstall Homebrew (if needed)****

Homebrew provides an uninstall script:

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```
This removes Homebrew and installed packages. Review the script before running.


