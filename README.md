English| [简体中文](./README_cn.md)

# Performance Node
The Performance Node application is a web-based tool accessible via any computer or mobile device's browser regardless of brand. To set up and use the application:

<img src=".\doc\desktop_demo.jpg" alt="desktop_demo" style="zoom:70%;" />

<img src=".\doc\mult_device.jpg" alt="mult_device" style="zoom:70%;" />

## 1. Usage

### 1.1 Install the pfnode Package
**Note:** When running `apt update`, ensure that the RDK source is available.

**Foxy:**
```bash
sudo apt update
sudo apt install tros-pfnode
```
**Humble:**
```bash
sudo apt update
sudo apt install tros-humble-pfnode
```

### 1.2 Install Python Packages, Mainly Flask and Psutil

```bash
pip install psutil -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
pip install flask -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```

### 1.3 Run the Package

**Foxy:**
```bash
source /opt/tros/setup.bash
ros2 run pfnode pfnode
```
**Humble:**
```bash
source /opt/tros/humble/setup.bash
ros2 run pfnode pfnode
```

### 1.4 Access Port 7999 of X3 on the Same Local Network

```bash
192.168.xxx.xxx:7999
```

## 2. Parameter Description
```bash
$ ros2 run pfnode pfnode --help
usage: app.py [-h] [--device DEVICE] [--port PORT] [--debug DEBUG] [--log LOG]

optional arguments:
  -h, --help       show this help message and exit
  --device DEVICE  0: RDK X3 (Module), 1: RDK Ultra, 2: RDK X5
  --port PORT      enter the port you like.
  --debug DEBUG    Flask Debug Mode, 0:false, 1:true.
  --log LOG        Flask log, 0:false, 1:true.
```
For example, if you want to manually specify the board as RDK Ultra, use port 6666, enable debug mode, and enable Flask log display, you can do so as follows:
```bash
ros2 run pfnode pfnode --device 1 --port 6666 --debug 1 --log 1
```

### Default Parameters
The default value for the `--device` parameter is -1, which means auto-detection; the program will automatically determine the device based on the device tree file in use.
The default value for the `--port` parameter is 7999, one less than TROS's 8000, as a tribute to the great TROS!
The default value for the `--debug` parameter is 0, meaning false, and the Flask Debug mode is disabled by default.
The default value for the `--log` parameter is 0, meaning false, and Flask HTTP request/response logging is disabled by default.

5. **Data Collection**
   - **CPU Data**: The CPU usage percentages for each logical processor are obtained using `psutil.cpu_percent(percpu=True)`. A list of float values is returned, with each entry corresponding to the utilization of a specific CPU core, maintaining the same order across calls even when invoked from different threads.

   - **BPU Data**: BPU utilization data comes from files specific to the devices:
     - For RDK X3, RDK X3 Module, and RDK Ultra: `/sys/devices/system/bpu/bpu0/ratio` and `/sys/devices/system/bpu/bpu1/ratio`.

   - **Memory Data**: Memory usage stats are gathered through `psutil.virtual_memory()`, returning a named tuple with details such as `available` memory, which is the amount immediately allocatable to processes without swapping, and `used` memory, which is calculated differently across platforms and intended for informational purposes.

   - **Temperature Data**: Temperature readings come from different paths for each device:
     - RDK X3, RDK X3 Module: `/sys/class/hwmon/hwmon0/temp1_input`
     - RDK Ultra: `/sys/devices/virtual/thermal/thermal_zone8/temp` (among others)

   - **Frequency Data**:
     - RDK X3, RDK X3 Module read frequency data from one file: `/sys/devices/system/cpu/cpufreq/policy0/cpuinfo_cur_freq`.
     - RDK Ultra doesn't collect this data due to its impact on system load.

   - **Disk Usage Data**: Disk usage statistics are retrieved with `psutil.disk_usage()`, providing information about total capacity, used space, free space, and usage percentage for a specified directory. The `used` field indicates total used space while `free` represents available space for regular users.

6. **Performance Mode Settings**
   Performance modes can be changed by sending commands to the command line via `os.system()`:
   - For RDK X3 and RDK X3 Module, setting the CPU governor to 'performance' mode involves echoing the word 'performance' to a specific file.
   - For RDK Ultra, similar commands are run for all eight CPU policies.

The application includes various power/performance modes such as 'performance' (highest frequency), 'powersave' (lowest frequency), and 'schedutil' (Linux 4.7+ strategy adjusting frequency based on scheduler-provided CPU utilization info).

Users encountering issues can submit issues or pull requests. There's also a satisfaction survey for the Performance Node, displayed as an image link.

<img src=".\doc\survey.jpg" alt="survey" style="zoom:20%;" />