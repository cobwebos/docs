---
title: "可缩放用于 MySQL 服务器的 Azure 数据库的 Azure CLI 示例 | Microsoft Docs"
description: "此示例 CLI 脚本在查询指标后将用于 MySQL 服务器的 Azure 数据库缩放为不同的性能级别。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c54262218e6174dc85905cb067c334cc6a401946
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 监视和缩放用于 MySQL 服务器的 Azure 数据库
此示例 CLI 脚本在查询指标后将用于 MySQL 服务器的单个 Azure 数据库缩放为不同的性能级别。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az mysql server update \
--resource-group myresource \
--name mysqlserver4demo \
--compute-units 100
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
```azurecli
az group delete --name myresource
```

## <a name="next-steps"></a>后续步骤
[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)

