# Ground Station

This document is about Ground Station Function(Waypoint, Hotpoint, Follow Me). Data type and push data are introduced, more control command please refer to ground station part in [OPEN Protocol](OPENProtocol.md).

## Waypoint

### Data type: waypoint_mission_info_comm_t

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>length</td>
        <td>count of waypoints</td>
    </tr>
    <tr>
        <td>float32</td>
        <td>vel_cmd_range</td>
        <td>Maximum speed joystick input(2~15m)</td>
    </tr>
    <tr>
        <td>float32</td>
        <td>idle_vel</td>
        <td>Cruising Speed (without joystick input, no more than vel_cmd_range)</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>action_on_finish</td>
        <td>Action on finish<ul>
            <li>0: no action</li>
            <li>1: return to home</li>
            <li>2: auto landing</li>
            <li>3: return to point 0</li>
            <li>4: infinite mode， no exit</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_exec_num</td>
        <td>Function execution times<ul>
            <li>1: once</li>
            <li>2: twice</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>yaw_mode</td>
        <td>Yaw mode<ul>
            <li>0: auto mode(point to next waypoint)</li>
            <li>1: Lock as an initial value</li>
            <li>2: controlled by RC</li>
            <li>3: use waypoint's yaw(tgt_yaw)</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>trace_mode</td>
        <td>Trace mode<ul>
            <li>0: point to point, after reaching the target waypoint hover, complete waypoints action (if any), then fly to the next waypoint</li>
            <li>1: Coordinated turn mode, smooth transition between waypoints, no waypoints task</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>action_on_rc_lost</td>
        <td>Action on rc lost<ul>
            <li>0: exit waypoint and failsafe</li>
            <li>1: continue the waypoint</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>gimbal_pitch_mode</td>
        <td>Gimbal pitch mode<ul>
            <li>0: Free mode, no control on gimbal</li>
            <li>1: Auto mode, Smooth transition between waypoints</li>
        </ul></td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_lati</td>
        <td>Focus latitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_longti</td>
        <td>Focus longitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_alti</td>
        <td>Focus altitude (relative takeoff point height)</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>resv[15]</td>
        <td>reserved</td>
    </tr>
</table>


### Data Type: waypoint_comm_t

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>double</td>
        <td>lati</td>
        <td>waypoint latitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>longti</td>
        <td>waypoint longitude (radian)</td>
    </tr>
    <tr>
        <td>float</td>
        <td>alti</td>
        <td>waypoint altitude (relative takeoff point height)</td>
    </tr>
    <tr>
        <td>float</td>
        <td>damping_dis</td>
        <td>bend length (effective coordinated turn mode only)</td>
    </tr>
    <tr>
        <td>int16_t</td>
        <td>tgt_yaw</td>
        <td>waypoint yaw (degree)</td>
    </tr>
    <tr>
        <td>int16_t</td>
        <td>tgt_gimbal_pitch</td>
        <td>waypoint gimbal pitch</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>turn_mode</td>
        <td>turn mode<ul>
            <li>0: clockwise</li>
            <li>1: counter-clockwise</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>resv[8]</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>has_action</td>
        <td>waypoint action flag<ul>
            <li>0: no action</li>
            <li>1: has action</li>
        </ul>
        </td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>action_time_limit</td>
        <td>waypoint action time limit unit:s</td>
    </tr>
    <tr>
        <td>waypoint_action_comm_t</td>
        <td>action</td>
        <td>waypoint action</td>
    </tr>
</table>

### Data Type: waypoint_action_comm_t
<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t :4</td>
        <td>action_num</td>
        <td>count of action</td>
    </tr>
    <tr>
        <td>uint8_t :4</td>
        <td>action_rpt</td>
        <td>Repetitions</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>command_list[WP_ACTION_MAX_NUM]</td>
        <td>command list</td>
    </tr>
    <tr>
        <td>int16_t</td>
        <td>command_param[WP_ACTION_MAX_NUM]</td>
        <td>command param</td>
    </tr>
</table>

### Commands


|Commands|Commands value|Command param|Description|
|----|------|--------|----|
|WP_ACTION_STAY|0|Hover time unit：millisecond|Just hover|
|WP_ACTION_SIMPLE_SHOT|1|N/A|Take a photo|
|WP_ACTION_VIDEO_START|2|N/A|Start record|
|WP_ACTION_VIDEO_STOP|3|N/A|Stop record|
|WP_ACTION_CRAFT_YAW|4|YAW (-180~180)|Adjust the aircraft toward|
|WP_ACTION_GIMBAL_PITCH|5|PITCH|Adjust gimbal pitch 0: head -90: look down|



## Hotpoint
### Data Type: hotpoint_mission_setting_t

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>version</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_lati</td>
        <td>Hotpoint latitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_lonti</td>
        <td>Hotpoint longitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_alti</td>
        <td>Hotpoint altitude (relative takeoff point height)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>hp_radius</td>
        <td>Hotpoint radius (5m~500m)</td>
    </tr>
    <tr>
        <td>float</td>
        <td>angle_rate</td>
        <td>Angle rate (0~30°/s)</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>is_clockwise</td>
        <td>is clockwise<ul>
            <li>0: counter-clockwise</li>
            <li>1: clockwise</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>start_point</td>
        <td>start point<ul>
            <li>0: north to the hot point</li>
            <li>1: south to the hot point</li>
            <li>2: west to the hot point</li>
            <li>3: east to the hot point</li>
            <li>4: from current position to nearest point on the hot point</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>yaw_mode</td>
        <td>yaw mode<ul>
            <li>0: point to velocity direction</li>
            <li>1: align to hot point inside</li>
            <li>2: align to hot point ouside</li>
            <li>3: controlled by RC</li>
            <li>5: point to counter velocity direction</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reserved[11]</td>
        <td>reserved</td>
    </tr>
</table>
## Follow Me

### Data Type: follow_me_mission_setting_t

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>follow_mode</td>
        <td>follow mode(reserved)</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>yaw_mode</td>
        <td>yaw mode<ul>
            <li>1: point to target</li>
            <li>0: controlled by RC</li>
        </ul></td>
    </tr>
    <tr>
        <td>double</td>
        <td>init_latitude</td>
        <td>init_latitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>init_longitude</td>
        <td>init_longitude (radian)</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>init_alti</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>init_mag_angle</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>follow_sensitivity</td>
        <td>reserved</td>
    </tr>
</table>

### Data Type: cmd_mission_follow_target_info

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>double</td>
        <td>lati</td>
        <td>Target latitude (radian)</td>
    </tr>
    <tr>
        <td>double</td>
        <td>lonti</td>
        <td>Target longitude (radian)</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>alti</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>mag_angle</td>
        <td>reserved</td>
    </tr>
</table>
---
## CMD SET 0x02 CMD ID 0x03 Ground Station State
State type is defined by first byte 'mission_type' in state. The 'mission_tpye' is defined with the following enum.
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
###Waypoint(NAVI_MISSION_WAYPOINT)

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_type</td>
        <td>mission type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>target_waypoint</td>
        <td>target waypoint</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>curr_state</td>
        <td>current state</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>error_notification</td>
        <td>error notification</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>reserved</td>
        <td>reserved</td>
    </tr>
</table>
###Hotpoint (NAVI_MISSION_HOTPOINT)

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_type</td>
        <td>mission type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_status</td>
        <td>mission status<ul>
            <li>0:init</li>
            <li>1:running</li>
            <li>2:paused</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>hp_exec_radius</td>
        <td>distance to hotpoint：cm</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reason</td>
        <td>state</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>hp_exec_vel</td>
        <td>surround linear speed * 10 and rounding</td>
    </tr>
</table>
###Follow Me(NAVI_MISSION_FOLLOWME)

<table>
    <tr>6
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_type</td>
        <td>mission type</td>
    </tr>
 <tr>
        <td>uint8_t:4</td>
        <td>mission_status</td>
        <td>mission status<ul>
            <li>0: init</li>
            <li>1: following</li>
            <li>2: waitting（The GPS level of UAV is lower than 5；The disconnection with target info has lasted over 6 seconds.）</li>
        </ul></td>
    </tr>
        <tr>
        <td>uint8_t:4</td>
        <td>gps_level</td>
        <td>GPS level<ul>
            <li>0: GPS_SIGNAL_LEVEL_0</li>
            <li>1: GPS_SIGNAL_LEVEL_1</li>
            <li>2: GPS_SIGNAL_LEVEL_2</li>
            <li>3: GPS_SIGNAL_LEVEL_3</li>
            <li>4: GPS_SIGNAL_LEVEL_4</li>
            <li>5: GPS_SIGNAL_LEVEL_5</li>
        </ul></td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>drone2target_dist</td>
        <td>the distance from UAV to target unit: cm</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reason</td>
        <td>error code</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>package_interval_time</td>
        <td>package_interval_time unit: 0.02s</td>
    </tr>
</table>
###Other State(NAVI_MODE_ATTI & NAVI_MISSION_IOC)

<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>mission_type</td>
        <td>mission type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>last_mission_type</td>
        <td>last mission type</td>
    </tr>
    <tr>
        <td>uint8_t :1</td>
        <td>is_broken</td>
        <td>mission_type = NAVI_MODE_ATTI: <ul>mission automatically exits because conditions are not satisfied</ul>
            mission_type = NAVI_MODE_IOC: <ul>IOC cannot be executed because conditions are not satisfied</ul></td>
    </tr>
    <tr>
        <td>uint8_t :7</td>
        <td>reserved1</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reason</td>
        <td>error code, when 'is_broken' is true.</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reserved2</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reserved3</td>
        <td>reserved</td>
    </tr>
</table>



## CMD SET 0x02 CMD ID 0x04 Waypoint Event
First Byte 'incident_type' is the type of event. Event is defined with the following enum.
~~~c
typedef enum
{
    NAVI_UPLOAD_FINISH,
    NAVI_MISSION_FINISH,
    NAVI_MISSION_WP_REACH_POINT,
}incident_type;
~~~
### Data upload(NAVI_UPLOAD_FINISH)
<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>incident_type</td>
        <td>incident type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>is_mission_vaild</td>
        <td>is mission vaild</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>estimated_run_time</td>
        <td>estimated run time</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>reserved</td>
        <td>reserved</td>
    </tr>
</table>
### Waypoint finish(NAVI_MISSION_FINISH)
<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>incident_type</td>
        <td>incident type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>repeat</td>
        <td>action on finish</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>reserved</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>reserved2</td>
        <td>reserved</td>
    </tr>
</table>
### Reach waypoint(NAVI_MISSION_WP_REACH_POINT)
<table>
    <tr>
        <th>Data Type</th>
        <th>Data Name</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>incident_type</td>
        <td>incident type</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>waypoint_index</td>
        <td>waypoint index</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>curr_state</td>
        <td>current state</td>
    </tr>
    <tr>
        <td>uint8_t</td>
        <td>reserved</td>
        <td>reserved</td>
    </tr>
    <tr>
        <td>uint16_t</td>
        <td>reserved2</td>
        <td>reserved</td>
    </tr>
</table>
