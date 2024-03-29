# Steamdeck Dev

This repo contains the... things... necessary to get a working dev environment going on the SteamDeck.

Tested on the 256GB version.

## Installation/Usage

This requires a few simple pre-requisites:

1. You have to set a Sudo password
2. Ansible must be installed

### Pre-requisites

The fastest/least destructive way to get Ansible to be functioning on your Steamdeck is with [Miniconda](https://docs.conda.io/en/latest/miniconda.html). Miniconda is a streamlined version of Conda - an open source package management system and environment system for Linux, MacOS, and Windows. If you've used Python's `venv` - it's like that but for NodeJS, Python, and a series of other lanaguages.

To install Miniconda, download the [latest installer](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links) for Miniconda3 Linux 64-bit. Validate the sha256 hash after the installer downloads with the command `sha256sum Miniconda3-py*` and validating the output matches what the website lists as the proper hash. Run the installer (DO NOT USE SUDO), and restart your terminal.

Once Miniconda is installed and activated (verifiable by the command `conda env list`), install Ansible with pip `pip install ansible`.

## Running the Playbook

Once Conda and Ansible are installed, clone this repo and execute the playbook with `ansible-playbook main.yml --ask-become-pass`

## What does this do?

Broadly, it helps keep my 256GB Steam Deck in working order for development, side projects, and work as a general purpose workstation. I have a somewhat neat dock, external/portable monitor, and kb/mouse setup. Unfortunately, I'm not yet ready to completely ditch SteamOS for Ubuntu/Windows/[Something Else](https://www.pcgamesn.com/steam-deck/apple-macos-7-mod) and occassionally after SteamOS update pushes the system image changes and my carefully installed software is blown away.

This project is an answer - a way to continue to use my Steam Deck primarily as a workstation replacement, while still allowing for the gaming experience I've come to love.

## What does this do (really)?

First, we check to see the current status of the SteamOS Read Only with the Ansible builtin command module and `steamos-readonly status`, registering the output (which should be either `enabled`, signifying that the file system is in read-only mode; or `disabled`, signifying that the file system is read-write).

Depending on if the file system is in read-only or read-write mode, we can "Recover" after an update:

1. Change to the file system to read-write mode.
2. Inititalizes the pacman keyring
3. Populates the keyring with the initial set of keys

Regardless of if the file system is read-only or read-write, a set of packages are then installed:

* `terraform` - Infrastructure as Code tool
* `packer` - Hashicorp tool for image management
* `podman`/ `fuse-overlayfs` / `slirp4netns` - Container management/creation platform and tools
* `tmux` - Multi terminal windows/panes!
* `code`/`code-marketplace` - VS Code text editor and plugin marketplace
* `base-devel` - gcc, compilers, development things
* `globalprotect-openconnect` / `expect` - Gotta VPN sometimes
* `fortune-mod` - Helps with making my shells start off nice
* `stow` - Dotfile management
* `libfido2` / `opensc` / `ccid` / `pcsc-tools` - Yubikey things
* `xclip` - Clipboard management

The playbook goes on to enable the `pcscd` service (to read smart cards/
yubikeys), and copy over some VSCode setings/install various VSCode Plugins.

Lastly, `subuid`/`subgid` are configured to support rootless podman.



## Known Issues

### Existing files

Upon a fresh SteamOS update, some packages will fail to install, listing conflicting files:

- `cni-plugins: /opt/cni/bin/bandwidth exists in filesystem`
- `cni-plugins: /opt/cni/bin/bridge exists in filesystem`
- `cni-plugins: /opt/cni/bin/dhcp exists in filesystem`
- `cni-plugins: /opt/cni/bin/firewall exists in filesystem`
- `cni-plugins: /opt/cni/bin/host-device exists in filesystem`
- `cni-plugins: /opt/cni/bin/host-local exists in filesystem`
- `cni-plugins: /opt/cni/bin/ipvlan exists in filesystem`
- `cni-plugins: /opt/cni/bin/loopback exists in filesystem`
- `cni-plugins: /opt/cni/bin/macvlan exists in filesystem`
- `cni-plugins: /opt/cni/bin/portmap exists in filesystem`
- `cni-plugins: /opt/cni/bin/ptp exists in filesystem`
- `cni-plugins: /opt/cni/bin/sbr exists in filesystem`
- `cni-plugins: /opt/cni/bin/static exists in filesystem`
- `cni-plugins: /opt/cni/bin/tuning exists in filesystem`
- `cni-plugins: /opt/cni/bin/vlan exists in filesystem`
- `cni-plugins: /opt/cni/bin/vrf exists in filesystem`
- `containers-common: /etc/containers/registries.d/default.yaml exists in filesystem`
- `vpnc: /etc/vpnc/vpnc-script exists in filesystem`

The Playbook can be run with `filecleanup` var set to true to remove those files.

### Copilot Plugin

The Copilot plugin for VSCode requires authentication with Github - something that the default open vscode does not currently support. I'm investigating an alternative.

## Acknowledgements

-  Yay installation/fixes from Reddit User `zhengnanlee` [code](https://pastebin.com/QG0eT6tH).
