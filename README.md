
**Kria KV260** 
Link: <https://www.amd.com/en/products/system-on-modules/kria/k26/kv260-vision-starter-kit/getting-started/getting-started.html>

**Setting up the SD card image** 

1. Download the [Kria™ KV260 Vision AI Starter Kit Image](https://ubuntu.com/download/amd).
1. Use a flashing tool (Balena etcher / win32 disk imager / RPI images or similar) for flashing the image on the SD Card
1. Follow the instructions in the tool and select the downloaded image to flash onto the microSD card.

**Connecting Everything**

![A circuit board with a fan and wires

Description automatically generated](Aspose.Words.74ed720f-55d2-43da-a6ad-c75571746470.001.png)














There is also an option to connect to DisplayPort instead of HDMI, but for this, we must have a DisplayPort cable and a supported DisplayPort monitor. We can also connect a USB keyboard and mouse for an optimal experience.

**Booting up the starter kit**

1. The Starter Kit uses an FTDI USB to COM port device that requires the FTDI virtual COM port driver to be installed on the machine.

   <https://ftdichip.com/drivers/vcp-drivers/>

1. Four COM ports are enumerated, and the second numbered COM port corresponds to the UART.

   Configuring the terminal program (e.g., TeraTerm, PuTTy) 

- Baud rate = 115200
- Data bits = 8
- Stop bits = 1
- Flow control = None
- Parity = None
1. Power ON the Starter Kit by connecting the power supply to the AC plug. The power LEDs should illuminate, and a Linux UART response can be seen on the terminal program interface.
1. The Starter Kit QSPI boots the board using SD boot mode and loads the SD contents to boot into Linux. At initial log-in, the platform requires you to set a new password. When the system boots to Linux and asks for a login username, enter the default username as ***“ubuntu”*** and password as ***“ubuntu”*** and set a new user password.
1. Verify Internet connectivity via “ping” or “DNS lookup.”

   **Ping 8.8.8.8**

   If we can observe that packet transmit/receive worked and there is no packet loss with the above ping command, this means the Internet connectivity is working and active.

Note: DV's current software supports/provides a [PPA repository](https://launchpad.net/~inivation-ppa/+archive/ubuntu/inivation) for Focal (20.04 LTS) and Jammy (22.04 LTS) on the x86\_64, arm64, and armhf architectures, and a [separate PPA](https://launchpad.net/~inivation-ppa/+archive/ubuntu/inivation-bionic) repository for Bionic (18.04 LTS) on the x86\_64, x86, arm64, and armhf architectures.

*We have used the Ubuntu Server 22.04 LTS* 

**Inivation Cameras (DVS 128 and DAVIS 346)
DV on Kria KV260**

Link: <https://dv-processing.inivation.com/rel_1_7/installation.html>

**Installation of dv-processing**

1. sudo add-apt-repository ppa:inivation-ppa/inivation
1. sudo apt-get update
1. sudo apt-get install dv-runtime
1. sudo apt-get install dv-processing

The dv-processing library is also available in Python using Pybind11 bindings. 

- python3 -m pip install dv-processing

Pip installation is the only supported method for installation in a virtual Python environment (VirtualEnv / Conda).

Or 

- sudo apt-get install dv-processing-python

**PS:** Install opencv for testing the cameras with any of the codes: https://dv-processing.inivation.com/rel\_1\_7/reading\_data.html

$ Pip3 install opencv-python

**To discover connected devices, use the command:**

$ dv-list-devices

**PYNQ installation on Kria**

1. git clone <https://github.com/Xilinx/Kria-PYNQ.git>
1. cd Kria-PYNQ/
1. sudo bash install.sh -b KV260

This can take a while, around 30 min. After one can connect to the JupyterLab via web browser with kria:9090/lab the password is ***xilinx.***

Or IP:9090/lab password is *xilinx (check the IP using ifconfig). 

**Note: Restart Kria for the installation to be effective.***

**Evaluation KIT - Gen3M VGA-CD 1.1 (Same steps for connecting to Kria & PYNQ)**

Deploying Metavision SDK using the pre-built Ubuntu package with Kria is not feasible. We need to build the packages from source using OpenEB.

Link: <https://docs.prophesee.ai/stable/installation/linux_openeb.html#chapter-installation-linux-openeb>
Supported SDK versions for the Cameras: <https://docs.prophesee.ai/stable/installation/index.html#chapter-installation-evk-support>

**Compiling OpenEB on Linux**

1. git clone [https://github.com/prophesee-ai/openeb.git --branch 3.1.1](https://github.com/prophesee-ai/openeb.git%20--branch%203.1.1) 

   **Installing Dependencies:**

1. sudo apt update
1. sudo apt -y install apt-utils build-essential software-properties-common wget unzip curl git cmake
1. sudo apt -y install libopencv-dev libboost-all-dev libusb-1.0-0-dev libprotobuf-dev protobuf-compiler
1. sudo apt -y install libhdf5-dev hdf5-tools libglew-dev libglfw3-dev libcanberra-gtk-module ffmpeg

   optional for running tests

- sudo apt -y install libgtest-dev libgmock-dev

For the Python API, we need Python and some additional libraries.Supported Python 3.9 and 3.10 on Ubuntu 22.04 and Python 3.11 and 3.12 on Ubuntu 24.04.

Recommened using Python with virtualenv to avoid conflicts with other installed Python packages. So, first install it along with some Python development tools:

- sudo apt -y install python3.x-venv python3.x-dev

\# where "x" is 9, 10, 11 or 12 depending on the Python version

Next, create a virtual environment and install the necessary dependencies:

- python3 -m venv /tmp/prophesee/py3venv --system-site-packages
- /tmp/prophesee/py3venv/bin/python -m pip install pip --upgrade
- /tmp/prophesee/py3venv/bin/python -m pip install -r OPENEB\_SRC\_DIR/utils/python/ requirements\_openeb.txt


There is no pre-compiled version of pybind11 available, so you need to install it manually:

- wget https://github.com/pybind/pybind11/archive/v2.11.0.zip
- unzip v2.11.0.zip
- cd pybind11-2.11.0
- mkdir build && cd build
- cmake .. -DPYBIND11\_TEST=OFF
- cmake --build .
- sudo cmake --build . --target install

**Compilation OpenEB**

1. mkdir build && cd build
1. cmake .. -DCMAKE\_BUILD\_TYPE=Release -DBUILD\_TESTING=OFF
1. cmake --build . --config Release -- -j `nproc`

   Once the compilation is done, we have two options: we can choose to work directly from the build folder or we can deploy the OpenEB files in the system path (/usr/local/lib, /usr/local/include…).

   **Option 1 - working from build folder**

   To use OpenEB directly from the build folder, we need to update some environment variables using this script (which you may add to your ~/.bashrc to make it permanent):

- source utils/scripts/setup\_env.sh

Prophesee camera plugin is included in OpenEB, but you still need to copy the udev rules files in the system path and reload them so that your camera is detected with this command:

- sudo cp <OPENEB\_SRC\_DIR>/hal\_psee\_plugins/resources/rules/\*.rules /etc/udev/rules.d
- sudo udevadm control --reload-rules
- sudo udevadm trigger

**Option 2 - deploying in the system path**

- To deploy OpenEB, launch the following command:
- sudo cmake --build . --target install

We  also need to update LD\_LIBRARY\_PATH and HDF5\_PLUGIN\_PATH (which we may add to your ~/.bashrc to make it permanent):

- export LD\_LIBRARY\_PATH=$LD\_LIBRARY\_PATH:/usr/local/lib
- export HDF5\_PLUGIN\_PATH=$HDF5\_PLUGIN\_PATH:/usr/local/hdf5/lib/plugin  # On Ubuntu 22.04
- export HDF5\_PLUGIN\_PATH=$HDF5\_PLUGIN\_PATH:/usr/local/lib/hdf5/plugin  # On Ubuntu 24.04

**Interfacing Inivation cameras directly with the Pynq Z2**

Dependencies:

- *Python3*. <https://phoenixnap.com/kb/how-to-install-python-3-ubuntu>
- *OpenCV*. <https://pypi.org/project/opencv-python/>
- *Numpy*. <https://numpy.org/install/>
- *PYNQ*. <https://pypi.org/project/pynq/>
- *Libcaer:* <https://gitlab.com/inivation/dv/libcaer.git>
- *PyAER*. <https://github.com/duguyue100/pyaer>

Libcaer: 

- ` `cd libcaer
- mkdir build
- cd build
- cmake ..
- make
- sudo make install

`  `path :  Installing: /usr/local/lib/libcaer.so

SWIG:

- git clone <https://github.com/duguyue100/swig>
- cd swig
- ./autogen.sh
- ./configure
- make -j4
- sudo make install

path: /usr/local/share/swig

payer:

- git clone https://github.com/duguyue100/pyaer.git
- cd payer
  in setup.py file 
  update this path:
  # Set correct paths for libcaer if platform in ["linux", "linux2"]:

  ` `libcaer\_include = "/usr/local/include/libcaer" # Updated path 

  libcaer\_lib = "/usr/local/lib" # Updated path 

  # SWIG options to include the libcaer headers

  ` `swig\_opts = ["-I/usr/local/include”]  #updated path

  if platform == "darwin": 

  include\_dirs += ["/usr/local/include"] 

  library\_dirs += ["/usr/local/lib"] 

  swig\_opts += ["-I/usr/local/include"]

- make develop

  We need to ensure that the path to the directory containing libcaer.so.3 is included in the LD\_LIBRARY\_PATH variable. Since it is in /usr/local/lib

- export LD\_LIBRARY\_PATH=/usr/local/lib:$LD\_LIBRARY\_PATH
- echo $LD\_LIBRARY\_PATH

  otherwise it will throw and error when importing payer

  error: 

  >>> import pyaer

  Traceback (most recent call last):

  `  `File "/pyaer/pyaer/\_\_init\_\_.py", line 23, in <module>

  `    `from pyaer import libcaer\_wrap as libcaer  # noqa

  `  `File "/pyaer/pyaer/libcaer\_wrap.py", line 10, in <module>

  `    `from . import \_libcaer\_wrap

  ImportError: libcaer.so.3: cannot open shared object file: No such file or directory

  During handling of the above exception, another exception occurred:

  Traceback (most recent call last):

  `  `File "<stdin>", line 1, in <module>

  `  `File "/pyaer/pyaer/\_\_init\_\_.py", line 25, in <module>

  `    `raise ImportError(

  ImportError: libcaer might not be in the LD\_LIBRARY\_PATH or your numpy might not be the required version. Try to load \_libcaer\_wrap.so fromthe package directory, this will provide more information.

**Running the examples in the repository for testing DVS128**

- git clone <https://github.com/GuillermoFdez98/Video-pipeline-for-event-based-sensors.git>
- cd Video-pipeline-for-event-based-sensors/Scripts/

  example code

- python3 dvs128\_timeslib\_tracking\_pynq.py

  **Files for running on PYNQ-Z2 using DAVIS346**
- Link: <https://github.com/ShaminiKoravuna/VideoTracking346.git>


