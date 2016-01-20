# Ground Station Programming Guide

This file is a simple roadmap of how to implement groudstation tasks, i.e. the waypoint task, the hotpoint task and the follow me task.

## Waypoint Task

Waypoint is an important subset of groundstation functions.
With the help of waypoint API, developers can make drone fly throught a given group of GPS coordinates.

### How to use

1. Init the Waypoint Task.

  Before starting the waypoint task, developers should upload the coordinates information. There are two parts of uploading in waypoint logic, upload the required information of the whole task at first, then upload every waypoint data within this task. Developers can start the waypoint task if and only if both of the task and they waypoint details are uploaded.
  
  To upload the task information, developers should 
  
  /CODE/
  
  

2. Upload Waypoint Data.

  After a succesfful update waypoint task, developers should updload the detail of every waypoint in this task one by one, with its corresponding index.
  
  /CODE/
  
  Other than the basic parameters of waypoint, it is also possible to set actions when reaching the waypoint. 
  
  /CODE/
  

3. Start Waypoint Task

  Developers can start the waypoint task after the previous two steps.
  
  /CODE/

4. Other Waypoint APIs

  There are severl other waypoint APIs developers can make use of, such as pause, resume and stop the task. Developers can also set and read the idle speed as well as the maximum speed. 
  
  Please refer to the [groundstation documents](GroundStationProtocol.md) for detail.
  
## Hotpoint Task

Hotpoint is a functionality that allows the drone to circle around a given point of interest with certain radius. Developers can 

1. Init and Start Hotpoint Task

  Unlike waypoint task, the drone will execute hotpoint task immediately after the initialization step has been finished.
  
  To initialize the hotpoint task, developers should set the coordinate of point of interest, as well as the radius, velocity and several other parameters, then upload them.
  
  /CODE/

2. Other Hotpoint APIs

  There are severl other hotpoint APIs developers can make use of, such as pause, resume and stop the task. Developers can also set and read the idle speed as well as the radius. 
  
  Please refer to the [groundstation documents](GroundStationProtocol.md) for detail.
  
## Follow Me Task

  Follow me allows your drone to follow the movement of target by itself. However, developers should update the target position and send it to drone in order to achieve the follow me movement.

1. Init and Start Follow Me Task

  Similar to hotpoint, follow me task will start immediately after the initialization step.
  
  To initialize the follow me task, developers should set the target position at first.

  /CODE/
  
2. Update Target Position

  After starting the follow me task, developers should keep telling the current position of target. i.e. update the position of target. Otherwise the drone will hover in the current position because it believes the target doesn't move.

3. Other Follow Me APIs

  There are severl other follow me APIs developers can make use of, such as pause, resume and stop the task. 
  
  Please refer to the [groundstation documents](GroundStationProtocol.md) for detail.

## The Status Push Information and Event Push Information

  There is a broadcast data of drone status in firmware 2.3.
  Similary, we have built a protocol to push groundstation information.
  
1. Status Push Information

  The status information takes up CMD SET 0x02 with CMD ID 0x03. 
  
  There are four kinds of status corresponding to the task types, waypoint, hotpoint, follow me and non of above.
  
  Please refer to the [groundstation documents](GroundStationProtocol.md) for detail.

2. Event Push Information

  The event push information takes up CMD SET 0x02 with CMD ID 0x04 and it is specifically designed for waypoint task. 
  
  There are three events included in the push info, data uploaded, task finished and waypoint reached.
  
  Please refer to the [groundstation documents](GroundStationProtocol.md) for detail.
