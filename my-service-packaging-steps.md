# This file gives noob step by step instructions on how to create and deploy a service for EmbassyOS 0.3.0.

## Steps:

# Part 1: Build enviroment


## Install docker (this worked for Pop_OS! 22.04 LTS. your setup may need different commands)
    sudo apt update
    sudo apt install  ca-certificates  curl  gnupg  lsb-release
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io -y
    sudo systemctl status docker

## Set Permissions for docker
    sudo usermod -aG docker "$USER"
    exec sudo su -l $USER
## Test to make sure docker is working by running hello-world
    sudo docker run hello-world

## Set buildx as the default builder
    docker buildx install
    docker buildx create --use

## Enable cross-arch emulated builds in docker
    docker run --rm --privileged linuxkit/binfmt:v0.8

## Install yq
    sudo snap install yq

## Install essentials build packages
    sudo apt-get install -y build-essential openssl libssl-dev libc6-dev clang libclang-dev ca-certificates

## Install Rust
    curl https://sh.rustup.rs -sSf | sh
    # Choose #1 (default install)
    source $HOME/.cargo/env

## Install toml
    cargo install toml-cli

## Install avahi client
    apt install libavahi-client-dev

## Build and install embassy-sdk (0.3.0 branch)
    cd ~/ && git clone -b integration/0.3.0 https://github.com/Start9Labs/embassy-os.git
    cd embassy-os/appmgr/
    cargo install --path=. --bin=embassy-sdk

## Now you are ready to build your first EmbassyOS service!

# Part 2: Clone and Build the project locally, and install on EmbassyOS

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


# You should now be able to log into your EmbassyOS GUI and see your service!

# Now that you know how to install a service, you probably want to know how to actually create one using an existing application. 
# Here are some notes I took to help explain all the parts that I got stuck on while packaging my first app for the EmbassyOS 0.3.0

# IMPORTANT! Make sure the app you want to wrap has a repo, and you should clone that repo for yourself so you have a stable version to wrap. I cloned mine under the Start9 github account. 

1. Create github repo for wrapper
    Name it as follows "Start9/[application_name]-wrapper"

2. Create required files for wrapper
    assets/compat/config_spec.yaml
    assets/compat/config_rules.yaml
    docs/instructions.md
    .gitignore
    .gitmodules
    LICENSE
    Makefile
    README.md
    docker_entrypoint.sh
    icon.png
    manifest.yaml
    # you can use the hello-world-wrapper template if you wanna shortcut making these, but you will need to go back into each one and modify them

3. Link the repo of the application you are wrapping as a submodule to your newly created repo.
    git submodule add [url_to_app_repo]
    # you should see another folder appear in your repo folder structure with the name of the app @ branch-id (e.g. cwtch @ cbef826 )

4. Edit manifest.yaml to populate the Services Home Page, set the path for config and properties, and set backup and restore settings. 
    # You can edit manifest.yaml in a few ways 
    #     a) on github.com using the web UI
    #     b) vscode using the editor and checking it back in to the repo when finished
    # If you decide to edit locally and check in, the commands I've used to check in each time are as follows:
        git init
        git add . 
        git commit -m "insert comment here for whatever you changed"
        git push
    # A good example of a manifest.yaml is here: https://github.com/Start9Labs/mastodon-wrapper/blob/0.3.0/manifest.yaml

5. Edit the Dockerfile to create the container

6. Edit the Makefile to execute the docker build process

7. Edit the docker_entrypoint.sh to set up how the app will start on EmbassyOS

8. Replace the icon.jpg with the application logo/icon

9. Edit the README.md to give fellow developers instructions on how to use this wrapper

10. Edit the doc/instructions.md to give users instruction on how to use the app inside of EmbassyOS

11. Edit the assets/compat/config_spec.yaml to set the config fields for the app

12. Edit the assets/compat/config_rules.yaml to set up rules and config dependencies for the app

13. If setting up a properties page, then you will also need a configurator directory, and a src directory inside of it and a few other things:
    mkdir configurator
    mkdir configurator/src
    touch configurator/src/main.rs
    touch configurator/.gitignore
    touch configurator/Cargo.toml
    ## I think everything else is auto-generated. You will need to edit each file above as follows
    
    !Add this to main.rs
    # TBD - this is where i'm currently stuck
    ## Add this to .gitignore
    /target
    Cargo.lock
    ## Add this to Cargo.toml
        [package]
        name = "configurator"
        version = "0.1.0"
        authors = ["Aiden McClelland <me@drbonez.dev>"]
        edition = "2018"
        [dependencies]
        hyper = { version = "0.14.4", features = ["server", "http1", "http2", "tcp", "stream"] }
        tokio = { version = "1.4.0", features = ["full"] }
    ## Edit the docker_entrypoint.sh to include the following:
    configurator

    
