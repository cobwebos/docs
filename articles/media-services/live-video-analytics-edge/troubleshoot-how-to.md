---
title: 排查 IoT Edge 上的实时视频分析问题
description: 本文介绍 IoT Edge 上的实时视频分析的故障排除步骤。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045564"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>排查 IoT Edge 上的实时视频分析问题

本文介绍 IoT Edge 上的实时视频分析的故障排除步骤。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

### <a name="diagnostics"></a>诊断

作为部署实时视频分析的一部分，你将设置 Azure 资源，如 IoT 中心和 IoT Edge 设备。 作为诊断问题的第一步，始终可以按照以下说明正确设置边缘：

1. [运行“check”命令](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [检查 IoT Edge 版本](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [检查 IoT Edge 安全管理器的状态及其日志](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [查看通过 IoT Edge 中心的消息](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [重启容器](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [检查防火墙和端口配置规则](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>部署前问题

如果边缘基础结构正常，则可以查找部署清单文件的问题。 若要在边缘设备上与任何其他 IoT 模块一起部署实时视频 IoT Edge 分析，请使用包含边缘中心、边缘代理和其他模块及其属性的部署清单。 如果 JSON 的格式不正确，可能会出现如下错误： 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

未能分析 <deployment manifest.json> 参数 "content" 的文件 "" 中的 json，出现异常： "额外数据：行101第1列（字符5325）"

如果遇到此错误，则建议检查 JSON 文件中是否缺少括号或文件结构的其他问题。 您可以使用诸如[记事本 + + WITH JSON 查看器插件](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)的客户端或联机工具（例如） https://jsonformatter.curiousconcept.com/ 来验证文件结构。

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>部署–用 media graph 直接方法诊断 

在边缘设备上正确部署 IoT Edge 模块上的实时视频分析后，可以通过调用[直接方法](direct-methods.md)来创建和运行媒体关系图。 可以通过直接方法使用门户来运行 media graph 诊断：

1. 通过门户，中转到连接到 edge 设备的 IoT 中心。
    1. 进入 IoT 中心边栏选项卡后，查找自动设备管理->IoT Edge。
    1. 单击 "IoT Edge 应显示一系列边缘设备。 选择要诊断的设备。
         
        ![边缘设备](./media/troubleshoot-how-to/lva-sample-device.png)
    1. 检查响应代码是否为 200-正常。 [IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime)有不同的响应代码，如：
        1. 400 - 部署配置格式不正确或无效。
        1. 417 - 没有为设备设置部署配置。
        1. 412 - 部署配置中的架构版本无效。
        1. 406 - IoT Edge 设备脱机或不发送状态报告。
        1. 500 - IoT Edge 运行时中出现了一个错误。
    1. 单击该设备还应显示已部署的预期 IoT Edge 模块的列表及其状态
    1. 如果 "在部署中指定" 和 "设备报告" 列指示 "是"，则可以在 IoT Edge 模块上的实时视频分析上调用直接方法。 单击该模块后，会转到一个屏幕，可以在其中查看所需的属性和报告的属性，并可以调用直接方法。 
        1. 检查报告的属性和所需属性可帮助您了解模块属性是否已与部署同步。 如果没有，你可以重新启动边缘 
        1. 使用[直接方法](direct-methods.md)指南调用一些方法，尤其简单一些方法（如 GraphTopologyList）。 本指南还指定了预期的请求和响应负载以及错误代码。 简单直接方法成功后，可以确保实时视频分析边缘模块功能正常。
        
        ![直接方法](./media/troubleshoot-how-to/direct-method.png) 
1. 如果收到状态501代码，请检查直接方法名称是否正确。 如果方法名称和请求有效负载准确，则应返回结果，并显示成功代码 = 200。 如果请求有效负载不准确，你将获得状态 = 400 和响应有效负载，指示错误代码和消息，这些错误代码和消息应该有助于诊断直接方法调用的问题。 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>后期部署–在运行期间诊断日志中的问题 

边缘模块的容器日志应具有诊断<!--<todo:add link to diagnostics doc>--> 应有助于在模块运行时中调试你的问题的信息。 你可以[检查容器日志中是否存在问题](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues)和自行诊断，但如果执行了上述所有检查但仍遇到问题，请从 IoT Edge 设备收集日志，其中包含可由 Azure 团队进一步分析的["支持捆绑" 命令](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command)。 你可以与我们[联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获得支持和提交收集的日志。

## <a name="common-error-resolutions"></a>常见错误解决方法

实时视频分析作为 IoT Edge 模块部署到边缘设备上，并且与 IoT Edge 代理和中心模块一起工作。 实时视频分析部署会遇到的一些常见错误是由底层 IoT 基础结构的问题导致的。 IoT Edge 代理和中心的一些常见错误包括：

1. [IoT Edge 代理在大约一分钟后停止](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute)。
1. [IoT Edge 代理无法访问模块的映像（403）](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403)。
1. [边缘代理模块报告 "空配置文件"，并且设备上不会启动模块](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
1. [IoT Edge 中心无法启动](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start)。
1. [IoT Edge security daemon 失败，主机名无效](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
1. [实时视频分析或任何其他自定义 IoT Edge 模块无法将消息发送到 Edge 中心，出现404错误](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
1. [IoT Edge 模块部署成功后，将从设备中消失](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-set-up-script-issues"></a>边缘设置脚本问题

作为我们的文档的一部分，我们提供了一个用于部署边缘和云资源的[脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)，以便开始使用实时视频分析边缘。 在本部分中，我们捕获了你可能会遇到的有关脚本的错误以及如何调试它们。

脚本运行部分创建几个资源，但失败，并出现以下消息：

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
    
若要解决此问题：

1. 运行下面的命令：

    ```
    az --version
    ```
1. 确保安装了以下扩展。 在编写本指南时，扩展的版本如下所示：

    | 分机 | Version |
    |---|---|
    |azure-cli   |      版|
    |命令模块-nspkg         |   2.0.3|
    |核心  |    版|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |“扩展”：    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 如果任何扩展插件早于以上发布号，请使用命令将扩展更新到最新版本：

    ```
    az extension update --name <Extension name>
    ```

    例如，`az extension update --name azure-iot`

### <a name="sample-app-issues"></a>示例应用问题

作为我们版本的一部分，我们提供了一些 .NET 示例代码，让我们的开发人员社区开始使用。 在本部分中，我们捕获了在运行示例代码时可能会遇到的错误，以及如何调试此类错误。

1. 在直接方法调用中，Program.cs 失败并出现以下错误：

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. 确保在 VS code 环境中安装了[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)，并连接到 IoT 中心设置。 （Ctrl + shift + P，然后选择 "选择 IoT 中心方法" 以连接到订阅和 IoT 中心）
1. 检查是否可以通过 VS Code 调用边缘模块上的直接方法（例如，使用以下有效负载 {" @apiVersion "： "1.0"} 调用 GraphToplogyList），并且应返回以下响应。 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 如果上述操作失败，请尝试以下操作：
    1. 在边缘设备上，在命令提示符下，键入。
    
    ```
    sudo systemctl restart iotedge
    ```

    这会重启边缘设备和所有模块。 请等待几分钟，然后运行以下各项以确认模块正在运行，然后再次尝试使用 DirectMethod。

    ```
    sudo iotedge list
    ```
    1. 如果上述操作也失败，请尝试重新启动 VM 或计算机。
    1. 如果全部失败，请运行以下各项，以获取一个 ZIP 文件，其中包含所有[相关日志](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command)，并附加到该[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. 如果收到错误响应400代码，请确保方法调用有效负载按照[直接方法](direct-methods.md)指南格式正确。
1. 如果您收到状态200代码，则表示您的集线器运行正常，模块部署正确且响应迅速。 下一步是检查应用配置是否准确。 应用配置由 appsettings.js上的 "文件" 中的以下字段组成。 请仔细检查 deviceId 和 moduleId 是否准确。 检查此项的一种简单方法是通过 VSCode 中的 "Azure IoT 中心扩展" 部分。 文件 appsettings.js上的值和 IoT 中心部分应该匹配。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT 中心](./media/troubleshoot-how-to/iot-hub.png)

1. 最后，请确保在 appsettings.js中提供 IoT 中心连接字符串而不是 IoT 中心设备连接字符串，因为它们的[格式](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)不同。

### <a name="live-video-analytics-working-with-external-modules"></a>使用外部模块的实时视频分析

通过 HTTP 扩展处理器的实时视频分析可以扩展 media graph，使用 REST 通过 HTTP 发送和接收来自其他 IoT Edge 模块的数据。  作为[特定示例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，media graph 可以将视频帧作为图像发送到外部推理模块，如 Yolo V3，并接收基于 JSON 的分析结果。 在这种拓扑中，事件的最终目标主要是 IoT 中心。 如果在集线器上看不到推理事件，请检查以下各项：

1. 检查 media graph 要发布到的中心是否与你要检查的中心相同。 有时，当你创建多个部署时，最终将会获得多个中心，并可能会错误地检查事件中心。
1. 如果已部署并运行外部模块，请通过 VSCode 进行检查。 在此处的示例图中，rtspsim 和 cv 是运行外部到 lvaEdge 模块 IoT Edge 模块。

    ![IOT 中心](./media/troubleshoot-how-to/iot-hub.png)
1. 检查是否要将事件发送到正确的 URL 终结点。 外部 AI 容器公开一个 URL 和一个其接收的端口，并返回 POST 请求中的数据。 此 URL 被指定为 Http 扩展处理器的终结点： url 属性。 如[拓扑 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)中所示，将设置为推断 url 参数。 确保参数的默认值（ http://yolov3/score) 或传入的值是准确的，你可以使用 "卷" 来测试它是否正常工作。  
    1. 例如，在本地计算机上运行的 yolo v3 容器和容器的 IP 地址是172.17.0.3 （使用 docker 检查查找 IP 地址）。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    返回的结果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. 如果正在使用 Http 扩展处理器运行某个关系图的一个或多个实例，则在每个 Http 扩展处理器之前都应有一个帧速率筛选器来管理视频源的每秒帧数（fps）。 在某些情况下，边缘计算机的 CPU/内存使用率很高，则可能会丢失某些推理事件。 若要解决此情况，请在 "帧速率" 筛选器上设置 maximumFps 属性的低值。 可以在图形的每个实例上将其设置为0.5 （"maximumFps"：0.5），然后重新运行以检查中心上的推理事件。
    1. 另外，还可以使用更高的 CPU 和内存来获取功能更强大的边缘计算机。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>并行多个直接方法–超时失败 

IoT Edge 上的实时视频分析提供了一种基于方法的直接编程模型，该模型允许设置多个拓扑和多个图形实例。 作为拓扑和图形设置的一部分，您将在边缘模块上调用多个直接方法调用。 如果调用了多个方法调用，特别是启动和停止关系图的方法调用，则可能会遇到一些超时故障，如下面所示。 

程序集初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 引发了异常。 IotHubException： IotHubException （常见情况）的操作为：（& e）：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

我们建议您不要以并行方式调用直接方法，而是按顺序（即 只有上一个直接方法调用完成后才调用。

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>收集用于提交支持票证的日志

当自行指导故障排除步骤不能解决您的问题时，您应该转到 Azure 门户并提出[支持票证](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

完成以下步骤以收集应添加到票证的相关日志。 你将能够在支持请求的 "**详细信息**" 选项卡上上传日志文件。

### <a name="support-bundle"></a>支持-捆绑

如果需要从 IoT Edge 设备收集日志，最简单的方法是使用 `support-bundle` 命令。 此命令收集：

- 模块日志
- IoT Edge 安全管理器和容器引擎日志
- Iotedge 检查 JSON 输出
- 有用的调试信息

#### <a name="use-the-iot-edge-security-manager"></a>使用 IoT Edge 安全管理器
 
IoT Edge 安全管理器 负责在启动时初始化 IoT Edge 系统和预配设备等操作。 如果 IoT Edge 未启动，则安全管理器日志可能会提供有用的信息。 查看 IoT Edge 安全管理器的更详细日志：

1. 在 IoT Edge 设备上编辑 IoT Edge 守护程序设置：

    ```
    sudo systemctl edit iotedge.service
    ```

1. 更新以下行：

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. 通过运行以下命令重新启动 IoT Edge 安全守护程序：

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. 运行 `support-bundle` 带有--from 标志的命令，指定要从过去获取日志的时间。 例如，在过去两小时后，将收到日志。 您可以更改此标志的值，以包含不同时间段的日志。

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>LVA 调试日志

请按照以下步骤配置 IoT Edge 模块上的 LVA 以生成调试日志：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”  。
1. 在设备列表中单击目标设备的 ID。
1. 单击顶部菜单上的 "**设置模块**" 链接。

  ![设置模块 azure 门户](media/troubleshoot-how-to/set-modules.png)

5. 在 "IoT Edge 模块" 部分下，找到并单击 " **lvaEdge**"。
1. 单击 "**容器创建选项**"。
1. 在 "绑定" 部分中，添加以下命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    这会将日志文件夹绑定到边缘设备和容器。

1. 单击 "**更新**" 按钮
1. 单击页面底部的 "查看" 和 "**创建**" 按钮。 将进行一次简单的验证，并在绿色横幅下成功发布验证消息。
1. 单击“创建”按钮。 
1. 接下来，更新**模块标识**克隆，使 DebugLogsDirectory 参数指向将收集日志的目录：
    1. 在 "**模块**" 表下选择 " **lvaEdge** "。
    1. 单击 "**模块标识**" "克隆" 链接。 你会在页面顶部找到此页。 这将打开一个可编辑窗格。
    1. 在**所需的项**下添加以下键值对：

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. 单击“保存” 。

1. 重现问题。
1. 从门户中的 "IoT 中心" 页面连接到虚拟机。
1. 导航到该 `/var/local/mediaservices/logs` 文件夹，然后压缩此文件夹的 bin 内容并与我们共享。 （这些日志文件并不是为了自行诊断。 它们适用于 Azure 工程分析你的问题。）

1. 可以通过将**模块标识**中的该值设置为*null*来停止日志收集。 返回到**模块标识**克隆页，并将以下参数更新为：

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>后续步骤

[教程：将基于事件的视频录制到云中并从云中播放](event-based-video-recording-tutorial.md)
