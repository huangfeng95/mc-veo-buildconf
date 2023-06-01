# mc-veo-buildconf
Build bootstrapping for the Motion Compensated Visual-Event Odometry (MC-VEO)

# MC-VEO: Motion Compensated Visual-Event Odometry

This is the code for the paper [**MC-VEO: A Visual-event Odometry with Accurate 6-DoF Motion Compensation**].

License
-------
This source code is GPLv3 license. See the LICENSE file for further details.

Installation
-------

1. Make sure that the Ruby interpreter is installed on your machine. [Rock](https://www.rock-robotics.org/) requires ruby 2.3 or higher, which is provided on Debian and Ubuntu by the ruby2.3 package.  This installation process is tested with Ruby 2.7 on Ubuntu 20.04.

```console
docker@hjf $ ruby --version
ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x86_64-linux-gnu]
docker@hjf $ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.4 LTS
Release:	20.04
Codename:	focal
```
2. Create and “cd” into the directory in which you want to install the toolchain.
```console
docker@hjf $ mkdir mc-veo && cd mc-veo && mkdir dev && cd dev
docker@dev $ pwd
/home/hjf/mc-veo/dev
```
3. To build MC-VEO, use this [bootstrap.sh](bootstrap.sh) script. Save it in the folder you just created.

```console
docker@dev $ wget https://raw.githubusercontent.com/huangfeng95/mc-veo-buildconf/master/bootstrap.sh
```
4. In a console run
```console
docker@dev $ sh bootstrap.sh
```
5. Follow the installation guide and answer the questions. In case you hesitate choose the answer by default.

```console
Connecting to www.rock-robotics.org (www.rock-robotics.org)|185.199.111.153|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 30078 (29K) [application/octet-stream]
Saving to: 'autoproj_bootstrap'

autoproj_bootstrap       100%[=================================>]  29.37K  --.-KB/s    in 0.007s  

2022-04-13 15:58:06 (3.97 MB/s) - 'autoproj_bootstrap' saved [30078/30078]

Which protocol do you want to use to access huangfeng95/mc-veo-buildconf.git on github.com?
[git|ssh|http] (default: http) 


So, what do you want ? (all, none or a comma-separated list of: os gem pip) [all] 
Would you like autoproj to keep apt packages up-to-date? [yes] 

The current directory is not empty, continue bootstrapping anyway ? [yes] 


How should I interact with github.com (git, http, ssh)
If you give one value, it's going to be the method used for all access
If you give multiple values, comma-separated, the first one will be
used for pulling and the second one for pushing. An optional third value
will be used to pull from private repositories (the same than pushing is
used by default) [http,ssh]

Which flavor of Rock do you want to use ?
Stay with the default ('master') if you want to use Rock on the most recent
distributions (Ubuntu 16.04 and later). Use 'stable' only for 
now officially unsupported distributions (Ubuntu 14.04) [master]
```

6. Answer here 'yes' in case you want to activate python. You need a python installation in your system for this.

```console
Do you want to activate python? [no] yes
Select the path to the python executable [/usr/bin/python3] 
```

7. Select 'master' in case you want to build MC-VEO with DSO backend or type 'ceres' otherwise

```console
Which flavor of MC-VEO do you want to use ?
Stay with the default ('master') if you want to use MC-VEO with DSO backend
otherwise select ('ceres') [master] 
```
8. Choose the answers by default in the questions regarding RTT/Orocos. Select 'gnulinux' for Linux based systems
```console
Do you need compatibility with OCL ? (yes or no)
New Rock users that don't need backward compatibility with legacy Orocos components
probably want to say 'no'. Otherwise, say 'yes'.
Saying 'yes' will significantly impact compilation time and the size of the resulting binaries
Please answer 'yes' or 'no' [no]

the target operating system for Orocos/RTT (gnulinux, xenomai, or macosx) [gnulinux] 
```
8. In case no problems occurred the installation shoudl finish "successfully"
```console
updated environment
Command finished successfully at 2022-04-13 16:47:58 +0200 (took 44 mins 20 secs)
```
9. Don't forget to source the environmental variables
```console
docker@dev $ source env.sh
```

Execution
-------

All the executing scripts are in bundles/mc-veo directory of your installation.
```console
docker@hjf $ cd mc-veo/dev
docker@dev $ source env.sh
docker@dev $ cd bundles/mc-veo
```

Download one dataset in log format, for example the atrium dataset:
```console
docker:mc-veo (master) $ wget https://download.ifi.uzh.ch/rpg/eds/atrium/atrium.log -O /tmp/atrium.log
```

Run the [run_mc-veo_log.rb](https://github.com/huangfeng95/bundles-mc-veo/blob/master/scripts/slam/mc-veo/run_mc-veo_logs.rb) script in one terminal
```console
docker@mc-veo (master) $ ruby scripts/slam/mc-veo/run_mc-veo_logs.rb --dataset=atrium --log_type=davis /tmp/atrium.log
```

Open another terminal and visualize the progress with the [mc-veo_visualizaton.rb](https://github.com/huangfeng95/bundles-mc-veo/blob/master/scripts/gui/mc-veo_visualization.rb) script.
```console
docker@hjf $ cd mc-veo/dev
docker@dev $ source env.sh
docker@dev $ cd bundles/mc-veo
docker@mc-veo (master) $ ruby scripts/gui/mc-veo_visualization.rb 
```

It does not matter the order you run the scripts, visualization or running. The visualization will always wait until the running gives data.

Log Files and Conversion to Rosbag
-------

The log files are created in the `/mc-veo/dev/bundles/mc-veo/tmp` folder by default (You can change the folder [here](https://github.com/huangfeng95/bundles-mc-veo/blob/0952ca893178efdaefdde72a93672eeccedfadef/config/app.yml#L12)). The log files are named in a folder as `/tmp/date`, e.g.: `/tmp/20220401-1015` MC-VEO is not real time. You can convert the log file to the correct time by running the following command in your log folder
```console
docker@hjf $ cd mc-veo/dev
docker@dev $ source env.sh
docker@dev $ cd .bundles/mc-veo/tmp/20220401-1015
docker@20220401-1015 $ rock-convert --use_sample-time mc_veo.0.log 
```

The resulting `mc_veo.0.log` file should be in the newly created `updated` folder in `/tmp/20220401-1015`.
You can replay the log files and visualize the results with [mc-veo_visualization.rb](https://github.com/huangfeng95/bundles-mc-veo/blob/master/scripts/gui/mc-veo_visualization.rb) by doing 
```console
docker@hjf $ cd mc-veo/dev
docker@dev $ source env.sh
docker@dev $ cd bundles/mc-veo
docker@mc-veo (master) $ ruby scripts/gui/mc-veo_visualization.rb --log /tmp/20220401-1015/updated/mc_veo.0.log 
```

Pocolog files (i.e.: `mc-veo.0.log`) conversion to Rosbag is possible by running the [pocolog2rosbag.py](https://github.com/jhidalgocarrio/bundles-e2calib/blob/master/scripts/pocolog/pocolog2rosbag.py). It is explained in the [e2calib](https://github.com/huangfeng95/e2calib/) repository.


Troubleshooting
-------
In case you see CORBA related errors when runing the [run_mc-veo_log.rb](https://github.com/huangfeng95/bundles-mc-veo/blob/master/scripts/slam/mc-veo/run_mc-veo_logs.rb). Restart the omniorb deamon

```console
docker@dev $ sudo /etc/init.d/omniorb4-nameserver stop
docker@dev $ sudo rm -f /var/lib/omniorb/*
docker@dev $ sudo /etc/init.d/omniorb4-nameserver start
```

The Event-to-Image Tracker: Source Code
-------
MC-VEO source code is structure as follows:

* [buildconf](https://github.com/huangfeng95/mc-veo-buildconf): is this reposity where the bootstrapping mechanism to install MC-VEO is located.
* [bundles/mc-veo](https://github.com/huangfeng95/bundles-mc-veo): this is the bundles. Bundles are collections of all files needed to run a particular system. Those are configuration files, calibration files and script files for executing the binaries.
* [slam/mc-veo](https://github.com/huangfeng95/slam-mc-veo): this is the MC-VEO C++ library.
* [slam/orogen/mc-veo](https://github.com/huangfeng95/slam-orogen-mc-veo): this is the Task that builds a class with all the system's functionalities.

Additional Resources on Event Cameras
-------
* [Event-based Vision Survey](http://rpg.ifi.uzh.ch/docs/EventVisionSurvey.pdf)
* [List of Event-based Vision Resources](https://github.com/huangfeng95/event-based_vision_resources)
* [Event Camera Simulator](http://rpg.ifi.uzh.ch/esim)
* [RPG research page on Event Cameras](http://rpg.ifi.uzh.ch/research_dvs.html)
* [TUB course](https://sites.google.com/view/guillermogallego/teaching/event-based-robot-vision)
* [E2Calib: How to Calibrate Your Event Camera](https://github.com/huangfeng95/e2calib)
* [Event Camera in the CARLA Simulator](https://carla.readthedocs.io/en/latest/ref_sensors/#dvs-camera)
