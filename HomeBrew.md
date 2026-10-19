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
