I. Prerequesites
================

**Note**: Every time you see `host$` – this means that command should be executed on **HOST** macOS computer. Every time you see `box$` – this means that command should be executed on virtual **GUEST** Linux OS.

**Note**: If you found mistake or something from written below is not working, then open issue and specify exact step which fails. I.e. `Step B.1.ii`.

A. Initial setup
----------------

1. Download and install software.

    - Vagrant: https://www.vagrantup.com
    - VirtualBox: https://www.virtualbox.org

    **Note**: If you have troubles with VirtualBox installation addressed blocked Kernel Extensions, then have a look on this SE question: [VirtualBox 5.1.28 fails to install on MacOS 10.13 due to KEXT security](https://apple.stackexchange.com/questions/301303/virtualbox-5-1-28-fails-to-install-on-macos-10-13-due-to-kext-security)

2. Verify Vagrant installation.

    ```bash
    host$ vagrant --version
    ```

3. Clone this repository.

    ```bash
    host$ git clone https://github.com/vgorloff/Android-On-Swift.git
    host$ cd Android-On-Swift
    ```

B. Setting up Ubuntu box
------------------------

**Note**: Most of the steps similar to [Getting Started](https://www.vagrantup.com/intro/getting-started/index.html) from Vagrant website.

1. Setup Ubuntu box.

    1. Download Ubuntu box image.

        **Note**: Usually you need to download box image once.

        ```bash
        host$ vagrant box add ubuntu/bionic64
        ```

        **Note**: You can explore trending boxes here:

        - https://app.vagrantup.com/boxes/search
        - https://app.vagrantup.com/ubuntu/boxes/bionic64

    2. (Optionall) Verify downloaded Ubuntu image in local folder.

        ```bash
        host$ ls -l ~/.vagrant.d/boxes
        ```

    3. Start box and connect via SSH.

        ```bash
        host$ vagrant up
        host$ vagrant ssh
        ```

        **Note**: Box will be created in directory specified in VirtualBox settings. Detalis in [this post](http://www.thisprogrammingthing.com/2013/changing-the-directory-vagrant-stores-the-vms-in/).

    4. (Optionall) Verify Ubuntu version.

        ```bash
        box$ lsb_release -irc
        ```

    5. (Optionall) Explore synced folders:

        ```bash
        box$ ls -l /vagrant
        ```

        **Note**: You should see this `Readme.md` file inside Ubuntu Box.

    As result we have Ubuntu box running on macOS.

2. Review Box settings.

    Since we going to compile Swift. It is a good idea to review box's Memory and CPU settings to avoid situations like below.

    ```
    /usr/bin/ld.gold: out of memory
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    ```

    ```
    LLVM ERROR: out of memory
    ```

    1. Shutdown box.

        ```bash
        host$ vagrant halt
        ```

    2. Review and update Vagrantfile if needed. Increase CPU and Memory values if you have enough resources.

        ```ruby
        vb.memory = "5120"
        vb.cpus = "4"
        ```

    3. Start Box again.

        ```bash
        host$ vagrant up
        ```

    4. (Optionall) Save snapshot of Box.

        ```bash
        host$ vagrant snapshot save "01. After installing clean OS"
        ```

        **Note**: Under the hood it will save VirtualBox snapshot.

C. (Optional) Setting Up Visual Studio Code
-------------------------------------------

If you going to edit Ruby files, then it worth to install Visual Studio Code for macOS and Ruby plugin.

1. Visual Studio Code: https://code.visualstudio.com
2. Ruby language support: https://marketplace.visualstudio.com/items?itemName=rebornix.Ruby

D. Getting Sources
------------------

1. Get Swift sources.

    **Note**: Steps taken from official [Guide](https://github.com/apple/swift).

    ```bash
    host$ mkdir -p Sources/swift
    host$ cd Sources/swift

    host$ git clone https://github.com/apple/swift.git
    host$ ./swift/utils/update-checkout --clone
    ```

2. Get Android NDK.

    1. Download and Unpack archive.

        ```bash
        host$ curl -O https://dl.google.com/android/repository/android-ndk-r18b-linux-x86_64.zip
        host$ unzip -q -o android-ndk-*.zip
        ```

    2. If you prefer manual download, then visit https://developer.android.com/ndk/downloads/.

        **Note**: At a time of writing this text there was release named: `android-ndk-r18b-linux-x86_64.zip`

    3. Ensure that archive extracted to folder `Sources/android-ndk-r18b`.


3. Get ICU (International Components for Unicode).

    1. Download and Unpack archive.

        ```bash
        host$ curl -O http://download.icu-project.org/files/icu4c/63.1/icu4c-63_1-src.tgz
        host$ tar -xzf icu4c-63_1-src.tgz
        ```

    2. If you prefer manual download, then visit http://site.icu-project.org/download

        **Note**: At a time of writing this text there was release named `ICU4C 63.1` and downloadable archive with sources named `icu4c-63_1-src.tgz`.

    3. Ensure that archive extracted to folder `Sources/icu`.

    As result, file structure should be like below:

    ```
    ...
    Sources
        - android-ndk-r18b
        - icu
        - swift
    ...
    ```

4. Verify accessibility of sources inside Box.

    ```bash
    box$ ls -l /vagrant/Sources
    ```

5. Verify environment variables inside Box.

    ```bash
    box$ env | sort
    ```

    Should output:

    ```
    ...
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/vagrant/Sources/android-ndk-r18b
    PWD=/home/vagrant
    RUBYOPT=-W0
    SA_BUILD_ROOT_ANDK=/vagrant/Build/android-ndk
    SA_BUILD_ROOT_ICU=/vagrant/Build/icu
    SA_BUILD_ROOT_SWIFT=/vagrant/Build/swift-android
    SA_BUILD_ROOT=/vagrant/Build
    SA_INSTALL_ROOT_ANDK=/vagrant/Install/android-ndk
    SA_INSTALL_ROOT_ICU=/vagrant/Install/icu
    SA_INSTALL_ROOT_SWIFT=/vagrant/Install/swift
    SA_INSTALL_ROOT=/vagrant/Install
    SA_PATCHES_ROOT_ICU=/vagrant/Patches/icu
    SA_PATCHES_ROOT=/vagrant/Patches
    SA_PROJECTS_ROOT=/vagrant/Projects
    SA_SOURCES_ROOT_ANDK=/vagrant/Sources/android-ndk-r18b
    SA_SOURCES_ROOT_ICU=/vagrant/Sources/icu
    SA_SOURCES_ROOT_SWIFT=/vagrant/Sources/swift
    SA_SOURCES_ROOT=/vagrant/Sources
    SHELL=/bin/bash
    ...
    ```

E. Installing dependencies on Box
---------------------------------------

1. Install development packages and Verify Ruby and Rake version.

    ```bash
    host$ vagrant ssh

    box$ bash /vagrant/Scripts/Shell/bootstrap.sh
    ```

4. (Optionall) Take snapshot.

    ```bash
    host$ vagrant snapshot save "Initial Setup"
    ```

II. Usage
=========

Remaining process of compilling ICU and Swift, building and deploying sample projects automated via Rakefile.

```bash
box$ cd /vagrant/
box$ rake
```

**Note**: Some Rake targets need to be executed on `host`, but some on `box`. Don't mix up execution environment.
