---
title: "Azure Cloud Shell（预览版）故障排除 | Microsoft Docs"
description: "Azure Cloud Shell 故障排除"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell 故障排除
Azure Cloud Shell 中问题的已知解决方法包括：

## <a name="error-400-disallowedoperation"></a>错误: 400 DisallowedOperation
 - **详细信息**：使用 Azure Active Directory 订阅时，无法创建存储。
 - **解决方法**：使用能够创建存储资源的 Azure 订阅。 Azure AD 订阅无法创建 Azure 资源。

## <a name="powershell-resolutions"></a>PowerShell 解决方法

### <a name="no-home-directory-persistence"></a>$Home 目录没有持久性
  - **详细信息**：向 $Home 写入数据的任何应用程序（例如 git、vim，等等）不会持久保存在不同的 PowerShell 会话中。
  - **解决方法**：在 PowerShell 配置文件中，在 `clouddrive` 中创建指向 $Home 的应用程序特定文件夹的符号链接。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>按 Ctrl+C 不会退出 Cmdlet 提示符
 - **详细信息**：尝试退出 Cmdlet 提示符时，按 `Ctrl+C` 不会退出提示符。
 - **解决方法**：若要退出提示符，请依次按 `Ctrl+C` 和 `Enter`。

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序
  - **详细信息**：如果用户启动 GUI 应用，提示符不会返回。  例如，当用户使用 `git` 关闭已启用双重身份验证的专用存储库时，会显示双重身份验证代码的对话框。
  - **解决方法**：按 `Ctrl+C` 退出命令。

### <a name="get-help--online-does-not-open-the-help-page"></a>运行 Get-help -online 不会打开帮助页
 - **详细信息**：如果用户键入 `Get-Help Find-Module -online`，会看到如下所示的错误消息：`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **解决方法**：复制 URL，并在浏览器上手动将其打开。
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM 的远程管理故障排除
 - **详细信息**：由于 WinRM 的默认 Windows 防火墙设置，用户可能会看到以下错误：`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **解决方法**：确保 VM 正在运行。 可以运行 `Get-AzureRmVM -Status` 确定 VM 状态。  接下来，在远程 VM 上添加新的防火墙规则，允许从任何子网建立 WinRM 连接，例如，

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 可以使用 [Azure custom script extension][customex] 来避免在添加新防火墙规则时登录到远程 VM。
 可将上述脚本保存在某个文件（例如 `addfirerule.ps1`）中，并将其上传到 Azure 存储容器。
 然后尝试以下命令：

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
