---
title: 管理防火墙规则 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 使用 Azure 门户为后格雷SQL - 超大规模 （Citus） 创建和管理 Azure 数据库的防火墙规则
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977533"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>管理 Azure 数据库的防火墙规则，用于后格雷SQL - 超大规模（Citus）
服务器级防火墙规则可用于管理从指定的 IP 地址或 IP 地址范围对超大规模 （Citus） 协调器节点的访问。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 服务器组[为 PostgreSQL = 超大规模 （Citus） 服务器组创建 Azure 数据库](quickstart-create-hyperscale-portal.md)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

> [!NOTE]
> 在创建后格雷SQL - 超大规模 （Citus） 服务器组的 Azure 数据库期间，还可以访问这些设置。 在"**网络"** 选项卡下，单击 **"公共终结点**"。
> ![Azure 门户 - 网络选项卡](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. 在 PostgreSQL 服务器组页上，在"安全"标题下，单击 **"网络**"以打开防火墙规则。

   ![Azure 门户 - 单击"网络"](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 单击在工具栏上**添加客户端 IP**（下面的选项 A） 或链接（选项 B）。 无论哪种方式，都会自动创建具有计算机公共 IP 地址的防火墙规则，Azure 系统会感知到该规则。

   ![Azure 门户 - 单击"添加客户端 IP"](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

或者，单击 **+Add 0.0.0.0 - 255.255.255（** 选项 B 右侧）不仅允许您的 IP，还允许整个 Internet 访问协调节点的端口 5432。 在此情况下，客户端仍必须使用正确的用户名和密码登录才能使用群集。 然而，我们建议只允许全球访问很短的时间，并且只允许非生产数据库访问。

3. 验证 IP 地址，并保存配置。 在某些情况下，Azure 门户识别出的 IP 地址与访问 Internet 和 Azure 服务器时所使用的 IP 地址不同。 因此，可能需要更改起始 IP 和结束 IP，以使规则正常工作。
   使用搜索引擎或其他联机工具来查看自己的 IP 地址。 例如，搜索“我的 IP 是多少”。

   ![在必应中搜索“我的 IP 是多少”](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 添加其他地址范围。 在防火墙规则中，可以指定单个 IP 地址或地址范围。 如果希望将规则限制为单个 IP 地址，请在起始 IP 和结束 IP 字段中输入相同的地址。 打开防火墙使管理员、用户和应用程序能够访问端口 5432 上的协调器节点。

5. 在工具栏上单击“保存”**** 以保存此服务器级防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

## <a name="connecting-from-azure"></a>从 Azure 连接

有一种简单的方法可以授予对 Azure 上托管的应用程序（如 Azure Web 应用应用程序或在 Azure VM 中运行的应用程序）的超大规模数据库访问权限。 只需将 **"允许 Azure 服务和资源"设置为**从 **"网络"** 窗格在**门户中**访问此服务器组选项，然后点击 **"保存**"。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复这些步骤来管理防火墙规则。
* 要添加当前计算机，请单击按钮以 =**添加客户端 IP**。 单击 **“确定”**，保存这些更改。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击 **“确定”**，保存这些更改。
* 若要修改现有规则，单击规则中的任意字段并修改。 单击 **“确定”**，保存这些更改。
* 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则****。 单击 **“确定”**，保存这些更改。

## <a name="next-steps"></a>后续步骤
- 详细了解[防火墙规则的概念](concepts-hyperscale-firewall-rules.md)，包括如何解决连接问题。
