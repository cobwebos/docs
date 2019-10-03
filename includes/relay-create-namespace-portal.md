---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210158"
---
1. 登录到 [Azure 门户][Azure portal]。
1. 选择“创建资源”。 然后，选择“集成” > “中继”。 如果未在列表中看到“中继”，请选择右上角的“查看全部”。
1. 选择 "**创建**"，然后在 "**名称**" 字段中输入命名空间名称。 Azure 门户检查该名称是否可用。
1. 选择要在其中创建命名空间的 Azure 订阅。
1. 对于 "[资源组](../articles/azure-resource-manager/manage-resource-groups-portal.md)"，请选择要在其中放置命名空间的现有资源组，或创建新的资源组。  
1. 选择应在其中托管命名空间的国家或地区。

    ![创建命名空间][create-namespace]

1. 选择“创建”。 Azure 门户创建命名空间并启用它。 几分钟后，系统将为用户的帐户预配资源。

### <a name="get-management-credentials"></a>获取管理凭据

1. 选择 "**所有资源**"，然后选择新创建的命名空间名称。
1. 选择“共享访问策略”。  
1. 在“共享访问策略”下，选择“RootManageSharedAccessKey”。
1. 在 **"SAS 策略" 下：RootManageSharedAccessKey**，选择 "**主连接字符串**" 旁边的 "**复制**" 按钮。 此操作会将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。
1. 重复上述步骤，将**主密钥**的值复制和粘贴到临时位置，供以后使用。  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
