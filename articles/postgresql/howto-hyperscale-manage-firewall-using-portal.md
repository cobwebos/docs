---
title: 管理防火墙规则-超大规模（Citus）-Azure Database for PostgreSQL
description: 使用 Azure 门户创建和管理 Azure Database for PostgreSQL-超大规模（Citus）的防火墙规则
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b7de5755a9a1e49b994e7efa7fc4bca58cc6cfd9
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584038"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>管理 Azure Database for PostgreSQL 的防火墙规则-超大规模（Citus）
服务器级防火墙规则可用于从指定的 IP 地址或 IP 地址范围管理对超大规模（Citus）协调器节点的访问。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 服务器组[创建 Azure Database for PostgreSQL –超大规模（Citus）服务器组](quickstart-create-hyperscale-portal.md)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

> [!NOTE]
> 在创建 Azure Database for PostgreSQL 超大规模（Citus）服务器组的过程中，还可以访问这些设置。 在 "**网络**" 选项卡下，单击 "**公用终结点**"。
> ![Azure 门户-网络 "选项卡](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. 在 "PostgreSQL 服务器组" 页上的 "安全" 标题下，单击 "**网络**" 打开防火墙规则。

   ![Azure 门户单击网络](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 单击工具栏上的 "添加客户端 IP"，或在链接中单击 "**添加客户端 IP**" （选项 B）。 无论采用哪种方式，都会使用计算机的公共 IP 地址自动创建防火墙规则，如 Azure 系统所示。

   ![Azure 门户单击 "添加客户端 IP"](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

或者，单击选项 B 右侧的 " **+ 添加 0.0.0.0-255.255.255.255** "，不仅可以访问 IP，还允许使用整个 internet 访问协调器节点的端口5432。 在这种情况下，客户端仍必须使用正确的用户名和密码登录才能使用该群集。 尽管如此，我们建议仅在短时间内允许全球访问，仅允许非生产数据库。

3. 验证 IP 地址，并保存配置。 在某些情况下，Azure 门户识别出的 IP 地址与访问 Internet 和 Azure 服务器时所使用的 IP 地址不同。 因此，可能需要更改起始 IP 和结束 IP，以使规则正常工作。
   使用搜索引擎或其他联机工具来查看自己的 IP 地址。 例如，搜索“我的 IP 是多少”。

   ![在必应中搜索“我的 IP 是多少”](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 添加其他地址范围。 在防火墙规则中，可以指定单个 IP 地址或地址范围。 如果希望将规则限制为单个 IP 地址，请在起始 IP 和结束 IP 字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序就可以访问端口5432上的协调器节点。

5. 在工具栏上单击“保存”**** 以保存此服务器级防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

## <a name="connecting-from-azure"></a>从 Azure 连接

有一种简单的方法可授予超大规模数据库对 Azure 上托管的应用程序的访问权限（例如，Azure Web Apps 应用程序或在 Azure VM 中运行的应用程序）。 只需在门户中从 "**网络**" 窗格将 "**允许 Azure 服务和资源访问此服务器组**" 选项设置为 **"是"** ，然后单击 "**保存**"。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复这些步骤来管理防火墙规则。
* 要添加当前计算机，请单击按钮 "+**添加客户端 IP**"。 单击 **“确定”**，保存这些更改。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击 **“确定”**，保存这些更改。
* 若要修改现有规则，单击规则中的任意字段并修改。 单击 **“确定”**，保存这些更改。
* 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则****。 单击 **“确定”**，保存这些更改。

## <a name="next-steps"></a>后续步骤
- 详细了解[防火墙规则的概念](concepts-hyperscale-firewall-rules.md)，包括如何排查连接问题。
