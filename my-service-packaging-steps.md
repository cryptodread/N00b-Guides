This file gives noob step by step instructions on how to create and deploy a service for EmbassyOS 0.3.0.

Steps:

Part 1: Build enviroment


1. Install docker
    curl -fsSL https://get.docker.com -o- | bash
    sudo usermod -aG docker "$USER"
    exec sudo su -l $USER
# UPDATE: had to rebuild docker, and this command didnt work. the following commands did:
    wget https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/docker-ce-cli_20.10.9~3-0~ubuntu-focal_amd64.deb
    sudo dpkg -i docker-ce-cli_20.10.9~3-0~ubuntu-focal_amd64.deb
    sudo docker run hello-world

2. Set buildx as the default builder
    docker buildx install
    docker buildx create --use

3. Enable cross-arch emulated builds in docker
    docker run --rm --privileged linuxkit/binfmt:v0.8

4. Install yq
    sudo snap install yq

5. Install essentials build packages
    sudo apt-get install -y build-essential openssl libssl-dev libc6-dev clang libclang-dev ca-certificates

6. Install Rust
    curl https://sh.rustup.rs -sSf | sh
    # Choose #1 (default install)
    source $HOME/.cargo/env

7. Install toml
    cargo install toml-cli

8. Install avahi client
    apt install libavahi-client-dev

9. Build and install embassy-sdk (0.3.0 branch)
    cd ~/ && git clone -b integration/0.3.0 https://github.com/Start9Labs/embassy-os.git
    cd embassy-os/appmgr/
    cargo install --path=. --bin=embassy-sdk

Now you are ready to build your first EmbassyOS service!

Part 2: Clone and Build the project locally, and install on EmbassyOS

10. Clone the project locally. Note the submodule link to the original project(s).
    git clone <URL_of_git_file_from_github>
    git submodule add <link_to_source_project>

11. Build the project
    make

12. Generate ssh key for Embassy
    ssh-keygen -t ed25519
    # Press Enter to leave filename as default
    # Press Enter to leave password empty
    # Press Enter to confirm password is empty
    less .ssh/id_ed25519.pub
    # Copy file contents to clipboard. This is your ssh pubkey.

13. On Embassy device, enter pubkey for ssh key
    # Click on Embassy in the menu
    # Click on SSH under SETTINGS
    # Click on + Add new key
    # Paste pubkey from clipboard 
 
14. Copy to Embassy Device
    ssh root@<LAN URL> <!-- Confirm you can ssh into your Embassy >
    exit <!-- Log out of Embassy >
    scp <service-name>.s9pk ssh root@<LAN URL>:/root/

15. Install on Embassy Device
    ssh root@<LAN URL>
    embassy-cli auth login
    embassy-cli package install <service-name>.s9pk


You should now be able to log into your EmbassyOS GUI and see your service!