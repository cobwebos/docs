---
title: "在 Azure 中模拟访问读取访问冗余存储时发生的故障 | Microsoft Docs"
description: "模拟访问读取访问异地冗余存储时发生的错误"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 151e875bd72598b0b788d68eee7fb186fca86f46
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>模拟访问读取访问冗余存储时发生的故障

本教程是一个系列中的第二部分。 在本教程中，使用 Fiddler 插入向[读取访问异地冗余](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) 存储帐户发送的请求的失败响应，从而模拟故障，并让应用程序从辅助终结点读取内容。

![方案应用程序](media/storage-simulate-failure-ragrs-account-app/scenario.png)

本系列教程的第二部分将介绍如何：

> [!div class="checklist"]
> * 运行和暂停应用程序
> * 模拟故障
> * 模拟主终结点还原

## <a name="prerequisites"></a>系统必备

完成本教程：

* 下载并安装 [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

若要完成本教程，必须先完成上一存储教程：[使用 Azure 存储实现应用程序数据的高可用性][previous-tutorial]。

## <a name="launch-fiddler"></a>启动 fiddler

打开 Fiddler，依次选择“规则”和“自定义规则”。

![自定义 Fiddler 规则](media/storage-simulate-failure-ragrs-account-app/figure1.png)

此时，Fiddler ScriptEditor 启动，并显示 SampleRules.js 文件。 此文件用于自定义 Fiddler。 在 `OnBeforeResponse` 函数中粘贴以下代码示例。 新代码会被注释掉，以确保它所创建的逻辑不会立即实现。 完成后，依次选择“文件”和“保存”，保存所做的更改。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![粘贴自定义规则](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>启动和暂停应用程序

在 Visual Studio 中，按 F5 或选择“启动”，开始调试应用程序。 在应用程序开始从主终结点读取内容后，立即按控制台窗口中的任意键，暂停应用程序。

## <a name="simulate-failure"></a>模拟故障

当应用程序暂停后，现在可以取消注释掉上一步在 Fiddler 中保存的自定义规则。 此代码示例查找对 RA-GRS 存储帐户发出的请求，如果路径包含映像名称 `HelloWorld`，则返回响应代码 `503 - Service Unavailable`。

转到 Fiddler，再依次选择“规则” -> “自定义规则...”。取消注释掉以下代码行，将 `STORAGEACCOUNTNAME` 替换为存储帐户名称。 依次选择“文件” -> “保存”，保存更改。

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

若要恢复应用程序，请按任意键。

当应用程序再次开始运行后，向主终结点发出的请求失败。 应用程序尝试重新连接到主终结点 5 次。 达到 5 次故障重试阈值后，便会从只读辅助终结点请求获取映像。 从辅助终结点成功检索映像 20 次后，应用程序便会尝试连接到主终结点。 如果仍无法访问主终结点，应用程序会继续从辅助终结点读取内容。 这种模式就是上一教程中介绍的[断路器](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)模式。

![粘贴自定义规则](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>模拟主终结点还原

借助上一步中设置的 Fiddler 自定义规则，向主终结点发出的请求失败。 为了再次模拟正常运行的主终结点，请删除用于注入 `503` 错误的逻辑。

若要暂停应用程序，请按任意键。

### <a name="remove-the-custom-rule"></a>删除自定义规则

转到 Fiddler，再依次选择“规则”和“自定义规则...”。注释掉或删除 `OnBeforeResponse` 函数中的自定义逻辑，保留默认函数。 依次选择“文件”和“保存”，保存所做的更改。

![删除自定义规则](media/storage-simulate-failure-ragrs-account-app/figure5.png)

完成后，按任意键恢复应用程序。 应用程序会继续从主终结点读取内容，直到达到 999 次读取上限为止。

![恢复应用程序](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>后续步骤

本系列教程的第二部分介绍了如何模拟故障来测试读取访问异地冗余存储，如执行以下操作：

> [!div class="checklist"]
> * 运行和暂停应用程序
> * 模拟故障
> * 模拟主终结点还原

请访问以下链接，查看预先生成的存储示例。

> [!div class="nextstepaction"]
> [Azure 存储脚本示例](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md