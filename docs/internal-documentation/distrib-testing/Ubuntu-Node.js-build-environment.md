# Ubuntu Node.js Build Environment

The build process takes over 1GB of RAM. A machine with at least 2GB of RAM is recommended to build Zonemaster-GUI.

The requirements to build the Zonemaster-GUI distribution zip file are Node.js
and npm. Below are instructions to create such a build environment on Ubuntu.

Node.js and npm are available from the [Node.js] official website. The required
Node.js version is 16. The process has been tested on Ubuntu 20.04, which we use
here.

1. Make a clean installation of Ubuntu 20.04.

2. Update the package database.
   ```sh
   sudo apt-get update
   ```

3. Install Node.js by using [NVM], a node version manager.
   ```sh
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
   ```

4. After installation, log out and log in again to handle [known issue], or just:

   ```sh
   source ~/.bashrc
   ```

5. Install the Node.js version that supports Zonemaster GUI
   ```sh
   nvm install 16.18.0
   ```

6. Switch to the previously installed version
   ```sh
   nvm use 16.18.0
   ```

[known issue]:                          https://github.com/nvm-sh/nvm#troubleshooting-on-linux
[Node.js]:                              https://nodejs.org/en/
[NVM]:                                  https://github.com/nvm-sh/nvm
