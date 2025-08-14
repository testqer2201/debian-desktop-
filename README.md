#!/bin/bash

# ===================================================================================
#
#        Enhanced KDE Plasma Desktop Setup Script for Debian-based Systems
#
#   This script installs the full KDE Plasma desktop environment along with
#   a curated list of essential applications, developer tools, and utilities.
#
#   Usage:
#   1. Make the script executable:  chmod +x setup_kde_desktop.sh
#   2. Run the script with sudo:    ./setup_kde_desktop.sh
#
#   ** WARNING: Run this on a minimal or server installation. **
#   ** Running it on a system that already has another desktop environment **
#   ** (like GNOME) may lead to conflicts. **
#
# =================================S==================================================

# --- SECTION 1: SCRIPT PREPARATION ---
# -----------------------------------------------------------------------------------
# Exit immediately if a command exits with a non-zero status.
set -e

# Check if the script is being run as root
if [ "$(id -u)" -ne 0 ]; then
  echo "This script must be run as root. Please use sudo." >&2
  exit 1
fi

echo "================================================="
echo "Starting Full KDE Desktop Environment Setup"
echo "================================================="


# --- SECTION 2: SYSTEM UPDATE & UPGRADE ---
# -----------------------------------------------------------------------------------
# First, we update the package list and upgrade existing packages to the latest versions.
echo "--> Updating and upgrading the system..."
apt update
apt upgrade -y
apt dist-upgrade -y


# --- SECTION 3: CORE KDE PLASMA DESKTOP ---
# -----------------------------------------------------------------------------------
# Install the full KDE Plasma desktop environment. `kde-full` includes the standard
# desktop, a wide range of applications, and extra add-ons.
echo "--> Installing KDE Plasma Desktop (kde-full)..."
# We use DEBIAN_FRONTEND=noninteractive to avoid prompts during installation.
DEBIAN_FRONTEND=noninteractive apt install -y kde-full


# --- SECTION 4: ESSENTIAL APPLICATIONS & UTILITIES ---
# -----------------------------------------------------------------------------------
# Install a selection of highly recommended applications and system tools.
echo "--> Installing essential applications and utilities..."
apt install -y \
    firefox-esr \
    thunderbird \
    vlc \
    gimp \
    inkscape \
    libreoffice-kde \
    gwenview \
    okular \
    partitionmanager \
    htop \
    neofetch \
    curl \
    wget \
    git \
    unzip \
    p7zip-full \
    flatpak \
    kde-config-flatpak \
    build-essential \
    kio-gdrive # Google Drive integration for Dolphin


# --- SECTION 5: DEVELOPER TOOLS (OPTIONAL) ---
# -----------------------------------------------------------------------------------
# Install common tools for software development.
echo "--> Installing common developer tools..."
apt install -y \
    python3-pip \
    python3-venv \
    openjdk-17-jdk \
    kate # Advanced KDE text editor

# Install Visual Studio Code
# We add the Microsoft GPG key and repository.
echo "--> Installing Visual Studio Code..."
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg
apt update
apt install -y code


# --- SECTION 6: MULTIMEDIA & FONTS ---
# -----------------------------------------------------------------------------------
# Install codecs for multimedia playback and additional fonts, including Microsoft's.
echo "--> Installing multimedia codecs and extra fonts..."
apt install -y \
    kubuntu-restricted-extras \
    libavcodec-extra \
    libdvd-pkg \
    fonts-firacode \
    fonts-powerline

# This command is needed to configure the `libdvd-pkg` package.
dpkg-reconfigure libdvd-pkg


# --- SECTION 7: SYSTEM CONFIGURATION & CLEANUP ---
# -----------------------------------------------------------------------------------
# Enable Flatpak by adding the Flathub repository.
echo "--> Configuring Flatpak..."
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Clean up unnecessary packages and cached files.
echo "--> Performing system cleanup..."
apt autoremove -y
apt clean



exit 0
