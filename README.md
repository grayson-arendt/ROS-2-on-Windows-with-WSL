
While dual-booting provides a more integrated ROS 2 experience, you can install and run ROS 2 Humble using Windows Subsystem for Linux (WSL). This guide will walk you through the steps.
## **Install Ubuntu 22.04 for WSL**

1. Open PowerShell as Administrator:
    - Search for PowerShell in the Start menu.
    - Right-click and select Run as Administrator.

2. Install Ubuntu 22.04:
```powershell
wsl --install -d Ubuntu-22.04
```

3. During the installation:
	- Create a username and password when prompted.
	- Once complete, your terminal will switch to a Bash shell, allowing you to run Linux commands.
  
## **Install ROS 2 Humble**

1. Add ROS 2 repositories:
```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

2. Update and install ROS 2:
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install ros-humble-desktop ros-dev-tools
```

## **Optimize WSL for Performance**
### Check CPU Cores and RAM
1. Open PowerShell and check the number of CPU cores:
```powershell
wmic cpu get NumberOfCores
```

2. Check your total RAM in GB:
```powershell
[math]::Round((([string](systeminfo | findstr /C:"Total Physical Memory")).Split(":")[-1].Trim() -replace "[^0-9]", "") / 1024, 2)
```

### Configure WSL Resources
1. Open the WSL configuration file in Notepad:
```powershell
notepad $env:USERPROFILE\.wslconfig
```

2. If prompted, confirm to create the file. Add the following settings (adjust values based on your hardware):
```txt
[wsl2]
memory=16GB # Allocate 16GB of RAM (50% of total RAM)
processors=8 # Use 8 logical processors (Total number of cores)
swap=8GB # Set 8GB of swap space (25% of total RAM)
```

3. Save and close the file, then restart WSL:
```powershell
wsl --shutdown
wsl
```

## **Set Up ROS 2 Environment**
1. Navigate to the Home Directory:
```bash
cd ~
```

2. Source the ROS 2 environment:
```bash
source /opt/ros/humble/setup.bash
```

3. Add this line to your `.bashrc` file so it runs every time you open a terminal:
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

## **Clone and Build the Lunabotics-2025 Repository**
1. Follow the [Lunabotics-2025 GitHub repository](https://github.com/grayson-arendt/Lunabotics-2025) instructions to:
	- Clone the repository.
	- Install dependencies.
	- Build and run the project.

2. To speed up the build process:
	- Limit workers during the build: `colcon build --parallel-workers 4`
	- Set multi-threading options: `export MAKEFLAGS="-j4"`

### Notes:
- Experiment with `--parallel-workers` and `MAKEFLAGS` values for faster builds without crashes.
- If performance issues come up, adjust WSL resources in `.wslconfig`.
- `c++: fatal error: Killed signal terminated program cc1plus` was a frequently occurred error I encountered, which is due to limited resources. Decrease the `--parallel-workers` and `MAKEFLAGS` numbers.
- You will also not be able to run the code in `manual` mode, since it requires creating a new gnome-terminal from the launch file. Instead, use `ros2 launch lunabot_bringup simulation_launch.py robot_mode:=autonomous`.
