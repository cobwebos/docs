---
title: 为 Micro 焦点企业开发人员 4.0 Azure 虚拟机上设置 Micro 焦点 CICS BankDemo
description: 运行 Micro 焦点 BankDemo 应用程序在 Azure 虚拟机 (Vm) 来了解如何使用微焦点企业服务器和企业开发人员。
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: be94cf0367f93f14249239fce5e09c8635a01136
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892477"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>为 Azure 上微焦点企业开发人员 4.0 设置 Micro 焦点 CICS BankDemo

如果设置了 Micro 焦点 Enterprise Server 4.0 和在 Azure 上的企业开发人员 4.0 时，你可以测试 IBM z/OS 的工作负荷的部署。 本文介绍如何设置 CICS BankDemo，附带了企业级开发版的示例应用程序。

CICs 代表客户信息控制系统，事务平台由许多联机大型机应用程序。 BankDemo 应用程序非常适合于学习 Enterprise Server 和企业开发人员工作方式，以及如何管理和部署实际的应用程序完成，但的绿屏终端。

## <a name="prerequisites"></a>必备组件

- 使用的 VM[企业级开发版](set-up-micro-focus-azure.md)。 请记住企业开发人员有一个企业服务器上的完成实例用于开发和测试目的。 这是用于演示企业服务器的实例。

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。 下载并安装在企业开发人员 VM 上。 企业服务器必须具有数据库的 CICS 区域管理和 BankDemo 应用程序还使用名为 BANKDEMO 的 SQL Server 数据库。 此演示假设您为这两个数据库使用 SQL Server Express。 安装时，选择基本安装。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS)。 SSMS 用于管理数据库和运行 T-SQL 脚本。 下载并安装在企业开发人员 VM 上。

- [Visual Studio 2017](https://azure.microsoft.com/downloads/)最新 service pack 或[Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)，可以免费下载。

- Rumba 桌面或其它 3270 仿真程序。

## <a name="configure-the-windows-environment"></a>配置 Windows 环境

在 VM 上安装企业开发人员 4.0 后，必须配置其附带 Enterprise Server 的实例。 若要执行此操作，需要安装几个其他 Windows 功能，如下所示。

1. 使用 RDP 登录到你创建的企业服务器 4.0 虚拟机上。

2. 单击**搜索**旁边的图标**启动**按钮，然后输入**Windows 功能**。 服务器管理器添加角色和功能向导将打开。

3. 选择**Web 服务器 (IIS) 角色**，然后检查以下：

    - Web 管理工具
    - IIS 6 管理兼容性 （选择所有可用的功能）
    - IIS 管理控制台
    - IIS 管理脚本和工具
    - IIS 管理服务

4. 选择**World Wide Web 服务**，并检查以下各项：

     应用程序开发功能：
    - .NET 可扩展性
    - ASP.NET
    - 常见 HTTP 功能：添加所有可用的功能
    - 运行状况和诊断：添加所有可用的功能
    - 安全性：
        - 基本身份验证
        - Windows 身份验证

5. 选择**Windows 进程激活服务**及其所有子级。

6. 有关**功能**，检查**Microsoft.NET framework 3.5.1**，并检查以下各项：

    - Windows Communication Foundation HTTP 激活
    - Windows Communication Foundation 非 HTTP 激活

7. 有关**功能**，检查**Microsoft.NET framework 4.6**，并检查以下各项：

   - 命名的管道激活
   - TCP 激活
   - TCP 端口共享

     ![添加角色和功能向导：角色服务](media/01-demo-roles.png)

8. 当已选择所有选项时，单击**下一步**安装。

9. 之后的 Windows 功能，转到**控制面板\>系统和安全\>管理工具**，然后选择**Services**。 向下滚动，并确保以下服务正在运行，并将设置为**自动**:

    - **NetTcpPortSharing**
    - **Net.Pipe Listener Adapter**
    - **Net.tcp Listener Adapter**

10. 若要配置 IIS 和 WAS 支持，从菜单中找到**Micro 焦点企业开发人员命令提示 （64 位）** 运行**管理员**。

11. 类型**wassetup – i**然后按**Enter**。

12. 运行该脚本后，可以关闭窗口。

## <a name="configure-the-local-system-account-for-sql-server"></a>为 SQL Server 配置的本地系统帐户

一些企业服务器进程需要能够登录到 SQL Server 上，创建数据库和其他对象。 这些进程使用本地系统帐户，因此您必须向该帐户授予 sysadmin 权限。

1. 启动**SSMS**然后单击**Connect**连接到本地 SQLEXPRESS 服务器使用 Windows 身份验证。 它应位于**服务器名称**列表。

2. 在左侧，展开**安全**文件夹，然后选择**登录名**。

3. 选择**NT AUTHORITY\\系统**，然后选择**属性**。

4. 选择**服务器角色**，并检查**sysadmin**。

     ![SSMS 对象资源管理器窗口：登录名属性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>创建 BankDemo 数据库及其所有对象

1. 打开**Windows 资源管理器**并导航到**c:\\用户\\公共\\文档\\微焦点\\企业级开发版\\示例\\大型机\\CICS\\DotNet\\BankDemo\\SQL**。

2. 将复制的内容**BankDemoCreateAll.SQL**文件到剪贴板。

3. 打开**SSMS**。 在右侧，单击**服务器**，然后选择**新查询**。

4. 粘贴剪贴板的内容**新查询**框。

5. 通过单击执行 SQL **Execute**从**命令**上述查询的选项卡。

该查询应运行且未出错。 完成后，必须 BankDemo 应用程序的示例数据库。

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>验证已创建的数据库表和对象

1. 右键单击**BANKDEMO**数据库并选择**刷新**。

2. 展开**数据库**，然后选择**表**。 你应看到如下所示的内容。

     ![在对象资源管理器中展开 BANKDEMO 表](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>生成企业级开发版中的应用程序

1. 打开 Visual Studio 并登录。

2. 下**文件**菜单选项，选择**打开项目/解决方案**，导航到**c:\\用户\\公共\\文档\\Micro焦点\\企业开发人员\\示例\\大型机\\CICS\\DotNet\\BankDemo**，然后选择**sln**文件。

3. 需要一些时间来检查的对象。 COBOL 程序显示在解决方案资源管理器以及 CopyBooks (CPY) 和 JCL CBL 扩展名。

4. 右键单击**BankDemo2**项目，然后选择**设为启动项目**。

    > [!NOTE]
    > BankDemo 项目利用了 HCOSS （主机兼容性选项适用于 SQL Server），不用于此演示。

5. 在中**解决方案资源管理器**，右键单击**BankDemo2**项目，然后选择**生成**。

    > [!NOTE]
    > 在解决方案级的生成导致错误，因为尚未配置 HCOSS。

6. 当生成项目后时，检查**输出**窗口。 它应如下图所示。

     ![输出窗口会显示成功生成](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>部署到区域数据库 BankDemo 应用程序

1. 以管理员身份打开企业开发人员命令提示符下 （64 位）。

2. 导航到 **%公共 %\\文档\\Micro Focus\\企业开发人员\\示例\\大型机\\CICS\\DotNet\\BankDemo**。

3. 在命令提示符处，执行**bankdemodbdeploy**和包含要部署到，例如的数据库的参数：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 请务必使用正斜杠 （/） 不反斜杠 (\\)。 此脚本运行一段时间。

![管理：企业开发人员命令提示窗口](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在企业管理器中的.NET BankDemo 区域的创建

1. 打开**企业服务器以进行.NET 管理**UI。

2. 若要从 Windows 启动 MMC 管理单元中，**启动**菜单中，选择**Micro 焦点企业开发人员\>配置\>Enterprise Server for.NET 管理员**。(对于 Windows Server 中，选择**Micro 焦点企业开发人员\>Enterprise Server for.NET 管理员**)。

3. 展开**区域**容器，然后右键单击左的窗格中**CICS**。

4. 选择**定义的区域**创建名为新的 CICS 区域**BANKDEMO**、 (local) 数据库中托管。

5. 提供的数据库服务器实例中，单击**下一步**，然后输入区域名称**BANKDEMO**。

     ![定义区域对话框](media/07-demo-cics.png)

6. 若要选择跨区域数据库的区域定义文件，找到**地区\_bankdemo\_db.config**中**c:\\用户\\公共\\文档\\Micro 焦点\\企业开发人员\\示例\\大型机\\CICS\\DotNet\\BankDemo**。

     ![定义区域的区域名称：BANKDEMO](media/08-demo-cics.png)

7. 单击“完成”。

## <a name="create-xa-resource-definitions"></a>创建 XA 资源定义

1. 在左窗格中**企业服务器以进行.NET 管理**UI 中，展开**系统**，然后**XA 资源定义**。 此设置定义该区域与企业服务器和应用程序数据库的互操作。

2. 右键单击**XA 资源定义**，然后选择**添加服务器实例**。

3. 在下拉列表框中，选择**数据库服务实例**。 它将是本地计算机 SQLEXPRESS。

4. 选择从下的实例**XA 资源定义 (machinename\\sqlexpress)** 容器并单击**添加**。

5. 选择**数据库 XA 资源定义**，然后键入**BANKDEMO**有关**名称**并**区域**。

     ![新数据库 XA 资源定义屏幕](media/09-demo-xa.png)

6. 单击省略号 (**...**) 以显示连接字符串向导。 有关**服务器名称**，类型 **(local)\\SQLEXPRESS**。 有关**Logon**，选择**Windows 身份验证**。 对于数据库名称，键入**BANKDEMO**

     ![编辑连接字符串屏幕](media/10-demo-string.png)

7. 测试连接。

## <a name="start-the-bankdemo-region"></a>启动 BANKDEMO 区域

> [!NOTE]
> 第一步非常重要：必须设置要使用刚刚创建的 XA 资源定义的区域。

1. 导航到**BANDEMO CICS 区域**下**区域容器**，然后选择**编辑区域启动文件**从**操作**窗格。 向下滚动到 SQL 属性并输入**bankdemo**有关**XA 资源名称**，或使用省略号以将其选中。

2. 单击**保存**图标以保存所做的更改。

3. 右键单击**BANKDEMO CICS 区域**中**控制台**窗格，然后选择**启动/停止区域**。

4. 在底部**启动/停止区域**出现在中间窗格中，选择框**启动**。 几秒钟后将启动区域。

     ![SQL 启动/停止框](/media/11-demo-sql.png)

     ![CICS 区域 BANKDEMO-启动屏幕](media/12-demo-cics.png)

## <a name="create-a-listener"></a>创建侦听器

您需要为访问 BankDemo 应用程序的 TN3270 会话创建一个侦听器。

1. 在左窗格中，展开**配置编辑器**，然后选择**侦听器**。

2. 单击**打开的文件**图标，然后选择**seelistener.exe.config**文件。 此文件进行编辑，并且每次企业服务器启动时加载。

3. 请注意，两个区域之前定义 （ESDEMO 和 JCLDEMO）。

4. 若要为 BANKDEMO 创建一个新的区域，右键单击**区域**，然后选择**添加区域**。

5. 选择**BANKDEMO 区域**。

6. 通过右键单击添加 TN3270 通道**BANKDEMO 区域**，然后选择**添加通道**。

7. 有关**名称**，输入**TN3270**。 有关**端口**，输入**9024**。 （请注意 ESDEMO 应用程序使用端口 9230，因此需要使用不同的端口。）

8. 若要保存该文件，请单击**保存**图标，或选择**文件** \> **保存**。

9. 若要启动侦听器，请单击**启动侦听器**图标，或选择**选项** \> **启动侦听器**。

     ![侦听器配置编辑器 windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>配置 Rumba 访问 BankDemo 应用程序

需要执行的最后一件事就是配置 3270 会话使用 Rumba，3270 仿真程序。 此步骤中，可通过刚创建的侦听器访问 BankDemo 应用程序。

1. 从 Windows**启动**菜单上，启动 Rumba 桌面。

2. 下**连接**菜单项，选择**TN3270**。

3. 单击**插入**并键入**127.0.0.1**的 IP 地址和**9024**为用户定义的端口。

4. 在对话框的底部，单击**Connect**。 将显示黑色的 CICS 屏幕。

5. 类型**银行**显示 BankDemo 应用程序的初始 3270 屏幕。

6. 对于用户 ID，键入**B0001**对于密码，输入任何内容。 第一个屏幕 BANK20 将打开。

![大型机显示欢迎屏幕](media/14-demo.png)
![大型机显示-Rumba-子系统演示屏幕](media/15-demo.png)

祝贺你！ 您当前正在运行的 CICS 应用程序在 Azure 中使用微焦点企业服务器。

## <a name="next-steps"></a>后续步骤

- [Azure 上的 Docker 容器中运行企业服务器](run-enterprise-server-container.md)
- [大型机迁移-门户](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [有关将大型机迁移到 Azure 的介绍](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
