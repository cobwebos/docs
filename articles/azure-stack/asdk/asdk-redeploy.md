---
title: "重新部署 Azure 堆栈开发工具包 (ASDK) |Microsoft 文档"
description: "在本教程中，你将学习如何重新安装 ASDK。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 579414f79da29a443ddaf8e167bf3889a647f33d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>教程： 重新部署 ASDK
在本教程中，你将学习如何重新部署 Azure 堆栈开发工具包 (ASDK) 在非生产环境中。 因为不支持升级 ASDK，你需要完全重新部署它移动到较新版本。 你还可以重新 ASDK 部署在只是想要从头开始通过任何时间。

> [!IMPORTANT]
> 升级到较新版本的 ASDK 不受支持。 你必须重新部署开发工具包主机计算机上的 ASDK 每当你想要评估 Azure 堆栈的较新版本。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 删除 Azure 注册 
> * 重新部署 ASDK

## <a name="remove-azure-registration"></a>删除 Azure 注册 
如果以前已向 Azure 注册 ASDK 安装，你应该在重新部署 ASDK 之前删除注册资源。 重新注册 ASDK 时重新部署 ASDK 启用应用商店联合。 如果以前未注册 ASDK 与 Azure 订阅，则可以跳过本节。

若要删除注册资源，使用**删除 AzsRegistration** cmdlet，注销 Azure 堆栈。 然后，使用**删除 AzureRMRsourceGroup** cmdlet 可从你的 Azure 订阅中删除 Azure 堆栈资源组：

1. 以有权访问特权终结点的计算机上管理员身份打开 PowerShell 控制台。 有关 ASDK，这是开发工具包主机计算机。

2. 运行以下 PowerShell 命令以注销 ASDK 安装和删除**azurestack**从你的 Azure 订阅的资源组：

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Login-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. 系统会提示你登录到你的 Azure 订阅和本地 ASDK 安装运行脚本时。
4. 完成脚本后，你应看到类似于下面的示例消息：

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure 堆栈现在成功应从你的 Azure 订阅中注销。 此外，还应删除的 azurestack 资源组中，向 Azure 注册 ASDK 时创建。

## <a name="redeploy-the-asdk"></a>重新部署 ASDK
若要重新部署 Azure 堆栈，你必须通过从头开始如下所述。 步骤会有所不同，具体取决于使用 Azure 堆栈安装程序 (asdk installer.ps1) 脚本来安装 ASDK。

### <a name="redeploy-the-asdk-using-the-installer-script"></a>重新部署使用安装程序脚本 ASDK
1. ASDK 在计算机上，打开提升的 PowerShell 控制台并导航到中的 asdk installer.ps1 脚本**AzureStack_Installer**目录位于非系统驱动器上。 运行该脚本，然后单击**重新启动**。

   ![运行 asdk installer.ps1 脚本](media/asdk-redeploy/1.png)

2. 选择基操作系统 (不**Azure 堆栈**)，然后单击**下一步**。

   ![重新启动到主机操作系统](media/asdk-redeploy/2.png)

3. 开发工具包主机重新启动到基本操作系统后，请以本地管理员身份登录。 找到并删除**C:\CloudBuilder.vhdx**已用作以前的部署的一部分的文件。 

4. 重复相同的步骤，执行到第一条[部署 ASDK](asdk-deploy.md)。

### <a name="redeploy-the-asdk-without-using-the-installer"></a>重新 ASDK 部署而不使用安装程序
如果你未使用 asdk installer.ps1 脚本安装 ASDK，你必须手动重新开发工具包主机计算机配置重新部署 ASDK 之前。

1. 通过运行启动的系统配置实用工具**msconfig.exe** ASDK 计算机上。 上**启动**选项卡上，选择主机计算机操作系统 （而不是 Azure 堆栈），单击**设为默认值**，然后单击**确定**。 单击**重新启动**出现提示时。

      ![设置启动配置](media/asdk-redeploy/4.png)

2. 开发工具包主机重新启动到基本操作系统后，请开发工具包主机计算机的本地管理员身份登录。 找到并删除**C:\CloudBuilder.vhdx**已用作以前的部署的一部分的文件。 

3. 重复相同的步骤，执行到第一条[部署使用 PowerShell ASDK](asdk-deploy-powershell.md)。


## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 删除 Azure 注册 
> * 重新部署 ASDK

前进到下一步的教程，若要了解如何添加 Azure 堆栈应用商店项。

> [!div class="nextstepaction"]
> [添加 Azure 堆栈应用商店项](asdk-marketplace-item.md)




