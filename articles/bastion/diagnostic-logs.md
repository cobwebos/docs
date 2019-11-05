---
title: 启用和使用 Azure 堡垒诊断日志 |Microsoft Docs
description: 本文介绍如何启用和使用 Azure 堡垒诊断日志。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 8ae421b48f3cfa8055f636052c990f99e0c775b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512400"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>启用并使用堡垒诊断日志

当用户使用 Azure 堡垒连接到工作负荷时，堡垒可以记录远程会话的诊断。 然后，可以使用诊断来查看哪些用户连接到哪些工作负荷，以及在何处以及其他此类相关日志记录信息。 若要使用诊断，必须在 Azure 堡垒上启用诊断日志。 本文将帮助您启用诊断日志，然后查看日志。

## <a name="enable"></a>启用诊断日志

1. 在[Azure 门户](https://portal.azure.com)中，导航到 azure 堡垒资源，并从 azure 堡垒页面中选择 "**诊断设置**"。

   ![诊断设置](./media/diagnostic-logs/1diagnostics-settings.png)
2. 选择 "**诊断设置**"，然后选择 " **+ 添加诊断设置**" 以添加日志的目标。

   ![添加诊断设置](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在 "**诊断设置**" 页上，选择要用于存储诊断日志的存储帐户的类型。

   ![选择存储位置](./media/diagnostic-logs/3add-storage-account.png)
4. 完成设置后，它将类似于以下示例：

   ![示例设置](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>查看诊断日志

若要访问诊断日志，可以直接使用在启用诊断设置时指定的存储帐户。

1. 导航到存储帐户资源，然后导航到 "**容器**"。 你会看到在存储帐户 blob 容器中创建的**bastionauditlogs** blob。

   ![诊断设置](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 导航到容器中时，会在博客中看到各种文件夹。 这些文件夹指示 Azure 堡垒资源的资源层次结构。

   ![添加诊断设置](./media/diagnostic-logs/2-resource-h.png)
3. 导航到要访问/查看其诊断日志的 Azure 堡垒资源的完整层次结构。 "Y ="、"m ="、"d ="、"h =" 和 "m =" 分别表示诊断日志的年、月、日、小时和分钟。

   ![选择存储位置](./media/diagnostic-logs/3-resource-location.png)
4. 找到由 Azure 堡垒创建的 json 文件，其中包含导航到的时间段的诊断日志数据。

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

阅读[堡垒常见问题解答](bastion-faq.md)。
