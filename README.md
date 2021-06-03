# Linux Development on Windows 10
Instructions for setting up Linux development tools on Windows 10 using WSL2.

## 1. Update Windows 10 [recommended]
Some of the instructions here are fairly new and may not work if Windows 10 is not on a somewhat recent build.
Check the WSL2 install instructions for specific requirements but you can open **Start**, type
'Windows update', and open the Windows Update settings. If Windows is up-to-date you are probably good to go.

## 2. Install Windows Subsystem for Linux 2 (WSL2)
Windows Subsystem for Linux 2 is latest version of WSL and is pretty much a VM running on Hyper-V. Follow the
[instructions here](https://docs.microsoft.com/en-us/windows/wsl/install-win10#manual-installation-steps) under
**Manual Installation Steps** for steps to get WSL2 installed.

### Ensure virtualization is enabled:
If you get an error during the WSL2 setup similar to `Error: 0x80370102 The virtual machine could not be started 
because a required feature is not installed.` you may need to double-check that you have virtualization enabled
in your computer's BIOS settings. See the [troubleshooting page here](https://docs.microsoft.com/en-us/windows/wsl/troubleshooting#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed)
for additional help

### Linux distribution choices:
When you get to **Step 6** of the install instructions you will be asked to pick a Linux distribution to install.
I would recommend using **Ubuntu 18.04** or **Ubuntu 20.04** as these seem to be the most stable and have the
best support for external packages currently (as of June 2021). If you prefer a Linux distribution you can pick
something else but you'll need to adapt the remaining instructions here as most of the commands are for Ubuntu.

## 3. Install Windows Terminal [recommended]
Windows Terminal is a terminal tool from Microsoft with better support for Linux and other non-Windows shells. I
recommend that you install and use Windows Terminal to reduce the chance of hitting some terminal interaction issue.
Download and install the .msixbundle file from [here](https://github.com/microsoft/terminal/releases/latest).

## 4. Test WSL2
Open Windows Terminal and open a new tab to Ubuntu.

![image](https://user-images.githubusercontent.com/4840146/120707287-fde4a980-c46e-11eb-90e1-3e659ecfb972.png)

When the terminal opens to Bash run the following command and make sure packages update successfully.

```
sudo apt update
```

## 5. Setup Git & GitHub access in Ubuntu
In the Ubuntu terminal run the following commands to setup git and an SSH key for GitHub:

```bash
mkdir -p ~/.ssh
ssh-keygen -o -a 102 -t ed25519 -f ~/.ssh/id_ed25519_github -C "username@example.com/Device"
sudo apt install -y git
echo '
# Github
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_ed25519_github' >> ~/.ssh/config 
git config --global user.email "your.username@examlpe.com"
git config --global user.name "Your Name"
```

Now you can add your public key to GitHub to authorize access:

```bash
cat ~/.ssh/id_ed25519_github.pub
```

Copy the output and paste it in to the **New SSH Key** dialog on [this GitHub settings page](https://github.com/settings/keys)

After saving your public key to GitHub test your GitHub access with the following command:

```bash
ssh -T git@github.com
```

You should get a message similar to `Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`

## 6. Install Development Tools [recommended]
The following are additional tools that are usually required or helpful for development on Linux:

```bash
sudo apt update && \
sudo apt install -y \
    bash-completion \
    build-essential \
	curl \
    dnsutils \
    htop \
    iputils-ping \
	man \
	mtr-tiny \
	nano \
    tig \
    tldr \
    traceroute \
	wget \
    vim
```

## 7. Install Python [recommended]
Run the following commands to install Python in Ubuntu. You can change the version number as-needed.

```bash
export PYTHON_VERSION=3.8

sudo add-apt-repository -y ppa:deadsnakes/ppa
sudo apt install -y "python$PYTHON_VERSION" "python$PYTHON_VERSION-venv" "python$PYTHON_VERSION-dev"
mkdir -p "$HOME/bin"
ln -f -s "/usr/bin/python$PYTHON_VERSION" "$HOME/bin/python"
ln -f -s "/usr/bin/python$PYTHON_VERSION" "$HOME/bin/python3"
echo '
PATH="$HOME/bin:$PATH"
' >> ~/.bashrc
source ~/.bashrc
```

You should then be able to test Python and check the installed version number with

```bash
python -V
```

## 8. Install Golang [recommended]
Run the following commands to install Golang in Ubuntu. You can change the version number as-needed.

```bash
export VERSION=1.14

cd /tmp
wget -O "go$VERSION.linux-amd64.tar.gz" "https://dl.google.com/go/go$VERSION.linux-amd64.tar.gz"
sudo tar -C /usr/local -xf "go$VERSION.linux-amd64.tar.gz"
echo '
PATH="/usr/local/go/bin:$HOME/go/bin:$PATH"
' >> ~/.bashrc
source ~/.bashrc
```

You should then be able to test Golang and check the installed version number with

```bash
go version
```
