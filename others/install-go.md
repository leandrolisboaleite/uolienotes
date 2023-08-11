---
description: Debian based script
---

# Install GO

LASTEST\_VERSION link may change overtime

```bash
#!/bin/bash

# Check if the script is run with root privileges
if [ "$EUID" -ne 0 ]; then
  echo "Please run this script as root or using sudo."
  exit 1
fi

# Set up the installation directory
INSTALL_DIR="/usr/local"

# Fetch the latest version of Go available
LATEST_VERSION=$(curl -sSL https://go.dev/dl/ | grep -oP 'go[0-9]+\.[0-9]+\.[0-9]+' | head -n 1)

if [ -z "$LATEST_VERSION" ]; then
  echo "Could not determine the latest version of Go. Aborting."
  exit 1
fi

# Set up the URL and installation path
GO_INSTALL_PATH="$INSTALL_DIR/$LATEST_VERSION"
GO_DOWNLOAD_URL="https://golang.org/dl/$LATEST_VERSION.linux-amd64.tar.gz"

# Download and install Go
echo "Downloading and installing Go $LATEST_VERSION..."
wget -qO- "$GO_DOWNLOAD_URL" | tar -C $INSTALL_DIR -xzf -

# Set up environment variables
echo "Setting up environment variables..."
echo "export PATH=\$PATH:$GO_INSTALL_PATH/bin" >> /etc/profile.d/golang.sh

# Update current shell session
export PATH=$PATH:$GO_INSTALL_PATH/bin

# Update the user's .bashrc
echo "Updating ~/.bashrc for persistence..."
echo "export PATH=\$PATH:$GO_INSTALL_PATH/bin" >> /etc/skel/.bashrc
source /etc/skel/.bashrc

# Output Go version to verify installation
echo "Go $LATEST_VERSION has been installed successfully."
go version

```
