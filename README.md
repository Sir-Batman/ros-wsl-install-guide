# ROS on Windows Installation Instructions

By Scott Chow

Last Updated: August 31, 2019

This guide takes you through the installation process of Ubuntu 18.04 and ROS Melodic on Windows 10 using Windows Subsystem for Linux.

Overall guide based heavily on the tutorial found [here](https://janbernloehr.de/2017/06/10/ros-windows).
Adapted and tested for Ubuntu 18.04 and ROS Melodic running on Windows 10.

---

## Install Windows Subsystem for Linux

This section is based on the Microsoft guide [Here](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

1. Open PowerShell as Administrator and run: 

```Powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

2. Restart the computer when prompted

## Install Ubuntu Distro

1. Open the Microsoft Store and choose Ubuntu 18.04. The link to Ubuntu 18.04 is provided [here](https://www.microsoft.com/en-us/p/ubuntu-1804-lts/9n9tngvndl3q) for convenience.

2. Press `Get`

3. Initialize the distro by following the instructions from [here](https://docs.microsoft.com/en-us/windows/wsl/initialize-distro) copied below

4. Launch the distro from the Microsoft Store page and wait for it to finish installing...

5. Enter in a username and password when prompted.

6. You are now done installing Ubuntu

## Optional Quality of Life Tips and Tricks

Here are some Tips and Tricks that are optional, but will make life much easier to know how to do.

1. Disable Windows bell in shell. Windows plays an annoying bell during tab completion as discussed [here](https://github.com/Microsoft/WSL/issues/715#issuecomment-344888457). One can disable this bell by the following command:

```bash
echo "set visualbell" >> ~/.vimrc
```

Note for vim users, you can also disable bells for vim with the following command:

```bash
echo "set bell-style visible" >> ~/.inputrc
```

Restart the client for the change to take effect.

2. Copy and Paste

Pasting into the shell can be done by right-clicking the shell. 

There are some references to an "Enable Ctrl-Shift-C/V" for copy and paste option found [here](https://devblogs.microsoft.com/commandline/copy-and-paste-arrives-for-linuxwsl-consoles/), but it was not available on my version. I would suggest trying to see if these instructions work for your version. 

a) Right-click the top bar on the shell window.

b) Go to `Properties`

c) Under the `Options` tab, check the box `Use Ctrl+Shift+C/V as Copy/Paste`.

3. Locating the Linux folder in Windows Explorer. 

Per this [post](https://askubuntu.com/questions/759880/where-is-the-ubuntu-file-system-root-directory-in-windows-subsystem-for-linux-an), the `/` directory in the Linux shell can be found by navigating to `C:\Users\<username>\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState` in Windows Explorer. 

Your `/home` or `~` directory will be located in `C:\Users\<username>\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs\home`. This will let you open files/folder from the Windows-side outside the shell.

If you do not see the `AppData` folder on Windows, you will need to enable `Hidden items` by:

a) Open a Windows Explorer window

b) Under the `View` Tab, under the `Show/hide` heading,  Check the `Hidden Items` checkbox.


## Install ROS

Installing ROS is mostly the same as the default instructions found [here](http://wiki.ros.org/melodic/Installation/Ubuntu) with the exception of adding keys (step 2 below).

1. Setup your computer to accept software from packages.ros.org. 

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```
2. Add Keys (Note this differs from the instructions page because `dirmngr` is broken per [this](https://github.com/Microsoft/WSL/issues/3286) Github issue.)

```bash
curl -sL "http://keyserver.ubuntu.com/pks/lookup?op=get&search=0x<INSERT KEY HERE>"  | sudo apt-key add

```

To get the key, go to the installation guide [here](http://wiki.ros.org/melodic/Installation/Ubuntu) and Look at the Set up your keys step. You should see something like:

```bash
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

As of the writing of this guide, this key is `C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654`, so replace `<INSERT KEY HERE>` with the key so that it looks like the following:

```bash
curl -sSL 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC1CF6E31E6BADE8868B172B4F42ED6FBAB17C654' | sudo apt-key add -

```

3. Update the list of repositories, install the full version of ROS Melodic, and setup `rosdep` for installing system dependencies:
```bash
sudo apt update
sudo apt install -y ros-melodic-desktop-full
sudo rosdep init
rosdep update
```

4. Source ROS Melodic so that it can be accessed in this window:

```bash
source /opt/ros/melodic/setup.bash
```

Note: you will have to run this command every new window you create. If you want this command to be run automatically, add it to your `.bashrc` file with the following line:

```bash
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

5. Check to make sure everything works by running:
```bash
roscore
```
No errors should appear and it should say that ROS is running.

## Install XServer

To support GUIs such as RViz, an X Server is required.

1. Install VcXsrv from [here](https://sourceforge.net/projects/vcxsrv/).

2. Configure WSL to use it:

```bash
echo "export DISPLAY=:0" >> ~/.bashrc
source ~/.bashrc
```

3. Launch VcXsrv from the start menu. All default settings work except uncheck `Native opengl`.

## Installing Turtlebot

1. Install the turtlebot and its related packages
```bash
sudo apt install ros-melodic-turtlebot3*
source ~/.bashrc
```

2. Bring up Turtlebot 3 in Gazebo to check everything is working.
```bash
export TURTLEBOT3_GAZEBO=waffle
roslaunch turtlebot3_gazebo turtlebot3_simulation.launch
```

and in a new terminal:
```bash
export TURTLEBOT3_GAZEBO=waffle
roslaunch turtlebot3_fake turtlebot3_fake.launch
```

3. You should see the turtlebot moving about in some capacity in Rviz and Gazebo
