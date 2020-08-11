---
title: 设置实验室以使用 Azure SQL 数据库进行管理和开发 |Azure 实验室服务
description: 了解如何设置实验室以使用 Azure SQL 数据库进行管理和开发。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9fc0a965869207ba8d1b4eb6f45e878ae4b93c3a
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079016"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>设置实验室来管理和开发 SQL Server

本文介绍如何为 Azure 实验室服务中的基本 SQL Server 管理和开发类设置实验室。  数据库概念是在大学的大多数计算机科学部门中讲授的一个介绍性课程。 结构化查询语言 (SQL) 是一个国际标准。  SQL 是一种用于关系数据库管理的标准语言，包括在数据库中添加、访问和管理内容。  最值得注意的是，其快速处理、可靠的可靠性、易用性和灵活性。

本文介绍如何使用[Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)和[Azure Data Studio](https://github.com/microsoft/azuredatastudio)在实验室中设置虚拟机模板。  对于此实验，我们将为整个实验室使用一个共享的[SQL Server 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。 [AZURE SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)是平台即服务 (PaaS) 从 Azure 数据库引擎提供服务。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)。 你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 有关如何启用 marketplace 映像的详细信息，请参阅[指定可用于实验室创建者的 marketplace 映像](specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
| 市场映像 | 启用 "Visual Studio 2019 社区 (Windows 10 企业版 N 上的最新版本)  (x64) " 映像，以便在实验室帐户中使用。 |

### <a name="shared-resource-configuration"></a>共享资源配置

若要在实验室服务中使用共享资源，首先需要创建虚拟网络和资源本身。  若要创建虚拟网络并将其连接到实验室，请遵循[如何在 Azure 实验室服务中使用共享资源创建实验室](how-to-create-a-lab-with-shared-resource.md)。  请记住，实验室服务外部的任何资源将单独计费，且不会包含在实验室成本预估中。

>[!WARNING]
>在创建实验室之前，应设置实验室的共享资源。  如果在创建实验室*之前*未将 vnet[对等互连到实验室帐户](how-to-connect-peer-virtual-network.md)，实验室将无法访问共享资源。

既然处理了东西的网络端，就可以创建一个 SQL Server 数据库。  我们将创建一个[数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)，因为它是 Azure SQL 数据库的最快速部署选项。  对于其他部署选项，请创建[弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)、[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)或[SQL 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal)。

1. 从 "Azure 门户" 菜单中，选择 "**创建新资源**"。
2. 选择 " **SQL 数据库**"，并单击 "**创建**" 按钮。
3. 在 "**创建 SQL 数据库**" 窗体的 "**基本**信息" 选项卡上，选择数据库的资源组。  我们将使用*sqldb*。
4. 对于 "**数据库名称**"，请输入*classlabdb*。
5. 在**服务器**设置下，单击 "**新建**" 以创建新服务器来保存数据库。
6. 在 "**新服务器**" 弹出窗口中，输入服务器名称。  我们将使用*classlabdbserver*。  服务器名称必须全局唯一。
7. 输入*azureuser*作为**服务器管理员登录名**。
8. 输入一个便于记忆的密码。  密码长度必须至少为8个字符，并且必须包含特殊字符。
9. 选择 "区域" 作为 "**位置**"。  如果可能，请输入与实验室帐户相同的位置，并对等互连 vnet 以最大程度地减少延迟。
10. 单击 **"确定"** 以返回到 "**创建 SQL 数据库**" 窗体。
11. 单击 "**计算 + 存储**" 设置下的 "**配置数据库**" 链接。
12. 根据需要为类修改数据库设置。  可以在 "预配" 和 "无服务器" 选项之间进行选择。  在此示例中，我们将使用自动缩放无服务器选项，其中最大 Vcore 为4，最小 Vcore 为1。 我们会将 autopause 设置保持为至少1小时。 单击“应用”。
13. 单击 "**下一步：网络**" 按钮。
14. 在 "网络" 选项卡上，为**连接方法**选择 "专用终结点"。
15. 在 "**专用终结点**" 部分中，单击 "**添加专用终结点**"。
16. 在 "**创建专用终结点**" 弹出窗口中，选择与虚拟网络对等互连相同的资源组。
17. 对于 "**位置**"，请选择与虚拟网络相同的位置。
18. 对于 "**名称**"，请输入*labsql-endpt*。
19. 将目标 subresource 设置为 SqlServer。
20. 对于 "**虚拟网络**"，请选择与实验室帐户相同的虚拟网络对等互连。
21. 对于 "**子网**"，请选择要在其中托管终结点的子网。  分配给终结点的 IP 将来自分配给该子网的范围。
22. 将**与专用 DNS 的集成**设置为 "**否**"。 为简单起见，我们将通过自己的专用 DNS 区域或我们自己的 DNS 服务器使用 Azure 的 DNS。
23. 单击“确定”。
24. 单击“下一步:  其他设置”。
25. 对于 "**使用现有数据**" 设置，请选择 "**示例**"。  创建数据库时，将使用 AdventureWorksLT 数据库中的数据。
26. 单击“查看 + 创建”。
27. 单击“创建”。

SQL 数据库部署成功完成后，可以在实验室模板计算机上创建实验室并安装软件。

### <a name="lab-settings"></a>实验室设置

设置教室实验室时，请使用下表中的设置。 有关如何创建教室实验室的详细信息，请参阅[设置课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
| 虚拟机大小 | 中。 此大小最适用于关系数据库、内存中缓存和分析。 |
| 虚拟机映像 | Visual Studio 2019 社区 (Windows 10 企业版 N (x64 上的最新版本) )  |

创建实验室后，让我们用所需的软件修改模板机。

## <a name="visual-studio"></a>Visual Studio

上面选择的映像包括[Visual Studio 2019 社区](https://visualstudio.microsoft.com/vs/community/)。  映像上已安装所有工作负荷和工具集。  使用 "Visual Studio 安装程序安装所需的[任何可选工具](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)。  [登录到 Visual Studio](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio)以解锁社区版。

Visual Studio 包括**数据存储和处理**工具集，其中包括 SQL SERVER DATA TOOLS (SSDT) 。  有关 SSDT 功能的详细信息，请参阅[SQL Server Data Tools 概述](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)。  若要验证与类的共享 SQL Server 的连接是否成功，请参阅[连接到数据库并浏览现有对象](https://docs.microsoft.com/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)。 如果系统提示，请将模板计算机 IP 添加到可连接到 SQL Server 实例的[允许计算机的列表](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)中。

Visual Studio 支持多种工作负荷，包括**Web & 云和****桌面 & 移动**工作负荷。  这两个工作负荷都支持 SQL Server 作为数据源。 有关使用 ASP.NET Core SQL Server 的详细信息，请参阅[Azure App Service 教程中的生成 ASP.NET Core 和 SQL 数据库应用](https://docs.microsoft.com/azure/app-service/tutorial-dotnetcore-sqldb-app)。  使用[SqlClient](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)库从[Xamarin](https://docs.microsoft.com/xamarin)应用连接到 SQL 数据库。

## <a name="install-azure-data-studio"></a>安装 Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio)是一种跨平台的多数据库桌面环境，适用于在 Windows、MacOS 和 Linux 上使用本地和云数据平台系列的数据专业人员。

1. 下载[适用于 Windows 的 Azure Data Studio*系统*安装程序](https://go.microsoft.com/fwlink/?linkid=2127432)。 若要查找其他受支持的操作系统的安装程序，请切换到[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)下载 "页。
2. 在 "**许可协议**" 页上，选择 "**我接受协议"**。 单击“下一步”。
3. 在“选择目标位置”页上，单击“下一步”。
4. 在“选择开始菜单文件夹”页上，单击“下一步”。
5. 如果需要桌面图标，请在 "**选择其他任务**" 页上，选中 "**创建桌面图标**"。  单击“下一步”。
6. 在 "**准备安装**" 上，单击 "**下一步**"。
7. 等待安装程序运行。  单击“完成”。

现在我们已安装 Azure Data Studio，接下来设置到 Azure SQL 数据库的连接。

1. 在 Azure Data Studio 的 "**欢迎**" 页上，单击 "**新建连接**" 链接。
2. 在 "**连接详细信息**" 框中，填写所需的信息。
    - 将**服务器**设置为*classlabdbserver.database.windows.net*
    - 将**用户名**设置为*azureuser*
    - 将 "**密码**" 设置为用于创建数据库的密码。
    - 选中 "**记住密码**"。
    - 对于 "**数据库**"，请选择*classlabdb*。
3. 单击“连接” 。

## <a name="install-sql-server-management-studio"></a>安装 SQL Server Management Studio

[SQL Server Management Studio (SSMS) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)是用于管理任何 SQL 基础结构的集成环境。  SSMS 是数据库管理员使用的工具，用于部署、监视和升级数据基础结构。

1. [下载 Sql Server Management Studio](https://aka.ms/ssmsfullsetup)。 下载完成后，启动安装程序。
2. 在**欢迎**页上，单击 "**安装**"。
3. 在 "**安装已完成**" 页上，单击 "**关闭**"。
4. 启动 Sql Server Management Studio。  
5. 在 "**依赖关系配置过程**" 页上，单击 "**关闭**"。

未安装 SSMS，你可以[连接和查询 SQL Server](https://docs.microsoft.com/sql/ssms/tutorials/connect-query-sql-server)。 设置连接时，请使用以下值：

- 服务器类型：数据库引擎
- 服务器名称： *classlabdbserver.database.windows.net*
- 身份验证：SQL Server 身份验证
- 登录名： *azureuser*
- Password：用于创建数据库的密码。

## <a name="cost-estimate"></a>成本估算

我们来介绍此类的可能的成本估算。 估计不包括运行 SQL Server 的成本。  有关数据库定价的当前详细信息，请参阅[SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database)。

我们将使用一类25名学生。 计划的类时间有20小时。 此外，每个学生在计划的类时间之外为家庭作业或分配获取10小时配额。 选择的虚拟机大小为 "中"，即 "42 实验室单位"。

下面是此类的可能的成本估算示例：

25名学生 \* (20 个计划小时 \+ 10 个配额小时) * 0.42 美元/小时 = 315.00 美元

>[!IMPORTANT]
>成本估算仅用于示例目的。 有关定价的最新详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建、管理和发布模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
