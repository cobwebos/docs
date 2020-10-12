---
title: 管理防火墙规则-Azure 门户 Azure Database for MySQL-灵活的服务器
description: 使用 Azure 门户为 Azure Database for MySQL 灵活的服务器创建和管理防火墙规则
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 132319575147c2ff1075881b1f1faec8bc5029f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934767"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户为 Azure Database for MySQL 灵活的服务器创建和管理防火墙规则

> [!IMPORTANT]
> Azure Database for MySQL 灵活的服务器当前为公共预览版。

Azure Database for MySQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

1. 公共访问（允许的 IP 地址）
2. 专用访问（VNet 集成）

在本文中，我们将重点介绍如何使用 **公共访问权限创建 MySQL 服务器 (允许的 IP 地址) ** 使用 Azure 门户，并提供在创建灵活的服务器后管理防火墙规则的概述。 通过 *公共访问 (允许的 ip 地址) *，与 MySQL 服务器的连接仅限于允许的 ip 地址。 需要在防火墙规则中使用客户端 IP 地址。 若要了解详细信息，请参阅 [公共访问 (允许的 IP 地址) ](./concepts-networking.md#public-access-allowed-ip-addresses)。 可以在创建服务器时定义防火墙规则 (建议的) 但也可在以后添加。 本文介绍如何使用公共访问权限 (允许的 IP 地址) 来创建和管理防火墙规则。

## <a name="create-a-firewall-rule-when-creating-a-server"></a>创建服务器时创建防火墙规则

1. 在门户左上角选择“创建资源”  (+)。
2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。
3. 选择“灵活服务器”作为部署选项。
4. 填写 " **基本** 信息" 窗体。
5. 请参阅 " **网络** " 选项卡以配置要连接到服务器的方式。
6. 在 " **连接方法**" 中，选择 " *公共访问 (允许的 IP 地址") *。 若要创建 **防火墙规则**，请指定防火墙规则名称和单个 IP 地址或地址范围。 如果要将规则限制为单个 IP 地址，请在 "起始 IP 地址" 和 "结束 IP 地址" 字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序便可以访问 MySQL 服务器上他们拥有有效凭据的任何数据库。
   > [!Note]
   > Azure Database for MySQL 灵活的服务器在服务器级别创建防火墙。 除非创建了规则来为特定的 IP 地址打开防火墙，否则此防火墙会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。

7. 选择“查看 + 创建”，查看你的灵活服务器配置。
8.  选择“创建”以预配服务器。 预配可能需要几分钟时间。

## <a name="create-a-firewall-rule-after-server-is-created"></a>创建服务器后创建防火墙规则

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要在其上添加防火墙规则的 Azure Database for MySQL 灵活服务器。
2. 在 "灵活服务器" 页上的 " **设置** " 标题下，单击 " **网络** " 以打开灵活服务器的网络页面。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security&quot;:::-->

3. 单击 &quot;防火墙规则&quot; 中的 " **添加当前客户端 IP 地址** "。 该操作会自动创建一条防火墙规则，其中包含计算机的公共 IP 地址（由 Azure 系统标识）。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Connection Security&quot;:::-->

3. 单击 &quot;防火墙规则&quot; 中的 " 以使规则按预期方式工作。

   可以使用搜索引擎或其他联机工具查看自己的 IP 地址。 例如，搜索“我的 IP 是多少”。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Azure portal - click Connection Security&quot;:::-->

3. 单击 &quot;防火墙规则&quot; 中的 " 字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序便可以访问 MySQL 服务器上他们拥有有效凭据的任何数据库。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - click Connection Security&quot;:::-->

3. 单击 &quot;防火墙规则&quot; 中的 " 以保存此防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Connection Security&quot;:::-->

3. 单击 &quot;防火墙规则&quot; 中的 ":::-->

## <a name="connect-from-azure"></a>从 Azure 连接

你可能想要使 Azure 中部署的资源或应用程序能够连接到你的灵活服务器。 这包括 Azure App Service 中托管的 web 应用程序、在 Azure VM 上运行的 web 应用程序、Azure 数据工厂数据管理网关等。

当 Azure 中的应用程序尝试连接到你的服务器时，防火墙将验证是否允许 Azure 连接。 若要启用此设置，可在门户中从 "**网络**" 选项卡上选择 "**允许从 Azure 中的 azure 服务和资源访问此服务器**" 选项，然后单击 "**保存**"。

资源不需要位于同一虚拟网络 (VNet) 或防火墙规则的资源组中，即可启用这些连接。 如果不允许该连接尝试，则该请求不会到达 Azure Database for MySQL 的灵活服务器。

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。
>
> 建议选择 **专用访问 (VNet 集成) ** 以安全访问灵活的服务器。
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有防火墙规则

重复以下步骤来管理防火墙规则。

- 要添加当前计算机，请在防火墙规则中单击 "+ **添加当前客户端 IP 地址** "。 单击“保存”以保存更改。
- 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击“保存”以保存更改。
- 若要修改现有规则，单击规则中的任意字段并修改。 单击“保存”以保存更改。
- 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则。 单击“保存”以保存更改。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活的服务器防火墙规则](./concepts-networking.md#public-access-allowed-ip-addresses)
- [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./how-to-manage-firewall-cli.md)。