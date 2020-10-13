---
title: 还原已删除的 Azure Database for MySQL 服务器
description: 本文介绍如何使用 Azure 门户在 Azure Database for MySQL 中还原已删除的服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 1de11a07bdbac153b6813915cfa52edfa6484aec
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876887"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>还原已删除的 Azure Database for MySQL 服务器

删除服务器后，可以在服务中将数据库服务器备份最多保留五天。 只能从服务器最初驻留的 Azure 订阅访问和还原数据库备份。 可通过以下建议步骤在服务器删除后的 5 天内恢复已删除的 MySQL 服务器资源。 仅当服务器的备份仍可用且未从系统中删除时，建议的步骤才适用。 

## <a name="pre-requisites"></a>先决条件
若要还原已删除的 Azure Database for MySQL 服务器，需要执行以下操作：
- 托管原始服务器的 Azure 订阅名称
- 创建服务器的位置

## <a name="steps-to-restore"></a>要还原的步骤

1. 从 Azure 门户中的 "监视" 边栏选项卡中转到 [活动日志](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) 。 

2. 在活动日志中，单击 " **添加筛选器** " （如下所示），并设置以下筛选器 

    - **订阅** = 托管已删除服务器的订阅
    - **资源类型** = DBforMySQL/服务器 (Azure Database for MySQL 服务器)  
    - **操作** = 删除 MySQL Server (DBforMySQL/服务器/Delete)  
 
     [![为删除 MySQL 服务器操作筛选的活动日志](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. 双击 "删除 MySQL 服务器" 事件并单击 "JSON" 选项卡，并记下 JSON 输出中的 "resourceId" 和 "submissionTimestamp" 属性。 ResourceId 采用以下格式：/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver。
 
 4. 请参阅 [创建服务器 REST API 页面](https://docs.microsoft.com/rest/api/mysql/servers/create) ，然后单击以绿色突出显示的 "试用" 选项卡，然后用 Azure 帐户登录。
 
 5. 提供 resourceGroupName、serverName (删除的服务器名称) ，subscriptionId，派生自步骤3中捕获的 resourceId 属性，同时预填充 api 版本，如图所示。
 
     [![使用 REST API 创建服务器](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. 滚动到 "请求正文" 部分，并粘贴以下内容替换 "删除的服务器位置"、"submissionTimestamp" 和 "resourceId"。 对于 "restorePointInTime"，请将 "submissionTimestamp" 值指定为减 **15 分钟** ，以确保命令不会出错。
 
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```

7. 如果看到响应代码201或202，则已成功提交还原请求。 

8. 服务器创建可能需要一些时间，具体取决于原始服务器上预配的数据库大小和计算资源。 可以通过筛选来监视活动日志中的还原状态 
   - **订阅** = 你的订阅
   - **资源类型** = DBforMySQL/服务器 (Azure Database for MySQL 服务器)  
   - **操作** = 更新 MySQL Server 创建

## <a name="next-steps"></a>后续步骤
- 如果尝试在5天内还原服务器，并且在准确遵循前面所述的步骤后仍然收到错误，请打开支持事件以获得帮助。 如果在5天后尝试还原已删除的服务器，则会出现错误，因为找不到该备份文件。 在这种情况下，请勿打开支持票证。 如果从系统中删除备份，则支持团队无法提供任何帮助。 
- 为了防止意外删除服务器，我们强烈建议使用 [资源锁](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)。
