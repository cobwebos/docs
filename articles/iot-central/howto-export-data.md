---
title: 导出 Azure IoT Central 中的数据 | Microsoft Docs
description: 如何从 Azure IoT Central 应用程序中导出数据
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 3ca2bc56c03e5bbabbd9b2f17edc621bdd94b02f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622477"
---
# <a name="export-your-data-in-azure-iot-central"></a>导出 Azure IoT Central 中的数据

本文介绍如何使用 Azure IoT Central 中的连续数据导出功能定期将数据导出到 Azure Blob 存储帐户。 可以将“度量”、“设备”和“设备模板”导出至 [Apache AVRO](https://avro.apache.org/docs/current/index.html) 格式的文件。 导出的数据可用于冷路径分析，例如 Azure 机器学习中的训练模型或 Microsoft Power BI 中的长期趋势分析。

> [!Note]
> 启用连续数据导出时，只能获得从那时之后的数据。 目前，关闭连续数据导出后将暂时无法检索数据。 若要保留更多的历史数据，请及早启用连续数据导出。

## <a name="prerequisites"></a>先决条件

- 延长为 30 天的试用版 IoT Central 应用程序或付费应用程序。
- 含 Azure 订阅的 Azure 帐户。
- 同一 Azure 帐户是 IoT Central 应用程序中的管理员。
- 同一 Azure 帐户有权在同一 Azure 订阅中创建存储帐户或访问现有存储帐户。

## <a name="types-of-data-to-export"></a>要导出的数据的类型

### <a name="measurements"></a>度量

设备发送的度量将以每分钟一次的频率导出到存储帐户。 数据包含 IoT Central 在此期间从所有设备接收的所有新消息。 导出的 AVRO 文件所用格式与通过 [IoT 中心消息路由](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)导出到 Blob 存储中的消息文件格式相同。

> [!NOTE]
> 发送度量的设备由设备 ID 表示（请参阅以下部分）。 若要获取设备名称，请导出设备快照。 使用与设备 ID 匹配的 connectionDeviceId 来关联每条消息记录。

以下示例演示已解码 AVRO 文件中的记录：

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>设备

首次启用连续数据导出时，会导出包含所有设备的单个快照。 快照包含：
- 设备 ID。
- 设备名。
- 设备模板 ID。
- 属性值。
- 设置值。

每分钟写入一次新的快照。 快照包含：

- 上一快照导出后添加的新设备。
- 上一快照导出后其属性和设置值已更改的设备。

> [!NOTE]
> 上一快照导出后删除的设备不会导出。 目前，快照没有已删除设备标记。
>
> 每台设备所属的设备模板由设备模板 ID 表示。 若要获取设备模板的名称，请导出设备模板快照。

已解码 AVRO 文件中的每条记录如下所示：

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>设备模板

首次启用连续数据导出时，会导出包含所有设备模板的单个快照。 快照包含： 
- 设备模板 ID。
- 度量数据类型和最小/最大值。
- 属性数据类型和默认值。
- 设置数据类型和默认值。

每分钟写入一次新的快照。 快照包含：

- 上一快照导出后添加的新设备模板。
- 上一快照导出后其度量、属性和设置定义已更改的设备模板。

> [!NOTE]
> 上一快照导出后删除的设备模板不会导出。 目前，快照没有已删除设备模板标记。

已解码 AVRO 文件中的每条记录如下所示：

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="set-up-continuous-data-export"></a>设置连续数据导出

1. 如果还没有 Azure 存储帐户，请在 Azure 门户中[新建存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 在具有 IoT Central 应用程序的 Azure 订阅中创建该存储帐户。
    - 对于帐户类型，选择“常规用途”或“Blob 存储”。
    - 选择包含 IoT Central 应用程序的订阅。 如果没有看到订阅，则可能需要登录到其他 Azure 帐户或请求该订阅的访问权限。
    - 选择现有资源组，或创建一个新的资源组。 了解[如何新建存储帐户](https://aka.ms/blobdocscreatestorageaccount)。

2. 在存储帐户中创建容器，以便导出 IoT Central 数据。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择“容器”来新建容器。

   ![创建容器](media/howto-export-data/createcontainer.png)

3. 使用同一 Azure 帐户登录到 IoT Central 应用程序。

4. 在“管理”下，选择“数据导出”。

   ![配置连续数据导出](media/howto-export-data/continuousdataexport.PNG)

5. 在“存储帐户”下拉列表框中，选择自己的存储帐户。 在“容器”下拉列表框中，选择自己的容器。 在“要导出的数据”下，通过将类型设置为“打开”来指定要导出的各类数据。

6. 若要启用连续数据导出，将“数据导出”设置为“打开”。 选择“保存”。

7. 几分钟后，数据便会出现在存储帐户中。 浏览存储帐户。 选择“浏览 Blob”> 你的容器。 将看到导出数据的三个文件夹。 包含导出数据的 AVRO 文件的默认路径是：
    - 消息：{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro
    - 设备：{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro
    - 设备模板：{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro

## <a name="read-exported-avro-files"></a>读取导出的 AVRO 文件

AVRO 是一种二进制格式，因此无法在其原始状态下读取文件。 这些文件可以解码为 JSON 格式。 下面的示例展示了如何分析度量、设备和设备模板 AVRO 文件。 这些示例对应于前一部分中所述的示例。

### <a name="read-avro-files-by-using-python"></a>使用 Python 读取 AVRO 文件

#### <a name="install-pandas-and-the-pandavro-package"></a>安装 pandas 和 pandavro 包

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>分析度量 AVRO 文件

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>分析设备 AVRO 文件

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["id"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>分析设备模板 AVRO 文件

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>使用 C# 读取 AVRO 文件

#### <a name="install-the-microsofthadoopavro-package"></a>安装 Microsoft.Hadoop.Avro 包

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>分析度量 AVRO 文件

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>分析设备 AVRO 文件

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>分析设备模板 AVRO 文件

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>使用 Javascript 读取 AVRO 文件

#### <a name="install-the-avsc-package"></a>安装 avsc 包

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>分析度量 AVRO 文件

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>分析设备 AVRO 文件

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the id property.
        const deviceId = record.id;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>分析设备模板 AVRO 文件

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>后续步骤

了解如何导出数据后，继续进行下一步：

> [!div class="nextstepaction"]
> [如何在 Power BI 中直观显示数据](howto-connect-powerbi.md)
