---
title: 为 Azure 虚拟机上的微聚焦企业开发人员4.0 设置微焦点 CICS BankDemo
description: 在 Azure 虚拟机（Vm）上运行微聚焦 BankDemo 应用程序，了解如何使用微聚焦企业服务器和企业开发人员。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411073"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>为 Azure 上的微聚焦企业开发人员4.0 设置微焦点 CICS BankDemo

在 Azure 上设置微焦点企业服务器4.0 和企业开发人员4.0 时，可以测试 IBM z/OS 工作负荷的部署。 本文介绍如何设置 CICS BankDemo，这是一个随企业开发人员提供的示例应用程序。

CICs 代表客户信息控制系统，这是许多在线大型机应用程序使用的事务平台。 BankDemo 应用程序非常适合用于了解企业服务器和企业开发人员的工作方式，以及如何管理和部署使用绿色屏幕终端完成的实际应用程序。

> [!NOTE]
> 即将推出：有关在 Azure Vm 上设置[微中心企业服务器 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110)的说明。

## <a name="prerequisites"></a>必备条件

- 包含[企业开发人员](set-up-micro-focus-azure.md)的 VM。 请记住，企业开发人员提供了一个完整的企业服务器实例，以用于开发和测试目的。 此实例是用于演示的企业服务器的实例。

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。 下载并将其安装在企业开发人员 VM 上。 企业服务器要求使用数据库来管理 CICS 区域，BankDemo 应用程序还使用名为 BANKDEMO 的 SQL Server 数据库。 此演示假设您对这两个数据库都使用 SQL Server Express。 安装时，请选择 "基本" 安装。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) （SSMS）。 SSMS 用于管理数据库和运行 T-sql 脚本。 下载并将其安装在企业开发人员 VM 上。

- [Visual studio 2019](https://azure.microsoft.com/downloads/) ，其中包含最新的 Service Pack 或[visual studio 社区](https://visualstudio.microsoft.com/vs/community/)，可以免费下载。

- Rumba Desktop 或其他3270仿真器。

## <a name="configure-the-windows-environment"></a>配置 Windows 环境

在虚拟机上安装企业开发人员4.0 后，必须配置该服务器附带的企业服务器实例。 为此，需要安装一些其他 Windows 功能，如下所示。

1. 使用 RDP 登录到所创建的企业服务器 4.0 VM。

2. 单击 "**开始**" 按钮旁的 "**搜索**" 图标，然后键入 " **Windows 功能**"。 此时将打开服务器管理器添加角色和功能向导 "。

3. 选择 " **Web 服务器（IIS）角色**"，然后检查以下选项：

    - Web 管理工具
    - IIS 6 管理兼容性（选择所有可用功能）
    - IIS 管理控制台
    - IIS 管理脚本和工具
    - IIS 管理服务

4. 选择 "**万维网服务**"，然后检查以下选项：

     应用程序开发功能：
    - .NET 可扩展性
    - ASP.NET
    - 常见 HTTP 功能：添加所有可用功能
    - 运行状况和诊断：添加所有可用功能
    - 安全性：
        - 基本身份验证
        - Windows 身份验证

5. 选择**Windows 进程激活服务**及其所有子项。

6. 对于**功能**，请检查**Microsoft .NET framework 3.5.1**，并检查以下选项：

    - Windows Communication Foundation HTTP 激活
    - Windows Communication Foundation 非 HTTP 激活

7. 对于**功能**，请检查**Microsoft .NET framework 4.6**，并检查以下选项：

   - 命名管道激活
   - TCP 激活
   - TCP 端口共享

     ![添加角色和功能向导：角色服务](media/01-demo-roles.png)

8. 选择所有选项后，单击 "**下一步**" 以安装。

9. 在 Windows 功能后，请参阅 **"控制面板\> " "系统\>和安全" "管理工具**"，然后选择 "**服务**"。 向下滚动并确保以下服务正在运行，并设置为 "**自动**"：

    - **NetTcpPortSharing**
    - **Net.Pipe 侦听器适配器**
    - **Net.tcp 侦听器适配器**

10. 若要配置 IIS 和 WAS 支持，请从菜单中找到 "**微焦点企业开发人员命令提示（64位）** " 并以**管理员身份**运行。

11. 键入**wassetup – i** ，然后按**enter**。

12. 脚本运行后，可以关闭窗口。

## <a name="configure-the-local-system-account-for-sql-server"></a>为 SQL Server 配置本地系统帐户

某些企业服务器进程需要能够 SQL Server 登录，并创建数据库和其他对象。 这些进程使用本地系统帐户，因此你必须为该帐户授予 sysadmin 权限。

1. 启动**SSMS** ，然后单击 "**连接**" 以使用 Windows 身份验证连接到本地 SQLEXPRESS 服务器。 它应该在 "**服务器名称**" 列表中可用。

2. 在左侧展开 "**安全**" 文件夹，然后选择 "**登录名**"。

3. 选择 " **NT\\机构系统**" 并选择 "**属性**"。

4. 选择 "**服务器角色**" 和 "检查**sysadmin**"。

     ![SSMS 对象资源管理器窗口：登录属性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>创建 BankDemo 数据库及其所有对象

1. 打开**Windows 资源管理器**，导航到**\\C\\：\\Users\\公共文档\\微聚焦\\企业\\开发\\人员\\示例\\大型机\\CICS DotNet BankDemo SQL**。

2. 将**BankDemoCreateAll**文件的内容复制到剪贴板。

3. 打开**SSMS**。 在右侧，单击 "**服务器**"，然后选择 "**新建查询**"。

4. 将剪贴板的内容粘贴到新的**查询**框中。

5. 通过在查询上方的 "**命令**" 选项卡中单击 "**执行**" 来执行 SQL。

查询应运行且没有错误。 完成后，你将获得 BankDemo 应用程序的示例数据库。

![Sqlquery1.sql 输出](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>验证是否已创建数据库表和对象

1. 右键单击**BANKDEMO**数据库，然后选择 "**刷新**"。

2. 展开**数据库**并选择 "**表**"。 应会看到如下所示的内容。

     ![BANKDEMO 表已展开对象资源管理器](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>在企业开发人员中构建应用程序

1. 打开 Visual Studio 并登录。

2. 在 "**文件**" 菜单选项下，选择 "**打开项目/解决方案**"，导航到**C：\\用户\\\\\\\\\\\\\\\\公共文档\\微聚焦企业开发人员示例大型机 CICS DotNet BankDemo**，然后选择**sln**文件。

3. 需要一些时间来检查对象。 COBOL 程序与 CBL 扩展一起显示在解决方案资源管理器中，同时包含 CopyBooks （CPY）和 JCL。

4. 右键单击 " **BankDemo2** " 项目，然后选择 "**设为启动项目**"。

    > [!NOTE]
    > BankDemo 项目利用了 HCOSS （SQL Server 的主机兼容性选项），该功能不适用于此演示。

5. 在**解决方案资源管理器**中，右键单击**BankDemo2**项目，然后选择 "**生成**"。

    > [!NOTE]
    > 如果未配置 HCOSS，则在解决方案级别生成会导致错误。

6. 生成项目时，请检查 "**输出**" 窗口。 它应该如下图所示。

     ![显示成功生成的输出窗口](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>将 BankDemo 应用程序部署到区域数据库

1. 以管理员身份打开企业开发人员命令提示符（64位）。

2. 导航到**\\% PUBLIC% Documents\\微聚焦\\企业开发人员\\示例\\大型机\\CICS\\DotNet\\BankDemo**。

3. 在命令提示符下，执行**bankdemodbdeploy**并包括要部署到的数据库的参数，例如：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 请确保使用正斜杠（/）而不是反斜杠（\\）。 此脚本将运行一段时间。

![管理：企业开发人员命令提示窗口](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在适用于 .NET 的企业管理员中创建 BankDemo 区域

1. 打开 "**用于 .Net 管理的企业服务器**" UI。

2. 若要启动 MMC 管理单元，请从 Windows "**开始**" 菜单中选择 "**微企业\>开发\>人员配置适用于 .net 管理员的企业服务器**"。（对于 Windows Server，请选择 "**适用于\> .Net 管理员的微企业开发人员企业服务器**"）。

3. 展开左窗格中的 "**区域**" 容器，然后右键单击 " **CICS**"。

4. 选择 "**定义区域**"，以在（本地）数据库中创建名为**BANKDEMO**的新 CICS 区域。

5. 提供数据库服务器实例，单击 "**下一步**"，然后输入区域名称**BANKDEMO**。

     !["定义区域" 对话框](media/07-demo-cics.png)

6. 若要选择跨区域数据库的区域定义文件，请在**C\\：\\用户公共\\文档\\微聚焦\\企业开发人员\\示例\\大型机\\CICS\\DotNet\\bankdemo**中找到**区域\_bankdemo\_** 。

     ![定义区域区域名称： BANKDEMO](media/08-demo-cics.png)

7. 单击 **“完成”** 。

## <a name="create-xa-resource-definitions"></a>创建 XA 资源定义

1. 在**适用于 .Net 管理的企业服务器**UI 的左窗格中，依次展开 "**系统**" 和 " **XA 资源定义**"。 此设置定义区域与企业服务器和应用程序数据库的互操作方式。

2. 右键单击 " **XA 资源定义**"，然后选择 "**添加服务器实例**"。

3. 在下拉框中，选择 "**数据库服务实例**"。 它将成为本地计算机 SQLEXPRESS。

4. 从 " **XA 资源定义"\\（machinename sqlexpress）** 容器下选择该实例，然后单击 "**添加**"。

5. 选择 "**数据库 XA 资源定义**"，然后键入 " **BANKDEMO** " 作为 "**名称**和**区域**"。

     ![新数据库 XA 资源定义屏幕](media/09-demo-xa.png)

6. 单击省略号（**...**）以打开 "连接字符串向导"。 对于 "**服务器名称**"，请键入 **（\\local） SQLEXPRESS**。 对于**登录**，请选择 " **Windows 身份验证**"。 对于 "数据库名称"，请键入**BANKDEMO**

     ![编辑连接字符串屏幕](media/10-demo-string.png)

7. 测试连接。

## <a name="start-the-bankdemo-region"></a>启动 BANKDEMO 区域

> [!NOTE]
> 第一步非常重要：必须将区域设置为使用刚创建的 XA 资源定义。

1. 导航到 "**区域" 容器**下的 " **BANDEMO CICS" 区域**，然后从 "**操作**" 窗格中选择 "**编辑区域启动文件**"。 向下滚动到 "SQL 属性"，为**XA 资源名称**输入**bankdemo** ，或使用省略号将其选中。

2. 单击 "**保存**" 图标以保存所做的更改。

3. 右键单击**控制台**窗格中的 " **BANKDEMO CICS 区域**"，然后选择 "**启动/停止区域**"。

4. 在出现在中间窗格中的 "**启动/停止区域**" 框的底部，选择 "**启动**"。 几秒钟后，区域开始。

     ![SQL 启动/停止框](media/11-demo-sql.png)

     ![CICS 区域 BANKDEMO-已启动屏幕](media/12-demo-cics.png)

## <a name="create-a-listener"></a>创建侦听器

为访问 BankDemo 应用程序的 TN3270 会话创建侦听器。

1. 在左侧窗格中，展开 "**配置编辑器**"，然后选择 "**侦听器**"。

2. 单击 "**打开文件**" 图标，然后选择 " **seelistener** " 文件。 此文件将被编辑，并在企业服务器每次启动时加载。

3. 请注意前面定义的两个区域（ESDEMO 和 JCLDEMO）。

4. 若要为 BANKDEMO 创建新区域，请右键单击 "**区域**"，然后选择 "**添加区域**"。

5. 选择 " **BANKDEMO 区域**"。

6. 右键单击 " **BANKDEMO" 区域**，然后选择 "**添加频道**"，添加 TN3270 通道。

7. 对于 "**名称**"，请输入**TN3270**。 对于 "**端口**"，请输入**9024**。 ESDEMO 应用程序使用端口9230，因此你需要使用其他端口。

8. 若要保存文件，请单击 "**保存**" 图标或选择 "**文件** \> " "**保存**"。

9. 若要启动侦听器，请单击 "**启动侦听器**" 图标或选择 "**选项** \> " "**启动侦听器**"。

     ![侦听器配置编辑器窗口](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>配置 Rumba 以访问 BankDemo 应用程序

你需要做的最后一件事是使用 Rumba （3270模拟器）配置3270会话。 此步骤使你可以通过创建的侦听器访问 BankDemo 应用程序。

1. 在 Windows 的 "**开始**" 菜单中，启动 Rumba Desktop。

2. 在 "**连接**" 菜单项下，选择 " **TN3270**"。

3. 对于 "IP 地址"，请单击 "**插入**" 并键入**127.0.0.1** ，并为用户定义的端口键入**9024** 。

4. 在对话框底部，单击 "**连接**"。 此时会显示一个黑色的 CICS 屏幕。

5. 键入**bank**显示 BankDemo 应用程序的初始3270屏幕。

6. 对于 "用户 ID"，键入**B0001** ，为密码键入 "任何内容"。 第一个屏幕 BANK20 将打开。

![大型机显示欢迎屏幕](media/14-demo.png)
![大型机显示-Rumba-子系统演示屏幕](media/15-demo.png)

祝贺你！ 现在，使用微焦点企业服务器在 Azure 中运行 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

- [在 Azure 上的 Docker 容器中运行企业服务器](run-enterprise-server-container.md)
- [大型机迁移-门户](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭密大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
