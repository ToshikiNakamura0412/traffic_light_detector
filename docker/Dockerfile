FROM nvidia/cuda:11.1.1-cudnn8-devel-ubuntu20.04
RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

########## nvidia-docker1 hooks ##########
LABEL com.nvidia.volumes.needed="nvidia_driver"
ENV PATH /usr/local/nvidia/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64:${LD_LIBRARY_PATH}
########## non-interactive ##########
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get upgrade -y

RUN apt-get install -y vim \
	wget \
	unzip \
	git \
	build-essential \
    tmux


RUN apt-get update && \
	apt-get install -y python3-pip \
	python3-tk \
    libsm6 \
	libxrender1 \
	libxext-dev \
    libgl1-mesa-dev \
    libopencv-dev \
    sharutils \
    iproute2 \
    iputils-ping \
    libeigen3-dev

RUN python3 -m pip install -U --force-reinstall pip

## Pytorch Setup
RUN pip install torch \
                torchvision \
                ultralytics

######### ROS Noetic insatall ##########
## NOTE: "lsb_release" -> "lsb-release"
RUN apt-get update && apt-get install -y lsb-release &&\
	sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' &&\
	apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 &&\
	apt-get update && apt-get install -y ros-noetic-desktop-full
########## ROS setup ##########
RUN mkdir -p /home/catkin_ws/src && \
	cd /home/catkin_ws/src && \
	/bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_init_workspace" && \
	cd /home/catkin_ws && \
	/bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_make" && \
	echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc && \
	echo "source /home/catkin_ws/devel/setup.bash" >> ~/.bashrc && \
	echo "export ROS_PACKAGE_PATH=\${ROS_PACKAGE_PATH}:/home/catkin_ws" >> ~/.bashrc && \
	echo "export ROS_WORKSPACE=/home/catkin_ws" >> ~/.bashrc
ENV PATH=$PATH:/opt/ros/noetic/bin/roslaunch
## cmk
RUN echo "function cmk(){\n	lastpwd=\$OLDPWD \n	cpath=\$(pwd) \n cd /home/catkin_ws \n catkin_make \$@ \n cd \$cpath \n	OLDPWD=\$lastpwd \n}" >> ~/.bashrc
RUN cd /home/catkin_ws/src && \
    git clone -b yolov8n https://github.com/YoshikiMaekawa2000/traffic_light_detector.git

RUN ln -sf /home/catkin_ws/src/traffic_light_detector/launch/launch.sh /etc/profile.d/launch.sh

WORKDIR /home/catkin_ws/src/traffic_light_detector/launch
