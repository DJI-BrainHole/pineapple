# Ground Station

This part is about Goundstation related functions(Waypoint, Hotpoint and Follow Me), which has been introduced briefly in the [OPEN Protocol](OPENProtocol.md#cmd-set-0x03-ground-station-cmd-set).

In this document, we will introduce the command set and command id again at first, then show the detailed data structures of every command's data frmae. 

## Command Set and Command Id

<table>
    <tr>
        <th>CMD Set</th>
        <th>CMD Group</th>
        <th>CMD ID</th>
        <th>Description</th>
    </tr>
    <tr>
        <th rowspan="19">0x03</th>
        <th rowspan="8">Waypoint</th>
        <th>0x10</th>
        <th>upload waypoint task data</th>
    </tr>
    <tr>
        <th>0x11</th>
        <th>upload the waypoint data with certain index</th>
    </tr>
    <tr>
        <th>0x12</th>
        <th>start/stop waypoint mission</th>
    </tr>
    <tr>
        <th>0x13</th>
        <th>pause/resume waypoint mission</th>
    </tr>
    <tr>
        <th>0x14</th>
        <th>download waypoint task data</th>
    </tr>
    <tr>
        <th>0x15</th>
        <th>download certain waypoint data with given index</th>
    </tr>
    <tr>
        <th>0x16</th>
        <th>set waypoint mission idle velocity</th>
    </tr>
    <tr>
        <th>0x17</th>
        <th>read waypoint mission idle velocity</th>
    </tr>
    <tr>
        <th rowspan="7">Hotpoint</th>
        <th>0x20 </th>
        <th>upload hotpoint data and start hotpoint mission</th>
    </tr>
    <tr>
        <th>0x21</th>
        <th>stop hotpoint mission</th>
    </tr>
    <tr>
        <th>0x22</th>
        <th>pause/resume hotpoint mission</th>
    </tr>
    <tr>
        <th>0x23</th>
        <th>set hotpoint mission idle velocity</th>
    </tr>
    <tr>
        <th>0x24</th>
        <th>set hotpoint mission radius</th>
    </tr>
    <tr>
        <th>0x25</th>
        <th>reset yaw of hotpoint mission</th>
    </tr>
    <tr>
        <th>0x26</th>
        <th>download hotpoint mission data</th>
    </tr>
    <tr>
        <th rowspan="4">Follow me</th>
        <th>0x30</th>
        <th>upload follow me data and start</th>
    </tr>
    <tr>
        <th>0x31</th>
        <th>stop follow me mission</th>
    </tr>
    <tr>
        <th>0x32</th>
        <th>pause/resume follow me mission</th>
    </tr>
    <tr>
        <th>0x33</th>
        <th>upload the target data of follow me</th>
    </tr>
    <tr>
        <th>0x02</th>
        <th>Mission State Push Info</th>
        <th>0x03</th>
        <th>current mission state</th>
    </tr>
    <tr>
        <th>0x02</th>
        <th>Waypoint Mission Event Push Info</th>
        <th>0x04</th>
        <th>mission event</th>
    </tr>
</table>

## Data Structure

### 0x03, 0x10: upload waypoint task data

Request:
```c
typedef struct {

    uint8_t length;//count of waypoints
    float vel_cmd_range;//Maximum speed joystick input(2~15m)
    float idle_vel;//Cruising Speed (without joystick input, no more than vel_cmd_range)
    uint8_t action_on_finish;//Action on finish
                            //0: no action
                            //1: return to home
                            //2: auto landing
                            //3: return to point 0
                            //4: infinite mode， no exit
    uint8_t mission_exec_num;//Function execution times
                            //1: once
                            //2: twice
    uint8_t yaw_mode;//Yaw mode
                    //0: auto mode(point to next waypoint)
                    //1: Lock as an initial value
                    //2: controlled by RC
                    //3: use waypoint's yaw(tgt_yaw)
    uint8_t trace_mode;//Trace mode
                    //0: point to point, after reaching the target waypoint hover, complete waypoints action (if any), then fly to the next waypoint
                    //1: Coordinated turn mode, smooth transition between waypoints, no waypoints task
    uint8_t action_on_rc_lost;//Action on rc lost
                            //0: exit waypoint and failsafe
                            //1: continue the waypoint
    uint8_t gimbal_pitch_mode;//Gimbal pitch mode
                            //0: Free mode, no control on gimbal
                            //1: Auto mode, Smooth transition between waypoints
    double hp_lati;//Focus latitude (radian)
    double hp_longti;//Focus longitude (radian)
    float hp_alti;//Focus altitude (relative takeoff point height)
    uint8_t resv[15];//reserved
    
}waypoint_mission_info_comm_t;
```

ACK: `uint8_t`, always be 0 no matter data valid or not.

### 0x03, 0x11: upload the waypoint data with certain index

Request:
```c
typedef struct {
    double latitude;//waypoint latitude (radian)
    double longitude;//waypoint longitude (radian)
    float altitude;//waypoint altitude (relative altitude from takeoff point)
    float damping_dis;//bend length (effective coordinated turn mode only)
    int16_t tgt_yaw;//waypoint yaw (degree)
    int16_t tgt_gimbal_pitch;//waypoint gimbal pitch
    uint8_t turn_mode;//turn mode
                    //0: clockwise
                    //1: counter-clockwise
    uint8_t resv[8];//reserved
    
    uint8_t has_action;//waypoint action flag
                    //0: no action
                    //1: has action
    uint16_t action_time_limit;//waypoint action time limit unit:s
    waypoint_action_comm_t action;//waypoint action

} waypoint_comm_t

```

ACK:
```c
struct waypoint_upload_ack{
    uint8_t ack;
    uint8_t index;
};
```
    

For the waypoint action:

```c
typedef struct {
    uint8_t action_num :4;//total number of actions
    uint8_t action_rpt :4;//total running times
    
    uint8_t command_list[15];//command list, 15 at most
    int16_t command_param[15];//command param, 15 at most

}waypoint_action_comm_t
```

There are totally six kinds of actions as follows, which should be set in `command_list`.


|Commands|Commands value|Command param|Description|
|----|------|--------|----|
|WP_ACTION_STAY|0|Hover time unit: **milli**second|Just hover|
|WP_ACTION_SIMPLE_SHOT|1|N/A|Take a photo|
|WP_ACTION_VIDEO_START|2|N/A|Start record|
|WP_ACTION_VIDEO_STOP|3|N/A|Stop record|
|WP_ACTION_CRAFT_YAW|4|YAW (-180~180)|Adjust the aircraft toward|
|WP_ACTION_GIMBAL_PITCH|5|PITCH|Adjust gimbal pitch 0: head -90: look down|

## 0x03, 0x12: start/stop waypoint mission

Request:

```c
uint8_t start;//0-> start, 1-> cancel
```

ACK: `uint8_t`

## 0x03, 0x13: pause/resume waypoint mission

Request:

```c
uint8_t pause;//0-> pause, 1-> resume
```

ACK: `uint8_t`


## 0x03, 0x14: download waypoint task

Request:

`uint8_t` with arbitrary value.

ACK:

```c
struct waypoint_task_download_ack{
    uint8_t ack;
    waypoint_mission_info_comm_t wp_task;//defined in previous part
};
```

## 0x03, 0x15: download a certain waypoint

Request:

```c
uint8_t index;
```

ACK:

```c
struct waypoint_download_ack{
    uint8_t ack;
    uint8_t index;
    waypoint_comm_t wp_data;//defined in previous
};
```

## 0x03, 0x16: set idle velocity

Request:

```c
float idle_veloctity;
```

ACK:

```c
struct waypoint_set_vel_ack{
    uint8_t ack;
    float idle_velcity;
};
```

## 0x03, 0x17: read idle velocity

Request:

`uint8_t` with arbitrary value

ACK:

```c
struct waypoint_read_vel_ack{
    uint8_t ack;
    float idle_velcity;
};
```


## 0x03, 0x20: upload and start hotpoint task

Request:

```c
typedef struct{
    uint8_t version;//reserved, kept as 0
    double hp_latitude;//Hotpoint latitude (radian)
    double hp_longitude;//Hotpoint longitude (radian)
    double hp_altitude;//Hotpoint altitude (relative altitude from takeoff point
    double hp_radius;//Hotpoint radius (5m~500m)
    float angle_rate;//Angle rate (0~30°/s)
    uint8_t is_clockwise;// 0->fly in counter-clockwise direction, 1->clockwise direction
    uint8_t start_point_position;//start point position
                                //0: north to the hot point
                                //1: south to the hot point
                                //2: west to the hot point
                                //3: east to the hot point
                                //4: from current position to nearest point on the hot point
    uint8_t yaw_mode;//yaw mode
                    //0: point to velocity direction
                    //1: face inside
                    //2: face ouside
                    //3: controlled by RC
                    //4: same as the starting yaw
    
    uint8_t reserved[11];//reserved

} hotpoint_mission_setting_t;
```

ACK:

```c
struct hotpoint_upload_ack{
    uint8_t ack;
    float max_radius;
};
```

## 0x03, 0x21: stop hotpoint mission

Request:

`uint8_t` with arbitrary value

ACK: `uint8_t`

## 0x03, 0x22: pause hotpoint mission

Request:

```c
uint8_t pause; //0->pause, 1->resume
```

ACK: `uint8_t`

## 0x03, 0x23: set hotpoint idle velocity

```c
typedef struct{
    uint8_t is_clockwise;
    float idle_velocity;
}hotpoint_set_vel_t;
```

ACK: `uint8_t`

## 0x03, 0x24: set hotpoint radius

Request:

```c
float radius;
```

ACK: `uint8_t`

## 0x03, 0x25: reset hotpoint yaw

Requset:

`uint8_t` with arbitrary value

ACK: `uint8_t`

## 0x03, 0x26: download hotpoint task

Request:

`uint8_t` with arbitary value

ACK:

```c
struct hotpoint_download_ack {
    uint8_t ack;
    hotpoint_mission_setting_t hotpoint_task;
};
```

## 0x03, 0x30: upload and start follow me task

Request:

```c
typedef struct{
    uint8_t follow_mode;// follow mode(reserved), set as 0
    uint8_t yaw_mode;//yaw mode
                    //1: point to target
                    //0: controlled by RC
    double init_latitude;//initial position latitude (radian)
    double init_longitude;//initial position longitude (radian)
    uint16_t init_alti;//initial position altitude
    uint16_t init_mag_angle;//reserved
    uint8_t follow_sensitivity;//reserved, set as 0
}follow_me_mission_setting_t;
```

ACK: `uint8_t`

## 0x03, 0x31: stop follow me task

Request:

`uint8_t` with arbitary value

ACK: `uint8_t`

## 0x03, 0x32: pause/resume follow me task

Request:

```c
uint8_t pause; //0->pause, 1->resume
```

ACK: `uint8_t`

## 0x03, 0x33: update target position

Request:
 
```c
typedef struct{
    double latitude; //Target latitude (radian)
    double longitude;//Target longitude (radian)
    uint16_t altitude;//Target altitude
    uint16_t mag_angle;//reserved

}cmd_mission_follow_target_info;
```

NO ACK


## 0x02, 0x03 Current Mission State Push Information 

There are four kinds of mission states with the same struct size.

Developers can separate them by their first bytes, i.e. `mission_type`.

The 'mission_tpye' is defined with the following enum.
~~~c
typedef enum
{
    NAVI_MODE_ATTI,
    NAVI_MISSION_WAYPOINT,
    NAVI_MISSION_HOTPOINT,
    NAVI_MISSION_FOLLOWME,
    NAVI_MISSION_IOC,
}navi_type;
~~~

waypoint mission push information

```c
typedef struct{
    uint8_t mission_type; //mission type, should be NAVI_MISSION_WAYPOINT
    uint8_t target_waypoint; //current target waypoint index
    uint8_t current_state;//current state
    uint8_t error_notification;//error notification
    uint16_t reserved;//reserved

} cmd_mission_waypoint_status_push_t;
```

hotpoint mission push information

```c
typedef struct{
    uint8_t mission_type;// mission type, should be NAVI_MISSION_HOTPOINT
    uint8_t mission_status;//mission status
                        //0:init
                        //1:running
                        //2:paused
    uint16_t hp_exec_radius; //distance to the hotpoint: cm
    uint8_t reason;
    uint8_t hp_exec_vel;//angular velocity in ground frame, degree * 10
} cmd_mission_hotpoint_status_push_t;
```

follow me mission push information

```c
typedef struct{
    uint8_t mission_type;// mission type, should be NAVI_MISSION_FOLLOWM
    uint8_t reserved_1;
    uint16_t reserved_2;
    uint16_t reserved_3;
}cmd_mission_folowme_status_push_t;
```

the other two states(NAVI_MODE_ATTI & NAVI_MISSION_IOC)

```c
typedef struct{
    uint8_t mission_type;
    uint8_t last_mission_type;
    uint8_t is_broken :1;
    uint8_t reserved_1 :7;
    uint8_t reason;
    uint8_t reserved_2;
    uint8_t reserved_3;
}cmd_mission_default_status_push_t;
```

## 0x02, 0x04 Mission Event Push Information

There are three kinds of waypoint mission events with the same struct size.

Developers can separate them by their first bytes, i.e. `incident_type`.

The 'incident_type' is defined with the following enum.

~~~c
typedef enum
{
    NAVI_UPLOAD_FINISH,
    NAVI_MISSION_FINISH;
    NAVI_MISSION_WP_REACH_POINT,
}incident_type;
~~~

waypoint mission upload event push information

```c
typedef struct{
    uint8_t incident_type;
    uint8_t is_mission_valid;
    uint16_t estimated_run_time;
    uint16_t reserved;
}cmd_mission_wp_upload_incident_t;
```

waypoint mission finish event push information

```c
typedef struct{
    uint8_t incident_type;
    uint8_t repeat;
    uint16_t reserved_1;
    uint16_t reserved_2;
}cmd_mission_wp_finish_incident_t;
```

waypoint reached event push information

```c
typedef struct{
    uint8_t incident_type;
    uint8_t waypoint_index;
    uint8_t current_state;
    uint8_t reserved_1;
    uint8_t reserved_2;
}cmd_mission_wp_reached_incident_t;
```
