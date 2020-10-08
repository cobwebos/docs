---
title: 对 IoT Edge 上的实时视频分析进行故障排除 - Azure
description: 本文介绍对 IoT Edge 上的实时视频分析进行故障排除的步骤。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823188"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>对 IoT Edge 上的实时视频分析进行故障排除

本文介绍对 Azure IoT Edge 上的实时视频分析 (LVA) 进行故障排除的步骤。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

### <a name="diagnostics"></a>诊断

在部署实时视频分析的过程中，需设置 Azure 资源，如 IoT 中心和 IoT Edge 设备。 作为诊断问题的第一步，请务必确保按照以下说明正确设置 Edge 设备：

1. [运行 `check` 命令](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [检查 IoT Edge 版本](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [检查 IoT Edge 安全管理器的状态及其日志](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [查看经过 IoT Edge 中心的消息](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [重启容器](../../iot-edge/troubleshoot.md#restart-containers)。
1. [检查防火墙和端口配置规则](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>部署前的问题

如果边缘基础结构正常，则可以查找部署清单文件的问题。 若要在 IoT Edge 设备上与任何其他 IoT 模块一起部署 IoT Edge 模块上的实时视频分析，请使用包含 IoT Edge 中心、IoT Edge 代理和其他模块及其属性的部署清单。 如果 JSON 代码格式不正确，则可能会收到以下错误： 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

未能分析参数“content”的文件“<deployment manifest.json>”中的 JSON，出现异常：“额外数据：第 101 行第 1 列 (char 5325)”

如果遇到此错误，建议检查 JSON 中是否缺少括号或存在文件结构的其他问题。 可以使用客户端（如 [Notepad++ 与 JSON Viewer 插件](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)）或联机工具（如 [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/)）来验证文件结构。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>在部署期间：用媒体图直接方法诊断 

在 IoT Edge 设备上正确部署 IoT Edge 模块上的实时视频分析后，可以通过调用[直接方法](direct-methods.md)来创建和运行媒体图。 可以使用 Azure 门户通过直接方法来运行媒体图诊断：

1. 在 Azure 门户中，转到连接到 IoT Edge 设备的 IoT 中心。

1. 查找“自动设备管理”，然后选择“IoT Edge” 。  

1. 在 Edge 设备列表中，选择要诊断的设备。  
         
    ![显示 Edge 设备列表的 Azure 门户的屏幕截图](./media/troubleshoot-how-to/lva-sample-device.png)

1. 检查响应代码是否为 200-OK。 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)的其他响应代码包括：
    * 400 - 部署配置格式不正确或无效。
    * 417 - 没有为设备设置部署配置。
    * 412 - 部署配置中的架构版本无效。
    * 406 - IoT Edge 设备脱机或不发送状态报告。
    * 500 - IoT Edge 运行时中出现了一个错误。

1. 如果收到状态 501 代码，请检查以确保直接方法名称正确。 如果方法名称和请求有效负载准确，则应获得结果，并显示成功代码 =200。 如果请求有效负载不准确，将显示状态 =400 以及指示错误代码和消息的响应有效负载，这些错误代码和消息应该有助于诊断直接方法调用的问题。
    * 检查报告的属性和所需属性有助于了解模块属性是否已与部署同步。 如果没有，可以重启 IoT Edge 设备。 
    * 使用[直接方法](direct-methods.md)指南来调用一些方法，尤其是一些简单的方法，如 GraphTopologyList。 本指南还指定了所需的请求和响应有效负载以及错误代码。 简单的直接方法成功后，可以确保实时视频分析 IoT Edge 模块功能正常。
        
       ![IoT Edge 模块的“直接方法”窗格的屏幕截图。](./media/troubleshoot-how-to/direct-method.png) 

1. 如果“在部署中指定”和“由设备报告”列指示“是”，则可以针对 IoT Edge 模块上的实时视频分析调用直接方法 。 选择该模块后，会转到一个页面，可以在其中查看所需的属性和报告的属性，并可以调用直接方法。 请记住以下几点： 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>后期部署：在运行期间诊断日志中的问题 

IoT Edge 模块的容器日志应包含诊断信息，以帮助调试模块运行时的问题。 可以[检查容器日志中的问题](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)，并对问题进行自我诊断。 

如果你已经运行了前面的所有检查，但仍然遇到问题，请使用 [`support bundle` 命令](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)从 IoT Edge 设备收集日志，以供 Azure 团队进一步分析。 可以[与我们联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获取支持，并提交收集的日志。

## <a name="common-error-resolutions"></a>常见错误解决方法

实时视频分析作为 IoT Edge 模块部署到 Edge 设备上，并且与 IoT Edge 代理和中心模块协作。 在部署实时视频分析时会遇到的一些常见错误是由底层 IoT 基础结构的问题导致的。 这些错误包括：

* [IoT Edge 代理在大约一分钟后停止](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge 代理无法访问某个模块的映像 (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge 代理模块报告“配置文件为空”，且设备上不会启动任何模块](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge 中心未能启动](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [由于主机名无效，IoT Edge 安全守护程序失败](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [实时视频分析或任何其他自定义 IoT Edge 模块无法将消息发送到边缘中心，出现 404 错误](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* [IoT Edge 模块部署成功，然后从设备中消失](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-setup-script-issues"></a>边缘设置脚本问题

作为文档的一部分，我们提供了[设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)来部署边缘和云资源，以便开始使用实时视频分析边缘。 本节介绍你可能遇到的一些脚本错误，以及调试这些错误的解决方案。

问题：脚本运行部分创建几个资源，但失败，并出现以下消息：

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
    |azure-cli   |      2.5.1*|
    |command-modules-nspkg         |   2.0.3|
    |core  |    2.5.1*|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 如果已安装的扩展的版本早于此处列出的版本号，请使用以下命令更新扩展：

    ```
    az extension update --name <Extension name>
    ```

    例如，你可以运行 `az extension update --name azure-iot`。

### <a name="sample-app-issues"></a>示例应用问题

在发布过程中，我们提供了一些 .NET 示例代码来帮助引导我们的开发人员社区。 本节介绍你运行示例代码时可能遇到的一些错误，以及调试这些错误的解决方案。

问题：在直接方法调用中，Program.cs 失败并出现以下错误：

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. 确保在 Visual Studio Code 环境中安装了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)，并已设置与 IoT 中心的连接。 若要执行此操作，请选择“Ctrl+Shift+P”，然后选择“选择 IoT 中心方法”。

1. 检查是否可以通过 Visual Studio Code 在 IoT Edge 模块上调用直接方法。 例如，使用以下有效负载调用 GraphTopologyList {&nbsp;"@apiVersion"："1.0"}. 应该会收到以下响应： 

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

    a. 在 IoT Edge 设备上转到命令提示符，并运行以下命令：
    
      ```
      sudo systemctl restart iotedge
      ```

      此命令将重启 IoT Edge 设备和所有模块。 等待几分钟，然后在再次尝试使用直接方法之前，请通过运行以下命令确认模块正在运行：

      ```
      sudo iotedge list
      ```

    b. 如果上述方法也失败，请尝试重启虚拟机或计算机。

    c. 如果所有方法均失败，请运行以下命令以获取一个压缩文件，其中包含所有[相关日志](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)，并将其附加到[支持工单](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. 如果收到错误响应 400 代码，请确保按照[直接方法](direct-methods.md)指南，为方法调用有效负载设置了正确的格式。
1. 如果收到状态 200 代码，则表示中心运行正常，模块部署正确且正常响应。 

1. 检查应用配置是否准确。 应用配置包含 appsettings.json 文件中的以下字段。 请仔细检查以确保 deviceId 和 moduleId 准确。 一种简便的检查方法是转到 Visual Studio Code 中的 Azure IoT 中心扩展部分。 Appsettings.json 文件中的值和 IoT 中心部分应匹配。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. 请确保在 appsettings.json 文件中提供 IoT 中心连接字符串，而不是 IoT 中心设备连接字符串，因为[连接字符串的格式不同](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) 。

### <a name="live-video-analytics-working-with-external-modules"></a>用于外部模块的实时视频分析

通过 HTTP 扩展处理器的实时视频分析可以扩展媒体图，以使用 REST 通过 HTTP 发送和接收来自其他 IoT Edge 模块的数据。 作为[特定示例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，媒体图可以将视频帧作为图像发送到外部推理模块（如 Yolo v3），并接收基于 JSON 的分析结果。 在这种拓扑中，事件的目标主要是 IoT 中心。 如果在中心上看不到推理事件，请检查以下各项：

* 检查媒体图要发布到的中心是否与要检查的中心相同。 创建多个部署时，最终可能会获得多个中心，并可能会错误地检查错误的事件中心。
* 在 Visual Studio Code 中，检查外部模块是否已部署并正在运行。 在此处的示例图中，rtspsim 和 cv 是在 lvaEdge 模块外部运行的 IoT Edge 模块。

    ![显示 Azure IoT 中心内模块运行状态的屏幕截图。](./media/troubleshoot-how-to/iot-hub.png)

* 检查是否要将事件发送到正确的 URL 终结点。 外部 AI 容器公开一个 URL 和一个端口，通过该端口接收并返回 POST 请求中的数据。 此 URL 被指定为 HTTP 扩展处理器的 `endpoint: url` 属性。 如[拓扑 URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) 中所示，终结点设置为推理 URL 参数。 请确保参数的默认值或传入的值是准确的。 可以使用客户端 URL (cURL) 来测试它是否正常工作。  

    例如，下面是一个在本地计算机上运行的 Yolo v3 容器，其 IP 地址为 172.17.0.3。 使用 Docker inspect 查找 IP 地址。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返回的结果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* 如果正在使用 HTTP 扩展处理器运行图形的一个或多个实例，则在每个 HTTP 扩展处理器之前都应有一个帧速率筛选器来管理视频源的每秒帧数 (fps)。 

   在某些情况下，边缘计算机的 CPU 或内存使用率很高，可能会丢失某些推理事件。 若要解决此问题，请在帧速率筛选器上将 maximumFps 属性设置为低值。 可以在图的每个实例上将其设置为 0.5 ("maximumFps":0.5)，然后重新运行该实例以检查中心上的推理事件。

   另外，可以使用更高的 CPU 和内存来获取功能更强大的边缘计算机。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>并行的多个直接方法 - 超时失败 

IoT Edge 上的实时视频分析提供了一种基于直接方法的编程模型，该模型支持设置多个拓扑和多个图形实例。 在拓扑和图形设置的过程中，可在 IoT Edge 模块上调用多个直接方法调用。 如果并行调用了多个方法调用，特别是启动和停止图形的方法调用，则可能会遇到一些超时故障，如下面所示： 

程序集初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 引发了异常。 Microsoft.Azure.Devices.Common.Exceptions.IotHubException：Microsoft.Azure.Devices.Common.Exceptions.IotHubException：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

建议不要并行调用直接方法。 按顺序调用它们（也就是说，仅在前一个直接方法调用完成之后才进行另一个直接方法调用）。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>收集用于提交支持票证的日志

当自行指导故障排除步骤不能解决您的问题时，请转到 Azure 门户并提出 [支持票证](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> 日志中可能包含个人身份信息 (PII) 例如你的 IP 地址。 一旦我们完成了日志的所有本地副本的检查并关闭支持票证，就会立即将其删除。  

若要收集应添加到票证的相关日志，请按照后续部分中的说明进行操作。 你可以在支持请求的 **详细信息** 窗格上上传日志文件。

### <a name="use-the-support-bundle-command"></a>使用支持捆绑服务命令

如果需要从 IoT Edge 设备收集日志，最简单的方法是使用 `support-bundle` 命令。 此命令收集：

- 模块日志
- IoT Edge 安全管理器和容器引擎日志
- Iotedge 检查 JSON 输出
- 有用的调试信息

1. 运行 `support-bundle` 包含 *--"--自* " 标志的命令来指定你希望日志覆盖的时间。 例如，在下半年，过去两小时会获得日志。 您可以更改此标志的值，以包含不同时间段的日志。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   此命令在运行命令的目录中创建一个名为 *support_bundle.zip* 的文件。 
   
1. 将 *support_bundle.zip* 文件附加到支持票证。

### <a name="live-video-analytics-debug-logs"></a>实时视频分析调试日志

若要在 IoT Edge 模块上配置实时视频分析以生成调试日志，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并中转到 IoT 中心。
1. 在左侧窗格中，选择 " **IoT Edge**"。
1. 在设备列表中，选择目标设备的 ID。
1. 在窗格顶部，选择 " **设置模块**"。

   ![Azure 门户中的 "设置模块" 按钮的屏幕截图。](media/troubleshoot-how-to/set-modules.png)

1. 在 " **IoT Edge 模块** " 部分中，查找并选择 " **lvaEdge**"。
1. 选择 **容器创建选项**。
1. 在 " **绑定** " 部分中，添加以下命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 此命令绑定边缘设备和容器之间的日志文件夹。 如果要在其他位置收集日志，请使用以下命令，将 **$LOG _LOCATION_ON_EDGE_DEVICE** 替换为要使用的位置： `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. 选择“更新”  。
1. 选择“查看 + 创建”  。 成功的验证消息将在绿色横幅下发布。
1. 选择“创建”  。
1. 更新 **模块标识** ，以指向 DebugLogsDirectory 参数，该参数指向收集日志的目录：

    a. 在 " **模块** " 表下，选择 " **lvaEdge**"。  
    b. 在窗格顶部，选择 " **模块标识符**"。 此时将打开一个可编辑窗格。  
    c. 在 " **所需的密钥**" 下，添加以下键/值对：  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 此命令绑定边缘设备和容器之间的日志文件夹。 如果要在设备上的其他位置收集日志：
    > 1. 在 " **绑定** " 部分中创建调试日志位置的绑定，将 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 并 **$DEBUG _LOG_LOCATION** 替换为所需的位置： `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. 使用以下命令，将 **$DEBUG _LOG_LOCATION** 替换为上一步中使用的位置：  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. 选择“保存”。

1. 重现此问题。
1. 从门户中的 " **IoT 中心** " 页面连接到虚拟机。
1. 压缩 *debugLogs* 文件夹中的所有文件。

   > [!NOTE]
   > 这些日志文件并不是为了自行诊断。 它们适用于 Azure 工程团队分析你的问题。

   a. 在以下命令中，请确保将 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 替换为之前设置的边缘设备上的调试日志的位置。  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. 将 *debugLogs.zip* 文件附加到支持票证。

1. 可以通过将 **模块标识** 中的值设置为 *null*来停止日志收集。 返回到 **模块标识** 克隆页，并将以下参数更新为：

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>后续步骤

[教程：将基于事件的视频录制到云中并从云中播放](event-based-video-recording-tutorial.md)
