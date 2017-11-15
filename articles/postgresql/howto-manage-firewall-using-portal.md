---
title: "使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则 | Microsoft Docs"
description: "使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 96e917d1ea147e3b53b00002675ed16facb69255
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则
使用服务器级防火墙规则，管理员可以从指定的 IP 地址或某个范围的 IP 地址访问 Azure Database for PostgreSQL 服务器。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 一个服务器[创建 Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则
1. 在 PostgreSQL 服务器页上的“设置”标题下，单击“连接安全性”，打开 Azure Database for PostgreSQL 的“连接安全性”页。

  ![Azure 门户 - 单击连接安全性](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. 在工具栏上选择“添加我的 IP”。 该操作会自动创建一条防火墙规则，其中包含计算机的公共 IP 地址（由 Azure 系统标识）。

  ![Azure 门户 - 单击“添加我的 IP”](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 验证 IP 地址，并保存配置。 在某些情况下，Azure 门户识别出的 IP 地址与访问 Internet 和 Azure 服务器时所使用的 IP 地址不同。 因此，可能需要更改起始 IP 和结束 IP，以使规则正常工作。
使用搜索引擎或其他联机工具来查看自己的 IP 地址（例如，在必应中搜索“我的 IP 是多少”）。

  ![在必应中搜索“我的 IP 是多少”](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 添加其他地址范围。 在 Azure Database for PostgreSQL 防火墙规则中，可以指定单个 IP 地址，也可以指定某个范围的地址。 如果希望将规则限制为单个 IP 地址，请在起始 IP 和结束 IP 字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序可以登录到 PostgreSQL 服务器上他们拥有有效凭据的任何数据库。

  ![Azure 门户 - 防火墙规则 ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. 在工具栏上单击“保存”以保存此服务器级防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

  ![Azure 门户 - 单击“保存”](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有的服务器级别防火墙规则
重复这些步骤来管理防火墙规则。
* 若要添加当前计算机，请单击“添加我的 IP”的 + 按钮。 单击“保存”以保存更改。
* 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击“保存”以保存更改。
* 若要修改现有规则，单击规则中的任意字段并修改。 单击“保存”以保存更改。
* 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则。 单击“保存”以保存更改。

## <a name="next-steps"></a>后续步骤
- 同样，请参阅[使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-cli.md)。
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)。
