---
title: "Azure Functions 运行时安装 | Microsoft Docs"
description: "如何安装 Azure Functions 运行时预览版 2"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>安装 Azure Functions 运行时预览版 2

若要安装 Azure Functions 运行时预览版 2，必须执行以下步骤：

1. 确保计算机满足最低要求。
1. 下载 [Azure Functions 运行时预览版安装程序](https://aka.ms/azafrv2)。
1. 卸载 Azure Functions 运行时预览版 1。
1. 安装 Azure Functions 运行时预览版 2。
1. 完成 Azure Functions 运行时预览版 2 的配置。
1. 在 Azure Functions 运行时预览版中创建第一个函数

## <a name="prerequisites"></a>先决条件

在安装 Azure Functions 运行时预览版之前，必须准备好以下资源：

1. 运行着 Microsoft Windows Server 2016 或 Microsoft Windows 10 Creators Update（专业版或企业版）的计算机。
1. 在网络中运行的一个 SQL Server 实例。  最低版本要求是 SQL Server Express。

## <a name="uninstall-previous-version"></a>卸载以前的版本

如果之前已安装 Azure Functions 运行时预览版，则必须将其卸载，然后安装最新版本。  可通过 Windows 中的“添加/删除程序”卸载 Azure Functions 运行时预览版。

## <a name="install-the-azure-functions-runtime-preview"></a>安装 Azure Functions 运行时预览版

Azure Functions 运行时预览版安装程序会引导完成 Azure Functions 运行时预览版管理角色和辅助角色的安装。  可以将管理角色和辅助角色安装在同一计算机上。  但是，随着添加更多的函数应用，必须在附加计算机上部署更多辅助角色以便能够将函数扩展到多个辅助角色。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>将管理角色和辅助角色安装在同一计算机上

1. 运行 Azure Functions 运行时预览版安装程序。

    ![Azure Functions 运行时预览版安装程序][1]

1. 单击“下一步”。
1. 在阅读 **EULA** 的条款后，**选中复选框**以接受条款并单击“下一步”以继续操作。
1. 选择要在此计算机上安装的角色“Functions 管理角色”和/或“Functions 辅助角色”并单击“下一步”。

    ![Azure Functions 运行时预览版安装程序 - 角色选择][3]

    > [!NOTE]
    > 可以在其他许多计算机上安装 **Functions 辅助角色**。 为此，请按照这些说明进行操作，并且仅在安装程序中选择“Functions 辅助角色”。

1. 单击“下一步”，让 **Azure Functions 运行时安装向导**开始在计算机上执行安装过程。
1. 完成后，安装向导将启动“Azure Functions 运行时”配置工具。

    ![Azure Functions 运行时预览版安装程序完成][6]

    > [!NOTE]
    > 如果是在 **Windows 10** 上进行安装并且未启用**容器**功能，则 **Azure Functions 运行时安装程序**会提示重新启动计算机来完成安装。

## <a name="configure-the-azure-functions-runtime"></a>配置 Azure Functions 运行时

若要完成 Azure Functions 运行时安装，必须完成配置。

1. **Azure Functions 运行时**配置工具会显示计算机上安装了哪些角色。

    ![Azure Functions 运行时预览版配置工具][7]

1. 单击“数据库”选项卡，输入 SQL Server 实例的连接详细信息（包括指定[数据库主密钥](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)）并单击“应用”。  需要与 SQL Server 实例建立连接，才能让 Azure Functions 运行时创建数据库来支持运行时。

    ![Azure Functions 运行时预览版数据库配置][8]

1. 单击“凭据”选项卡。在此处，必须为托管所有函数应用的文件共享创建两个凭据。  为**文件共享所有者**和**文件共享用户**指定**用户名**和**密码**组合，并单击“应用”。

    ![Azure Functions 运行时预览版凭据][9]

1. 单击“文件共享”选项卡。在此处，必须指定文件共享位置的详细信息。  系统可以创建文件共享，也可以选择一个现有文件共享并单击“应用”。  如果选择了新的文件共享位置，则必须指定一个目录供 Azure Functions 运行时使用。

    ![Azure Functions 运行时预览版文件共享][10]

1. 单击“IIS”选项卡。此选项卡显示 Azure Functions 运行时配置工具会在 IIS 中创建的网站的详细信息。  可以在此处为 Azure Functions 运行时预览版门户指定自定义的 DNS 名称。  单击“应用”以完成操作。

    ![Azure Functions 运行时预览版 IIS][11]

1. 单击“服务”选项卡。此选项卡显示 Azure Functions 运行时配置工具中的服务的状态。  完成初始配置后，如果 **Azure Functions 主机激活服务**未运行，请单击“启动服务”

    ![Azure Functions 运行时预览版配置完成][12]

1. 使用 `https://<machinename>.<domain>/` 浏览到 **Azure Functions 运行时门户**。

    ![Azure Functions 运行时预览版门户][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>在 Azure Functions 运行时预览版中创建第一个函数

在 Azure Functions 运行时预览版中创建第一个函数

1. 使用 https://<machinename>.<domain> 浏览到 **Azure Functions 运行时门户** 例如 https://mycomputer.mydomain.com
1. 系统会提示**登录**。如果是在域中部署的，请使用帐户用户名和密码登录；否则，请使用本地帐户用户名和密码登录到门户。

![Azure Functions 运行时预览版门户登录][14]

1. 若要创建函数应用，必须创建订阅。  在门户的左上角，单击订阅旁边的 **+** 选项

![Azure Functions 运行时预览版门户订阅][15]

1. 选择“DefaultPlan”，输入订阅名称，单击“创建”。

![Azure Functions 运行时预览版门户订阅计划和名称][16]

1. 所有函数应用列在门户的左窗格中。  若要创建新的函数应用，请选择标题“函数应用”，单击 **+** 选项。

1. 输入函数应用的名称，选择正确的订阅，选择想要对其编程的 Azure Functions 运行时版本，并单击“创建”

![Azure Functions 运行时预览版门户 - 新建函数应用][17]

1. 新函数应用将列入门户的左窗格中。  选择“函数”，单击门户中间窗格顶部的“新建函数”。

![Azure Functions 运行时预览版模板][18]

1. 选择“计时器触发器”函数，在右侧浮出控件中为函数命名，将“计划”更改为 `*/5 * * * * *`（此 cron 表达式可让计时器函数每隔五秒执行一次），并单击“创建”

![Azure Functions 运行时预览版 - 新计时器函数配置][19]

1. 现已创建函数。  可以通过展开门户底部的“日志”窗格查看函数应用的执行日志。

![Azure Functions 运行时预览版 - 函数执行][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
