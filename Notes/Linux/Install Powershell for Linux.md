# Install Powershell for Linux

Reference: https://docs.microsoft.com/en-us/powershell/scripting/install/install-ubuntu


```bash
# Update the list of packages
sudo apt update
# Install pre-requisite packages.
sudo apt install wget apt-transport-https software-properties-common
# Download the Microsoft repository GPG keys
curl -OL https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
# Register the Microsoft repository GPG keys
sudo dpkg -i packages-microsoft-prod.deb
# Update the list of packages after we added packages.microsoft.com
sudo apt update
# Install PowerShell
sudo apt install powershell
# Start PowerShell
pwsh
```