---
title: 排查 IoT Edge 上的实时视频分析问题
description: 本文介绍 IoT Edge 上的实时视频分析的故障排除步骤。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: ca41a403f789fd529ac65c21799d3d3e7f3becf6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285452"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>排查 IoT Edge 上的实时视频分析问题

本文介绍 Azure IoT Edge 上的实时视频分析（LVA）的故障排除步骤。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

### <a name="diagnostics"></a>诊断

作为实时视频分析部署的一部分，可以设置 Azure 资源，如 IoT 中心和 IoT Edge 设备。 作为诊断问题的第一步，请按照以下说明操作，始终确保边缘设备设置正确：

1. [运行 `check` 命令](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [检查 IoT Edge 版本](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [检查 IoT Edge 安全管理器及其日志的状态](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [查看通过 IoT Edge 集线器的消息](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [重新启动容器](../../iot-edge/troubleshoot.md#restart-containers)。
1. [检查防火墙和端口配置规则](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>部署前问题

如果边缘基础结构良好，则可以查找部署清单文件的问题。 若要在 IoT Edge 设备上和任何其他 IoT 模块一起部署 IoT Edge 模块上的实时视频分析，请使用包含 IoT Edge 中心、IoT Edge 代理以及其他模块及其属性的部署清单。 如果 JSON 代码格式不正确，则可能会收到以下错误： 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

未能分析 <deployment manifest.json> 参数 "content" 的文件 "" 中的 JSON，出现异常： "额外数据：行101第1列（字符5325）"

如果遇到此错误，我们建议你检查 JSON 是否缺少括号或文件结构的其他问题。 若要验证文件结构，可以使用诸如[记事本 + + WITH Json 查看器插件](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)或联机工具（例如[json 格式化程序 & 验证程序](https://jsonformatter.curiousconcept.com/)）的客户端。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>部署期间：通过 media graph 直接方法诊断 

在 IoT Edge 设备上正确部署 IoT Edge 模块上的实时视频分析后，可以通过调用[直接方法](direct-methods.md)来创建和运行媒体关系图。 可以通过直接方法使用 Azure 门户来运行媒体图形的诊断：

1. 在 Azure 门户中，请前往连接到 IoT Edge 设备的 IoT 中心。

1. 查找 "**自动设备管理**"，然后选择 " **IoT Edge**"。  

1. 在边缘设备列表中，选择要诊断的设备。  
         
    ![显示一系列边缘设备的 Azure 门户屏幕截图](./media/troubleshoot-how-to/lva-sample-device.png)

1. 查看响应代码是否为*200-OK*。 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)的其他响应代码包括：
    * 400 - 部署配置格式不正确或无效。
    * 417 - 没有为设备设置部署配置。
    * 412 - 部署配置中的架构版本无效。
    * 406 - IoT Edge 设备脱机或不发送状态报告。
    * 500 - IoT Edge 运行时中出现了一个错误。

1. 如果收到状态501代码，请检查以确保直接方法名称准确无误。 如果方法名称和请求有效负载准确，则应该获得结果，并显示成功代码 = 200。 如果请求有效负载不准确，你将获得状态 = 400 和响应有效负载，指示错误代码和消息，这些错误代码和消息应该有助于诊断直接方法调用的问题。
    * 检查报告的属性和所需属性可帮助您了解模块属性是否已与部署同步。 如果没有，你可以重新启动 IoT Edge 设备。 
    * 使用[直接方法](direct-methods.md)指南调用一些方法，特别是简单的方法，例如 GraphTopologyList。 本指南还指定了预期的请求和响应负载以及错误代码。 简单直接方法成功后，可以确保实时视频分析 IoT Edge 模块功能正常。
        
       ![IoT Edge 模块的 "直接方法" 窗格的屏幕截图。](./media/troubleshoot-how-to/direct-method.png) 

1. 如果 "部署" 和 "**由设备报告**" 列**中指定**"*是"*，则可以在 IoT Edge 模块上的实时视频分析上调用直接方法。 选择要切换到的模块，可在其中查看所需的属性和报告的属性，以及调用直接方法。 请记住以下几点： 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>部署后：在运行期间诊断日志中的问题 

IoT Edge 模块的容器日志应包含诊断信息，以帮助在模块运行时中调试问题。 你可以[检查容器日志中是否存在问题](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)并自行诊断问题。 

如果你已经运行了前面的所有检查但仍遇到问题，请[使用 `support bundle` 命令](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)从 IoT Edge 设备中收集日志，以供 Azure 团队进一步分析。 你可以[联系我们](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获取支持，并提交收集的日志。

## <a name="common-error-resolutions"></a>常见错误解决方法

实时视频分析作为 IoT Edge 设备上的 IoT Edge 模块进行部署，并且与 IoT Edge 代理和中心模块一起工作。 实时视频分析部署会遇到的一些常见错误是由底层 IoT 基础结构的问题导致的。 这些错误包括：

* [IoT Edge 代理在大约一分钟后停止](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge 代理无法访问模块的映像（403）](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge 代理模块报告 "空配置文件"，并且设备上不会启动模块](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge 中心无法启动](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [IoT Edge security daemon 失败，主机名无效](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [实时视频分析或任何其他自定义 IoT Edge 模块无法将消息发送到边缘集线器，出现404错误](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* [IoT Edge 模块已成功部署，然后从设备中消失](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-setup-script-issues"></a>边缘安装脚本问题

作为文档的一部分，我们提供了一个用于部署边缘和云资源的[安装脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)，并使你开始使用实时视频分析边缘。 本部分介绍你可能会遇到的一些脚本错误以及用于调试这些错误的解决方案。

问题：脚本运行，其中部分创建了一些资源，但失败，并出现以下消息：

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
解决此问题：

1. 运行以下命令：

    ```
    az --version
    ```
1. 确保安装了以下扩展。 在本文发布时，扩展及其版本如下：

    | 分机 | 版本 |
    |---|---|
    |azure-cli   |      版|
    |命令模块-nspkg         |   2.0.3|
    |core  |    版|
    |nspkg    | 3.0.4|
    |遥测| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 如果已安装的扩展的版本早于此处列出的发行版号，请使用以下命令更新该扩展：

    ```
    az extension update --name <Extension name>
    ```

    例如，你可以运行 `az extension update --name azure-iot` 。

### <a name="sample-app-issues"></a>示例应用问题

作为我们版本的一部分，我们提供了一些 .NET 示例代码，可帮助我们的开发人员社区。 本部分介绍了在运行示例代码时可能会遇到的一些错误，以及用于调试这些代码的解决方案。

问题： Program.cs 失败，直接方法调用上出现以下错误：

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. 确保在 Visual Studio Code 环境中安装了[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)，并已设置到 IoT 中心的连接。 为此，请选择 "Ctrl + Shift + P"，然后选择 "**选择 IoT 中心方法**"。

1. 查看是否可以通过 Visual Studio Code 在 IoT Edge 模块上调用直接方法。 例如，调用具有以下有效负载的 GraphTopologyList { &nbsp; " @apiVersion "： "1.0"}。 应该会收到以下响应： 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code 中的响应的屏幕截图。](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 如果上述解决方案失败，请尝试以下操作：

    a. 在 IoT Edge 设备上，打开命令提示符，并运行以下命令：
    
      ```
      sudo systemctl restart iotedge
      ```

      此命令将重新启动 IoT Edge 设备和所有模块。 等待几分钟，然后再次尝试使用直接方法，并通过运行以下命令确认模块正在运行：

      ```
      sudo iotedge list
      ```

    b. 如果上述方法也失败，请尝试重新启动虚拟机或计算机。

    c. 如果所有方法均失败，请运行以下命令获取包含所有[相关日志](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)的压缩文件，并将其附加到[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. 如果收到错误响应*400*代码，请确保方法调用有效负载按照[直接方法](direct-methods.md)指南的格式正确。
1. 如果您收到状态*200*代码，则表示您的集线器运行正常，模块部署正确且响应迅速。 

1. 检查应用配置是否准确。 应用配置由*appsettings.js上*的 "文件" 中的以下字段组成。 仔细检查以确保 deviceId 和 moduleId 准确无误。 一种简单的检查方法是转到 Visual Studio Code 中的 "Azure IoT 中心扩展" 部分。 " *appsettings.js*文件" 和 "IoT 中心" 部分中的值应匹配。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. 在*appsettings.js*文件中，确保已提供 iot 中心连接字符串而*不*是 iot 中心设备连接字符串，因为[连接字符串的格式不同](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)。

### <a name="live-video-analytics-working-with-external-modules"></a>使用外部模块的实时视频分析

通过 HTTP 扩展处理器的实时视频分析可以扩展 media graph，使用 REST 通过 HTTP 发送和接收来自其他 IoT Edge 模块的数据。 作为[特定的示例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，media graph 可以将视频帧作为图像发送到外部推理模块，如 Yolo v3 并接收基于 JSON 的分析结果。 在这种拓扑中，事件的目标主要是 IoT 中心。 如果没有在中心看到推理事件，请检查以下各项：

* 查看 media graph 是否正在发布的中心，以及正在检查的中心是否相同。 当你创建多个部署时，你可能最终会获得多个中心并错误地检查事件中心。
* 在 Visual Studio Code 中，检查以确定是否已部署并运行外部模块。 在此处的示例图中，rtspsim 和 cv 是在 lvaEdge 模块外部运行 IoT Edge 模块。

    ![显示 Azure IoT 中心内模块运行状态的屏幕截图。](./media/troubleshoot-how-to/iot-hub.png)

* 查看是否正在将事件发送到正确的 URL 终结点。 外部 AI 容器公开一个 URL 和一个端口，通过该容器接收并返回 POST 请求中的数据。 此 URL 被指定为 `endpoint: url` HTTP 扩展处理器的属性。 如[拓扑 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)中所示，终结点设置为推断 URL 参数。 请确保[参数](http://yolov3/score)的默认值或传入的值是准确的。 您可以通过使用客户端 URL （卷）进行测试，以确定其是否正常工作。  

    例如，以下是在本地计算机上运行的 Yolo v3 容器，其 IP 地址为172.17.0.3。 使用 Docker 检查来查找 IP 地址。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返回的结果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* 如果运行的是使用 HTTP 扩展处理器的图形的一个或多个实例，则在每个 HTTP 扩展处理器之前都应有一个帧速率筛选器来管理视频源的每秒帧数（fps）。 

   在某些情况下，如果边缘计算机的 CPU 或内存使用率很高，则可能会丢失某些推理事件。 若要解决此问题，请在 "帧速率" 筛选器上设置 maximumFps 属性的低值。 您可以在关系图的每个实例上将其设置为0.5 （"maximumFps"：0.5），然后重新运行该实例，检查中心上的推理事件。

   或者，你可以使用更高的 CPU 和内存来获取功能更强大的边缘计算机。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>并行多个直接方法–超时失败 

IoT Edge 上的实时视频分析提供了一种基于方法的直接编程模型，可用于设置多个拓扑和多个图形实例。 作为拓扑和图形设置的一部分，您可以调用 IoT Edge 模块上的多个直接方法调用。 如果并行调用这两个方法调用（特别是启动和停止关系图的方法），则可能会遇到超时错误，如下所示： 

程序集初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 引发了异常。 IotHubException： IotHubException （常见情况）的操作为：（& e）：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

建议*不要*并行调用直接方法。 按顺序调用它们（也就是说，仅在上一个方法完成后才进行一个直接方法调用）。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>收集用于提交支持票证的日志

当自行指导故障排除步骤不能解决您的问题时，请转到 Azure 门户并提出[支持票证](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> 日志中可能包含个人身份信息（PII），例如你的 IP 地址。 一旦我们完成了日志的所有本地副本的检查并关闭支持票证，就会立即将其删除。  

若要收集应添加到票证的相关日志，请按照后续部分中的说明进行操作。 你可以在支持请求的**详细信息**窗格上上传日志文件。

### <a name="use-the-support-bundle-command"></a>使用支持捆绑服务命令

如果需要从 IoT Edge 设备收集日志，最简单的方法是使用 `support-bundle` 命令。 此命令收集：

- 模块日志
- IoT Edge 安全管理器和容器引擎日志
- Iotedge 检查 JSON 输出
- 有用的调试信息

1. 运行 `support-bundle` 包含 *--"--自*" 标志的命令来指定你希望日志覆盖的时间。 例如，在下半年，过去两小时会获得日志。 您可以更改此标志的值，以包含不同时间段的日志。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   此命令在运行命令的目录中创建一个名为*support_bundle.zip*的文件。 
   
1. 将*support_bundle.zip*文件附加到支持票证。

### <a name="live-video-analytics-debug-logs"></a>实时视频分析调试日志

若要在 IoT Edge 模块上配置实时视频分析以生成调试日志，请执行以下操作：

1. 登录到[Azure 门户](https://portal.azure.com)，并中转到 IoT 中心。
1. 在左侧窗格中，选择 " **IoT Edge**"。
1. 在设备列表中，选择目标设备的 ID。
1. 在窗格顶部，选择 "**设置模块**"。

   ![Azure 门户中的 "设置模块" 按钮的屏幕截图。](media/troubleshoot-how-to/set-modules.png)

1. 在 " **IoT Edge 模块**" 部分中，查找并选择 " **lvaEdge**"。
1. 选择**容器创建选项**。
1. 在 "**绑定**" 部分中，添加以下命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 此命令绑定边缘设备和容器之间的日志文件夹。 如果要在其他位置收集日志，请使用以下命令，将 **$LOG _LOCATION_ON_EDGE_DEVICE**替换为要使用的位置：`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. 选择“更新”。
1. 选择“查看 + 创建”。 成功的验证消息将在绿色横幅下发布。
1. 选择“创建”。
1. 更新**模块标识**，以指向 DebugLogsDirectory 参数，该参数指向收集日志的目录：

    a. 在 "**模块**" 表下，选择 " **lvaEdge**"。  
    b. 在窗格顶部，选择 "**模块标识符**"。 此时将打开一个可编辑窗格。  
    c. 在 "**所需的密钥**" 下，添加以下键/值对：  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 此命令绑定边缘设备和容器之间的日志文件夹。 如果要在其他位置收集日志，请使用以下命令，将 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE**替换为要使用的位置：  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. 选择“保存” 。

1. 重现问题。
1. 从门户中的 " **IoT 中心**" 页面连接到虚拟机。
1. 压缩*debugLogs*文件夹中的所有文件。

   > [!NOTE]
   > 这些日志文件并不是为了自行诊断。 它们适用于 Azure 工程团队分析你的问题。

   a. 在以下命令中，请确保将 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE**替换为之前设置的边缘设备上的调试日志的位置。  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. 将*debugLogs.zip*文件附加到支持票证。

1. 可以通过将**模块标识**中的值设置为*null*来停止日志收集。 返回到**模块标识**克隆页，并将以下参数更新为：

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>后续步骤

[教程：将基于事件的视频录制到云中并从云中播放](event-based-video-recording-tutorial.md)
