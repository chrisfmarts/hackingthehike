+++
author = "Chris Marts"
categories = ["Open Source", "Linux"]
date = ""
description = ""
image = ""
title = "Fedora 36 Guide"
type = "post"

+++
This getting started guide will help quickly set up Fedora 35 after a fresh install. It is my personal cheat sheet and I reference it for my own system.

This guide may seem familiar for those that referenced the [Fedora 34 Guide](https://www.hackingthehike.com/2021/03/19/fedora-34-getting-started-after-install-guide/). Many of the commands will be identical. In some instances -y has been added to expedite commands.

Fedora is one of my favorite Linux distros and for [good reason](https://www.hackingthehike.com/2020/06/10/why-fedora/). The developers have built a solid, sleek, stable Linux distribution that works as well on laptops as it does on desktops and servers.

## Final Release

Fedora 35 was released for general use on November 2, 2021. Fedora 35 is the most recent stable release.

## How To Use This Guide

Open a command line, a la Terminal, by pressing the Super key and type _Terminal_. Copy the commands and paste into the Terminal. Pasting in the Terminal requires Ctrl + Shift + V or right click and select paste.

## Explore Gnome 41

Gnome 40 and 41 are a mild departure from previous Gnome releases.

* ![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-04-1024x576.png)
* ![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-05-1024x576.png)
* ![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-06-1024x576.png)

The desktop now features a dock at the bottom of the screen and horizontal workspaces. Laptop users can appreciate the three finger gestures. A three finger swipe up will show the Activities overview. Another three finger swipe up will display all available applications. Workspaces can be easily switched with a three finger swipe to the left or right.

A single tap of the Super key also reveals the dock while a double tap of the Super key opens the app drawer.

## Get Up to Date

Before installing updates, consider adding the following DNF flags. These will enable delta RPM, fastest mirror, and a maximum of 10 parallel downloads. The cat command will display the dnf.conf file.

    echo 'fastestmirror=1' | sudo tee -a /etc/dnf/dnf.conf
    echo 'max_parallel_downloads=10' | sudo tee -a /etc/dnf/dnf.conf
    echo 'deltarpm=true' | sudo tee -a /etc/dnf/dnf.conf
    cat /etc/dnf/dnf.conf

The results from dnf.conf should match those below.

    # [main]
    # gpgcheck=1
    # installonly_limit=3
    # clean_requirements_on_remove=True
    # best=False
    # skip_if_unavailable=True
    # fastestmirror=1
    # max_parallel_downloads=10
    # deltarpm=true

Linux distros have frequent updates, patches, and feature upgrades. Its always important to keep software up to date for security reasons but its even more important with a fresh install. If its been a while since the distro was released, some of the packages may be feeling a little exposed. Get them up to date before doing anything else.

    sudo dnf -y upgrade --refresh

Reboot after updating the software.

## Update Firmware

Some manufacturers support firmware and UEFI updates on Linux. The following commands will find devices with firmware, search for possible firmware updates, and install the updates.

    sudo fwupdmgr get-devices
    sudo fwupdmgr refresh --force
    sudo fwupdmgr get-updates
    sudo fwupdmgr update

## Enable RPM Fusion

RPM Fusion offers packages that cannot be offered in the official Fedora repos due to various reasons, such as non free or proprietary licensing. These instructions are kindly borrowed from [RPM Fusion](https://rpmfusion.org/Configuration). An RPM is also available that will enable these repos. This command enables free and non free repos.

    sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

Update the system before installing the tainted free and tainted non free repos. Tainted free provides DVD playback support. Tainted non free contains non FOSS software and hardware drivers.

    sudo dnf upgrade --refresh
    sudo dnf groupupdate core
    sudo dnf install -y rpmfusion-free-release-tainted
    sudo dnf install -y rpmfusion-nonfree-release-tainted 
    sudo dnf install -y dnf-plugins-core
    sudo dnf install -y *-firmware 

## Flatpak and Snap

With Fedora 35, the 50 most popular Flatpaks are available. The full Flathub offering is available by adding their respective repository.

    flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
    flatpak update

Snap support is easily enabled as well.

    sudo dnf install -y snapd
    sudo ln -s /var/lib/snapd/snap /snap # for classic snap support
    sudo reboot now

## Multimedia

VLC handles most multimedia but I still prefer to add codec support whenever possible.

For DVD playback, install the libdvdcss package. Requires the tainted free repo.

     sudo dnf install -y libdvdcss 

The following command will adds packages for gstreamer enabled applications.

    sudo dnf install -y gstreamer1-plugins-{bad-*,good-*,ugly-*,base} gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel ffmpeg gstreamer-ffmpeg
    sudo dnf install -y lame* --exclude=lame-devel 

This package install complement packages needed by some applications for sound and video.

    sudo dnf -y groupupdate sound-and-video

Enable OpenH264 support in Firefox.

    sudo dnf config-manager --set-enabled fedora-cisco-openh264
    sudo dnf install -y gstreamer1-plugin-openh264 mozilla-openh264

After installing OpenH264, open Firefox and navigate menu > add ons > Plugins. Enable the OpenH264 plugin. [Verify](https://mozilla.github.io/webrtc-landing/pc_test.html) the plugin is working correctly.

Upgrade again.

    sudo dnf group upgrade --with-optional Multimedia

## Install Apps via Terminal

Many of these applications are found in the Software Center. Software installations can be stacked together to save time and effort. For example, if you wish to install VLC and GIMP, the command can be executed as _sudo dnf install -y install vlc gimp_ .

Geary is a simple to use email client

    sudo dnf install -y geary

Gnome Tweak Tool makes it easy to modify the system and accompanying Gnome Extensions helps to manage installed extensions.

    sudo dnf install -y gnome-tweaks gnome-extensions-app

VLC is a popular media player.

    sudo dnf install -y vlc 

Archive tools

     sudo dnf -y install unzip p7zip p7zip-plugins unrar 

GIMP

    sudo dnf -y install gimp 

QBittorrent – Bittorrent client

    sudo dnf -y install transmission 

Spotify – Music playing service

    sudo dnf config-manager --add-repo=https://negativo17.org/repos/fedora-spotify.repo  
    sudo dnf -y install spotify-client 

Dropbox – cloud storage

    sudo dnf -y install dropbox nautilus-dropbox 

Audacity – audio editor

    sudo dnf -y install audacity 

YouTube production program

    sudo dnf -y install obs-studio 

GParted – partition management utility

    sudo dnf -y install gparted 

Improved fonts. These are the same fonts that Pop!_OS includes.

    sudo dnf install -y fira-code-fonts 'mozilla-fira*' 'google-roboto*'

Open Gnome Tweaks to tweak the fonts.

![](https://www.hackingthehike.com/wp-content/uploads/2021/03/Gnome-Tweaks-fonts-1024x733.png)Adjusting fonts with Gnome Tweak Tool

## Install via Software Center

Fedora ships with the Gnome Software Center as the primary home for installing applications. Gnome Software is relatively easy to use with software categories.

![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-01-1024x697.png)Enable Third Party Repos

Upon first use, a prompt appears at the top to enable Third Party Software Repositories.

![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-03.png)Enable Third Party Repos

These repos make it easy to install Google Chrome and other software directly from the Gnome Software Center.

Explore the categories at the bottom to discover different software. The magnifying glass in the top left opens the search function.

![](https://www.hackingthehike.com/wp-content/uploads/2021/09/Fedora35-02-1024x697.png)Gnome Software Center in Fedora 35

The Software Center showcases "Editor's Choice" titles. I'm not really sure how these titles are chosen. Many of them appear to be popular Linux titles.

![](https://www.hackingthehike.com/wp-content/uploads/2021/03/GSC1-1024x735.png)Application view in Gnome Software Center

Clicking a software title will open a page with screenshots, a summary of the software, version information, and the option to install. The source menu in the top right corner displays different places or ways to install the software.

![](https://www.hackingthehike.com/wp-content/uploads/2021/03/GSC-Sources.png)Sources in Gnome Software Center

In the case of Geary, I installed from Fedora. Installing from Fedora uses the RPM package. That's often the best way to install the software with the smallest footprint and fastest load times. Software can also be installed from Flatpak here. For more information on Flatpaks, check out the [Best Flatpaks in 2021](https://www.hackingthehike.com/2021/03/08/best-flatpaks-for-linux/). When in doubt, install from the default source.

## Browsers

I recommend using Firefox, hands down. For a Chromium based browser, consider [Brave](https://brave.com/download/), [Opera](https://www.opera.com/download), or [Vivaldi](https://vivaldi.com/download/). Chrome is also easily available.

Brave is a privacy focused browser known for its advertising crypto called BAT.

    sudo dnf install dnf-plugins-core
    sudo dnf config-manager --add-repo https://brave-browser-rpm-release.s3.brave.com/x86_64/
    sudo rpm --import https://brave-browser-rpm-release.s3.brave.com/brave-core.asc
    sudo dnf install brave-browser

Chromium is the open source project that Chrome is built on.

    sudo dnf install -y chromium

OR if you want to install full blown Google-ized Chrome. If you want to install a different version, change the package from -stable to -beta or -unstable.

    sudo dnf install fedora-workstation-repositories
    sudo dnf config-manager --set-enabled google-chrome
    sudo dnf install -y google-chrome-stable

## Non Repo Software

AppImageLauncher is an AppImage management utility. It makes it easier to track which AppImages have been installed on your system, helps to integrate them, and allows the user to easily remove them. It is available for [download](https://github.com/TheAssassin/AppImageLauncher/releases) as an AppImage, DEB, RPM, and Tarball.

[Bitwarden](https://bitwarden.com/) is an open source password manager akin to LastPass or 1Pass. It integrates nicely on Android and has autofill ability. It syncs passwords across Android, Linux, ,iOS, OS X, and Windows. Bitwarden can be installed through AppImage, DEB, Flatpak, RPM, and Snap. DEB and RPM packages do not auto update. CLI Tools are available along with browser extensions for most browsers.

[Simplenote](https://simplenote.com/) is a free note taking app developed by Automattic (the Wordpress people). Simplenote syncs across virtually everything and supports AppImage, DEB, Flatpak, and RPM. Part of the appeal of Simplenote is markdown support but the other cool thing is Wordpress support. Simplenote can be tied to a Wordpress account for easy Wordpress posting.

[Standard Notes](https://standardnotes.org/) is a lot like Simplenote. Its free. Its open source. Its crazy cross platform. If the Automattic/Wordpress relationship makes you uncomfortable or maybe you just don't need it, try Standard Notes instead. Standard Notes has one big advantage over Simplenote: extensions. Yes, the extensions do cost but they effectively turn Standard Notes into a mini office suite with support for markdown, rich text, LaTeX, and HTML to backup support for Dropbox, Google Drive, and OneDrive. Standard Notes can be installed from AppImage, Flatpak, and Snap.

Try a [secure email service](https://www.hackingthehike.com/2020/06/25/protonmail-vs-tutanota/) like Protonmail or Tutanota. Gmail, Outlook, and other free email services offer tons of storage. In exchange, they mine your data to show you ads. Secure email services encrypt your data so they are unable to read it. Tutanota offers a free AppImage and Flatpak desktop client while Protonmail enables IMAP with a paid plan.

#### Flatpak

Many Gnome applications are available as Flatpaks now. Fedora even defaults to Flatpak in Gnome Software. Check out the list of [Best Flatpaks](https://www.hackingthehike.com/2021/03/08/best-flatpaks-for-linux/) for more options.

#### Snap

Snap has become a popular option for packaging Linux applications as well. I'm not a huge gamer but [Urban Terror](https://snapcraft.io/urban-terror) is a great online FPS.

Snaps do not display in Gnome Software. Consult [Snapcraft](https://snapcraft.io/) to find more.

## Extensions

**With the move to Gnome 40, many extensions are not available or are not yet working.** This will be updated as extensions become available.

I don't recommend using many [Gnome Extensions](https://extensions.gnome.org/). They allow easy customization of the desktop but more than a couple may hurt system performance.

#### Dash to Dock

[Dash to Dock](https://extensions.gnome.org/extension/307/dash-to-dock/) converts the bottom dock style bar into a proper application dock. It can be tweaked to autohide, extend to the edges, and moved sides.

#### GSConnect

[GSConnect](https://extensions.gnome.org/extension/1319/gsconnect/) works with [KDE Connect](https://play.google.com/store/apps/details?id=org.kde.kdeconnect_tp) on Android. Before I go any further: Do not install KDE Connect on your Linux system. It will interfere with GSConnect. GSConnect is the Gnome implementation of KDE Connect. The Android app works with both.

![](https://www.hackingthehike.com/wp-content/uploads/2020/06/GSConnect2.png)GSConnect

That said, GSConnect allows notification syncing so that your phone calls, messages, and app notifications will all appear on your desktop. Phone battery life is also displayed. It goes beyond just notifications though.

![](https://www.hackingthehike.com/wp-content/uploads/2020/06/GSConnect.png)GSConnect Messaging

Text messages can be responded to or even started from the desktop. It also opens up file transfers between devices. While I like Google Messages for RCS and web access, it doesn't compare to the features GSConnect offers.

## Change the Desktop Environment

Fedora uses Gnome by default with several other desktop environment (DE) spins available. These DEs are still available after installing Fedora.

Available desktop environments can be found with this command:

    dnf grouplist -v

The command will yield a list similar to this:

* Fedora Custom Operating System (custom-environment)
* Minimal Install (minimal-environment)
* Fedora Server Edition (server-product-environment)
* Fedora Workstation (workstation-product-environment)
* Fedora Cloud Server (cloud-server-environment)
* KDE Plasma Workspaces (kde-desktop-environment)
* Xfce Desktop (xfce-desktop-environment)
* LXDE Desktop (lxde-desktop-environment)
* LXQt Desktop (lxqt-desktop-environment)
* Cinnamon Desktop (cinnamon-desktop-environment)
* MATE Desktop (mate-desktop-environment)
* Sugar Desktop Environment (sugar-desktop-environment)
* Deepin Desktop (deepin-desktop-environment)
* Development and Creative Workstation (developer-workstation-environment)
* Web Server (web-server-environment)
* Infrastructure Server (infrastructure-server-environment)
* Basic Desktop (basic-desktop-environment)

Using the package name from the list above, install the desktop enviroment with a simple dnf install substituting kde-desktop-environment with the preferred DE.

    sudo dnf -y install @kde-desktop-environment

DEs can be switched at the login screen or alternatively, with the Desktop Switcher tool.

    sudo dnf -y install switchdesk switchdesk-gui

Open the Desktop Switcher and select the preferred DE.

## Conclusion

Do you have any preferred tweaks for Fedora?