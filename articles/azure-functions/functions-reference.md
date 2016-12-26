---
title: "Azure Functions 开发人员参考 | Microsoft Docs"
description: "了解所有语言和绑定通用的 Azure Functions 概念和组件。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/20/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 0d13f6ae1b68b27f54ce9ffb10454b35cc6cdf29
ms.openlocfilehash: a897e725855aa6651898a58ef3830c22b8ae7621


---
# <a name="azure-functions-developer-reference"></a>Azure Functions developer reference（Azure Functions 开发人员参考）
在 Azure Functions 中，特定函数共享一些核心技术概念和组件，不受所用语言或绑定限制。 跳转学习某个特定语言或绑定的详细信息之前，请务必通读此通用概述。

本文假定你已阅读了 [Azure Functions 概述](functions-overview.md)且熟悉[触发器、绑定和 JobHost 运行时间等 WebJobs SDK 概念](../app-service-web/websites-dotnet-webjobs-sdk.md)。 Azure Functions 以 WebJobs SDK 为基础。 

## <a name="function-code"></a>函数代码
函数是 Azure Functions 的基本概念。 使用所选语言编写函数代码，并将代码文件和配置文件保存在同一文件夹中。 配置的名称为 `function.json`，其中包含 JSON 配置数据。 支持各种语言，并且对每种语言进行了优化，使每种语言体验有些许不同，以达到最佳工作效果。 

Function.json 文件定义函数绑定和其他配置设置。 运行时使用此文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 下面是一个示例 function.json 文件。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

将 `disabled` 属性设置为 `true`，阻止函数执行。

在 `bindings` 属性配置两个触发器和绑定。 每个绑定共享一些通用设置和一些特定于特定类型的绑定的设置。 每个绑定都需要以下设置：

| 属性 | 值/类型 | 注释 |
| --- | --- | --- |
| `type` |字符串 |绑定类型。 例如，`queueTrigger`。 |
| `direction` |'in', 'out' |表示绑定是用于接收数据到函数中或是从函数发送数据。 |
| `name` |字符串 |将用于函数中绑定数据的名称。 对于 C#，它将是参数名称；对于 JavaScript，它是键/值列表中的键。 |

## <a name="function-app"></a>函数应用
函数应用由一个或多个经 Azure App Service 共同管理的独立函数组成。 函数应用中的所有函数共享相同的定价计划、连续部署和运行时版本。 由多种语言编写的函数可共享相同的函数应用。 将函数应用视为组织和共同管理函数的一种方法。 

## <a name="runtime-script-host-and-web-host"></a>运行时（脚本宿主和 web 主机）
运行时或脚本宿主是基础 WebJobs SDK 主机，可侦听事件、收集和发送数据，并最终运行代码。 

为加快 HTTP 触发器，在生产方案中的脚本宿主前还设有 Web 主机。 拥有两个主机有助于将脚本宿主与由 Web 主机管理的前端通信隔离开来。

## <a name="folder-structure"></a>文件夹结构
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

设置用于将函数部署到 Azure App Service 中的函数应用的项目时，可以将此文件夹结构视为站点代码。 可使用现有工具（如持续集成和部署或自定义部署脚本），完成时间包安装部署或代码转换部署。

> [!NOTE]
> 确保将 `host.json` 文件和函数文件夹直接部署到 `wwwroot` 文件夹。 请勿在部署中包含 `wwwroot` 文件夹。 否则，最后将得到 `wwwroot\wwwroot` 文件夹。 
> 
> 

## <a name="a-idfileupdatea-how-to-update-function-app-files"></a><a id="fileupdate"></a> 如何更新函数应用文件
通过 Azure 门户中内置函数编辑器可更新 function.json 文件和函数代码文件。 要上传或更新其他文件，例如 package.json 或 project.json 或是依赖项，必须使用其他部署方法。

Function App 都建立在应用服务之上，因此所有[可用于标准 Web 应用的部署选项](../app-service-web/web-sites-deploy.md)也均可用于 Function App。 以下为可用的上传或更新函数应用文件的一些方法。 

#### <a name="to-use-app-service-editor"></a>使用应用服务编辑器
1. 在 Azure Functions 门户中，单击“函数应用设置”。
2. 在“高级设置”部分中，单击“转到应用服务设置”。
3. 在“应用菜单导航”下的“开发工具”中单击“应用服务编辑器”。
4. 单击“开始”。
   
   加载“应用服务编辑器”后，wwwroot 下将出现 host.json 文件和函数文件夹。 
5. 打开进行编辑，或者从开发计算机拖放文件进行上传。

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>使用函数应用的 SCM (Kudu) 终结点
1. 导航到：`https://<function_app_name>.scm.azurewebsites.net`。
2. 单击“调试控制台”> CMD。
3. 导航到 `D:\home\site\wwwroot\` 更新 host.json 或导航到 `D:\home\site\wwwroot\<function_name>` 更新函数文件。
4. 将想要上传的文件拖放到文件网格中相应的文件夹。 文件网格中有两个区域可放置文件。 对于 .zip 文件，会出现一个带标签的框，显示“将文件拖到此处进行上传并解压缩。” 对于其他文件类型，将文件拖放到“解压缩”框以外的文件网格中。

#### <a name="to-use-ftp"></a>使用 FTP
1. 请按[此处](../app-service-web/web-sites-deploy.md#ftp)的说明进行操作，配置 FTP。
2. 连接到函数应用站点时，复制一个已更新 host.json 文件到 `/site/wwwroot` 或将函数文件复制到 `/site/wwwroot/<function_name>`。

#### <a name="to-use-continuous-deployment"></a>使用连续部署
按照本主题中的说明 [Azure Functions 连续部署](functions-continuous-deployment.md) 进行操作。

## <a name="parallel-execution"></a>并行执行
多个触发事件发生的速度超过了单线程函数运行的处理速度时，运行时可并行多次调用函数。  如果 Function App 正在使用[消耗量托管计划](functions-scale.md#consumption-service-plan)，则 Function App 可自动扩大。  无论应用是在消耗量托管计划还是常规[应用服务托管计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)上运行，每个 Function App 实例都可能使用多个线程并行处理并发函数调用。  每个 Function App 实例中并发函数的最大调用数根据所用触发器类型以及 Function App 中其他函数所用资源而有所不同。

## <a name="azure-functions-pulse"></a>Azure Functions 脉冲
脉冲是一个实时事件流，显示了函数的运行频率，以及成功和失败次数。 还可监视平均执行时间。 随着时间的推移我们将向其中添加更多功能和自定义项。 可从“监视”选项卡访问“脉冲”页。

## <a name="repositories"></a>存储库
Azure Functions 代码为开放源，位于 GitHub 存储库：

* [Azure Functions 运行时](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions 门户](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions 模板](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 扩展](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>绑定
下面是所有受支持的绑定表。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>报告问题
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions C# 开发人员参考](functions-reference-csharp.md)
* [Azure Functions F# 开发人员参考](functions-reference-fsharp.md)
* [Azure Functions NodeJS 开发人员参考](functions-reference-node.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Azure Functions：Azure App Service 团队博客之旅](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/)。 Azure Functions 的开发历史。




<!--HONumber=Nov16_HO4-->


