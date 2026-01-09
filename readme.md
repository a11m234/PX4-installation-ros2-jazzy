# Installing PX4 and ROS 2 Jazzy on Ubuntu 24.04

This guide details the steps to install the PX4 Autopilot software and ROS 2 Humble Hawksbill on Ubuntu 22.04 LTS.

## Prerequisites

* **Ubuntu 24.04 LTS:** A fresh installation is recommended.
    * **Dual Boot:** Generally preferred for better performance, especially for simulations. https://www.youtube.com/watch?v=qq-7X8zLP7g.
    * **Virtual Machine (e.g., VirtualBox, VMware):** An alternative, but may have performance limitations. Ensure you allocate sufficient resources (CPU cores, RAM, disk space). https://www.youtube.com/watch?v=Hva8lsV2nTk .


## Step 1: Initial System Update and Essential Tools

First, ensure your system is up-to-date and install essential tools like `git` (for version control) and `curl` (for transferring data).

1.  **Open a Terminal:** Press `Ctrl+Alt+T` or search for "Terminal".
2.  **Update Package Lists:**
    ```bash
    sudo apt update
    ```
3.  **Install Git & Curl:**
    ```bash
    sudo apt install git curl -y
    ```
    *(The `-y` flag automatically confirms the installation)*
4.  **Upgrade Installed Packages:**
    ```bash
    sudo apt upgrade -y
    ```
5.  **(Recommended) Restart:** It's often good practice to restart after significant updates.
    ```bash
    sudo reboot now
    ```

## Step 2: Install PX4 Autopilot Source Code

We will clone the PX4 source code from GitHub and run the official setup script to install dependencies.

1.  **Navigate to Home Directory:** Open a new terminal after rebooting.
    ```bash
    cd 
    ```
    
2.  **Clone the PX4 Repository:** This command downloads the PX4 source code and its submodules.
    ```bash
    git clone https://github.com/PX4/PX4-Autopilot.git --recursive
    ```
    * `--recursive` is crucial to download necessary submodules. This might take some time.*
3.  **Run the Ubuntu Setup Script:** This script installs all required dependencies for building and simulating PX4.
    ```bash
    bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
    ```
    * *You might be prompted for your password multiple times.*
4.  **(Required) Restart Your System:** A restart is often necessary for all system changes (like group permissions) to take effect correctly.
    ```bash
    sudo reboot now
    ```

## Step 3: Build and Run PX4 SITL Simulation and QGroundcontrol 

Let's verify the PX4 installation by building and running a basic Software-In-The-Loop (SITL) simulation using Gazebo Garden (the default simulator).

1.  **Navigate to the PX4 Directory:** Open a terminal.
    ```bash
    cd ~/PX4-Autopilot
    ```
2.  **Build and Run Simulation:** This command compiles the PX4 firmware for SITL and launches the Gazebo simulator with an X500 quadcopter model.
    ```bash
    make px4_sitl gz_x500
    ```
    * *The first build will take a significant amount of time.*
    * *You should see the Gazebo simulator window open with a drone model.*
    * *You can stop the simulation by pressing `Ctrl+C` in the terminal where you ran the `make` command.*

3.  **Install QGROUNDCONTROL:**
    * Install the necessary requirements.
    ```bash
    sudo usermod -a -G dialout $USER
    sudo apt-get remove modemmanager -y
    sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
    sudo apt install libfuse2 -y
    sudo apt install libxcb-xinerama0 libxkbcommon-x11-0 libxcb-cursor-dev -y
    ```
    * After installing the requirements https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html go to this website and find the QGC.AppImage file and download it or otherwise
    * Download QGC from this link https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl.AppImage.
    * Now go to the folder where it is downloaded and Run below 
    ```bash
    chmod +x ./QGroundControl.AppImage
    ./QGroundControl.AppImage   # (or double click)
    ```

## Step 4: Install ROS 2 Humble Hawksbill

Now, we'll install ROS 2 Humble, the recommended ROS 2 version for Ubuntu 22.04.

1.  **Set Locale:** Ensure your system supports UTF-8.
    ```bash
    sudo apt update && sudo apt install locales -y
    sudo locale-gen en_US en_US.UTF-8
    sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
    export LANG=en_US.UTF-8
    ```
    * *You can check your locale settings with `locale`.*
2.  **Enable Ubuntu Universe Repository:**
    ```bash
    sudo apt install software-properties-common -y
    sudo add-apt-repository universe -y
    sudo apt update && sudo apt install curl -y
    ```
3.  **Add the ROS 2 Apt Repository:**
    ```bash
    sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | 
    sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
    ```
4.  **Update Apt Repositories Again:**
    ```bash
    sudo apt update
    ```
5.  **Upgrade System Packages (Optional but Recommended):** Ensure there are no conflicts.
    ```bash
    sudo apt upgrade -y
    ```
6.  **Install ROS 2 Desktop:** This includes ROS, RViz, demos, tutorials, and more.
    ```bash
    sudo apt install ros-humble-desktop -y
    ```
7.  **Install Development Tools:** Useful for building ROS 2 packages.
    ```bash
    sudo apt install ros-dev-tools -y
    ```
8.  **Source the Setup Script:** Make ROS 2 commands available in your current terminal and add it to your `.bashrc` to automatically source it in new terminals.
    ```bash
    source /opt/ros/humble/setup.bash
    echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
    ```
    * *Close and reopen your terminal or run `source ~/.bashrc` for the change to take effect in the current terminal.*
9.  **Install Essential Python Packages:** Required for ROS 2 tools and message generation.
    ```bash
    pip install --user -U empy==3.3.4 pyros-genmsg setuptools
    ```
    * *`--user` installs packages for the current user only.*
    * *`-U` upgrades if already installed.*

10. **Verify ROS 2 Installation:** Check the installed ROS distribution.
    ```bash
    echo $ROS_DISTRO
    ```
    * *This command should output: `humble`*
11. **Install ROS bridge:**
     * *To install the bridge for use with ROS 2 "Humble" and Gazebo Harmonic (on Ubuntu 22.04):
     ```bash
    sudo apt install ros-humble-ros-gzharmonic
     ```
12. **Running ROS bridge Camera:**
    
    ```bash
    ros2 run ros_gz_bridge parameter_bridge /camera@sensor_msgs/msg/Image@gz.msgs.Image
     ```
## Step 5: Install Micro XRCE-DDS Agent

The Micro XRCE-DDS Agent acts as a bridge, translating PX4's internal uORB messages into the DDS messages used by ROS 2.

1.  **Navigate to Home Directory (or preferred location):**
    ```bash
    cd ~
    ```
2.  **Clone the Agent Repository:** We clone a specific stable branch (`v2.4.2` in this case, check PX4 docs for current recommendations if needed).
    ```bash
    git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
    ```
3. ** There is an error in the Micro XRCE-DDS Agent:**
   * *You need to modify the line 98 and 99 of CMakeLists.txt so:
      set(_fastdds_tag 2.12.1)  -> to the below lines
    ```bash
    set(_fastdds_version 2.10)
    set(_fastdds_tag v2.10.6)
    ```

3.  **Build the Agent:**
    ```bash
    cd Micro-XRCE-DDS-Agent
    mkdir build
    cd build
    cmake ..
    make
    ```
4.  **Install the Agent:**
    ```bash
    sudo make install
    ```
5.  **Update Library Links:** Ensure the system can find the installed libraries.
    ```bash
    sudo ldconfig /usr/local/lib/
    ```

## Step 6: Running the Agent

To allow communication between PX4 SITL and ROS 2, you need to run the Micro XRCE-DDS agent.

1.  **Start the Agent:** Open a **new terminal** and run:
    ```bash
    cd PX4-Autopilot/
    make px4_sitl gz_x500
    ```
2. **Open a new terminal:** and run this
    ```bash  
    MicroXRCEAgent udp4 -p 8888
    ```
    * This starts the agent listening for UDP connections on port 8888, which is the default configuration for PX4 SITL.
    * You need to keep this terminal open while running the PX4 simulation and ROS 2 nodes that need to communicate with PX4.
      
## Step 7: Build and Run PX4-ROS 2 Communication Workspace

Now, let's set up a ROS 2 workspace to use the `px4_ros_com` package, which provides examples and tools for interacting with PX4 via ROS 2.

1.  **Create Workspace Directory:** Open a **new terminal**. Create a directory for your workspace (e.g., `ws_sensor_combined`) including the `src` subdirectory, and navigate into `src`.
    ```bash
    mkdir -p ~/ws_sensor_combined/src
    cd ~/ws_sensor_combined/src
    pip install --user setuptools==65.5.1
    ```
    **FOR UBUNTU 24.04 use this cmd only**
    ```bash
    python3.12 -m pip install --upgrade pip setuptools wheel
    ```
    * *Using a consistent naming convention like `~/ros2_ws/src` or `~/px4_ros_ws/src` can be helpful.*
2.  **Clone Required Repositories:** Clone the `px4_msgs` (defines the message types) and `px4_ros_com` (provides communication nodes and examples) repositories into the `src` directory.
    ```bash
    git clone https://github.com/PX4/px4_msgs.git
    git clone https://github.com/PX4/px4_ros_com.git
    ```
3.  **Build the Workspace:**
    * Navigate to the root of the workspace (`ws_sensor_combined`).
        ```bash
        cd ..
        ```
    * Source your ROS 2 Humble installation. This makes ROS 2 build tools available.
        ```bash
        source /opt/ros/humble/setup.bash
        ```
    * Build the workspace using `colcon`. Colcon is the standard build tool for ROS 2 packages.
        ```bash
        colcon build
        echo "source ~/ws_sensor_combined/install/setup.bash" >> ~/.bashrc
        ```
        * This command finds and builds all ROS 2 packages located in the `src` directory.
        * It will create `build`, `install`, and `log` directories in your workspace root (`~/ws_sensor_combined`).

4.  **Run the Sensor Combined Listener Example:**
    * **Ensure Prerequisites are Running:**
        * PX4 SITL simulation (from Step 3) must be running.
        * Micro XRCE-DDS Agent (from Step 6) must be running in its own terminal.
    * **Open a NEW Terminal:** It's best practice to run nodes in a fresh terminal.
    * **Navigate to Workspace:**
        ```bash
        cd ~/ws_sensor_combined/
        ```
    * **Source ROS 2 Environment:**
        ```bash
        source /opt/ros/humble/setup.bash
        ```
    * **Source Local Workspace Setup:** This makes the packages you just built in *this specific workspace* available to ROS 2 commands.
        ```bash
        source install/local_setup.bash
        ```
    * **Launch the Listener Node:** Use `ros2 launch` to run the example launch file(before launching this file make sure you are running the px4 sitl). Launch files can start one or more nodes with specific configurations.
        ```bash
        ros2 launch px4_ros_com sensor_combined_listener.launch.py
        ```
5.  **Verify Output:** If everything is set up correctly, you should see output similar to this in the terminal where you launched the listener, indicating that the ROS 2 node is receiving sensor data from the PX4 simulation via the Micro XRCE-DDS bridge:
    ```
    RECEIVED DATA FROM SENSOR COMBINED
    ================================
    ts: 870938190
    gyro_rad[0]: 0.00341645
    gyro_rad[1]: 0.00626475
    gyro_rad[2]: -0.000515705
    gyro_integral_dt: 4739
    accelerometer_timestamp_relative: 0
    accelerometer_m_s2[0]: -0.273381
    accelerometer_m_s2[1]: 0.0949186
    accelerometer_m_s2[2]: -9.76044
    accelerometer_integral_dt: 4739
    ... (repeating) ...
    ```
    * You can stop the listener node by pressing `Ctrl+C` in its terminal.

## Next Steps

**Offical pages for refference of the above:**
* **1) Toolchain setup :** https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu.html .
* **2) gazebo simulation :** https://docs.px4.io/main/en/sim_gazebo_gz/ .
* **3) Ros2 user guide by PX4 :** https://docs.px4.io/main/en/ros2/user_guide.html#humble .
