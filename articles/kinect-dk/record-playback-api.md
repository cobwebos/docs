---
title: Azure Kinect 录制和播放 API
description: 使用录制和播放 API
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 深度, rgb, 录制, 播放, matroska, mkv
ms.openlocfilehash: 47933e2331d64cac74dd40c99ea7d70ab7c80cd0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452873"
---
# <a name="the-azure-kinect-record-and-playback-api"></a>Azure Kinect 录制和播放 API

传感器 SDK 提供一个 API 用于将设备数据记录到 Matroska (.mkv) 文件。 Matroska 容器格式可存储视频篇目、IMU 样本和设备校准结果。 可以使用随附的 [k4arecorder](record-sensor-streams-file.md) 命令行实用工具生成录制内容。 还可以直接使用录制 API 录制内容以及自定义录制的内容。

有关录制 API 的详细信息，请参阅 [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831)。

有关 Matroska 文件格式规范的详细信息，请参阅[录制文件格式](record-file-format.md)页。

## <a name="use-the-playback-api"></a>使用播放 API

可以使用播放 API 打开录制文件。 通过播放 API 可以访问格式与其他传感器 SDK 相同的传感器数据。

### <a name="open-a-record-file"></a>打开录制文件

在以下示例中，我们将使用 [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) 打开一个录制文件，输出录制长度，然后使用 [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) 关闭该文件。

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>读取捕获

打开文件后，可以开始读取录制内容中的捕获。 以下示例将读取文件中的每个捕获。

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>在录制内容中查找

到达文件末尾后，我们可能需要返回并再次读取。 若要完成此过程，可以使用 [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) 进行回读，但根据录制内容的长度，这种做法可能非常缓慢。
我们可以改用 [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) 函数转到文件中的特定点。

在此示例中，我们以微秒为单位指定了时间戳，以定位到文件中的各个点。

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>读取标记信息

录制内容还可能包含各种元数据，例如设备序列号和固件版本。 此元数据存储在录制标记中，可以使用 [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) 函数来访问。

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>录制标记列表

下面是可以包含在录制文件中的所有默认标记的列表。 其中的许多值可用作 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 结构的一部分，可以使用 [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) 函数来读取。

如果某个标记不存在，则假设采用默认值。

| 标记名称                     | 默认值      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 字段 | 说明     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | "OFF"              | `color_format` / `color_resolution`  | 可能的值："OFF"、"MJPG_1080P"、"NV12_720P"、"YUY2_720P" 等                                      |
| `K4A_DEPTH_MODE`             | "OFF"              | `depth_mode` / `depth_track_enabled` | 可能的值："OFF"、"NFOV_UNBINNED"、"PASSIVE_IR" 等                                                |
| `K4A_IR_MODE`                | "OFF"              | `depth_mode` / `ir_track_enabled`    | 可能的值："OFF"、"ACTIVE"、"PASSIVE"                                                                    |
| `K4A_IMU_MODE`               | "OFF"              | `imu_track_enabled`                  | 可能的值："ON"、"OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | 不适用                                  | 请参阅 [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | 值以纳秒为单位存储，API 以微秒为单位。                                                        |
| `K4A_WIRED_SYNC_MODE`        | "STANDALONE"       | `wired_sync_mode`                    | 可能的值："STANDALONE"、"MASTER"、"SUBORDINATE"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | 值以纳秒为单位存储，API 以微秒为单位。                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | 不适用                                  | 设备颜色固件版本，例如 "1.x.xx"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | 不适用                                  | 设备深度固件版本，例如 "1.x.xx"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | 不适用                                  | 录制设备序列号                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | 请参阅下面的[时间戳同步](record-playback-api.md#timestamp-synchronization)。                       |
| `K4A_COLOR_TRACK`            | 无               | 不适用                                  | 请参阅[录制文件格式 - 识别篇目](record-file-format.md#identifying-tracks)。                     |
| `K4A_DEPTH_TRACK`            | 无               | 不适用                                  | 请参阅[录制文件格式 - 识别篇目](record-file-format.md#identifying-tracks)。                     |
| `K4A_IR_TRACK`               | 无               | 不适用                                  | 请参阅[录制文件格式 - 识别篇目](record-file-format.md#identifying-tracks)。                     |
| `K4A_IMU_TRACK`              | 无               | 不适用                                  | 请参阅[录制文件格式 - 识别篇目](record-file-format.md#identifying-tracks)。                     |

## <a name="timestamp-synchronization"></a>时间戳同步

Matroska 格式要求录制内容必须以时间戳 0 开头。 [在外部同步相机](record-external-synchronized-units.md)时，每个设备中的第一个时间戳可以不为 0。

为了在录制和播放之间切换时保留设备的原始时间戳，该文件会存储一个要应用到时间戳的偏移量。

`K4A_START_OFFSET_NS` 标记用于指定时间戳偏移量，以便在录制后可以重新同步文件。 可将此时间戳偏移量添加到文件中的每个时间戳，以重新构造原始设备时间戳。

起始偏移量也会在 [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 结构中提供。
