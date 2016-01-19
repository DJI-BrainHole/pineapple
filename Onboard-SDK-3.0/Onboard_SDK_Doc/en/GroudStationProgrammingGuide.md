# Ground Station Programming Guide

This file is a simple roadmap of how to implement groudstation tasks, i.e. the waypoint task, the hotpoint task and the follow me task.

## Waypoint Task

Waypoint is an important subset of groundstation functions.
With the help of waypoint API, developers can make drone fly throught a given group of GPS coordinates.

### How to use

1. Init the Waypoint Task.

  Before starting the waypoint task, developers should upload the coordinates information. There are two parts of uploading in waypoint logic, upload the required information of the whole task at first, then upload every waypoint data within this task. Developers can start the waypoint task if and only if both of the task and they waypoint details are uploaded.
  
  To upload the task information, developers should 
  
  <code>
  
  

2. Upload Waypoint Data.

  After a succesfful update waypoint task, developers should updload the detail of every waypoint in this task one by one, with its corresponding index.
  
  <code>
  
  Other than the basic parameters of waypoint, it is also possible to set actions when reaching the waypoint. 
  
  <code>
  
  * the action

3. Start Waypoint Task

  Developers can start the waypoint task after the previous two steps.
  
  <code>

4. Other Waypoint API

  code/code/tons of code
  
## Hotpoint Task

1. Init and Start Hotpoint Task

2. Other Hotpoint API

## Follow Me Task

1. Init and Start Follow Me Task

2. Update Target Position

3. Other Follow Me API

## The Pushed Status and Event Notification

1. Status Notification 

2. Event Notification
