# Archer AR612 Core configurator

> [!TIP]
> Uses for:
> * Core manufacture setup
> * Core calibration

- [Installation](#installation)
- [Usage](#usage)
    - [Read the parameters](#read-the-parameters)
    - [Write the parameters](#write-the-parameters)
    - [Save runtime changes](#save-runtime-changes)
    - [Commands list](#commands-list)

- [Available params](#available-params)
    - [General](#general)
    - [Image params](#image-params)
    - [Calibration](#calibration)
    - [Bad points removal](#bad-points-removal)
    - [Playback options](#playback-options)
    - [OLED params](#oled-params)
    - [Core control](#core-control)
    - [Programming data](#programming-data)

    - [Advanced](#advanced)
        - [Debugging instructions](#debugging-instructions)
        - [Image parameter 1](#image-parameter-1)
        - [Image parameter 2](#image-parameter-2)
        - [Detector parameters](#detector-parameters)
        - [Peripheral sampling information](#peripheral-sampling-information)

- [Build](#build)
- [Known issues](#known-issues)

### Installation

* [From releases](https://github.com/o-murphy-tvt/archer-ar612-cfg/releases)
* Git clone:

```shell
git clone https://github.com/o-murphy-tvt/archer-ar612-cfg
cd archer-ar612-cfg
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python -m archer-ar612-cfg
```

* Build:

```shell
pyinstaller archer-ar612-cfg.spec
```

## Usage

The app uses simple scripts for configuring the CORE
It allows to use [COMMANDS](#commands-list) and [PARAMS](#available-params)
The [COMMANDS](#commands-list) runs a specific behaviour,
but the [PARAMS](#available-params) allows to set a specific values for configuration,
some [COMMANDS](#commands-list) binded directly to commonly used [PARAMS](#available-params)

> [!IMPORTANT]
> Each command must have a semicolon at the end

> [!TIP]
> You can add the comments to your script,
> put the comment into `/* comment string */`

### Read the parameters

For reading the specific parameter value or all values from parameters group use this synthax

```Bash
get <index>;

// read single params
get 0x330; /* OLED Backlight */
get 0x331; /* OLED Brightness */
get 0x332; /* OLED Contrast */

// read params group
get 0x31a; /* get detector_parameters */
```

For reading the specific parameter with bit mask use `:`

```Bash
get <index>:<bit>;

// read param with bitmask:
get 0x31a:4; /* get detector_parameters:sensor_cap */
```

set 0x31a:4 5;\t/* set detector_parameters:sensor_cap */

### Write the parameters

For write the specific parameter value use this synthax

```Bash
set <index> <value>;

// write single params
set 0x330 80; /* OLED Backlight */
```

For writing the specific parameter with bit mask use `:`

```Bash
set <index>:<bit> <value>;

// read param with bitmask:
set 0x31a:4 5; /* set detector_parameters:sensor_cap */ 
```

### Save runtime changes

To save runtime changes to ROM add the `save;` command in the end of script

```Bash
/* set backlight */
set 0x330 80;

/* save changes */
save;
```

### Commands list

| Command               | Behaviour                  | Values | Note |
|:----------------------|:---------------------------|--------|------|
| **>> BASIC**          |                            |        |      |
| `save`                | save current configuration | -      | -    |
| `ffc_on`              | enable auto shutter        | -      | -    |
| `ffc_off`             | disable auto shutter       | -      | -    |
| `set_ffc_time`        | set shutter timeout        | 1-10   | -    |
| `set_vcom`            | set oled backlight         | 0-200  | -    |
| `set_oled_brightness` | set oled brightness        | 0-100  | -    |
| `set_oled_contrast`   | set oled contrast          | 0-100  | -    |
|                       |                            |        |      |
| **>> CALIBRATION**    |                            |        |      |
| `collect_cold`        | collect cold background    | -      | -    |
| `collect_hot`         | collect hot background     | -      | -    |
| `calculate_k`         | collect calibration data   | -      | -    |
| `save_k`              | save calibration data      | -      | -    |
|                       |                            |        |      |
| **>> ADVANCED**       |                            |        |      |
| `sensor_cap_5`        | sets 0x31a:4 to 5          | -      | -    |
| `sensor_cap_7`        | sets 0x31a:4 to 7          | -      | -    |

restore_sensor_settings;\t\trestores 0x31a defaults

save;\t\tsave current configuration

## Available params

> [!IMPORTANT]
> Parameters safety
> (issue description are in notes)
> - :no_entry: - 100% TURNS THE CORE TO A BRICK
> - :warning: - CAN DAMAGE THE CORE
> - :question: - NOT TESTED
> - :white_check_mark: - SAFE FOR READ/WRITE OPERATIONS

### General

| Index   | Name                     | Tag             | Attribs |        Safe        | Values | Note |
|:--------|:-------------------------|:----------------|:-------:|:------------------:|--------|------|
| `0x200` | Project code             | `project_code`  |   -r    | :white_check_mark: | -      | -    |
| `0x201` | FPGA config version      | `fpga_cfg_ver`  |   -r    | :white_check_mark: | -      | -    |
| `0x202` | FPGA soft core version   | `fpga_core_ver` |   -r    | :white_check_mark: | -      | -    |
| `0x204` | Parameter storage        | `save_params`   |   -w    | :white_check_mark: | -      | -    |
| `0x300` | Restore factory settings | `factory_reset` |   -w    | :white_check_mark: | -      | -    |

### Image params

| Index   | Name                 | Tag               | Attribs |        Safe        | Values    | Note |
|:--------|:---------------------|:------------------|:-------:|:------------------:|-----------|------|
| `0x301` | FFC                  | `ffc`             |   -w    | :white_check_mark: | -         | -    |
| `0x302` | polarity             | `polarity`        |   -w    |     :question:     | -         | -    |
| `0x33a` | rotate               | `rotate`          |   -w    | :white_check_mark: | -         | -    |
| `0x33b` | magnification        | `magnification`   |   -w    | :white_check_mark: | 1/2/3/4/6 | -    |
| `0x304` | Image mode           | `image_mode`      |   -w    |     :question:     | -         | -    |
| `0x305` | Contrast             | `contrast`        |   -rw   | :white_check_mark: | 1-100     | -    |
| `0x306` | Brightness           | `brightness`      |   -rw   | :white_check_mark: | 1-100     | -    |
| `0x307` | Enhanced             | `enhanced`        |   -rw   | :white_check_mark: | 1-100     | -    |
| `0x311` | real-time data       | `rt_data`         |   -w    |     :question:     | -         | -    |
| `0x415` | Automatic correction | `auto_correction` |   -rw   |     :question:     | -         | -    |

### Calibration

| Index   | Name                                | Tag                 | Attribs |        Safe        | Values | Note |
|:--------|:------------------------------------|:--------------------|:-------:|:------------------:|--------|------|
| `0x313` | Collect high temperature background | `collect_hot`       |   -w    | :white_check_mark: | -      | -    |
| `0x314` | Collect low temperature background  | `collect_cold`      |   -w    | :white_check_mark: | -      | -    |
| `0x403` | Collect shutter background          | `collect_shutter`   |   -w    |     :question:     | -      | -    |
| `0x404` | Collect scene background            | `collect_scene`     |   -w    |     :question:     | -      | -    |
| `0x315` | calculate (K)                       | `calculate_k`       |   -w    | :white_check_mark: | -      | -    |
| `0x316` | initialization                      | `initialisation`    |   -w    |     :question:     | -      | -    |
| `0x320` | flash load K                        | `flash_load_k`      |   -w    |     :question:     | -      | -    |
| `0x321` | Save K                              | `save_k`            |   -w    | :white_check_mark: | -      | -    |
| `0x324` | flash loading bad pixels            | `flash_load_bad_px` |   -w    |     :question:     | -      | -    |
| `0x325` | save bad pixels                     | `save_bad_px`       |   -w    | :white_check_mark: | -      | -    |

###                                                                           

| Index   | Name         | Tag            | Attribs |    Safe    | Values | Note |
|:--------|:-------------|:---------------|:-------:|:----------:|--------|------|
| `0x32a` | Delta Switch | `delta_switch` |   -rw   | :question: | -      | -    |
| `0x414` | Delta Test   | `delta_test`   |   -rw   | :question: | -      | -    |

###                                                                           

| Index   | Name                    | Tag                       | Attribs |    Safe    | Values | Note |
|:--------|:------------------------|:--------------------------|:-------:|:----------:|--------|------|
| `0x32b` | flash load delta        | `flash_load_delta`        |   -w    | :question: | -      | -    |
| `0x32c` | save delta              | `save_delta`              |   -w    | :question: | -      | -    |
| `0x32d` | Temperature             | `temperature`             |   -w    | :question: | -      | -    |
| `0x405` | delt shutter switch     | `delt_shutter_switch`     |   -rw   | :question: | -      | -    |
| `0x406` | flash load delt shutter | `flash_load_delt_shutter` |   -w    | :question: | -      | -    |
| `0x407` | save delt shutter       | `save_delt_shutter`       |   -w    | :question: | -      | -    |

### Bad points removal

| Index   | Name                           | Tag                 | Attribs |        Safe        | Values | Note |
|:--------|:-------------------------------|:--------------------|:-------:|:------------------:|--------|------|
| `0x326` | Bad points X coordinate        | `bad_point_x`       |   -rw   | :white_check_mark: | -      | -    |
| `0x327` | Bad points Y coordinate        | `bad_point_y`       |   -rw   | :white_check_mark: | -      | -    |
| `0x328` | Bad points switch              | `bad_point_cross`   |   -rw   | :white_check_mark: | -      | -    |
| `0x329` | Bad point correction control-w | `bad_point_kill`    |   -w    | :white_check_mark: | -      | -    |
| `0x410` | Bad point cancel control       | `bad_point_restore` |   -w    | :white_check_mark: | -      | -    |

### Playback options

| Index   | Name            | Tag               | Attribs |    Safe    | Values | Note |
|:--------|:----------------|:------------------|:-------:|:----------:|--------|------|
| `0x32e` | Playback switch | `playback_switch` |   -w    | :question: | -      | -    |
| `0x32f` | test image      | `test_image`      |   -w    | :question: | -      | -    |
| `0x333` | playback source | `playback_source` |   -w    | :question: | -      | -    |

### OLED params

| Index   | Name            | Tag               | Attribs |        Safe        | Values | Note |
|:--------|:----------------|:------------------|:-------:|:------------------:|--------|------|
| `0x33c` | OLED display    | `oled_display`    |   -w    |     :question:     | -      | -    |
| `0x330` | OLED Backlight  | `oled_backlight`  |   -rw   | :white_check_mark: | -      | -    |
| `0x331` | OLED Brightness | `oled_brightness` |   -rw   | :white_check_mark: | -      | -    |
| `0x332` | OLED Contrast   | `oled_contrast`   |   -rw   | :white_check_mark: | -      | -    |
| `0x400` | OLED RFFSET     | `oled_roffset`    |   -rw   |     :question:     | -      | -    |
| `0x401` | OLED GOFFSET    | `oled_goffset`    |   -rw   |     :question:     | -      | -    |
| `0x402` | OLED BOFFSET    | `oled_boffset`    |   -rw   |     :question:     | -      | -    |
| `0x40d` | OLED VHSCAN     | `oled_vhscan`     |   -rw   |     :question:     | -      | -    |
| `0x40e` | OLED_LUT_ARRR   | `oled_lut_arrr`   |   -rw   |     :question:     | -      | -    |
| `0x40f` | OLED_LUT_DATA   | `oled_lut_data`   |   -rw   |     :question:     | -      | -    |

###                                                                           

| Index   | Name                                    | Tag                 | Attribs |        Safe        | Values | Note |
|:--------|:----------------------------------------|:--------------------|:-------:|:------------------:|--------|------|
| `0x334` | Vertical stripe parameter 1             | `vertical_stripe_1` |   -rw   |     :question:     | -      | -    |
| `0x335` | Vertical stripe parameter 2             | `vertical_stripe_2` |   -rw   |     :question:     | -      | -    |
| `0x336` | Auto bad point removal thresholdF 0-100 | `auto_bad_point_th` |   -rw   | :white_check_mark: | -      | -    |
| `0x338` | Auto FFC                                | `auto_ffc`          |   -rw   | :white_check_mark: | -      | -    |
| `0x339` | Auto FFC time                           | `auto_ffc_time`     |   -rw   | :white_check_mark: | -      | -    |
| `0x33d` | Cross X coordinate                      | `cross_x_coord`     |   -rw   |     :question:     | -      | -    |
| `0x33e` | Cross Y coordinate                      | `cross_y_coord`     |   -rw   |     :question:     | -      | -    |

### Core control

| Index   | Name                         | Tag                      | Attribs |    Safe    | Values | Note |
|:--------|:-----------------------------|:-------------------------|:-------:|:----------:|--------|------|
| `0x33f` | LED                          | `led`                    |   -w    | :question: | -      | -    |
| `0x408` | POWER                        | `power`                  |   -w    | :question: | -      | -    |
| `0x409` | proximity_switch_pwm         | `proximity_switch_pwm`   |   -rw   | :question: | -      | -    |
| `0x40a` | proximity_switch_frequency r | `proximity_switch_freq`  |   -w    | :question: | -      | -    |
| `0x40b` | LASER_POWER                  | `laser_power`            |   -rw   | :question: | -      | -    |
| `0x40c` | OsdCursor_CenterData         | `osd_cursor_center_data` |         | :question: | -      | -    |
| `0x411` | Cursor X Center              | `cursor_x_center`        |   -rw   | :question: | -      | -    |
| `0x412` | Cursor Y Center              | `cursor_y_center`        |   -rw   | :question: | -      | -    |
| `0x413` | osd_data                     | `osd_data`               |   -w    | :question: | -      | -    |
| `0x417` | bno055_remap_value           | `bno055_remap_value`     |   -rw   | :question: | -      | -    |
| `0x418` | bno055_remap_sign            | `bno055_remap_sign`      |   -rw   | :question: | -      | -    |

### Programming data

| Index   | Name                  | Tag | Attribs |    Safe    | Values | Note |
|:--------|:----------------------|:----|:-------:|:----------:|--------|------|
| `0x500` | Programming address   |     |   -rw   | :question: | -      | -    |
| `0x501` | Programming interface |     |   -rw   | :question: | -      | -    |

## Advanced

### Debugging instructions

| Index      | Name                              | Tag                       | Attribs |    Safe    | Values | Note |
|:-----------|:----------------------------------|:--------------------------|:-------:|:----------:|--------|------|
| `0x317`    | **>> Debugging instructions**     | debug                     |   -r    | :question: | -      | -    |
|            |                                   |                           |         |            |        |      |
| `0x317:00` | Analog video X-axis origin offset | `:av_x_offset`            |         | :question: | -      | -    |
| `0x317:01` | Analog video Y-axis origin offset | `:av_x_offset`            |         | :question: | -      | -    |
| `0x317:02` | on the keys                       | `:on_the_keys`            |         | :question: | -      | -    |
| `0x317:03` | under button                      | `:under_button`           |         | :question: | -      | -    |
| `0x317:04` | Press button to confirm           | `:press_btn_to_confirm`   |         | :question: | -      | -    |
| `0x317:05` | Cancel button                     | `:cancel_btn`             |         | :question: | -      | -    |
| `0x317:06` | X2000 wifi on                     | `:wifi_on`                |   -w    | :question: | -      | -    |
| `0x317:07` | X2000 wifi off                    | `:wifi_off`               |   -w    | :question: | -      | -    |
| `0x317:08` | ARM programming                   | `:arm_programming`        |         | :question: | -      | -    |
| `0x317:09` | OLED brightness adjustment        | `:oled_bright_adjustment` |         | :question: | -      | -    |
| `0x317:10` | Turn off the power                | `:turn_off_power`         |         | :question: | -      | -    |
| `0x317:11` | arm status                        | `:arm_status`             |         | :question: | -      | -    |
| `0x317:12` | arm reset                         | `:arm_reset`              |         | :question: | -      | -    |
| `0x317:13` | arm normal                        | `:arm_normal`             |         | :question: | -      | -    |
| `0x317:14` | Long press the camera button      | `:long_press_cam_btn`     |         | :question: | -      | -    |
| `0x317:15` | Short press the camera button     | `:short_press_cam_btn`    |         | :question: | -      | -    |

### Image parameter 1

| Index      | Name                     | Tag                      | Attribs |    Safe    | Values | Note |
|:-----------|:-------------------------|:-------------------------|:-------:|:----------:|--------|------|
| `0x318`    | **>> Image parameter 1** | image_params_1           |   -r    | :question: | -      | -    |
|            |                          |                          |         |            |        |      |
| `0x318:00` | agc_mode                 | `:agc_mode`              |         | :question: | -      | -    |
| `0x318:01` | agc_dde_en               | `:agc_dde_en`            |         | :question: | -      | -    |
| `0x318:02` | agc_base_sel             | `:agc_base_sel`          |         | :question: | -      | -    |
| `0x318:03` | agc_pause                | `:agc_pause`             |         | :question: | -      | -    |
| `0x318:04` | agc_filter_tc            | `:agc_filter_tc`         |         | :question: | -      | -    |
| `0x318:05` | agc_linear_gain_adj      | `:agc_linear_gain_adj`   |         | :question: | -      | -    |
| `0x318:06` | agc_clahe_mode           | `:agc_clahe_mode`        |         | :question: | -      | -    |
| `0x318:07` | agc_manual_gain          | `:agc_manual_gain`       |         | :question: | -      | -    |
| `0x318:08` | agc_manual_offset        | `:agc_manual_offset`     |         | :question: | -      | -    |
| `0x318:09` | agc_roi_left             | `:agc_roi_left`          |         | :question: | -      | -    |
| `0x318:10` | agc_roi_right            | `:agc_roi_right`         |         | :question: | -      | -    |
| `0x318:11` | agc_roi_top              | `:agc_roi_top`           |         | :question: | -      | -    |
| `0x318:12` | agc_roi_bottom           | `:agc_roi_bottom`        |         | :question: | -      | -    |
| `0x318:13` | agc_linear_gain_limit    | `:agc_linear_gain_limit` |         | :question: | -      | -    |
| `0x318:14` | agc_limit                | `:agc_limit`             |         | :question: | -      | -    |
| `0x318:15` | agc_usr_min              | `:agc_usr_min`           |         | :question: | -      | -    |
| `0x318:16` | agc_usr_max              | `:agc_usr_max`           |         | :question: | -      | -    |
| `0x318:17` | agc_detail_gain          | `:agc_detail_gain`       |         | :question: | -      | -    |
| `0x318:18` | agc_detail_threshold     | `:agc_detail_threshold`  |         | :question: | -      | -    |
| `0x318:19` | agc_mix_ratio            | `:agc_mix_ratio`         |         | :question: | -      | -    |
| `0x318:20` | agc_tail_percent_min     | `:agc_tail_percent_min`  |         | :question: | -      | -    |
| `0x318:21` | agc_tail_percent_max     | `:agc_tail_percent_max`  |         | :question: | -      | -    |

### Image parameter 2

| Index      | Name                     | Tag              | Attribs |    Safe    | Values | Note |
|:-----------|:-------------------------|:-----------------|:-------:|:----------:|--------|------|
| `0x319`    | **>> Image parameter 2** | `image_params_2` |   -r    | :question: | -      | -    |
|            |                          |                  |         |            |        |      |
| `0x319:00` | sfilter_parameter        | `:s_filter`      |   -rw   | :question: | -      | -    |
| `0x319:01` | tfilter_parameter        | `:t_filter`      |   -rw   | :question: | -      | -    |

### Detector parameters

| Index      | Name                       | Tag                   | Attribs |    Safe    | Values      | Note                            |
|:-----------|:---------------------------|:----------------------|:-------:|:----------:|-------------|---------------------------------|
| `0x31a`    | **>> Detector parameters** | `detector`            |   -r    | :question: | -           | -                               |
|            |                            |                       |         |            |             |                                 |
| `0x31a:00` | sensor_int                 | `:sensor_int`         |   -rw   | :warning:  | -           | -                               |
| `0x31a:01` | sensor_rst                 | `:sensor_rst`         |   -rw   | :warning:  | -           | -                               |
| `0x31a:02` | sensor_xmir                | `:sensor_xmir`        |   -rw   | :warning:  | -           | -                               |
| `0x31a:03` | sensor_ymir                | `:sensor_ymir`        |   -rw   | :warning:  | -           | -                               |
| `0x31a:04` | sensor_cap                 | `:sensor_cap`         |   -rw   | :warning:  | -           | can turns core to brick         |
| `0x31a:05` | sensor_sfb                 | `:sensor_sfb`         |   -rw   | :warning:  | -           | -                               |
| `0x31a:06` | sensor_gof                 | `:sensor_gof`         |   -rw   | :warning:  | -           | -                               |
| `0x31a:07` | sensor_crg                 | `:sensor_crg`         |   -rw   | :warning:  | -           | -                               |
| `0x31a:08` | sensor_select              | `:sensor_select`      |   -rw   | :warning:  | -           | -                               |
| `0x31a:09` | Restore_sensor_settings    | `:restore_sensor_set` |   -w    | :warning:  | 1 - restore | can fix the sensor freeze issue |

### Peripheral sampling information

| Index      | Name                                   | Tag                 | Attribs |    Safe    | Values | Note |
|:-----------|:---------------------------------------|:--------------------|:-------:|:----------:|--------|------|
| `0x337`    | **>> Peripheral sampling information** | `peref_smpl_info`   |   -r    | :question: | -      | -    |
|            |                                        |                     |         |            |        |      |
| `0x337:00` | sds18b20 temp                          | `:sds18b20_temp`    |   -r    | :question: | -      | -    |
| `0x337:01` | sbno055 azimut                         | `:sbno055_azimut`   |   -r    | :question: | -      | -    |
| `0x337:02` | sbme680 temp                           | `:sbme680_temp`     |   -r    | :question: | -      | -    |
| `0x337:03` | sbme680 humidity                       | `:sbme680_humidity` |   -r    | :question: | -      | -    |
| `0x337:04` | sbme680 pressure                       | `:sbme680_pressure` |   -r    | :question: | -      | -    |
