---
title: 使用 SQL FQDN 配置 Azure 防火墙应用程序规则
description: 本文介绍如何在 Azure 防火墙应用程序规则中配置 SQL FQDN。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 8b67574f435681d8071eda1ad954dcafb5124cbf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655102"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>使用 SQL FQDN 配置 Azure 防火墙应用程序规则

现在可以使用 SQL FQDN 配置 Azure 防火墙应用程序规则。 这样即可将来自虚拟网络的访问限制为指定的 SQL Server 实例。

使用 SQL FQDN，可以筛选流量：

- 从 VNet 到 Azure SQL 数据库或 Azure SQL 数据仓库。 例如：仅允许访问 sql-server1.database.windows.net。
- 从本地到 VNet 中运行的 Azure SQL 托管实例或 SQL IaaS。
- 从“分支到分支”到 VNet 中运行的 Azure SQL 托管实例或 SQL IaaS。

仅在[代理模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)下支持 SQL FQDN 筛选（端口 1433）。 如果在默认重定向模式下使用 SQL，则可以使用 SQL 服务标记作为[网络规则](overview.md#network-traffic-filtering-rules)的一部分来筛选访问。
如果对 SQL IaaS 通信使用非默认端口，则可以在防火墙应用程序规则中配置这些端口。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 进行配置

1. [使用 Azure CLI 部署 Azure 防火墙](deploy-cli.md)。
2. 如果筛选发往 Azure SQL 数据库、SQL 数据仓库或 SQL 托管实例的流量，请确保将 SQL 连接模式设置为“代理”。 若要了解如何切换 SQL 连接模式，请参阅 [Azure SQL 连接设置](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)。

   > [!NOTE]
   > 与重定向相比，SQL 代理模式可能会导致更大的延迟。 如果要继续使用重定向模式（这是在 Azure 中进行连接的客户端的默认模式），可以使用防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL [服务标记](service-tags.md)来筛选访问。

3. 使用 SQL FQDN 配置应用程序规则以允许访问 SQL Server：

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>使用 Azure 门户进行配置
1. [使用 Azure CLI 部署 Azure 防火墙](deploy-cli.md)。
2. 如果筛选发往 Azure SQL 数据库、SQL 数据仓库或 SQL 托管实例的流量，请确保将 SQL 连接模式设置为“代理”。 若要了解如何切换 SQL 连接模式，请参阅 [Azure SQL 连接设置](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)。  

   > [!NOTE]
   > 与重定向相比，SQL 代理模式可能会导致更大的延迟。 如果要继续使用重定向模式（这是在 Azure 中进行连接的客户端的默认模式），可以使用防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL [服务标记](service-tags.md)来筛选访问。
3. 添加带有适当协议、端口和 SQL FQDN 的应用程序规则，然后选择“保存”。
   ![带有 SQL FQDN 的应用程序规则](media/sql-fqdn-filtering/application-rule-sql.png)
4. 从 VNet 中的虚拟机访问 SQL，该虚拟机通过防火墙筛选流量。 
5. 验证 [Azure 防火墙日志](log-analytics-samples.md)是否显示该流量已获得允许。

## <a name="next-steps"></a>后续步骤

若要了解 SQL 代理和重定向模式，请参阅 [Azure SQL 数据库连接体系结构](../sql-database/sql-database-connectivity-architecture.md)。