---
title: 启用和使用 Azure 堡垒诊断日志
description: 在本文中，了解如何启用和使用 Azure 堡垒诊断日志。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989444"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>启用和使用堡垒诊断日志

当用户使用 Azure Bastion 连接到工作负荷时，Bastion 可以记录远程会话的诊断。 然后，可以使用诊断来查看哪些用户连接到哪些工作负荷、在什么时间、从何处以及其他此类相关日志记录信息。 为了使用诊断，必须在 Azure 堡垒上启用诊断日志。 本文可帮助您启用诊断日志，然后查看日志。

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>启用诊断日志

1. 在[Azure 门户](https://portal.azure.com)中，导航到 Azure 堡垒资源，并从 Azure 堡垒页中选择 **"诊断"设置**。

   ![诊断设置](./media/diagnostic-logs/1diagnostics-settings.png)
2. 选择**诊断设置**，然后选择 **"添加诊断设置**"以添加日志的目标。

   ![添加诊断设置](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在 **"诊断设置"** 页上，选择要用于存储诊断日志的存储帐户类型。

   ![选择存储位置](./media/diagnostic-logs/3add-storage-account.png)
4. 完成设置后，它将类似于此示例：

   ![示例设置](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>查看诊断日志

要访问诊断日志，可以直接使用启用诊断设置时指定的存储帐户。

1. 导航到存储帐户资源，然后导航到**容器**。 您将看到在存储帐户 blob 容器中创建的**见解日志-堡垒审核日志 blob。**

   ![诊断设置](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 导航到容器内部时，在博客中可以看到各种文件夹。 这些文件夹指示 Azure 堡垒资源的资源层次结构。

   ![添加诊断设置](./media/diagnostic-logs/2-resource-h.png)
3. 导航到要访问/查看其诊断日志的 Azure Bastion 资源的完整层次结构。 诊断日志的"y"、"m=""d=""h="和"m="分别指示诊断日志的年份、月份、日、小时和分钟。

   ![选择存储位置](./media/diagnostic-logs/3-resource-location.png)
4. 查找 Azure Bastion 创建的 json 文件，该文件包含导航到的时间段的诊断日志数据。

5. 从存储 Blob 容器下载 json 文件。 json 文件的示例条目如下所示供参考：

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

阅读[堡垒常见问题解答](bastion-faq.md)。
