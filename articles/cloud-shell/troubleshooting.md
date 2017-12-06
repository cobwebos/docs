---
title: "Azure Cloud Shell 故障排除 | Microsoft Docs"
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
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 233569303ea3651192aafe9681f58a9582625d29
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell 故障排除

Azure Cloud Shell 中问题的已知解决方法包括：

## <a name="general-resolutions"></a>常规解决方法

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>存储对话框 - 错误：403 RequestDisallowedByPolicy
- **详细信息**：通过 Cloud Shell 创建存储帐户时，由于管理员设置的 Azure 策略而失败。错误消息将包括：`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解决方法**：与 Azure 管理员联系，让其删除或更新拒绝存储创建的 Azure 策略。

### <a name="storage-dialog---error-400-disallowedoperation"></a>存储对话框 - 错误：400 DisallowedOperation
 - **详细信息**：使用 Azure Active Directory 订阅时，无法创建存储。
 - **解决方法**：使用能够创建存储资源的 Azure 订阅。 Azure AD 订阅无法创建 Azure 资源。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>终端输出 - 错误：无法连接终端：无法建立 websocket。 按 `Enter` 重新连接。
 - **详细信息**：Cloud Shell 需要能够与 Cloud Shell 基础结构建立 websocket 连接。
 - **解决方法**：检查是否已将网络设置配置为允许向域（*.console.azure.com）发送 https 请求和 websocket 请求。

## <a name="bash-resolutions"></a>Bash 解决方法

### <a name="cannot-run-az-login"></a>无法运行 az login

- **详细信息**：无法运行 `az login`，原因是已在用于登录 Cloud Shell 或 Azure 门户的帐户下进行身份验证。
- **解决方法**：利用用于登录或注销的帐户并对目标 Azure 帐户重新进行身份验证。

### <a name="cannot-run-the-docker-daemon"></a>无法运行 docker 守护程序

- **详细信息**：Cloud Shell 利用容器托管 shell 环境，因此不允许运行守护程序。
- **解决方法**：利用默认安装的 [docker-machine](https://docs.docker.com/machine/overview/) 管理远程 Docker 主机中的 docker 容器。

## <a name="powershell-resolutions"></a>PowerShell 解决方法

### <a name="no-home-directory-persistence"></a>$Home 目录没有持久性

- **详细信息**：应用程序（例如 git、vim，等等）向 `$Home` 写入的任何数据未在 PowerShell 会话之间持久保留。
- **解决方法**：在 PowerShell 配置文件中，在 `clouddrive` 中创建指向 $Home 的应用程序特定文件夹的符号链接。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>按 Ctrl+C 不会退出 Cmdlet 提示符

- **详细信息**：尝试退出 Cmdlet 提示符时，按 `Ctrl+C` 不会退出提示符。
- **解决方法**：若要退出提示符，请依次按 `Ctrl+C` 和 `Enter`。

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序

- **详细信息**：如果用户启动 GUI 应用，提示符不会返回。 例如，当用户克隆已启用双因素身份验证的专用 GitHub 存储库时，会显示用于完成双因素身份验证的对话框。  
- **解决方法**：关闭再重新打开 shell。


### <a name="get-help--online-does-not-open-the-help-page"></a>运行 Get-help -online 不会打开帮助页

- **详细信息**：如果用户键入 `Get-Help Find-Module -online`，会看到如下所示的错误消息：`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **解决方法**：复制 URL，并在浏览器上手动将其打开。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM 的远程管理故障排除

- **详细信息**：由于 WinRM 的默认 Windows 防火墙设置，用户可能会看到以下错误：`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **解决方法**：确保 VM 正在运行。 可以运行 `Get-AzureRmVM -Status` 确定 VM 状态。  接下来，在远程 VM 上添加新的防火墙规则，允许从任何子网建立 WinRM 连接，例如，

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 可以使用 [Azure custom script extension](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) 来避免在添加新防火墙规则时登录到远程 VM。
 可将上述脚本保存在某个文件（例如 `addfirerule.ps1`）中，并将其上传到 Azure 存储容器。
 然后尝试以下命令：

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` 在 Azure 驱动器中缓存结果

- **详细信息**：在 Azure 驱动器中缓存 `dir` 的结果。
- **解析**：在 Azure 驱动器视图中创建或删除资源后，运行 `dir -force` 以更新。
