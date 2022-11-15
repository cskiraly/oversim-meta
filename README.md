# OverSim and main code dependencies

OverSim was updated to run with newer Omnet++ and INET versions in 
https://github.com/inet-framework/oversim/releases/tag/v20190424

Running these old version together is challenging, thus this repository to make it easier.

## Dependencies

Quickstart for Ubuntu 20.04 is below. Follow https://doc.omnetpp.org/omnetpp/InstallGuide.pdf for others.

```
sudo apt-get update
sudo apt-get install build-essential clang lld gdb bison flex perl python3 python3-pip qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools libqt5opengl5-dev libxml2-dev zlib1g-dev doxygen graphviz libwebkit2gtk-4.0-37

python3 -m pip install --user --upgrade numpy pandas matplotlib scipy seaborn posix_ipc

sudo apt-get install libgmp3-dev
```

If you need the omnetpp GUI as well, this version requires Java 8. Newer versions of omnetpp were upgraded to support Java 11 and maybe newer.

### MacOS

If you have compile issues in the oversim submodule, check out the `osx` branch.

#### MacOS on M1

`arm64` is not supported in this version of omnetpp. Thus you need emulation. Start a shell with 
```
env /usr/bin/arch -x86_64 /bin/zsh --login
```

Dynamic linking also requires x86_64 libraries, which means you need an intel version of brew, and install dependencies again. In the above shell, install brew and dependencies.

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew install gmp qt5
```

You might need some exports
```
export PATH=$HOME/bin:/usr/local/bin:$PATH
export PATH="/opt/homebrew/opt/qt@5/bin:$PATH"
```

You might also need a working Java8 JDK
```
brew install openjdk@8
sudo ln -sfn /usr/local/opt/openjdk@8/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-8.jdk
export PATH="/usr/local/opt/openjdk@8/bin:$PATH"
export JAVA_HOME=$(/usr/libexec/java_home -v1.8.0_352)
```

## Compilation

```
cp omnetpp/configure.user.dist omnetpp/configure.user
```

Edit `omnetpp/configure.user` and set the following (or see the install guide to install dependencies for these too)
- WITH_OSG=no
- WITH_OSGEARTH=no

```
cd omnetpp
source setenv
./configure
make -j4
cd ..

cd inet
make makefiles
make -j4
cd ..

cd oversim
make makefiles
make -j4
cd ..
```

## Run

```
cd oversim/simulations
```

### Run the GUI
```
../src/OverSim
```

### Run without GUI (faster)
```
../src/OverSim -cChordLarge -uCmdenv

```

See http://www.oversim.org/wiki/OverSimUsage for more details.

### Run through Omnet++ Java GUI
The Java GUI is not part of the normal build process. It requires Java 8. To get it, download an [Omnet++ archive from releases](https://github.com/omnetpp/omnetpp/releases/tag/omnetpp-5.4.1) (OS and version specific) and copy the `/ide` folder to `omnetpp/ide`. Alternatively, follow instructions at https://github.com/omnetpp/omnetpp/issues/888 to build from source.

Once the IDE is installed, start with
`omnetpp`

In the IDE, create a workspace, than from the menu "Import" as existing projects the `inet` and then the `oversim` folders.


## FAQ
### GUI error: `Unknown Log format character '<'`

Remove `~./qtenvrc`
https://stackoverflow.com/questions/61497015/unknown-log-format-character
