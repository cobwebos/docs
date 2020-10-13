---
title: 启用和使用 Azure Bastion 资源日志
description: 本文介绍如何启用和使用 Azure Bastion 诊断日志。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445391"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>启用和使用 Bastion 资源日志

当用户使用 Azure Bastion 连接到工作负载时，Bastion 可以记录对远程会话的诊断。 然后你可以使用诊断来查看哪些用户在什么时间从哪里连接到哪些工作负载，以及其他此类相关的日志记录信息。 若要使用诊断，必须在 Azure Bastion 上启用诊断日志。 本文可帮助你先启用诊断日志，然后查看这些日志。

## <a name="enable-the-resource-log"></a><a name="enable"></a>启用资源日志

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 Azure Bastion 资源并从 Azure Bastion 页面选择“诊断设置”。

   ![显示 "诊断设置" 页的屏幕截图。](./media/diagnostic-logs/1diagnostics-settings.png)
2. 选择“诊断设置”，然后选择“+添加诊断设置”，以便为日志添加目标。

   ![显示 "诊断设置" 页的屏幕截图，其中选择了 "添加诊断设置" 按钮。](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在“诊断设置”页上，选择要用于存储诊断日志的存储帐户的类型。

   !["诊断设置" 页的屏幕截图，其中突出显示了选择存储位置部分。](./media/diagnostic-logs/3add-storage-account.png)
4. 完成设置后，设置将如以下示例所示：

   ![示例设置](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>查看诊断日志

若要访问诊断日志，可以直接使用在启用诊断设置时指定的存储帐户。

1. 导航到你的存储帐户资源，然后导航到“容器”。 你会看到在存储帐户 blob 容器中创建了 insights-logs-bastionauditlogs blob。

   ![诊断设置](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 导航到容器中时，会在 blob 中看到各种文件夹。 这些文件夹指示 Azure Bastion 资源的资源层次结构。

   ![添加诊断设置](./media/diagnostic-logs/2-resource-h.png)
3. 导航到要访问/查看其诊断日志的 Azure Bastion 资源的完整层次结构。 “y=”、“m=”、“d=”、“h=”和“m=”分别指示资源日志的年、月、日、小时和分钟。

   ![选择存储位置](./media/diagnostic-logs/3-resource-location.png)
4. 找到由 Azure Bastion 创建的 json 文件，其中包含导航到的时间段的诊断日志数据。

5. 从存储 blob 容器下载 json 文件。 下面显示了 json 文件中的一个示例条目供参考：

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
