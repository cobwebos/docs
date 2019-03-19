---
title: 排查 Microsoft Azure Site Recovery 提供程序升级失败问题 | Microsoft Docs
description: 了解和
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117296"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>排查 Microsoft Azure Site Recovery 提供程序升级故障

本文帮助你解决在 Microsoft Azure Site Recovery 提供程序升级期间可能导致失败的问题。

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>升级失败并报告已安装最新的 Site Recovery 提供程序

升级 Microsoft Azure Site Recovery 提供程序 (DRA) 时，统一安装程序升级失败并发出错误消息：

由于已安装该软件的更高版本，因此不支持升级。

若要升级，请使用以下步骤：

1. 下载 Microsoft Azure Site Recovery 统一安装程序：
   1. 在 [Azure Site Recovery 中的服务更新](service-updates-how-to.md##links-to-currently-supported-update-rollups)一文的“当前支持的更新汇总链接”部分，选择要升级到的提供程序。
   2. 在汇总页上找到“更新信息”部分，并下载 Microsoft Azure Site Recovery 统一安装的更新汇总。

2. 打开命令提示符并导航到下载的统一安装程序文件所在的文件夹。 使用以下命令从下载内容中提取安装程序文件：MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;提取的文件所在的文件夹路径&gt;。
    
    示例命令：

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. 在命令提示符下，导航到文件所提取到的文件夹，并运行以下安装命令：
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. 返回到下载的统一安装程序所在的文件夹，并运行 MicrosoftAzureSiteRecoveryUnifiedSetup.exe 以完成升级。 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>升级因要重命名的第三方文件夹失败

若要成功执行升级，第三方文件夹必须重命名。

解决问题。

1. 启动注册表编辑器 (regedit.exe) 并打开 HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 分支。
1. 检查 `Build_Version` 项值。 如果此项设置为最新版本，请减小版本号。 例如，如果最新版本为 9.22.\*，而 `Build_Version` 项设置为该值，请将它减小为 9.21.\*。
1. 下载最新的 Microsoft Azure Site Recovery 统一安装程序：
   1. 在 [Azure Site Recovery 中的服务更新](service-updates-how-to.md##links-to-currently-supported-update-rollups)一文的“当前支持的更新汇总链接”部分，选择要升级到的提供程序。
   2. 在汇总页上找到“更新信息”部分，并下载 Microsoft Azure Site Recovery 统一安装的更新汇总。
1. 打开命令提示符，导航到下载的统一安装程序文件所在的文件夹，然后使用以下命令从下载内容中提取安装程序文件：MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;提取的文件所在的文件夹路径&gt;。

    示例命令：

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. 在命令提示符下，导航到文件所提取到的文件夹，并运行以下安装命令：
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. 使用任务管理器监视安装进度。 当 CX_THIRDPARTY_SETUP.EXE 的进程不再显示在任务管理器中时，请转到下一步。
1. 验证 C:\thirdparty 是否存在，以及该文件夹是否包含 RRD 库。
1. 返回到下载的统一安装程序所在的文件夹，并运行 MicrosoftAzureSiteRecoveryUnifiedSetup.exe 以完成升级。 