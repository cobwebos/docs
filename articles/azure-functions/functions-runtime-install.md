---
title: "Azure Functions 运行时安装 | Microsoft Docs"
description: "如何安装 Azure Functions 运行时"
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
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>安装 Azure Functions 运行时预览版

如果希望安装 Azure Functions 运行时预览版，必须执行以下步骤：

1. 确保计算机满足最低要求
1. 下载 [Azure Functions 运行时预览版安装程序](https://aka.ms/azafr)。 
1. 安装 Azure Functions 运行时预览版
1. 完成 Azure Functions 运行时预览版的配置

## <a name="prerequisites"></a>先决条件

在安装 Azure Functions 运行时预览版之前，必须具有以下各项：

1. 运行着 Microsoft Windows Server 2016 或 Microsoft Windows 10 Creators Update（专业版或企业版）的计算机。
1. 在网络中运行的一个 SQL Server 实例。  最低版本要求是 SQL Server Express。

## <a name="install-the-azure-functions-runtime-preview"></a>安装 Azure Functions 运行时预览版

Azure Functions 运行时预览版安装程序会引导完成 Azure Functions 运行时预览版管理角色和辅助角色的安装。  可以将管理角色和辅助角色安装在同一计算机上。  但是，随着你添加更多函数，必须在附加计算机上部署更多辅助角色以便能够将函数扩展到多个辅助角色。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>将管理角色和辅助角色安装在同一计算机上

1. 运行 Azure Functions 运行时预览版安装程序。

    ![Azure Functions 运行时预览版安装程序][1]

1. **单击“下一步”**以通过安装程序的第一阶段
1. 在阅读 **EULA** 的条款后，**选中复选框**以接受条款并**单击“下一步”**以继续操作。
1. 现在，选择要在此计算机上安装的角色“Functions 管理角色”和/或“Functions 辅助角色”并**单击“下一步”**

    ![Azure Functions 运行时预览版安装程序 - 角色选择][3]

    > [!NOTE]
    > 可以在多台其他计算机上安装 **Functions 辅助角色**，要执行此操作，请遵循以下说明并且在安装程序中仅选择“Functions 辅助角色”。

1. **单击“下一步”**让 **Azure Functions 运行时安装程序**在计算机上进行安装。
1. 完成后，安装程序将启动 **Azure Functions 运行时配置工具**。

    ![Azure Functions 运行时预览版安装程序完成][5]

    > [!NOTE]
    > 如果是在 **Windows 10** 上进行安装并且未启用**容器**功能，则 **Azure Functions 运行时**安装程序会提示重新启动计算机来完成安装。

## <a name="configure-the-azure-functions-runtime"></a>配置 Azure Functions 运行时

若要完成 Azure Functions 运行时安装，必须完成配置。

1. **Azure Functions 运行时配置工具**会显示计算机上安装了哪些角色。

    ![Azure Functions 运行时预览版配置工具][6]

1. 单击“数据库”选项卡，输入 **SQL Server 实例的连接详细信息**并**单击“应用”**。  要使 Azure Functions 运行时创建数据库来支持运行时，这是必须执行的步骤。
    
    ![Azure Functions 运行时预览版数据库配置][7]

1. 单击“凭据”选项卡。在此屏幕上，必须为托管所有 Azure Functions 的文件共享创建两个凭据。  为**文件共享所有者**和**文件共享用户****指定用户名和密码**组合，并单击“应用”。

    ![Azure Functions 运行时预览版凭据][8]

1. 单击“文件共享”选项卡。在此屏幕中，必须指定**文件共享位置**的详细信息。  系统可以创建此位置，也可以选择一个现有文件共享并单击“应用”。  如果选择了新的文件共享位置，则必须指定一个目录供 Azure Functions 运行时使用。
    
    ![Azure Functions 运行时预览版文件共享][9]

1. 单击“IIS”选项卡。此选项卡显示 Azure Functions 运行时安装会在 IIS 中创建的网站的详细信息。  **单击“应用”**以完成操作。

    ![Azure Functions 运行时预览版 IIS][10]

1. 单击“服务”选项卡。此选项卡显示 Azure Functions 运行时安装中的服务的状态。  在初始配置后，如果 **Azure Functions 主机激活服务**没有运行，请单击“启动服务”

    ![Azure Functions 运行时预览版配置完成][11]

1. 最后，以 `https://<machinename>/` 形式浏览 **Azure Functions 运行时门户**

    ![Azure Functions 运行时预览版门户][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png