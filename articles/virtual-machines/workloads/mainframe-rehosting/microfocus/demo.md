---
title: 为 Azure 虚拟机上的微焦点企业开发人员 4.0 设置微焦点 CICS BankDemo
description: 在 Azure 虚拟机 （VM） 上运行微焦点银行演示应用程序，以了解如何使用微焦点企业服务器和企业开发人员。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411073"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>为 Azure 上的微焦点企业开发人员 4.0 设置微焦点 CICS 银行演示

在 Azure 上设置微焦点企业服务器 4.0 和企业开发人员 4.0 时，可以测试 IBM z/OS 工作负载的部署。 本文演示如何设置 CICS BankDemo，这是企业开发人员附带的示例应用程序。

CIC 代表客户信息控制系统，这是许多在线大型机应用程序使用的交易平台。 BankDemo 应用程序非常适合了解企业服务器和企业开发人员的操作方式以及如何管理和部署使用绿屏终端完成的实际应用程序。

> [!NOTE]
> 即将推出：有关在 Azure VM 上设置[微焦点企业服务器 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110)的说明。

## <a name="prerequisites"></a>先决条件

- 具有[企业开发人员的](set-up-micro-focus-azure.md)VM 。 请记住，企业开发人员在它上有一个完整的企业服务器实例，用于开发和测试目的。 此实例是用于演示的企业服务器实例。

- [SQL 服务器 2017 快递版](https://www.microsoft.com/sql-server/sql-server-editions-express). 下载并将其安装在企业开发人员 VM 上。 企业服务器需要一个数据库来管理 CICS 区域，BankDemo 应用程序还使用称为 BANKDEMO 的 SQL Server 数据库。 本演示假定您对两个数据库都使用 SQL Server Express。 安装时，选择基本安装。

- [SQL 服务器管理工作室](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)（SSMS）。 SSMS 用于管理数据库和运行 T-SQL 脚本。 下载并将其安装在企业开发人员 VM 上。

- [Visual Studio 2019](https://azure.microsoft.com/downloads/)与最新的服务包或[视觉工作室社区](https://visualstudio.microsoft.com/vs/community/)， 你可以免费下载.

- 伦巴桌面或其他3270仿真器。

## <a name="configure-the-windows-environment"></a>配置 Windows 环境

在 VM 上安装企业开发人员 4.0 后，必须配置附带的企业服务器实例。 为此，您需要安装一些其他 Windows 功能，如下所示。

1. 使用 RDP 登录到您创建的企业服务器 4.0 VM。

2. 单击 **"开始"** 按钮旁边的 **"搜索**"图标，然后键入**Windows 功能**。 服务器管理器添加角色和功能向导打开。

3. 选择**Web 服务器 （IIS） 角色**，然后检查以下选项：

    - Web 管理工具
    - IIS 6 管理兼容性（选择所有可用功能）
    - IIS 管理控制台
    - IIS 管理脚本和工具
    - IIS 管理服务

4. 选择**万维网服务**，并检查以下选项：

     应用程序开发功能：
    - .NET 可扩展性
    - ASP.NET
    - 常见 HTTP 功能：添加所有可用功能
    - 运行状况和诊断：添加所有可用功能
    - 安全性：
        - 基本身份验证
        - Windows 身份验证

5. 选择**Windows 进程激活服务**及其所有子级。

6. 有关**功能**，请检查**Microsoft .NET 框架 3.5.1**，并检查以下选项：

    - Windows 通信基础 HTTP 激活
    - Windows 通信基础非 HTTP 激活

7. 有关**功能**，请检查**Microsoft .NET 框架 4.6**，并检查以下选项：

   - 命名管道激活
   - TCP 激活
   - TCP 端口共享

     ![添加角色和功能向导：角色服务](media/01-demo-roles.png)

8. 选择所有选项后，单击 **"下一步**"进行安装。

9. Windows 功能后，转到**控制面板\>系统和安全\>管理工具**，然后选择 **"服务**"。 向下滚动并确保以下服务正在运行，并设置为**自动**：

    - **NetTcpPortSharing**
    - **Net.Pipe 侦听器适配器**
    - **Net.tcp 侦听器适配器**

10. 要配置 IIS 和 WAS 支持，请从菜单中找到**微焦点企业开发人员命令提示符 （64 位），** 然后以**管理员**身份运行。

11. **类型设置 _i，** 然后按**Enter**。

12. 脚本运行后，可以关闭窗口。

## <a name="configure-the-local-system-account-for-sql-server"></a>为 SQL 服务器配置本地系统帐户

某些企业服务器进程需要能够登录 SQL Server 并创建数据库和其他对象。 这些进程使用本地系统帐户，因此您必须授予该帐户的系统管理员权限。

1. 启动**SSMS，** 然后单击"**连接**"以使用 Windows 身份验证连接到本地 SQLEXPRESS 服务器。 它在 **"服务器名称"** 列表中应可用。

2. 在左侧，展开**安全**文件夹并选择 **"登录"。**

3. 选择**NT\\授权系统**并选择**属性**。

4. 选择**服务器角色**并检查**系统管理员**。

     ![SSMS 对象资源管理器窗口：登录属性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>创建 BankDemo 数据库及其所有对象

1. 打开**Windows 资源管理器**并导航到**C：\\\\\\\\\\\\\\\\\\\\用户\\公共文档微焦点企业开发人员示例主机 CICS DotNet 银行Demo SQL**.

2. 将**BankDemoCreateAll.SQL**文件的内容复制到剪贴板中。

3. 打开**SSMS**。 在右侧，单击 **"服务器"** 并选择 **"新建查询**"。

4. 将剪贴板的内容粘贴到 **"新建查询"** 框中。

5. 通过单击查询上方的"**执行"** 选项卡**Command**执行 SQL。

查询应运行时没有错误。 完成后，您拥有 BankDemo 应用程序的示例数据库。

![SQLQuery1.sql 输出](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>验证数据库表和对象是否已创建

1. 右键单击**BANKDEMO**数据库并选择 **"刷新**"。

2. 展开**数据库**并选择**表**。 您应该会看到类似以下内容。

     ![在对象资源管理器中扩展的 BANKDEMO 表](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>在企业开发人员中构建应用程序

1. 打开 Visual Studio 并登录。

2. 在 **"文件**"菜单选项下，选择 **"打开项目/解决方案**"，导航到**C：\\用户\\公共\\文档\\微\\焦点\\企业开发人员\\\\示例\\\\CICS DotNet BankDemo，** 然后选择**sln**文件。

3. 花一些时间检查对象。 COBOL 程序与抄本 （CPY） 和 JCL 一起显示在解决方案资源管理器中，并带有 CBL 扩展。

4. 右键单击**BankDemo2**项目，然后选择 **"设置为启动项目**"。

    > [!NOTE]
    > BankDemo 项目使用 HCOSS（SQL Server 的主机兼容性选项），该选项不用于此演示。

5. 在**解决方案资源管理器**中，右键单击**BankDemo2**项目并选择 **"生成**"。

    > [!NOTE]
    > 在解决方案级别构建会导致错误，因为尚未配置 HCOSS。

6. 生成项目时，请检查**输出**窗口。 它应该如下图所示。

     ![显示成功构建的输出窗口](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>将 BankDemo 应用程序部署到区域数据库

1. 打开企业开发人员命令提示符（64 位）作为管理员。

2. 导航到 **%PUBLIC%\\\\文档微\\焦点企业\\开发人员\\示例主机\\CICS\\\\DotNet 银行Demo**。

3. 在命令提示符下，执行**bankdemodbdeploy，** 并包含要部署到的数据库的参数，例如：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 请确保使用向前斜杠 （/） 而不是向后斜杠（）。\\ 此脚本运行一段时间。

![管理：企业开发人员命令提示窗口](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在企业管理员中为 .NET 创建银行演示区域

1. 打开**企业服务器 .NET 管理**UI。

2. 要启动 MMC 管理单元，请从 Windows**开始**菜单中选择**微焦点企业\>开发人员\>配置企业服务器。.NET Admin**。（对于 Windows 服务器，选择**用于\>.NET 管理员的微焦点企业开发人员企业服务器**。

3. 展开左侧窗格中的**区域**容器，然后右键单击**CICS**。

4. 选择 **"定义区域**"以创建一个名为**BANKDEMO**的新 CICS 区域，托管在（本地）数据库中。

5. 提供数据库服务器实例，单击 **"下一步**"，然后输入区域名称**BANKDEMO**。

     ![定义区域对话框](media/07-demo-cics.png)

6. 要选择跨区域数据库的区域定义文件，请在 C 中**查找\_区域\_bankdemo db.config：****\\\\用户\\公共\\文档微\\焦点企业\\开发人员\\示例主机\\CICS\\\\DotNet BankDemo**。

     ![定义区域 - 区域名称：银行](media/08-demo-cics.png)

7. 单击“完成”****。

## <a name="create-xa-resource-definitions"></a>创建 XA 资源定义

1. 在**企业服务器的左侧窗格中，用于 .NET 管理**UI，展开**系统**，然后**展开 XA 资源定义**。 此设置定义区域与企业服务器和应用程序数据库的交互操作方式。

2. 右键单击**XA 资源定义**并选择 **"添加服务器实例**"。

3. 在下拉框中，选择**数据库服务实例**。 它将是本地计算机 SQLEXPRESS。

4. 从**XA 资源定义（计算机名称\\sqlexpress）** 容器下选择实例，然后单击"**添加**"。

5. 选择**数据库 XA 资源定义**，然后键入**名称****和地区的** **BANKDEMO。**

     ![新的数据库 XA 资源定义屏幕](media/09-demo-xa.png)

6. 单击椭圆 （**...）** 以启动连接字符串向导。 对于**服务器名称**，键入 **（本地）SQLEXPRESS\\**。 对于**登录**，请选择**Windows 身份验证**。 对于数据库名称，键入**BANKDEMO**

     ![编辑连接字符串屏幕](media/10-demo-string.png)

7. 测试连接。

## <a name="start-the-bankdemo-region"></a>启动 BANKDEMO 区域

> [!NOTE]
> 第一步很重要：必须将区域设置为使用刚刚创建的 XA 资源定义。

1. 导航到**区域容器**下的**BANDEMO CICS 区域**，然后从 **"操作"** 窗格中选择 **"编辑区域启动文件**"。 向下滚动到 SQL 属性，然后输入**XA 资源名称**的**bankdemo，** 或使用省略号来选择它。

2. 单击 **"保存**"图标以保存更改。

3. 右键单击**控制台**窗格中的**BANKDEMO CICS 区域**，然后选择 **"开始/停止区域**"。

4. 在中间窗格中显示的 **"开始/停止区域"** 框的底部，选择 **"开始**"。 几秒钟后，区域开始。

     ![SQL 开始/停止框](media/11-demo-sql.png)

     ![CICS 地区银行 - 启动屏幕](media/12-demo-cics.png)

## <a name="create-a-listener"></a>创建侦听器

为访问 BankDemo 应用程序的 TN3270 会话创建侦听器。

1. 在左侧窗格中，展开 **"配置编辑器"** 并选择**侦听器**。

2. 单击 **"打开文件"** 图标并选择 **"seelistener.exe.config"** 文件。 每次企业服务器启动时，都将编辑并加载此文件。

3. 请注意之前定义的两个区域（ESDEMO 和 JCLDEMO）。

4. 要为 BANKDEMO 创建新区域，请右键单击 **"区域**"，然后选择"**添加区域**"。

5. 选择**银行区**。

6. 通过右键单击**BANKDEMO 区域**并选择 **"添加通道**"添加 TN3270 通道。

7. 对于**名称**，输入**TN3270**。 对于**端口**，输入**9024**。 ESDEMO 应用程序使用端口 9230，因此您需要使用不同的端口。

8. 要保存文件，请单击 **"保存**"图标或选择"**文件**\>**保存**"。

9. 要启动侦听器，请单击 **"开始侦听器"** 图标或选择 **"**\>**开始侦听器"** 选项。

     ![侦听器配置编辑器窗口](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>配置伦巴以访问 BankDemo 应用程序

您需要做的最后一件事是使用 3270 仿真器 Rumba 配置 3270 会话。 此步骤使您能够通过创建的侦听器访问 BankDemo 应用程序。

1. 从"窗口**开始"** 菜单中启动 Rumba 桌面。

2. 在 **"连接"** 菜单项下，选择**TN3270**。

3. 单击 IP 地址的**插入**和键入**127.0.0.1，** 为用户定义的端口键入**9024。**

4. 在对话框的底部，单击"**连接**"。 将显示一个黑色 CICS 屏幕。

5. 键入**银行**以显示 BankDemo 应用程序的初始 3270 屏幕。

6. 对于用户 ID，键入**B0001，** 对于密码，键入任何内容。 第一个屏幕 BANK20 打开。

![大型机显示器欢迎屏幕](media/14-demo.png)
![大型机显示 - 伦巴 - 子系统演示屏幕](media/15-demo.png)

祝贺你！ 现在，您正在使用微焦点企业服务器在 Azure 中运行 CICS 应用程序。

## <a name="next-steps"></a>后续步骤

- [在 Azure 上的 Docker 容器中运行企业服务器](run-enterprise-server-container.md)
- [大型机迁移 - 门户](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [疑难解答](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [将大型机与 Azure 迁移解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
