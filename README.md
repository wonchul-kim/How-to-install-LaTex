# How-to-install-LaTex

- For window
  - install MikTex
  - install Texmaker 

-------------------------------------------------------------------------------------------------------------
# mujoco
How to install mujoco


### Env. : Ubuntu 16.04 / python 3.6

### 1. Install required packages
```
sudo apt install -y build-essential autoconf libtool pkg-config python-opengl python-pil \
python-pyrex python-pyside.qtopengl idle-python2.7 qt4-dev-tools qt4-designer libqtgui4 \
libqtcore4 libqt4-xml libqt4-test libqt4-script libqt4-network libqt4-dbus python-qt4 python-qt4-gl \
libgle3 python-dev libgl1-mesa-dev libgl1-mesa-glx libosmesa6-dev python3-scipy libglew-dev patchelf
```

### 2. Install openai gym
```
git clone https://github.com/openai/gym.git
cd gym
sudo pip3 install -e .
```

### 3. Install mujoco
* download mujoco-pro150
* move mjpro150 file to ~/.mujoco/
* setupt env. : in ~/.bashrc file
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/.mujoco/mjpro150/bin
```
* To test the license and move mjkey.txt to ~/.mujoco/mjpro150/bin/
  then, test by /.simulate

* To import with the license, mjkey.txt should be ~/.mujoco/
``` 
git clone https://github.com/openai/mujoco-py.git
cd mujoco-py
python3 setup.py install
```



