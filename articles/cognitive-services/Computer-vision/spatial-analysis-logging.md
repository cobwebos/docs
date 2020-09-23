---
title: 空间分析容器的遥测和日志记录
titleSuffix: Azure Cognitive Services
description: 空间分析为每个容器提供通用的配置框架见解、日志记录和安全设置。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 2d19c061ad1e5cf033d2801df64a0ae37736c418
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983030"
---
# <a name="telemetry-and-troubleshooting"></a>遥测和故障排除

空间分析包括一组功能，可用于监视系统的运行状况，并有助于诊断问题。

## <a name="enable-visualizations"></a>启用可视化效果

若要在视频帧中启用 AI Insights 事件的可视化效果，需要使用 `.debug` [空间分析操作](spatial-analysis-operations.md)的版本。 有四个可用的调试操作。

编辑 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179) ，为环境变量使用正确的值 `DISPLAY` 。 它需要与 `$DISPLAY` 主计算机上的变量匹配。 更新部署清单后，重新部署容器。

部署完成后，你可能需要将该 `.Xauthority` 文件从主计算机复制到容器，然后重新启动。 在下面的示例中， `peopleanalytics` 是主计算机上的容器的名称。

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>收集系统运行状况遥测

Telegraf 是适用于空间分析的开源映像，可在 Microsoft 容器注册表中找到。 它采用以下输入并将其发送到 Azure Monitor。 可以用所需的自定义输入和输出生成 telegraf 模块。 空间分析中的 telegraf 模块配置属于 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)。 此模块是可选的，如果不需要，可以将其从清单中删除。 

输入： 
1. 空间分析指标
2. 磁盘指标
3. CPU 指标
4. Docker 指标
5. GPU 指标

输出：
1. Azure Monitor

提供的空间分析 telegraf 模块会将空间分析容器发出的所有遥测数据发布到 Azure Monitor。 有关将 Azure Monitor 添加到订阅的信息，请参阅 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 。

设置 Azure Monitor 后，你将需要创建用于使模块能够发送遥测数据的凭据。 你可以使用 Azure 门户来创建新的服务主体，或使用以下 Azure CLI 命令创建一个。

> [!NOTE] 
> 此命令要求你具有订阅的所有者特权。 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

在 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)中，查找 *telegraf* 模块，并将以下值替换为上一步中的服务主体信息，然后重新部署。

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

部署 telegraf 模块后，可以通过 Azure Monitor 服务或 Azure 门户的 IoT 中心内选择 " **监视** " 来访问报告的指标。

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor 遥测报表":::

### <a name="system-health-events"></a>系统运行状况事件

| 事件名称 | 描述|
|------|---------|
|archon_exit    |当用户将空间分析模块状态从 " *正在运行* " 更改为 " *已停止*" 时发送。  |
|archon_error   |容器中的任何进程崩溃时发送。 这是一个严重错误。  |
|InputRate  |图形处理视频输入的速率。 每5分钟报告一次。 | 
|OutputRate     |图形输出 AI insights 的速率。 每5分钟报告一次。 |
|archon_allGraphsStarted | 在所有关系图完成启动后发送。 |
|archon_configchange    | 当关系图配置已更改时发送。 |
|archon_graphCreationFailed     |在报告的图形 `graphId` 无法启动时发送。 |
|archon_graphCreationSuccess    |在报告的图形 `graphId` 成功启动时发送。 |
|archon_graphCleanup    | 当具有所报告的清理并退出的图形时发送 `graphId` 。 |
|archon_graphHeartbeat  |每分钟为某个技能的每个图形发送检测信号。 |
|archon_apiKeyAuthFail |由于以下原因，在计算机视觉资源密钥验证容器的失败时间超过24小时后发送： "超出配额"、"无效"、"脱机"。 |
|VideoIngesterHeartbeat     |每小时发送一次，以指示视频从视频源进行流式处理，并且在该小时内出现错误数。 报告每个图形。 |
|VideoIngesterState | 视频流的报表 *已停止* 或 *启动* 。报告每个图形。 |

##  <a name="troubleshooting-an-iot-edge-device"></a>排查 IoT Edge 设备问题

您可以使用 `iotedge` 命令行工具来检查正在运行的模块的状态和日志。 例如：
* `iotedge list`：报告正在运行的模块的列表。 
  可以进一步检查是否有错误 `iotedge logs edgeAgent` 。 如果 `iotedge` 停滞，你可以尝试重新启动它 `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` 重新启动特定模块 

## <a name="collect-log-files-with-the-diagnostics-container"></a>收集包含诊断容器的日志文件

空间分析会生成 Docker 调试日志，您可以使用这些日志来诊断运行时问题或包含在支持票证中。 空间分析诊断模块在 Microsoft 容器注册表中提供，供你下载。 在 [示例部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)中，查找 " *诊断* " 模块。

在 "env" 节中，添加以下配置：

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

>[!NOTE]
> 如果未在 ASE Kubernetes 环境中运行，则将日志记录模块的容器 create 选项替换为以下内容：
>
>`"createOptions": "{\"HostConfig\": {\"Binds\": [\"/var/run/docker.sock:/var/run/docker.sock\",\"/usr/bin/docker:/usr/bin/docker\"],\"LogConfig\": {\"Config\": {\"max-size\": \"500m\"}}}}"`

若要优化已上传到远程终结点（例如 Azure Blob 存储）的日志，建议保持较小的文件大小。 请参阅下面的示例，了解建议的 Docker 日志配置。

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>配置日志级别

日志级别配置允许您控制生成的日志的详细级别。 支持的日志级别有： `none` 、 `verbose` 、、 `info` `warning` 和 `error` 。 节点和平台的默认日志详细级别为 `info` 。 

通过将 `ARCHON_LOG_LEVEL` 环境变量设置为允许的值之一，可以全局修改日志级别。
还可以通过为所有已部署的技能全局或通过设置的值为每个特定的技能设置 IoT Edge 模块克隆文档， `platformLogLevel` `nodeLogLevel` 如下所示。

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>收集日志

> [!NOTE]
> 该 `diagnostics` 模块不影响日志记录内容，它只是帮助收集、筛选和上载现有日志。
> 若要使用此模块，必须使用 Docker API 版本1.40 或更高版本。

[示例部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)文件包括一个名为 `diagnostics` 的模块，该模块收集和上载日志。 默认情况下，此模块处于禁用状态，并且应在需要访问日志时通过 IoT Edge 模块配置启用。 

`diagnostics`集合是按需进行的，并通过 IoT Edge 直接方法进行控制，可以将日志发送到 Azure Blob 存储。

### <a name="configure-diagnostics-upload-targets"></a>配置诊断上传目标

在 IoT Edge 门户中，选择你的设备，然后选择 " **诊断** " 模块。 在 [*DeploymentManifest.js*](https://go.microsoft.com/fwlink/?linkid=2142179)的示例文件中，查找名为 "env" 的诊断的 " **环境变量** " 部分，并添加以下信息：

**配置上传到 Azure Blob 存储**

1. 创建你自己的 Azure Blob 存储帐户（如果尚未这样做）。
2. 从 Azure 门户获取存储帐户的 **连接字符串** 。 它将位于 " **访问密钥**" 中。
3. 空间分析日志将自动上传到名为 *rtcvlogs* 的 Blob 存储容器中，文件名格式如下： `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` 。

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>上载空间分析日志

在 `getRTCVLogs` 模块中，按需 IoT Edge 方法上载日志 `diagnostics` 。 


1. 中转到 IoT 中心门户页面，选择 " **边缘设备**"，然后选择设备和诊断模块。 
2. 请在模块的详细信息页中，单击 " ***直接方法*** " 选项卡。
3. 在 `getRTCVLogs` 负载中键入方法名称和 json 格式字符串。 可以输入 `{}` ，它是一个空负载。 
4. 设置连接和方法超时，并单击 " **调用方法**"。
5. 选择目标容器，并使用 " **日志记录语法** " 部分中描述的参数生成负载 json 字符串。 单击 " **调用方法** " 以执行请求。

>[!NOTE]
> `getRTCVLogs`使用空负载调用方法将返回设备上部署的所有容器的列表。 方法名称区分大小写。 如果提供了不正确的方法名称，则会收到501错误。

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="调用 getRTCVLogs 方法 ":::
![getRTCVLogs "直接方法" 页](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>日志记录语法

下表列出了查询日志时可以使用的参数。

| 关键字 | 说明 | 默认值 |
|--|--|--|
| StartTime | 所需的日志开始时间（以毫秒为单位）。 | `-1`，容器的运行时的开头。 当用作 `[-1.-1]` 时间范围时，API 将返回最近一小时内的日志。|
| EndTime | 所需日志结束时间（以毫秒为单位）。 | `-1`，当前时间。 当 `[-1.-1]` 使用时间范围时，api 将返回最近一小时内的日志。 |
| ContainerId | 用于提取日志的目标容器。| `null`如果没有容器 ID，则为。 API 使用 Id 返回所有可用的容器信息。|
| DoPost | 执行上传操作。 如果此设置为 `false` ，则它将执行所请求的操作并返回上传大小，而不执行上传。 当设置为时 `true` ，它将启动所选日志的异步上传 | `false`，请不要上传。|
| 限制 | 指示每个批处理要上载多少行日志 | `1000`，请使用此参数调整 post 速度。 |
| 筛选器 | 筛选要上传的日志 | `null`，可以根据空间分析日志结构将筛选器指定为键值对： `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` 。 例如： `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

下表列出了查询响应中的属性。

| 关键字 | 说明|
|--|--|
|DoPost| *True*或*false*。 指示日志是否已上传。 选择不上载日志时，api 会 ***同步***返回信息。 当你选择上传日志时，如果请求有效，则 api 返回200，并以 ***异步方式***开始上载日志。|
|TimeFilter| 应用于日志的时间筛选器。|
|ValueFilters| 应用于日志的关键字筛选器。 |
|时间戳| 方法执行开始时间。 |
|ContainerId| 目标容器 ID。 |
|FetchCounter| 日志行总数。 |
|FetchSizeInByte| 日志数据总量（以字节为单位）。 |
|MatchCounter| 日志行的有效数目。 |
|MatchSizeInByte| 有效的日志数据量（以字节为单位）。 |
|FilterCount| 应用筛选器后的日志行总数。 |
|FilterSizeInByte| 应用筛选器后的日志数据总量（以字节为单位）。 |
|FetchLogsDurationInMiliSec| 提取操作的持续时间。 |
|PaseLogsDurationInMiliSec| 筛选操作的持续时间。 |
|PostLogsDurationInMiliSec| Post 操作的持续时间。 |

#### <a name="example-request"></a>示例请求 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>示例响应 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

如果提取日志的行、时间和大小正确，请将 ***DoPost*** 替换为， `true` 并将具有相同筛选器的日志推送到目标。 

解决问题时，可以从 Azure Blob 存储导出日志。 

## <a name="common-issues"></a>常见问题

如果你在模块日志中看到以下消息，则可能表示你的 Azure 订阅需要获得批准： 

"容器处于无效状态。 订阅验证失败，状态为 "不匹配"。 Api 密钥不适用于给定的容器类型。 "

有关详细信息，请参阅 [请求批准以运行容器](spatial-analysis-container.md#request-approval-to-run-the-container)。 


## <a name="troubleshooting-the-azure-stack-edge-device"></a>Azure Stack 边缘设备的疑难解答

以下部分用于帮助调试和验证 Azure Stack Edge 设备的状态。

### <a name="access-the-kubernetes-api-endpoint"></a>访问 Kubernetes API 终结点。 

1. 在设备的本地 UI 中，请切换到 " **设备** " 页。 
2. 在 " **设备终结点**" 下，复制 Kubernetes API 服务终结点。 此终结点是采用以下格式的字符串：`https://compute..[device-IP-address]`。
3. 保存该终结点字符串。 稍后在配置 `kubectl` 以访问 Kubernetes 群集时，将使用此配置。

### <a name="connect-to-powershell-interface"></a>连接到 PowerShell 接口

远程从 Windows 客户端连接。 创建 Kubernetes 群集后，可以通过此群集管理应用程序。 你将需要连接到设备的 PowerShell 接口。 远程连接到设备的过程可能不同，具体取决于客户端的操作系统。 以下步骤适用于运行 PowerShell 的 Windows 客户端。

> [!TIP]
> * 在开始之前，请确保你的 Windows 客户端运行的是 Windows PowerShell 5.0 或更高版本。
> * [Linux 上也提供](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux)了 PowerShell。

1. 以管理员身份运行 Windows PowerShell 会话。 
    1. 请确保 Windows 远程管理服务在客户端上运行。 在命令提示符处，键入 `winrm quickconfig`。

2. 为设备 IP 地址分配变量。 例如，`$ip = "<device-ip-address>"` 。

3. 使用以下命令将设备的 IP 地址添加到客户端的受信任主机列表。 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. 在设备上启动 Windows PowerShell 会话。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. 在出现提示时提供密码。 使用用于登录本地 web 界面的相同密码。 默认的本地 web 接口密码是 `Password1` 。 

### <a name="access-the-kubernetes-cluster"></a>访问 Kubernetes 群集

创建 Kubernetes 群集后，可以使用 `kubectl` 命令行工具访问该群集。

1. 创建新的命名空间。 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. 创建一个用户并获取一个配置文件。 此命令将输出 Kubernetes 群集的配置信息。 复制此信息并将其保存到名为 *config*的文件中。不要将该文件保存为文件扩展名。
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. 将 *配置* 文件添加到本地计算机上用户配置文件中的 *kube* 文件夹。   

4. 将命名空间与您创建的用户关联。

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. `kubectl`使用以下命令在 Windows 客户端上安装：
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. 将 DNS 条目添加到系统上的 hosts 文件中。 
    1. 以管理员身份运行记事本，然后打开位于的 *主机* 文件 `C:\windows\system32\drivers\etc\hosts` 。 
    2. 在 hosts 文件中创建一个条目，其中包含从本地 UI 的 **设备** 页面获取的设备 IP 地址和 DNS 域。 应使用的终结点将类似于： `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` 。

7. 验证是否可以连接到 Kubernetes pod。

    ```powershell
    kubectl get pods -n "iotedge"
    ```

若要获取容器日志，请运行以下命令：

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>有用的命令

|命令  |说明  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | 生成 Kubernetes 配置文件。 使用命令时，将信息复制到名为 *config*的文件中。不要使用文件扩展名保存该文件。        |
| `Get-HcsApplianceInfo` | 返回有关设备的信息。 |
| `Enable-HcsSupportAccess` | 生成访问凭据以启动支持会话。 |

## <a name="next-steps"></a>后续步骤

* [部署计算 web 应用程序的人员](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [照相机位置指南](spatial-analysis-camera-placement.md)
* [区域和线条位置指南](spatial-analysis-zone-line-placement.md)
