#Summary of changes from firmware 2.3 to firmware 3.1

**Important**: Developers who update from 2.3 to 3.1 should update their programs (or the library) accordingly as well.

Developers starting on firmware 3.1 should ignore this file and check out the document directly.



## Protocol Updates

### New

|CMD SET|CMD ID|Description|
|-------|------|-----------|
|0x01|0x05|Arm/Disarm the Drone|
|0x03||Gound Station Protocol|
|0x04|0x00|Synchronize Timestamp|
|0x05||Virtual RC Protocol|


### Changed


|CMD SET|CMD ID|Difference|2.3|3.1|
|-------|------|---|---|---|
|0x00|0x00|Version Query Result|2.3.10.0|3.1.10.0
|0x00|0x01|App Level Removed|`typedef struct{ `<br>&nbsp;&nbsp;`uint32_t app_id;`<br>&nbsp;&nbsp;`uint32_t app_level;`<br>&nbsp;&nbsp;`uint32_t app_version;`<br>&nbsp;&nbsp;`uint8_t app_bundle_id[32];`<br>`} sdk_activation_info_t;`|`typedef struct{ `<br>&nbsp;&nbsp;`uint32_t app_id;`<br>&nbsp;&nbsp;~~`uint32_t app_level;`~~<br>&nbsp;&nbsp;`uint32_t app_version;`<br>&nbsp;&nbsp;`uint8_t app_bundle_id[32];`<br>`} sdk_activation_info_t;`|
|0x00|0x01|Activation SDK Version|0x02030a00|0x03010a00|
|0x01|0x03|Attitude Control Flag|`bit7&6: HORI_MODE`<br>`bit5&4: VERT_MODE`<br>`bit3: YAW_MODE`<br>`bit2&1: YAW_MODE`<br>`bit0: YAW_FRAME`|`bit7&6: HORI_MODE`<br>`bit5&4: VERT_MODE`<br>`bit3: YAW_MODE`<br>`bit2&1: YAW_MODE`<br>`bit0: STABLE_FLAG`|
|0x01|0x1A|Gimbal Speed Control|`typedef struct{`<br>&nbsp;&nbsp;`int16_t yaw_rate;`<br>&nbsp;&nbsp;`int16_t roll_rate;`<br>&nbsp;&nbsp;`int16_t pitch_rate;`<br>&nbsp;&nbsp;`{`<br>&nbsp;&nbsp;`unsigned char reserve: 7;`<br>&nbsp;&nbsp;`uint8_t enable: 1;`<br>&nbsp;&nbsp;`}`<br>`} gimbal_speed_control_t;`|`typedef struct{`<br>&nbsp;&nbsp;`int16_t yaw_rate;`<br>&nbsp;&nbsp;`int16_t roll_rate;`<br>&nbsp;&nbsp;`int16_t pitch_rate;`<br>&nbsp;&nbsp;`{`<br>&nbsp;&nbsp;`uint8_t reservce:5;`<br>&nbsp;&nbsp;`uint8_t yaw_back_to_center:1;`<br>&nbsp;&nbsp;`uint8_t ignore_user_stick: 1;`<br>&nbsp;&nbsp;`uint8_t enable: 1;`<br>&nbsp;&nbsp;`}`<br>`} gimbal_speed_control_t;`


## Broadcast Data Updates

|Struct Changed|2.3|3.1|
|--------------|---|---|
|Time Stamp|`uint32_t`|`typedef struct`<br>`{`<br>&nbsp;&nbsp;`uint32_t time;`<br>&nbsp;&nbsp;`uint32_t asr_ts;`<br>&nbsp;&nbsp;`uint8_t sync_flag;`<br>`}sdk_time_stamp_t;`|
|Ctrl Device|`typedef struct`<br>`{`<br>&nbsp;&nbsp;`uint8_t cur_ctrl_dev_in_navi_mode: 3;`<br>&nbsp;&nbsp;`uint8_t serial_req_status: 1;`<br>&nbsp;&nbsp;`uint8_t reserved: 4;`<br>`} ctrl_device_t;`|`typedef struct`<br>`{`<br>&nbsp;&nbsp;`uint8_t cur_api_ctrl_mode;`<br>&nbsp;&nbsp;`uint8_t cur_ctrl_dev_in_navi_mode: 3;`<br>&nbsp;&nbsp;`uint8_t serial_req_status: 1;`<br>&nbsp;&nbsp;`uint8_t vrc_enable)flag: 1;`<br>&nbsp;&nbsp;`uint8_t reserved: 3;`<br>`} ctrl_device_t;`


## Other Updates

1. Drone will runs into the F mode directly when power on with mode bar in position `F`, while in 2.3 firmware, the same situation, develoeprs should switch the mode bar away then back to `F` in order to enter F mode logic.

  Developers must pay attention and be careful on this change.
