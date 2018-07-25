---
title: Azure Cloud Shell 故障排除 | Microsoft Docs
description: Azure Cloud Shell 故障排除
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 21bc0633a9cc607325b48998791cb12631ecd0d7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856481"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的故障排除和限制

排查 Azure Cloud Shell 中问题的已知解决方案包括：

## <a name="general-troubleshooting"></a>常规故障排除

### <a name="early-timeouts-in-firefox"></a>FireFox 中的提前超时
- **详细信息**：Cloud Shell 利用打开的 Websocket 将输入/输出传递到你的浏览器。 FireFox 已经预设了可提前关闭 websocket 的策略，导致在 Cloud Shell 中提前超时。
- **解决方法**：打开 FireFox 并在 URL 框中导航到“about:config”。 搜索“network.Websocket.timeout.ping.request”并将值从 0 更改为 10。

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>存储对话框 - 错误：403 RequestDisallowedByPolicy
- **详细信息**：通过 Cloud Shell 创建存储帐户时，由于管理员设置的 Azure 策略而失败。错误消息将包括：`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解决方法**：与 Azure 管理员联系，让其删除或更新拒绝存储创建的 Azure 策略。

### <a name="storage-dialog---error-400-disallowedoperation"></a>存储对话框 - 错误：400 DisallowedOperation
 - **详细信息**：使用 Azure Active Directory 订阅时，无法创建存储。
 - **解决方法**：使用能够创建存储资源的 Azure 订阅。 Azure AD 订阅无法创建 Azure 资源。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>终端输出 - 错误：无法连接终端：无法建立 websocket。 按 `Enter` 重新连接。
 - **详细信息**：Cloud Shell 需要能够与 Cloud Shell 基础结构建立 websocket 连接。
 - **解决方法**：检查是否已将网络设置配置为允许向域（*.console.azure.com）发送 https 请求和 websocket 请求。

## <a name="bash-troubleshooting"></a>Bash 故障排除

### <a name="cannot-run-the-docker-daemon"></a>无法运行 docker 守护程序

- **详细信息**：Cloud Shell 利用容器托管 shell 环境，因此不允许运行守护程序。
- **解决方法**：利用默认安装的 [docker-machine](https://docs.docker.com/machine/overview/) 管理远程 Docker 主机中的 docker 容器。

## <a name="powershell-troubleshooting"></a>PowerShell 故障排除

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序

- **详细信息**：如果用户启动 GUI 应用，提示符不会返回。 例如，当用户克隆已启用双因素身份验证的专用 GitHub 存储库时，会显示用于完成双因素身份验证的对话框。  
- **解决方法**：关闭再重新打开 shell。

### <a name="get-help--online-does-not-open-the-help-page"></a>运行 Get-help -online 不会打开帮助页

- **详细信息**：如果用户键入 `Get-Help Find-Module -online`，会看到如下所示的错误消息：`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **解决方法**：复制 URL，并在浏览器上手动将其打开。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM 的远程管理故障排除

- **详细信息**：由于 WinRM 的默认 Windows 防火墙设置，用户可能会看到以下错误：`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- 解决方法：运行 `Enable-AzureRmVMPSRemoting` 以启用目标计算机上 PowerShell 远程处理的所有方面。
 

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` 在 Azure 驱动器中缓存结果

- **详细信息**：在 Azure 驱动器中缓存 `dir` 的结果。
- **解析**：在 Azure 驱动器视图中创建或删除资源后，运行 `dir -force` 以更新。

## <a name="general-limitations"></a>一般限制
Azure Cloud Shell 有以下已知限制：

### <a name="system-state-and-persistence"></a>系统状态和持久性

提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 20 分钟后会被回收。 Cloud Shell 需要装载 Azure 文件共享。 因此，订阅必须能够设置存储资源才能访问 Cloud Shell。 其他注意事项包括：

* 使用装载的存储时，仅持久保存 `clouddrive` 目录中的修改。 在 Bash 中，`$Home` 目录也会持久保存。
* 仅可从[已分配区域](persisting-shell-storage.md#mount-a-new-clouddrive)内部装载 Azure 文件共享。
  * 在 Bash 中，运行 `env` 可以找到设置为 `ACC_LOCATION` 的区域。
* Azure 文件仅支持本地冗余存储和异地冗余存储帐户。

### <a name="browser-support"></a>浏览器支持

Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

### <a name="copy-and-paste"></a>复制和粘贴

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>对于一个给定的用户，只有一个 shell 可处于活动状态

不管是在 **Bash** 还是 **PowerShell** 中，用户每次只能启动一种类型的 shell。 但是，每次可以运行 Bash 或 PowerShell 的多个实例。 在 Bash 和 PowerShell 之间切换会导致 Cloud Shell 重启，从而终止现有会话。

### <a name="usage-limits"></a>使用限制

Cloud Shell 适用于交互式用例。 因此，任何长时间运行的非交互式会话都会在没有预警的情况下终止。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>用户权限

权限设置为普通用户，不具有 sudo 访问权限。 不会保留 `$Home` 目录外部的任何安装。

### <a name="editing-bashrc"></a>编辑 .bashrc

编辑 .bashr 时要小心，执行这一操作可能导致 Cloud Shell 出现意外错误。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="azuread-module-name"></a>`AzureAD` 模块名称

`AzureAD` 模块名称当前为 `AzureAD.Standard.Preview`，该模块提供相同的功能。

### <a name="sqlserver-module-functionality"></a>`SqlServer` 模块功能

Cloud Shell 中包含的 `SqlServer` 模块仅具有对 PowerShell Core 的预发布版本支持。 具体而言，`Invoke-SqlCmd` 尚不可用。

### <a name="default-file-location-when-created-from-azure-drive"></a>从 Azure 驱动器创建时的默认文件位置：

使用 PowerShell cmdlet，用户无法在 Azure 驱动器下创建文件。 当用户使用其他工具（如 vim 或 nano）创建新文件时，文件将默认保存到 `$HOME`。 

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序

如果用户运行一条会创建 Windows 对话框的命令（例如 `Connect-AzureAD` 或 `Connect-AzureRmAccount`），将看到如下所示的错误消息：`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`。

### <a name="tab-completion-crashes-psreadline"></a>Tab 自动补全导致 PSReadline 崩溃

如果用户的 EditMode 在 PSReadline 中设置为 Emacs，用户尝试通过 Tab 自动补全显示所有可能性，而窗口大小过小，无法显示所有可能性，则 PSReadline 将崩溃。

### <a name="large-gap-after-displaying-progress-bar"></a>在显示进度栏后出现大间距型

如果用户执行显示进度栏的操作（例如，在 `Azure:` 驱动器中的 tab 自动补全），则光标可能设置不正确，且在以前的进度栏处出现间距。

### <a name="random-characters-appear-inline"></a>随机字符以内联显示

光标位置序列代码（例如 `5;13R`）可以在用户输入时显示。  这些字符可以手动删除。

## <a name="personal-data-in-cloud-shell"></a>Cloud Shell 中的个人数据

Azure Cloud Shell 非常重视你的个人数据，Azure Cloud Shell 服务捕获和存储的数据用于为你的体验提供默认值，例如最近使用的 shell、首选字号、首选字体类型和支持云驱动器的文件共享详细信息。 如果想要导出或删除此数据，我们添加了以下说明。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>导出
若要导出 Cloud Shell 为你保存的用户设置（如首选 shell、字号和字体类型），请运行以下命令。

1. 启动 Cloud Shell 中的 Bash
2. 运行以下命令：
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>删除
若要删除 Cloud Shell 为你保存的用户设置（如首选 shell、字号和字体类型），请运行以下命令。 下次启动 Azure Cloud Shell 时，系统会要求你再次载入文件共享。 

如果删除用户设置，不会删除实际的 Azure 文件共享。请转到“Azure 文件”完成该操作。

1. 启动 Cloud Shell 中的 Bash
2. 运行以下命令：
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
