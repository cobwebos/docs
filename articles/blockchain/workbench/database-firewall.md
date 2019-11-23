---
title: Configure Azure Blockchain Workbench database firewall
description: Learn how to configure the Azure Blockchain Workbench Preview database firewall to allow external clients and applications to connect.
ms.date: 09/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e6739b7ead9299f020465d3c50ed01826334ca76
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326001"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>配置 Azure Blockchain Workbench 数据库防火墙

本文演示如何使用 Azure 门户配置防火墙规则。 防火墙规则允许外部客户端或应用程序连接到 Azure Blockchain Workbench 数据库。

## <a name="connect-to-the-blockchain-workbench-database"></a>连接到 Blockchain Workbench 数据库

若要连接到要配置规则的数据库，请执行以下操作：

1. Sign in to the Azure portal with an account that has **Owner**     permissions for the Azure Blockchain Workbench resources.
2. 在左侧导航窗格中，选择“资源组”。
3. 选择你的 Blockchain Workbench 部署的资源组的名称。
4. 选择“类型”，对资源列表进行排序，然后选择 **SQL Server**。
5. 以下屏幕截图中的资源列表示例演示两个数据库：*master* 和 *lsgn-sdk*。 在 *lsgn-sdk* 上配置防火墙规则。

![列出 Blockchain Workbench 资源](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>创建数据库防火墙规则

若要创建防火墙规则，请执行以下操作：

1. 选择“lsgn-sdk”数据库的链接。
2. 在菜单栏上，选择“设置服务器防火墙”。

   ![设置服务器防火墙](./media/database-firewall/configure-server-firewall.png)

3. 若要为组织创建规则，请执行以下操作：

   * 输入**规则名称**
   * 输入地址范围的**起始 IP** 的 IP 地址
   * 输入地址范围的**结束 IP** 的 IP 地址

   ![创建防火墙规则](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > 如果只想添加计算机的 IP 地址，请选择“+ 添加客户端 IP”。
        
1. 若要保存防火墙配置，请选择“保存”。
2. 通过从应用程序或工具连接，测试为数据库配置的 IP 地址范围。 例如，SQL Server Management Studio。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的数据库视图](database-views.md)