---
title: Azure Service Fabric 网格常见问题 | Microsoft Docs
description: 了解 Azure Service Fabric 网格的常见问题和解答。
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f80f61cbfc1f7b719e73d7d29c6948bebe84aa6c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278304"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>有关 Service Fabric 网格的常见问题
Azure Service Fabric 网格是一个完全托管的服务，由此开发者可部署微服务应用程序，而无需管理虚拟机、存储或网络。 本文提供常见问题的解答。

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>如何报告问题或提问？

在 [service-fabric-mesh-preview GitHub 存储库](https://aka.ms/sfmeshissues)中询问问题、从 Microsoft 工程师处获得解答、报告问题。

## <a name="quota-and-cost"></a>配额和成本

**参与预览版的成本是多少？**

当前，将应用程序或容器部署到网格预览版不收取任何费用。 但是，建议删除你部署的资源，不要让它们保持运行，除非正在主动测试这些资源。

**内核和 RAM 的数量是否有配额限制？**

是的，每个订阅的配额设置如下：

- 应用程序数量 - 5 
- 每个应用程序的核心数 - 12 
- 每个应用程序的总内存 - 48 GB 
- 网络和入口终结点 - 5  
- 可以附加的 Azure 卷 - 10 
- 服务副本数 - 3 
- 可以部署的最大容器限制为 4 个内核、16GB RAM。
- 可以将部分核心以 0.5 个核心的增量分配给容器（最多 6 个核心）。

**我可以将我的已部署应用程序保留多长时间？**

当前，我们将应用程序的生存期限制为两天。 这是为了充分利用分配给预览版的空闲核心。 因此，仅允许你将给定的部署持续运行 48 小时，经过该时间量后，系统会关闭该部署。 如果看到此情况发生，可以通过在 Azure CLI 中运行 `az mesh app show` 命令并检查是否返回了 `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 来验证系统是否已将其关闭。 

例如： 

```cli
chackdan@Azure:~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

若要继续将同一应用程序部署到网格，应当删除与应用程序关联的资源组，或者单独删除应用程序和所有相关的网格资源（包括网络）。 

若要删除资源组，请使用 `az group delete <nameOfResourceGroup>` 命令。 

## <a name="supported-container-os-images"></a>支持的容器 OS 映像
部署服务时，可使用以下容器 OS 映像。

- Windows - windowsservercore 和 nanoserver
    - Windows Server 2016
    - Windows Server 版本 1709
- Linux
    - 无已知限制

## <a name="features-gaps-and-known-issues"></a>功能差距和已知问题

**部署应用程序后，与其相关联的网络资源似乎不是 IP 地址**

目前，存在一个已知问题 - IP 地址在延迟后出现。 几分钟后检查网络资源的状态，查看相关的 IP 地址。

**应用程序无法访问正确的网络/卷资源**

在应用程序模型中，需要使用网络和卷的完整资源 ID 才可访问相关资源。 快速入门示例中的情况如下所示：

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**看不到当前应用程序模型支持加密机密的方法**

是的，当前个人预览版中不支持加密机密。 

**DNS 在 Service Fabric 开发群集和网格中的工作方式不同**

存在一个已知问题，在此情况下，可能必须在本地开发群集和 Azure 网格中以不同的方式引用服务。 在本地开发群集中使用 {serviceName}.{applicationName}。 在 Azure Service Fabric 网格中使用 {servicename}。 Azure 网格目前不支持跨应用程序进行 DNS 解析。

有关在 Windows 10 上运行 Service Fabric 开发群集的其他已知 DNS 问题，请参阅此处：[调试 Windows 容器](/azure/service-fabric/service-fabric-how-to-debug-windows-containers)。

**使用 CLI 模块时遇到了此错误“ImportError: 无法导入名称 'sdk_no_wait'”**

如果使用的 CLI 版本早于 2.0.30，则可能会出现此错误 -

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**安装 CLI 扩展包时，出现不匹配的分发名称错误**

这并不意味着扩展没有安装。 应该仍然可以毫无问题地使用 CLI 命令。

**向外扩展时，看到所有的容器都受到影响，包括正在运行的容器**

这是一个 bug，应可在下次运行时刷新时得到修复。

## <a name="next-steps"></a>后续步骤

若要详细了解 Service Fabric 网格，请阅读[概述](service-fabric-mesh-overview.md)。
