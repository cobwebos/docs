---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728527"
---
## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>创建空的 SQL 数据库

定义好的一组[计算和存储资源](../articles/sql-database/sql-database-service-tiers-dtu.md)中有一个 Azure SQL 数据库。 该数据库在 [Azure 资源组](../articles/azure-resource-manager/resource-group-overview.md)和 [Azure SQL 数据库逻辑服务器](../articles/sql-database/sql-database-features.md)中运行。

按照以下步骤创建空的 SQL 数据库。

1. 在 Azure 门户的左上角单击“创建资源”。

1. 在“新建”页上，选择“数据库” > “SQL 数据库”。

   ![创建空数据库](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. 在“SQL 数据库”窗格中，键入或选择以下值：

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **数据库名称** | yourDatabase | 如需有效的数据库名称，请参阅[数据库标识符](/sql/relational-databases/databases/database-identifiers)。 |
   | **订阅** | yourSubscription  | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | yourResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | **选择源** | 空白数据库 | 指定创建空白数据库。 |

   ![创建数据库](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. 选择“服务器”，为新数据库配置一个服务器。 然后键入或选择以下值：

      | 设置       | 建议的值 | 说明 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **服务器名称** | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
      | 服务器管理员登录名 | 任何有效的名称 | 如需有效的登录名，请参阅[Database Identifiers](/sql/relational-databases/databases/database-identifiers)（数据库标识符）。|
      | **密码** | 任何有效的密码 | 密码必须至少有 8 个字符，且必须包含以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
      | **位置** | 任何有效的位置 | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |

      选择“选择”。

      ![创建数据库 - 服务器](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. 选择“定价层”，指定服务层、DTU 数和存储量。 浏览适用于每个服务层的 DTU 数和存储量的选项。

      选择服务器层、DTU 数和存储量之后，选择“应用”。

   1. 输入空白数据库的“排序规则”（就本教程来说，请使用默认值）。 有关排序规则的详细信息，请参阅[排序规则](/sql/t-sql/statements/collations)。

1. 填写“SQL 数据库”表单后，选择“创建”以创建数据库。 此步骤最长需要一分半钟才能完成。

1. 在工具栏上，单击“通知”可监视部署过程。

     ![通知](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>创建防火墙规则

SQL 数据库服务在服务器级别创建一个防火墙，用于阻止外部应用程序和工具连接到服务器或服务器上的任何数据库。 按照以下步骤为客户端的 IP 地址创建 [SQL 数据库服务器级防火墙规则](../articles/sql-database/sql-database-firewall-configure.md)。 完成此过程后，只能通过 IP 地址的 SQL 数据库防火墙建立外部连接。

> [!NOTE]
> SQL 数据库通过端口 1433 通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非管理员打开端口 1433。

1. 部署完成后，在左侧菜单中选择“SQL 数据库”，然后在“SQL 数据库”页上选择“yourDatabase”。 此时会打开数据库的“概述”页，其中显示了完全限定的服务器名称（例如 *yourserver.database.windows.net*），并提供了其他配置的选项。

1. 在后续步骤中，需复制此完全限定的服务器名称，并使用它连接到服务器及其数据库。

   ![服务器名称](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. 在工具栏上选择“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。

   ![服务器防火墙规则](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. 在工具栏上选择“添加客户端 IP”，将当前的 IP 地址添加到新的防火墙规则。 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

   1. 选择“保存”。 此时会针对当前的 IP 地址创建服务器级防火墙规则，在逻辑服务器上打开 端口 1433。

   1. 选择“确定”，然后关闭“防火墙设置”页。

现在，你的 IP 地址可以通过防火墙，并可使用 SSMS 或其他所选工具连接到 SQL 数据库服务器及其数据库。 请务必使用前面创建的服务器管理员帐户。

> [!IMPORTANT]
> 默认情况下，所有 Azure 服务都允许通过 SQL 数据库防火墙进行访问。 在此页上选择“关”可禁止所有 Azure 服务进行访问。
