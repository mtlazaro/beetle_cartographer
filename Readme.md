# Multi session example

Three bag files are tested:
- First session bag: 2020-11-10-14-32-56.bag
- Second session bag: 2020-11-10-15-22-08.bag
- Third session bag: 2020-11-10-15-47-02.bag

All sessions start from the same initial pose.

## First session
We launch the offline Cartographer modality for the first session in order to speed up the process.

```
$ roslaunch beetle_cartographer demo_beetle_cartographer_offline.launch bag_filenames:=<full-path-to>/2020-11-10-14-32-56.bag
```
The offline modality automatically saves the result in <full-path-to>/2020-11-10-14-32-56.bag.pbstream when it finishes. 

## Second session

We launch the multisession modality by passing 1) previous map, 2) new bag to process.

We launch Cartographer in a first terminal. 
```
$ roslaunch beetle_cartographer demo_beetle_cartographer_multisession.launch  load_state_filename:=<full-path-to>/2020-11-10-14-32-56.bag.pbstream bag_filename:=<full-path-to>/2020-11-10-15-22-08.bag
```
The process is PAUSED at this point. In a second terminal, we call the rosservice `/start_trajectory`, providing the initial pose:
```
$ rosservice call /start_trajectory "configuration_directory: '$(rospack find beetle_cartographer)/config'
configuration_basename: 'beetle_cartographer_2d.lua'
use_initial_pose: true
initial_pose:
  position: {x: 0.0, y: 0.0, z: 0.0}
  orientation: {x: 0.0, y: 0.0, z: 0.0, w: 1.0}
relative_to_trajectory_id: 0"
```

We call this service to start a new trajectory relative to trajectory 0. Once the service is call we can press the `SPACE` bar in the first terminal to start the bag processing.

### Saving second session

The online modality does not save the .pbstream by default. Instead, we call the following service **few seconds** before the mapping process finishes:
```
$ rosservice call /write_state "filename: '<full-path-to>/2020-11-10-15-22-08.bag.pbstream'
include_unfinished_submaps: true"
```

## Third session

We proceed similarly for the third session.

We launch Cartographer in a first terminal. We provide the previous .pbstream that will contain the two previous trajectories and the new bag file to process.
```
$ roslaunch beetle_cartographer demo_beetle_cartographer_multisession.launch  load_state_filename:=<full-path-to>/2020-11-10-15-22-08.bag.pbstream bag_filename:=<full-path-to>/2020-11-10-15-47-02.bag
```
In a second terminal, we call the rosservice `/start_trajectory`, providing the initial pose:
```
$ rosservice call /start_trajectory "configuration_directory: '$(rospack find beetle_cartographer)/config'
configuration_basename: 'beetle_cartographer_2d.lua'
use_initial_pose: true
initial_pose:
  position: {x: 0.0, y: 0.0, z: 0.0}
  orientation: {x: 0.0, y: 0.0, z: 0.0, w: 1.0}
relative_to_trajectory_id: 1"
```

We call this service to start a new trajectory relative to trajectory 1. Once the service is call we can press the `SPACE` bar in the first terminal to start the bag processing.

