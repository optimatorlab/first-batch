# Configuring our Ubuntu Boxes with Python Virtual Environments

*Updated March 12, 2018*


- Some of this material comes from https://www.pyimagesearch.com/2017/09/25/configuring-ubuntu-for-deep-learning-with-python/.

- The instructions below have been tested on Ubuntu 14.04 and 16.04.

- These instructions work for Python 2.7 and Python 3.  You can have multiple virtual environments, some running Python 2.7 and some running Python 3.


### The remainder of this guide is presented in 4 parts:

1. **Installing System Packages** -- You need to follow these steps just once.  Afterwards you can create as many virtual environments as you'd like.

	- *Note for Lab Users:*  You do not need to perform this step.  All necessary system packages have already been installed on your machine.  Please skip to the next section.
	
2. **Creating Python Virtual Environments** -- Follow these steps each time you want to create a new virtual environment.  These particular steps focus on creating virtualenvs designed for machine learning projects, but can be easily customized for each new project.

3. **Copying or Renaming a Virtual Environment** -- Take a look at this section if you want to rename a virtual environment, or to copy it for a different project.

4. **Pip Templates** -- It's easy to see what packages are installed in a particular virtual environment.  You can also export these packages and create a new virtual environment that uses the exact versions of those packages.

---

## 1) Install system packages

These steps should be followed just once (e.g., on a fresh installation of Ubuntu).

- *Note for Lab Users:*  You do not need to perform this step.  All necessary system packages have already been installed on your machine.  Please skip to the next section (creating a python virtual environment).

1. Update apt-get and upgrade existing system packages:

	```
	sudo apt-get update
	sudo apt-get upgrade
	```

2. Install new required system packages/dependencies:

	```
	sudo apt-get install build-essential cmake git unzip pkg-config
	sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
	sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
	sudo apt-get install libxvidcore-dev libx264-dev
	sudo apt-get install libgtk-3-dev
	sudo apt-get install libhdf5-serial-dev graphviz
	sudo apt-get install libopenblas-dev libatlas-base-dev gfortran
	sudo apt-get install python-tk python3-tk python-imaging-tk
	```

3. Install Python development libraries:

	```
	sudo apt-get install python2.7-dev python3-dev
	```
	
4. Install pip:
	
	```
	wget https://bootstrap.pypa.io/get-pip.py
	sudo python get-pip.py
	sudo python3 get-pip.py
	```
	
5. Install virtualenv and virtualenvwrapper:
	
	```
	sudo pip install virtualenv virtualenvwrapper
	sudo rm -rf ~/.cache/pip get-pip.py
	```

6. Configure virtualenv and virtualenvwrapper in your .bashrc profile:

	```
	echo -e "\n# Configure virtualenv and virtualenvwrapper" >> ~/.bashrc
	echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.bashrc
	echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3" >> ~/.bashrc
	echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc

	source ~/.bashrc
	```

	NOTES:
	
	- You should only run the above commands once.  Otherwise, it will just keep adding the same lines to your `.bashrc` file.
	
	- Setting the `VIRTUALENVWRAPPER_PYTHON` simply makes python3 the default; it doesn't lock us in to python3.
	
	- If you prefer to edit your `.bashrc` file manually, simply edit that file (e.g., via `nano` or `pico`) and place the following lines at the bottom of the file:
		
		```
		# Configure virtualenv and virtualenvwrapper
		export WORKON_HOME=$HOME/.virtualenvs
		export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
		source /usr/local/bin/virtualenvwrapper.sh
		```

	
		
## 2) Create a Python Virtual Environment

In this section we are referencing virtualenvs named "olab3" (using Python 3) and "olab2" (Python 2).

1. Create the virtual environment:

	- Python 3:

		```
		mkvirtualenv olab3 -p python3
		```

	- Python 2.7:

		```
		mkvirtualenv olab2 -p python2
		```
	
2. Make sure you're in the virtual environment.  

	When inside a virtualenv, you will see the name of that environment in parenthesis (at the beginning of your command line prompt.  If not:

	```
	workon olab3
	```

3. Install your Python packages.  The `--upgrade` flag will make sure you have the latest version, which is helpful if you've already installed a package in your virtualenv.

	- We'll start with some basics:
		
		```
		pip install --upgrade ipython
		pip install --upgrade numpy scipy matplotlib scikit-learn pandas pillow
		pip install --upgrade mglearn
		pip install --upgrade jupyter
		pip install --upgrade seaborn statsmodels
		```

	- Install image processing packages (including openCV):
		
		```
		pip install --upgrade imutils h5py requests progressbar2
		pip install --upgrade scikit-image
		pip install --upgrade opencv-python
		pip install --upgrade opencv-contrib-python
		```

		**NOTE**:  If you wish to compile openCV yourself (not necessary unless you need a version not available via pip), see the end of this document for the gory details.

	- Install Keras and TensorFlow:
	
		```
		pip install --upgrade tensorflow
		pip install --upgrade keras
		```

	- Other common packages used in the lab:
		
		```
		pip install --upgrade future
		pip install --upgrade pygame
		pip install --upgrade pyserial
		pip install --upgrade xbee
		pip install --upgrade pyglet
		pip install --upgrade netifaces
		pip install --upgrade rpy2			# Still valid/useful?  Does NOT work for Python 2	
		```

	- Speech processing packages:
	
		```
		pip install --upgrade SpeechRecognition		
		pip install --upgrade pocketsphinx			
		```

	- Packages used for pgRouting:

		```
		pip install --upgrade psycopg2
		pip install --upgrade sympy 
		pip install --upgrade nose
		pip install --upgrade geopy
		```
	
	- Link to PyQt4:
		
		**NOTE**: PyQt4 cannot be installed via pip.  Our Ubuntu 16.04 auto installer script installs `python-qt4` via `apt-get`.  
		
		Follow the steps below to create symbolic links so you can use `PyQt4` within your virtual environments.  Modify the paths as appropriate.  These steps assume our our Python 2.7 virtualenv is named `olab2`.
		
		```
		# Create symbolic link of PyQt4 to your virtual env 
		ln -s /usr/lib/python2.7/dist-packages/PyQt4/ ~/.virtualenvs/olab2/lib/python2.7/site-packages/
		```
		
		```
		# Create symbolic link of sip.so to your virtual env
		ln -s /usr/lib/python2.7/dist-packages/sip.so ~/.virtualenvs/olab2/lib/python2.7/site-packages/
		```
		
		```
		# Test it out:
		python
		>>> from PyQt4 import QtGui, QtCore
		```

	- Link to Gurobipy:
		
		**NOTE**:  See the [Gurobi Installation Guide](gurobi_installation.md) for general installation instructions. 
		
		The code below assumes that (1) we have a Python 2.7 virtual environment named `olab2` with a particular path, and (2) we have installed Gurobi 7.5.1 and have found the path to `gurobipy.so`.  **You may need to change these paths as appropriate for your setup.**
	
		```
		workon olab2
		cd ~/.virtualenvs/olab2/lib/python2.7/site-packages/	
		ln -s /opt/gurobi751/linux64/lib/python2.7/gurobipy.so	
		```

		```
		# Test it out:
		python
		>>> import gurobipy
		```
	
	- Link to rospy:

		**NOTE**: This worked for a (possibly old?) installation in Ubuntu 14.04:

		```	
		ln -s /usr/lib/python2.7/dist-packages/rospkg/ 		~/.virtualenvs/olab2/lib/python2.7/site-packages/
		ln -s /usr/lib/python2.7/dist-packages/catkin_pkg/ 	~/.virtualenvs/olab2/lib/python2.7/site-packages/
		```

	- Link to pymavlink/mavutil:

		**NOTE**: This worked for a (possibly old?) installation in Ubuntu 14.04:

		```
			/home/murray/mavlink/		???
			/home/murray/mavlink/pymavlink/		???
		```
		
		```
		ln -s /home/murray/.local/lib/python2.7/site-packages/pymavlink-1.1.71-py2.7-linux-x86_64.egg ~/.virtualenvs/olab2/lib/python2.7/site-packages/
		```
	
	- **FIXME** Document how to make these links:
	
		- from MAVProxy.modules.lib import mp_module
		- from MAVProxy.modules.lib import mp_util
		- from MAVProxy.modules.lib import textconsole

		- from dronekit import connect, VehicleMode, LocationGlobalRelative, LocationGlobal, Command

		- from dronekit_sitl import SITL	

###	Testing the installation:

1. Check the Keras config file:
	
	```
	pico ~/.keras/keras.json
	```
	
	There should be a block that looks like:
	
	```
	{
	    "image_data_format": "channels_last",
	    "backend": "tensorflow",
	    "epsilon": 1e-07,
	    "floatx": "float32"
	}
	```	

	Ensure that `image_data_format` is set to `channels_last` and `backend` is `tensorflow`.

2. Check the Python packages:

	- First, open python:
	
		```
		workon olab3
		python
		```
	
	- Then, issue the following commands within the python virtualenv:
	
		```
		# Test cv2:
		import cv2
		cv2.__version__
		# Should return a version number, like '3.4.1'
		
		# Test Keras:
		import keras
		# Should return a message indicating 'Using TensorFlow backend.'
	
		# Test our other packages:
		import pandas
		import numpy
		import sklearn
		import matplotlib
		import IPython
		import mglearn
		import scipy
		import PIL
		# There should be no *error* messages 
		# (you might get some deprecation warnings...we'll ignore for now)
	
		exit()	
		```

3. Check Jupyter:

	```
	workon olab3
	jupyter notebook
	```

	If Jupyter was successfully installed, your terminal will note that you are acting as a localhost, and a Web browser showing Jupyter's logo will appear. Hit Ctrl-C in the terminal to kill the localhost.

4. Exit from the virtual environment:

	```
	deactivate
	```
	
	Now, the command line prompt should no longer begin with `(olab3)`.

5. Using aliases as a shortcut to your virtual environments:

	- Open your `.bashrc` file for editing:
		```
		pico ~/.bashrc
		```
	
	- Add the following lines to `.bashrc` (assuming we have virtual environments named `olab2` and `olab3`):
		```
		# Create aliases for python virtual environments
		alias olab2='workon olab2'
		alias olab3='workon olab3'
		```

---

## 3) Copying or Renaming a Virtual Environment

- To create a copy of a virtualenv:

	```
	cpvirtualenv <original_name> <name_of_copy>
	```

- You can also use this workflow to rename a virtualenv:

	```
	cpvirtualenv <wrong_name> <correct_name>
	rmvirtualenv <wrong_name>
	```

---

## 4) Installing from a pip Template

This is a handy way to make sure that everyone is using the same versions of Python packages.  See the "Creating a pip template" section (below) to generate the list of installed pip packages for a particular virtualenv.

- Install packages from `requirements.txt`: 

	```
	workon <virtualenv_name>
	pip install -r requirements.txt
	```

---

### Creating a pip template

1. Activate your virtualenv:
	
	```
	workon <virtualenv name>
	```
	
2. Create a `requirements.txt` of currently installed packages: 

	```
	pip freeze > requirements.txt
	```
	
	NOTE: It would be wise to maintain separate requirements files for the lab.

---

## (optional) Compiling openCV 

These instructions come from https://www.pyimagesearch.com/2017/09/25/configuring-ubuntu-for-deep-learning-with-python/ .  See that site for details.  I'm leaving this here just in case we ever need it.


1. Get openCV (version 3.4.1):

	```
	cd ~
	wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.4.1.zip
	wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.4.1.zip

	unzip opencv.zip
	unzip opencv_contrib.zip
	```
	
2. Run Cmake:
	
	```
	cd ~/opencv-3.4.1/
	mkdir build
	cd build
	cmake -D CMAKE_BUILD_TYPE=RELEASE \
	    -D CMAKE_INSTALL_PREFIX=/usr/local \
	    -D WITH_CUDA=OFF \
	    -D INSTALL_PYTHON_EXAMPLES=ON \
	    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.4.1/modules \
	    -D BUILD_EXAMPLES=ON ..
	```

3. Compile (with 4 cores)

	```
	make -j4
	```

4. Install and cleanup:

	```
	sudo make install
	sudo ldconfig
	cd ~
	rm -rf opencv-3.4.1 opencv.zip
	rm -rf opencv_contrib-3.4.1 opencv_contrib.zip
	```

5. Symbolic linking openCV to virtual environment:

	**NOTE: I have Python 3.4 on my machine.**  Change paths below as appropriate.

	```
	cd ~/.virtualenvs/olab3/lib/python3.4/site-packages/
	ln -s /usr/local/lib/python3.4/site-packages/cv2.cpython-34m.so cv2.so
	cd ~
	```
	
	The pyimagesearch guide used `ln -s /usr/local/lib/python3.4/site-packages/cv2.cpython-35m-x86_64-linux-gnu.so cv2.so`.

6. Test openCV installation:

	```
	python
	>>> import cv2
	>>> cv2.__version__
	'3.4.1'
	```
	
---
