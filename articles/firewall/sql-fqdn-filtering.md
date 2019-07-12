---
title: 使用 SQL Fqdn 配置 Azure 防火墙应用程序规则
description: 在本文中，您将了解如何在 Azure 防火墙应用程序规则中配置 SQL Fqdn。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786593"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>使用 SQL Fqdn 配置 Azure 防火墙应用程序规则

> [!IMPORTANT]
> 与 SQL Fqdn 配置 azure 防火墙应用程序规则目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

现在可以使用 SQL Fqdn 配置 Azure 防火墙应用程序规则。 这可以限制从你的虚拟网络到仅指定 SQL server 实例的访问。

使用 SQL Fqdn，您可以筛选流量：

- 从你的 Vnet 到 Azure SQL 数据库或 Azure SQL 数据仓库。 例如：仅允许访问*sql server1.database.windows.net*。
- 从本地到 Azure SQL 托管实例或在 Vnet 中运行的 SQL IaaS。
- 从分支-到-辐射型到 Azure SQL 托管实例或在 Vnet 中运行的 SQL IaaS。

公共预览期间，SQL FQDN 筛选支持在[代理模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)唯一 （端口 1433年）。 如果在默认的重定向模式中使用 SQL，则可以筛选访问作为的一部分使用的 SQL 服务标记[网络规则](overview.md#network-traffic-filtering-rules)。
如果 SQL IaaS 流量使用非默认端口，可以在防火墙应用程序规则中配置这些端口。

> [!NOTE]
> 在通过 Azure CLI、 REST 和模板的所有区域中目前已与 SQL Fqdn 的应用程序规则。 门户用户界面添加到区域以增量方式，并可在所有区域推出实施完成后。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 配置

1. 部署[使用 Azure CLI 的 Azure 防火墙](deploy-cli.md)。
2. 如果筛选到 Azure SQL 数据库、 SQL 数据仓库或 SQL 托管实例的流量，请确保 SQL 连接模式设置为**代理**。 若要了解如何切换 SQL 连接模式，请参阅[Azure SQL 连接体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > SQL*代理*模式下可能会导致延迟时间相比的更长*重定向*。 如果你想要继续使用重定向模式，这是在 Azure 中连接的客户端的默认值，则可以筛选访问使用 SQL[服务标记](service-tags.md)防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

3. 使用 SQL FQDN，以允许访问 SQL server 配置应用程序规则：

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>使用 Azure 门户配置
1. 部署[使用 Azure CLI 的 Azure 防火墙](deploy-cli.md)。
2. 如果筛选到 Azure SQL 数据库、 SQL 数据仓库或 SQL 托管实例的流量，请确保 SQL 连接模式设置为**代理**。 若要了解如何切换 SQL 连接模式，请参阅[Azure SQL 连接体系结构](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > SQL*代理*模式下可能会导致延迟时间相比的更长*重定向*。 如果你想要继续使用重定向模式，这是在 Azure 中连接的客户端的默认值，则可以筛选访问使用 SQL[服务标记](service-tags.md)防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)。
3. 添加具有适当的协议、 端口和 SQL FQDN 的应用程序规则，然后选择**保存**。
   ![使用 SQL FQDN 的应用程序规则](media/sql-fqdn-filtering/application-rule-sql.png)
4. 从虚拟机的 VNet 中的筛选器的流量通过防火墙访问 SQL。 
5. 验证[Azure 防火墙日志](log-analytics-samples.md)显示允许的流量。

## <a name="next-steps"></a>后续步骤

若要了解有关 SQL 代理和重定向模式，请参阅[Azure SQL 数据库连接体系结构](../sql-database/sql-database-connectivity-architecture.md)。