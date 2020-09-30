---
title: 快速入门：创建 Azure DB for MySQL 灵活服务器 - Azure 门户
description: 本文逐步介绍如何使用 Azure 门户在几分钟内快速创建 Azure Database for MySQL 灵活服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569634"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>快速入门：使用 Azure 门户创建 Azure Database for MySQL 灵活服务器

Azure Database for MySQL 灵活服务器是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 MySQL 服务器。 本快速入门介绍如何使用 Azure 门户在几分钟内创建灵活服务器。

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
打开 Web 浏览器，然后访问 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>创建 Azure Database for MySQL 灵活服务器

使用一组定义的[计算和存储资源](./concepts-compute-storage.md)创建灵活服务器。 请在 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)中创建该服务器。

按照这些步骤创建灵活服务器：

1. 在门户左上角选择“创建资源”  (+)。

2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Azure Database for MySQL 选项":::

3. 选择“灵活服务器”作为部署选项。
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Azure Database for MySQL 选项":::    

4. 填写“基本”表单，其中包含以下信息： 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Azure Database for MySQL 选项"::: 
                                    
    |**设置**|建议的值|**说明**|
    |---|---|---|
    订阅|订阅名称|要用于服务器的 Azure 订阅。 如果你有多个订阅，请选择要计费的资源所在的订阅。|
    资源组|myresourcegroup| 新的资源组名称，或订阅中的现有资源组。|
    服务器名称 |*mydemoserver*|标识你的灵活服务器的唯一名称。 域名 mysql.database.azure.com 附加到提供的服务器名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 该名称必须至少包含 3 到 63 个字符。|
    管理员用户名 |mydemouser| 连接到服务器时使用的自己的登录帐户。 管理员登录名不能是“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”     。|
    密码 |你的密码| 服务器管理员帐户的新密码。 该密码必须包含 8 到 128 个字符。 密码必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字 (0 到 9)和非字母数字字符（!, $, #, % 等）。|
    区域|离用户最近的区域| 最靠近用户的位置。|
    版本|5.7| MySQL 主要版本。|
    计算 + 存储 | “可突增”、“Standard_B1ms”、“10 GiB”和“7 天”    | 新服务器的计算、存储和备份配置。 选择“配置服务器”。 “可突增”、“Standard_B1ms”、“10 GiB”和“7 天”分别是“计算层”、“计算大小”、“存储”和“备份保持期”的默认值      。 可以按原样保留这些滑块，也可以对其进行调整。 若要保存此计算和存储选择，请选择“保存”继续进行配置。 下面的屏幕截图显示了计算和存储选项。|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Azure Database for MySQL 选项":::

5. 配置网络选项

    在“网络”选项卡上，可以选择访问服务器的方式。 Azure Database for MySQL 灵活服务器提供了两个用于连接到服务器的选项：通过“公共访问(允许的 IP 地址)”和“专用访问(VNet 集成)” 。 使用“公共访问(允许的 IP 地址)”，对服务器的访问仅限于已添加到防火墙规则中的允许的 IP 地址。 除非创建了规则以打开特定 IP 地址或范围的防火墙，否则此防火墙会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。 使用“专用访问(VNet 集成)”，对服务器的访问仅限于虚拟网络。 本快速入门介绍如何启用公共访问以连接到服务器。 若要详细了解连接方法，请参阅[概念文章](./concepts-networking.md)。

    > [!NOTE]
    > 创建服务器后，无法更改连接方法。 例如，如果在创建过程中选择了“公共访问(允许的 IP 地址)”，则在创建后无法将其更改为“专用访问(VNet 集成)” 。 强烈建议创建采用专用访问的服务器，以使用 VNet 集成安全地访问你的服务器。 若要详细了解专用访问，请参阅[概念文章](./concepts-networking.md)。

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Azure Database for MySQL 选项":::  

6. 选择“查看 + 创建”，查看你的灵活服务器配置。

7. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

8. 在工具栏上选择“通知”（钟形图标）以监视部署过程。 完成部署后，可以选择“固定到仪表板”，以便在 Azure 门户仪表板上为此灵活服务器创建磁贴作为此服务器“概述”页的快捷方式。 选择“转到资源”可打开此服务器的“概述”页。

默认情况下，将在服务器下创建以下数据库：**information_schema**、**mysql**、**performance_schema** 和 **sys**。

> [!NOTE]
> 检查网络是否允许通过端口 3306 送出出站流量，该端口由 Azure Database for MySQL 灵活服务器使用，旨在避免连接问题。  

## <a name="connect-to-using-mysql-command-line-client"></a>使用 mysql 命令行客户端进行连接

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

可以选择 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md)，以从本地环境连接到服务器。 

使用 mysql.exe，通过以下命令进行连接。 将值替换为实际的服务器名称和密码。 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>清理资源
现在已成功在资源组中创建了 Azure Database for MySQL 灵活服务器。  如果将来不再需要这些资源，可以通过删除资源组或只删除 MySQL 服务器来删除它们。 若要删除资源组，请执行以下步骤：

1. 在 Azure 门户中，搜索并选择“资源组”。
1. 在资源组列表中，选择你的资源组的名称。
1. 在资源组的概述页面中，选择“删除资源组”。
1. 在确认对话框中，键入资源组的名称，然后选择“删除”。

若要删除服务器，可以单击服务器的“概述”页面上的“删除”按钮，如下所示 ：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Azure Database for MySQL 选项":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 MySQL 生成 PHP (Laravel) Web 应用](tutorial-php-database-app.md)
