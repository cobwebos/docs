---
title: 设计第一个关系数据库 C#
description: 了解如何使用 ADO.NET 通过 C# 在 Azure SQL 数据库的单一数据库中设计第一个关系数据库。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 0f1140bbefc7508666e763fcd4f1a04ba48cdfdd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354942"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>教程：在 Azure SQL 数据库 C&#x23; 和 ADO.NET 的单一数据库中设计关系数据库

Azure SQL 数据库是 Microsoft 云 (Azure) 中的关系型数据库即服务 (DBaaS)。 本教程介绍如何将 Azure 门户、ADO.NET 与 Visual Studio 结合使用来完成以下操作：

> [!div class="checklist"]
> * 使用 Azure 门户创建单一数据库*
> * 通过 Azure 门户设置服务器级 IP 防火墙规则
> * 使用 ADO.NET 和 Visual Studi 连接至数据库
> * 使用 ADO.NET 创建表
> * 使用 ADO.NET 插入、更新和删除数据
> * 查询数据 ADO.NET

*如果还没有 Azure 订阅，请在开始前[创建免费帐户](https://azure.microsoft.com/free/)。

> [!TIP]
> 以下 Microsoft Learn 模块可帮助你免费学习如何[开发和配置可查询 Azure SQL 数据库的 ASP.NET 应用程序](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)，包括创建简单数据库。

## <a name="prerequisites"></a>必备条件

安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/) 或更高版本。

## <a name="create-a-blank-single-database"></a>创建空的单一数据库

创建 Azure SQL 数据库中的单一数据库时，会使用定义好的一组计算和存储资源。 数据库在 [Azure 资源组](../azure-resource-manager/management/overview.md)中创建，使用[数据库服务器](sql-database-servers.md)进行托管。

遵循以下步骤创建空白的单一数据库。

1. 在 Azure 门户的左上角单击“创建资源”。 
2. 在“新建”  页上的“Azure 市场”部分中选择“数据库”  ，然后在“特别推荐”部分中单击“SQL 数据库”   。

   ![创建空数据库](./media/sql-database-design-first-database/create-empty-database.png)

3. 如上图所示，在“SQL 数据库”表单中填写以下信息  ：

    | 设置       | 建议的值 | 说明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **数据库名称** | yourDatabase  | 如需有效的数据库名称，请参阅[数据库标识符](/sql/relational-databases/databases/database-identifiers)。 |
    | **订阅** | yourSubscription   | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
    | **资源组** | yourResourceGroup  | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |
    | **选择源** | 空白数据库 | 指定创建空白数据库。 |

4. 单击“服务器”以使用现有的数据库服务器，或者创建并配置新的数据库服务器  。 选择现有服务器或单击“创建新服务器”，然后在“新建服务器”窗体中填写以下信息   ：

    | 设置       | 建议的值 | 说明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **服务器名称** | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |
    | 服务器管理员登录名  | 任何有效的名称 | 如需有效的登录名，请参阅[Database Identifiers](/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 |
    | **密码** | 任何有效的密码 | 密码必须至少有八个字符，且必须使用以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
    | **位置** | 任何有效的位置 | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |

    ![创建数据库 - 服务器](./media/sql-database-design-first-database/create-database-server.png)

5. 单击“选择”  。
6. 单击“定价层”  ，指定服务层级、DTU 或 vCore 数，以及存储量。 可以浏览相关选项，了解每个服务层级可提供的 DTU/vCore 数和存储。

    选择服务层、DTU 数或 vCore 数以及存储量后，然后单击“应用”  。

7. 输入空白数据库的“排序规则”（就本教程来说，请使用默认值）  。 有关排序规则的详细信息，请参阅 [Collations](/sql/t-sql/statements/collations)（排序规则）

8. 填写“SQL 数据库”窗体后，单击“创建”以预配单一数据库   。 这个步骤可能需要几分钟的时间。

9. 在工具栏上，单击“通知”可监视部署过程。 

   ![通知](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>创建服务器级 IP 防火墙规则

SQL 数据库服务在服务器级别创建 IP 防火墙。 此防火墙阻止外部应用程序和工具连接到服务器和服务器上的任何数据库，除非防火墙规则允许其 IP 通过防火墙。 若要启用与单一数据库的外部连接，必须首先为 IP 地址（或 IP 地址范围）添加 IP 防火墙规则。 遵循这些步骤创建 [SQL 数据库服务器级 IP 防火墙规则](sql-database-firewall-configure.md)。

> [!IMPORTANT]
> SQL 数据库服务通过端口 1433 进行通信。 如果尝试从企业网络内部连接到此服务，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到单一数据库，除非管理员打开端口 1433。

1. 部署完成后，在左侧菜单中单击“SQL 数据库”，然后在“SQL 数据库”页上单击“yourDatabase”    。 此时会打开数据库的概览页，其中显示了完全限定的“服务器名称”（例如 yourserver.database.windows.net），并提供了其他配置的选项   。

2. 复制此完全限定的服务器名称，将其用于从 SQL Server Management Studio 连接到服务器和数据库。

   ![服务器名称](./media/sql-database-design-first-database/server-name.png)

3. 单击工具栏上的“设置服务器防火墙”  。 此时会打开 SQL 数据库服务器的“防火墙设置”页。 

   ![服务器级别 IP 防火墙规则](./media/sql-database-design-first-database/server-firewall-rule.png)

4. 在工具栏上单击“添加客户端 IP”，将当前的 IP 地址添加到新的 IP 防火墙规则。  IP 防火墙规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。

5. 单击“ **保存**”。 此时会针对当前的 IP 地址创建服务器级 IP 防火墙规则，在 SQL 数据库服务器上打开端口 1433。

6. 单击“确定”，然后关闭“防火墙设置”页。  

你的 IP 地址现在可以通过 IP 防火墙。 现在可以使用 SQL Server Management Studio 或其他所选工具连接到单一数据库。 确保使用之前创建的服务器管理员帐户。

> [!IMPORTANT]
> 默认情况下，所有 Azure 服务都允许通过 SQL 数据库 IP 防火墙进行访问。  在此页上单击“关”即可对所有 Azure 服务执行禁用操作。

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了基本数据库任务，例如创建数据库和表、连接到数据库、加载数据和运行查询。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建数据库
> * 设置防火墙规则。
> * 使用 [Visual Studio 和 C#](sql-database-connect-query-dotnet-visual-studio.md) 连接至数据库
> * 创建表
> * 插入、更新、删除和查询数据

请转到下一教程，了解数据迁移。

> [!div class="nextstepaction"]
> [使用 DMS 将 SQL Server 脱机迁移到 Azure SQL 数据库](../dms/tutorial-sql-server-to-azure-sql.md)
