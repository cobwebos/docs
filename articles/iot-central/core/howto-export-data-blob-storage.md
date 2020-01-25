---
title: Azure Blob Storage에 데이터 내보내기 | Microsoft Docs
description: Azure IoT Central 애플리케이션에서 Azure Blob Storage에 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 79d578c910c7d08355901308e00db5912d1f3baf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721483"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Azure Blob Storage에 데이터 내보내기

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*이 항목의 내용은 관리자에게 적용됩니다.*

本文介绍如何使用 Azure IoT Central 中的连续数据导出功能定期将数据导出到**Azure Blob 存储帐户**或**Azure Data Lake Storage Gen2 存储帐户**。 可以将**度量**、**设备**和**设备模板**导出为 JSON 或 Apache Avro 格式的文件。 내보낸 데이터는 Azure Machine Learning의 학습 모델 또는 Microsoft Power BI의 장기 추세 분석과 같은 콜드 경로 분석에 사용할 수 있습니다.

> [!Note]
> 연속 데이터 내보내기를 켜면 그 시점 이후의 데이터만 얻게 됩니다. 현재는 연속 데이터 내보내기가 꺼져 있는 시간의 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 연속 데이터 내보내기를 일찍 켜세요.


## <a name="prerequisites"></a>필수 조건

- IoT Central 애플리케이션에서 관리자여야 함


## <a name="set-up-export-destination"></a>내보내기 대상 설정

如果没有要导出到的现有存储，请执行以下步骤：

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 可以详细了解如何创建新的[Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 存储帐户](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)。

    > [!Note] 
    > 如果选择将数据导出到 ADLS v2 存储帐户，则必须选择 "**帐户类型**" 作为 " **BlobStorage**"。 

    > [!Note] 
    > 你可以将数据导出到订阅中不同于即用即付 IoT Central 应用程序的存储帐户。 이 경우 연결 문자열을 사용하여 연결합니다.

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스**에서 **Blob 찾아보기**를 선택합니다. 맨 위에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.

## <a name="set-up-continuous-data-export"></a>연속 데이터 내보내기 설정

现在，你有了一个要将数据导出到的存储目标，请按照以下步骤设置连续数据导出。 

1. IoT Central 애플리케이션에 로그인합니다.

2. 在左窗格中，选择 "**数据导出**"。

    > [!Note]
    > 如果在左窗格中看不到 "数据导出"，则不是应用中的管理员。 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. 选择右上方的 " **+ 新建**" 按钮。 选择 " **Azure Blob 存储**" 作为导出目标。 

    > [!NOTE] 
    > 앱당 최대 내보내기 수는 5개입니다. 

    ![새 연속 데이터 내보내기 만들기](media/howto-export-data/export-new2.png)

4. 在下拉列表框中，选择**存储帐户命名空间**。 **연결 문자열 입력**인 목록의 마지막 옵션을 선택할 수도 있습니다. 

    > [!NOTE] 
    > 你将只能看到**与 IoT Central 应用相同的订阅**中的存储帐户命名空间。 이 구독 외부의 대상으로 내보내려는 경우 **연결 문자열 입력**을 선택하고 5단계를 참조합니다.

    > [!NOTE] 
    > 7일 평가판 앱의 경우 연결 문자열을 통해서만 연속 데이터 내보내기를 구성할 수 있습니다. 7일 평가판 앱에는 연결된 Azure 구독이 없기 때문입니다.

    ![创建新的导出到 Blob](media/howto-export-data/export-create-blob2.png)

5. (선택 사항) **연결 문자열 입력**을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 若要获取存储帐户的连接字符串，请转到 "Azure 门户中的存储帐户"-在 "**设置**" 下，选择 "**访问密钥**"-复制 key1 连接字符串或 key2 连接字符串
 
6. 从下拉列表框中选择一个容器。 如果没有容器，请在 Azure 门户中找到存储帐户：
    - 在 " **blob 服务**" 下，选择 " **blob**"。 单击 " **+ 容器**" 并为容器指定名称。 选择数据的公共访问级别（any 将适用于连续数据导出）。 了解[Azure 存储文档](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)中的详细信息。

7. 选择所需的**数据格式**： JSON 或[Apache Avro](https://avro.apache.org/docs/current/index.html)格式。

8. **데이터 내보내기** 아래에서 형식을 **켜기**로 설정하여 내보낼 각 데이터 형식을 지정합니다.

9. 若要启用连续数据导出，请确保已**启用** **数据导出**切换。 **저장**을 선택합니다.

   ![연속 데이터 내보내기 구성](media/howto-export-data/export-list-blob2.png)

10. 几分钟后，你的数据将显示在存储帐户中。


## <a name="path-structure"></a>路径结构

측정값, 디바이스 및 디바이스 템플릿 데이터를 1분마다 한 번 스토리지 계정으로 내보내며, 각 파일에는 마지막으로 내보낸 이후의 일괄 변경 내용이 포함됩니다. 导出的数据被放置在采用 JSON 或 Avro 格式的三个文件夹中。 스토리지 계정의 기본 경로는 다음과 같습니다.
- 消息： {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 设备： {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 设备模板： {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

可以通过导航到文件并选择 "**编辑 blob** " 选项卡，在 Azure 门户中浏览导出的文件。

## <a name="data-format"></a>데이터 형식 

### <a name="measurements"></a>측정값

내보낸 측정값 데이터에는 해당 시간에 IoT Central이 모든 디바이스에서 받은 모든 새 메시지가 포함되어 있습니다. 내보낸 파일은 [IoT Hub 메시지 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)에서 Blob 스토리지로 내보낸 메시지 파일과 동일한 형식을 사용합니다.

> [!NOTE]
> 确保你的设备正在发送的消息 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16``utf-32`）。 有关示例，请参阅[IoT 中心文档](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body)。

> [!NOTE]
> 측정값을 보내는 디바이스는 디바이스 ID로 표시됩니다(아래 섹션 참조). 디바이스 이름을 가져오려면 디바이스 스냅샷을 내보냅니다. 디바이스 레코드의 **deviceId**와 일치하는 **connectionDeviceId**를 사용하여 각 메시지 레코드의 상관 관계를 지정합니다.

다음 예제에서는 디코딩된 Avro 파일의 레코드를 보여 줍니다.

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>디바이스

연속 데이터 내보내기를 처음으로 켜면 모든 디바이스가 포함된 단일 스냅샷이 내보내집니다. 각 디바이스에는 다음 항목이 포함됩니다.
- IoT Central의 디바이스 `id`
- 디바이스의 `name`
- [Device Provisioning Service](/azure/iot-central/core/howto-connect-nodejs)의 `deviceId`
- 디바이스 템플릿 정보
- 속성 값
- 설정 값

새 스냅샷이 1분에 한 번씩 기록됩니다. 스냅샷에는 다음이 포함됩니다.

- 마지막 스냅샷 이후에 추가된 새 디바이스.
- 마지막 스냅샷 이후에 속성 및 설정 값이 변경된 디바이스.

> [!NOTE]
> 마지막 스냅샷 이후에 삭제된 디바이스는 내보내지지 않습니다. 현재는 삭제된 디바이스를 나타내는 표시기가 스냅샷에 없습니다.
>
> 각 디바이스가 속하는 디바이스 템플릿은 디바이스 템플릿 ID로 표시됩니다. 디바이스 템플릿 이름을 가져오려면 디바이스 템플릿 스냅샷을 내보내야 합니다.

导出的文件包含每条记录一行。 以下示例显示了 Avro 格式的记录，已解码：

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>디바이스 템플릿

연속 데이터 내보내기를 처음으로 켜면 모든 디바이스 템플릿이 포함된 단일 스냅샷이 내보내집니다. 각 디바이스 템플릿에는 다음 항목이 포함됩니다.
- 디바이스 템플릿의 `id`
- 디바이스 템플릿의 `name`
- 디바이스 템플릿의 `version`
- 측정 데이터 형식 및 최솟/최댓값
- 속성 데이터 형식 및 기본값
- 설정 데이터 형식 및 기본값

새 스냅샷이 1분에 한 번씩 기록됩니다. 스냅샷에는 다음이 포함됩니다.

- 마지막 스냅샷 이후에 추가된 새 디바이스 템플릿.
- 마지막 스냅샷 이후에 측정값, 속성 및 설정 정의가 변경된 디바이스 템플릿.

> [!NOTE]
> 마지막 스냅샷 이후에 삭제된 디바이스 템플릿은 내보내지지 않습니다. 현재는 삭제된 디바이스 템플릿을 나타내는 표시기가 스냅샷에 없습니다.

导出的文件包含每条记录一行。 以下示例显示了 Avro 格式的记录，已解码：

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>내보낸 Avro 파일 읽기

Avro는 이진 형식이므로 해당 원시 상태에서 파일을 읽을 수 없습니다. 파일을 JSON 형식으로 디코딩할 수 있습니다. 다음 예제에서는 측정값, 디바이스 및 디바이스 템플릿 Avro 파일을 구문 분석하는 방법을 보여 줍니다. 이 예제는 이전 섹션에서 설명한 예제와 동일합니다.

### <a name="read-avro-files-by-using-python"></a>Python을 사용하여 Avro 파일 읽기

#### <a name="install-pandas-and-the-pandavro-package"></a>pandas 및 pandavro 패키지 설치

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>측정값 Avro 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>디바이스 Avro 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>디바이스 템플릿 Avro 파일 구문 분석

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>C#을 사용하여 Avro 파일 읽기

#### <a name="install-the-microsofthadoopavro-package"></a>Microsoft.Hadoop.Avro 패키지 설치

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>측정값 Avro 파일 구문 분석

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
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>디바이스 Avro 파일 구문 분석

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

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
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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

#### <a name="parse-a-device-templates-avro-file"></a>디바이스 템플릿 Avro 파일 구문 분석

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

### <a name="read-avro-files-by-using-javascript"></a>Javascript를 사용하여 Avro 파일 읽기

#### <a name="install-the-avsc-package"></a>avsc 패키지 설치

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>측정값 Avro 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
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

#### <a name="parse-a-devices-avro-file"></a>디바이스 Avro 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
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

#### <a name="parse-a-device-templates-avro-file"></a>디바이스 템플릿 Avro 파일 구문 분석

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and version properties.
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

## <a name="next-steps"></a>다음 단계

데이터를 내보내는 방법을 알아보았으므로 다음 단계를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Power BI에서 데이터 시각화하는 방법](howto-connect-powerbi.md)
