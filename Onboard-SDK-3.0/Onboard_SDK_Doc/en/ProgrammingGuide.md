# Onboard SDK Programming Guide
---
All structures and functions are implemented in `/TODO/`. For more details, please refer source code.

---
## Callback mechanism

For all commands which has return value mentioned in OPEN Protocol, developers can get the return value by callback functions.

Activation function as an example:   

1.Define the callback function.  

/TODO/

2.Pass the name of callback function when you call to activate.

/TODO/

3.The meaning of return values(result) is explained in each commands in [OPEN Protocol](OPENProtocol.md#cmd-val--ack-val).

## Activation

/TODO/

## Obtain/Release Control Authorization

Before obtaining Control Authorization, please ensure that: 

* The 'enable API control' box is checked in the assistant software.
* The IOC mode inside the DJI GO APP is off.
* The mode selection bar of the remote controller is placed at the F position.

/TODO/

## Take off, Land and Return to home (RTH)
The return value of this function please refer to [Request Switch Result](OPENProtocol.md#cmd-id-0x02-request-switch-result)(Below codes do not use callback function).  

/TODO/

## Movement Control

We recommend developers to send yours Movement Control commands in 50Hz frequency. Developers can implement that by `usleep(20000)`、`ros::Duration(1/50)` or other ways which depend on the develop environment.

In Movement Control, specific meanings of arguements are decided by control mode byte. For more info about Movement Control, please refer to [Control mode byte part in Appendix](Appendix.md#control-mode-byte).

We recommend developers to use `HORI_POS` mode in horizontal movement. More details are shown in [Position Control](ProgrammingGuide.md#position-controlhori_pos) in this document. In this mode, speed and attitude are controlled by autopilot, thus developers do not concern about that.
    
> Please note that if the following conditions are met that the control mode is functional:
> 
* Only when the GPS signal is good (health\_flag >=3)，horizontal position control (HORI_POS) related control modes can be used.
* Only when GPS signal is good (health\_flag >=3)，or when Gudiance system is working properly with Autopilot，horizontal velocity control(HORI_VEL)related control modes can be used.


/TODO/

## Receive Flight Data
If developers want to get Flight Data, please check corresponding item in DJI assistant software. And examine the coordinate of part data.

Developers need to declare correct structure variables to save Flight Data.

Get quaternion as an example:  
1. Declare quaternion struction

/TODO/


2、Get the quaternion

/TODO/

Other data types and functions to obtain the corresponding data sent outside from autopilot, please refer `/TODO/`.

## GPS to North-East Coordinate
Convert GPS to North-East Coordinate. (GPS in radian，North-East Coordinate in meter)
For example, `origin_longti` and `origin_lati` , as the longitude and latitude of original position，are decided by developers and the position of UAV taking off is recommended to be the original position. `longti` and `lati` are longitude and latitude of UAV's current posistion. `x` and `y` are offset to the original position in the North and the East directions. The unit of offset is meter.

~~~c
#define C_EARTH (double) 6378137.0
/* From GPS to Ground */
{
    double dlati = lati-origin_lati;
    double dlongti= longti-origin_longti;

    double x = dlati * C_EARTH;
    double y = dlongti * C_EARTH * cos(lati / 2.0 + origin_lati / 2.0);
}
~~~

## Quaternion to RPY
Convert quaternion to roll, pitch and yaw in radian in body coordinate.
~~~c
    api_quaternion_data_t q;
    DJI_Pro_Get_Quaternion(&q);

    float roll  = atan2(2.0 * (q.q3 * q.q2 + q.q0 * q.q1) , 1.0 - 2.0 * (q.q1 * q.q1 + q.q2 * q.q2));
    float pitch = asin(2.0 * (q.q2 * q.q0 - q.q3 * q.q1));
    float yaw   = atan2(2.0 * (q.q3 * q.q0 + q.q1 * q.q2) , - 1.0 + 2.0 * (q.q0 * q.q0 + q.q1 * q.q1));
~~~

## Position Control(HORI_POS)
The input horizatal arguements is the offset between current position and target position, when `HORI_POS` as the mode of horizatal movement control. The unit of offset is meter.

For example, in ground frame, `target` is target position and `current` is UAV's current position. The coordinates of these positions are caculated by GPS, Guidance or other sensors. In most cases, GPS is a correct way to do this work.

Because, to autopilot, the maximum frequency of receiving data is 50Hz, the frequency of caculating off set should be over 50Hz to ensure the controlling is vaild.  

~~~c
void update_offset()
{
    offset_x = target_x - current_x;
    offset_y = target_y - current_y;
}


/* Command thread */

/TODO/
~~~


