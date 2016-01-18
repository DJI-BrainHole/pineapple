#Release Notes of the 3.1 firmware

This file documents all changes between firmware 3.1 and firmware 2.3.

Developers who update from 2.3 into 3.1 should also update their program (or the library) accordingly.


## Protocol Updates

### New
arm/disarm
virtual rc
GS protocols
sync timestamp

### Changed


|CMD SET|CMD ID|Difference|2.3|3.1|
|-------|------|---|---|---|
|0x00|0x00|Version Query Result|2.3.10.0|3.1.10.0
|0x00|0x01|App Level Removed|`typedef struct{ `<br>&nbsp;&nbsp;`uint32_t app_id;`<br>&nbsp;&nbsp;`uint32_t app_level;`<br>&nbsp;&nbsp;`uint32_t app_version;`<br>&nbsp;&nbsp;`uint8_t app_bundle_id[32];`<br>`} sdk_activation_info_t;`|`typedef struct{ `<br>&nbsp;&nbsp;`uint32_t app_id;`<br>&nbsp;&nbsp;~~`uint32_t app_level;`~~<br>&nbsp;&nbsp;`uint32_t app_version;`<br>&nbsp;&nbsp;`uint8_t app_bundle_id[32];`<br>`} sdk_activation_info_t;`|
|0x00|0x01|Activation SDK Version|0x02030a00|0x03010a00|
|0x01|0x03|Attitude Control Flag|`bit7&6: HORI_MODE`<br>`bit5&4: VERT_MODE`<br>`bit3: YAW_MODE`<br>`bit2&1: YAW_MODE`<br>`bit0: YAW_FRAME`|`bit7&6: HORI_MODE`<br>`bit5&4: VERT_MODE`<br>`bit3: YAW_MODE`<br>`bit2&1: YAW_MODE`<br>`bit0: STABLE_FLAG`|
|0x01|0x1A|Gimbal Speed Control|`typedef struct{`<br>&nbsp;&nbsp;`int16_t yaw_rate;`<br>&nbsp;&nbsp;`int16_t roll_rate;`<br>&nbsp;&nbsp;`int16_t pitch_rate;`<br>&nbsp;&nbsp;`{`<br>&nbsp;&nbsp;`unsigned char reserve: 7;`<br>&nbsp;&nbsp;`uint8_t enable: 1;`<br>&nbsp;&nbsp;`}`<br>`};`|`typedef struct{`<br>&nbsp;&nbsp;`int16_t yaw_rate;`<br>&nbsp;&nbsp;`int16_t roll_rate;`<br>&nbsp;&nbsp;`int16_t pitch_rate;`<br>&nbsp;&nbsp;`{`<br>&nbsp;&nbsp;`uint8_t reservce:5;`<br>&nbsp;&nbsp;`uint8_t yaw_back_to_center:1;`<br>&nbsp;&nbsp;`uint8_t ignore_user_stick: 1;`<br>&nbsp;&nbsp;`uint8_t enable: 1;`<br>&nbsp;&nbsp;`}`<br>`};`


## Broadcast Data Updates

### New

### Changed

## Other Updates

the F
