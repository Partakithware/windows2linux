# windows2linux - View this file as Raw ------>
Run windows exe/msi via Double-click on Linux (Ubuntu Tested) via Proton.

I'm a longtime Windows user and a C grade coder :/ (Not the best but I accomplish my goals)
I did my best to remember what I did to make it work as mine works fine, I use nemo and can Click-and-go use exes.

This will install all windows programs to the same compat-folder, meaning if you install .NET or something like directx everything can use it unlike in steam (Using steam you could just get Explorer++.exe and run stuff if not this method)

I wanted it to feel more natural as if I was on Windows and could just run things so I came up with this method.
(Hopefully this covers everything as at first I had some issues and I'm not sure if I covered my bases but I tried)

The Github proton seems to be missing some of the wine stuff installing proton on steam does, so you will still need steam installed to get those files at the very least.

========================================================================
Alright, here's a comprehensive, step-by-step listing of your solution for running Windows .exe and .msi files on Debian/Ubuntu Linux using Proton, from start to finish, with all the corrections and refinements:

Patakith's Solution for Running Windows Executables and Installers on Linux:

Goal: To enable seamless double-click execution of Windows .exe and .msi files on Debian/Ubuntu Linux using Proton, with a consistent Windows environment.

Steps:

Install Dependencies:

Open a terminal and run the following command to install the necessary packages:

sudo apt update && sudo apt install -y python3 git make cmake g++ clang \
    libdbus-1-dev libgl1-mesa-dev libvulkan-dev libx11-dev libxrandr-dev \
    libxinerama-dev libxcursor-dev libxi-dev libxext-dev libfreetype6-dev \
    libfontconfig1-dev libxcomposite-dev libxdamage-dev libxfixes-dev \
    libxrender-dev libsm-dev libudev-dev libwayland-dev libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev libpulse-dev libopenal-dev wine64 wine32
Obtain and Build Proton:

Navigate to your home directory: cd ~

Create a script named install_proton.sh and add the following:

#!/bin/bash

# Exit on error
set -e

echo "Updating package lists and installing dependencies..."
sudo apt update && sudo apt install -y python3 git make cmake g++ clang \
    libdbus-1-dev libgl1-mesa-dev libvulkan-dev libx11-dev libxrandr-dev \
    libxinerama-dev libxcursor-dev libxi-dev libxext-dev libfreetype6-dev \
    libfontconfig1-dev libxcomposite-dev libxdamage-dev libxfixes-dev \
    libxrender-dev libsm-dev libudev-dev libwayland-dev libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev libpulse-dev libopenal-dev

echo "Cloning Proton repository..."
git clone --recurse-submodules https://github.com/ValveSoftware/Proton.git

cd Proton

echo "Running bootstrap script..."
./bootstrap.sh

echo "Building Proton (this may take some time)..."
./build.sh

echo "Proton build complete! -END OF SCRIPT"

================================================================================
Make the script executable: chmod +x install_proton.sh

Run the script: ./install_proton.sh (This will create the "Proton" folder in your home directory).

================================================================================

Install Proton 9.0 (or your desired version) from Steam:

Open Steam and navigate to your Library.
In the "Games & Tools" section, search for "Proton 9.0" (or the version you want).
Install it.
Copy the Proton files:

Copy the contents of /home/yourusername/.steam/steam/steamapps/common/Proton 9.0 (Beta)/files/ to /home/yourusername/Proton/files/. (Replace yourusername with your actual username)

================================================================================

Create the Wrapper Script:

Create a script named wrapperinit.sh in your ~/Proton/ directory (or a directory in your $PATH, like ~/bin).

Add the following content:

#!/bin/bash

# Debugging: Check if a path is received
echo "Received path: '$1'"
export STEAM_COMPAT_DATA_PATH=~/Windows/
export STEAM_COMPAT_CLIENT_INSTALL_PATH="/home/yourusername/.local/share/Steam"

# Debugging: List the file (if a path is provided)
if [ -n "$1" ]; then
    ls -l "$1"
else
    echo "Error: No path provided."
    echo "Press Enter to close..."
    read
    exit 1 # Exit with an error code
fi

# Check if the file is an MSI
if [[ "$1" == *.msi ]]; then
    echo "Running MSI installation..."
    /home/yourusername/Proton/proton run msiexec /i "$1"
else
    echo "Running EXE file..."
    /home/yourusername/Proton/proton run "$1"
fi
================================================================================

Make the script executable: chmod +x /home/yourusername/Proton/wrapperinit.sh (Replace yourusername with your actual username)

================================================================================

Create the .desktop File:

Create a file named Proton.desktop in ~/.local/share/applications/.

Add the following content:

[Desktop Entry]
Name=Run Windows File
Exec=bash -c "/home/yourusername/Proton/wrapperinit.sh %f"
Type=Application
MimeType=application/x-ms-dos-executable;application/x-msdownload;application/x-msi;
NoDisplay=false
Terminal=false
Icon=steam
Categories=Utility;Application;

================================================================================

Replace yourusername with your actual username.

Set Default Application Associations:

Run the following commands:


xdg-mime default Proton.desktop application/x-ms-dos-executable
xdg-mime default Proton.desktop application/x-msi

================================================================================

Update Desktop Database:

Run: update-desktop-database ~/.local/share/applications/

================================================================================

Key Points:

Run the install_proton.sh script directly in your home directory.
The correct method for obtaining proton is to clone the github repo, then copy the entire "files" directory from a steam install of proton, then to build the github repo.
The install script that Maxwell provided automates the installation process.
The STEAM_COMPAT_DATA_PATH environment variable creates a consistent 'C drive' environment in ~/Windows/.
The STEAM_COMPAT_CLIENT_INSTALL_PATH environment variable is also very important.
The bash -c in the .desktop file is crucial for setting the environment variables correctly.
This solution has been tested and confirmed to work on Ubuntu by Maxwell Wingate.
Credit:

I found that this method provides a seamless way to run Windows executables and installers on Linux using Proton.

If it's not working after this I apologize I may have forgotten a step I did. I think this will do it though. Happy Trails and Good Luck.
