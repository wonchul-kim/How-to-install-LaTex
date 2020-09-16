# How to install and set Ubuntu server 

For multi-booting w/ window <br />
(ref: https://tw.saowen.com/a/a9cc5b7c90a6f350850d8554c018f7415981fc8d470b481c90afd7573f5e12cd)
***********************
Ubuntu 16.04 <br />
python 3.6.5 <br />
Cuda 9.0 (cudnn 7.3.1)
***********************

## 1. Install ubuntu
* USB should be implemented by UEFI version

```
* Three partitions are needed: swap, /, /home
* swap: about 2 times as memory (Logical, Beginning of this space)
* /: most of volume (Logical, Beginning of this space, ext4)(It's kind of C drive in Window)
* /home: rest of volume (Logical, Beginning of this space, ext4
(For example, within 200GB, I assigned 16GB for swap, 120GB for /, and the rest for /home)

*** Device for boot loader: window boot manager (not the disk)
```

## 2. Install NVIDIA Driver (version 396)
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-396 nvidia-modprobe
sudo apt-get install firefox
```
If you prompted to disable Secure Boot, select Disable. Then, reboot your machine, but enter BIOS to disable Secure Boot. 
To verify installation, restart your machine with reboot and type nvidia-smi. 

> nvidia-418 is for geforce gtx-950 

## 3. Install CUDA9.0
Download CUDA9.0 runfile
```
chmod +x filename
./filename --extract=$HOME
```
Then, there will be three unpacked components: 
1. NVIDIA driver that we ignore 
2. CUDA 9.0 installer
``` 
sudo ./cuda-linux.9.0. --- .run
```
To verify CUDA installation, install the sample tests by

3. CUDA 9.0 Samples
```
sudo ./cuda-sample. --- .run
```

After the installation finishes, you must configure the runtime library.
```
sudo bash -c "echo /usr/local/cuda/lib64/ > /etc/ld.so.conf.d/cuda.conf"

sudo ldconfig
```

It is also recommended for Ubuntu users to append string /usr/local/cuda/bin to system file /etc/environment so that nvcc will be included in $PATH. This will take effect after reboot. To do that, you just have to

```
sudo vim /etc/environment
```
In order to link cuda with tensorflow, add the below in ~/.bashrc
```
export LD_LIBRARY_PATH=/usr/local/cuda/lib64/
```

and then add :/usr/local/cuda/bin (including the ":") at the end of the PATH="/blah:/blah/blah" string (inside the quotes).
After a reboot, let's test our installation by making and invoking our tests:
```
cd /usr/local/cuda-9.2/samples
sudo make
cd /usr/local/cuda-9.2/samples/bin/x86_64/linux/release
./deviceQuery
```


## 4. Install Cudnn for CUDA9.0
Download cudnn with proper version for CUDA9.0
```
tar -zxf (filename)
cd cuda
sudo cp lib64/* /usr/local/cuda/lib64/
sudo cp include/* /usr/local/cuda/include/
```
Make sure that this process is done!!

## 5. Install dependencies for works

* Install Ubuntu system dependencies
```
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install build-essential cmake git unzip pkg-config
sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libgtk-3-dev
sudo apt-get install libhdf5-serial-dev graphviz
sudo apt-get install libopenblas-dev libatlas-base-dev gfortran
sudo apt-get install python-tk python3-tk python-imaging-tk
sudo apt-get install linux-image-generic linux-image-extra-virtual
sudo apt-get install linux-source linux-headers-generic
```

* Install python
``` 
sudo apt-get install python2.7-dev python3-dev
```
To upgrade python3.5 to python3.6, need to install python3.5 addtionally and switch the priority for python3.
```
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2
sudo update-alternatives --config python3
python3 -V

sudo apt-get install python3.6-dev
```

* Install pip
```
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
sudo python3 get-pip.py
```

* Install python virtual environment
```
sudo pip install virtualenv virtualenvwrapper
sudo rm -rf ~/.cache/pip get-pip.py
sudo apt-get install python3.6-gdbm
```

In order to use virtualenvwrapper, we need to update our ~/.bashrc file to include the following lines at the bottom of the file:
```
# virtualenv and virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
```

For python3
```
mkvirtualenv p3 -p python3
```
Then, there will p3 file for python3 virtualenv.
```
cd ~/.virtualenvs
cd p3/bin
. activate
deactivate
```

If virtualenvwrapper.sh is not in /usr/local/bin/, change the above setting by finding the file:
```
find /usr -name virtualenvwrapper.sh
```

Then, we can execute the virtualenv by:
```
workon p3
```

## 6. Install opencv ---- for python3.6
Working on virtualenv,
```
cd ~
wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.3.0.zip
wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.3.0.zip

unzip opencv.zip
unzip opencv_contrib.zip

cd ~/opencv-3.3.0/
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D WITH_CUDA=OFF \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.3.0/modules \
    -D BUILD_EXAMPLES=ON \
    -D PYTHON_EXECUTABLE=/usr/bin/python3.6 \
    -D PYTHON_INCLUDE=/usr/include/python3.6/ \
    -D PYTHON_LIBRARY=/usr/lib/python3.6/ \
    -D PYTHON_PACKAGES_PATH=/usr/local/lib/python3.6/dist-packages/ \
    -D PYTHON_NUMPY_INCLUDE_DIR=/usr/local/lib/python3.6/dist-packages/numpy/core/include/ \
      ..

make -j4
sudo make install
sudo ldconfig
```

To sym-link our OpenCV bindings into the virtual environment, issue the following commands:
```
cd ~/.virtualenvs/dl4cv/lib/python3.6/site-packages/
ln -s /usr/local/lib/python3.6/site-packages/cv2.cpython-36m-x86_64-linux-gnu.so cv2.so
```

## 7. Install tensorflow and etc.
* Install tensorflow-gpu
```
pip install scipy matplotlib pillow pyqt5
pip install imutils h5py requests progressbar2
pip install scikit-learn scikit-image
pip install tensorflow-gpu
```

* etc.

*install Synatics
```
sudo apt-get update
sudo apt-get install wget gdebi
wget http://http.us.debian.org/debian/pool/main/s/synaptiks/kde-config-touchpad_0.8.1-2_all.deb
sudo gdebi kde-config-touchpad_0.8.1-2_all.deb
sudo apt-mark hold kde-config-touchpad
synaptiks
```
*realsense2
 ```
 pip install realsense2
 ```
 
