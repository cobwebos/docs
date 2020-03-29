---
title: 密码保护代理发布历史记录 - Azure 活动目录
description: 记录了版本发行和行为更改历史记录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847843"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD 密码保护代理版本历史记录

## <a name="121250"></a>1.2.125.0

发布日期： 2019/3/22

* 修复事件日志消息中的轻微拼写错误
* 将 EULA 协议更新为最终常规可用性版本

> [!NOTE]
> 生成 1.2.125.0 是通用版本。 再次感谢您向大家提供的产品反馈！

## <a name="121160"></a>1.2.116.0

发布日期： 2019/3/13

* 获取 AzureAD 密码保护代理和获取 AzureAD 密码保护 DC 代理 cmdlet 现在报告软件版本和当前 Azure 租户具有以下限制：
  * 软件版本和 Azure 租户数据仅适用于运行版本 1.2.116.0 或更高版本的 DC 代理和代理。
  * 在重新注册（或续订）代理或林之前，不得报告 Azure 租户数据。
* 代理服务现在要求安装 .NET 4.7。
  * .NET 4.7 应该已安装在完全更新的 Windows 服务器上。 如果不是这样，请下载并运行[在 Windows 的 .NET 框架 4.7 脱机安装程序中找到的安装程序](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)。
  * 在服务器核心系统上，可能需要将 /q 标志传递给 .NET 4.7 安装程序才能成功。
* 代理服务现在支持自动升级。 自动升级使用与代理服务并行安装的 Microsoft Azure AD 连接代理更新程序服务。 默认情况下，自动升级处于打开状态。
* 可以使用设置-AzureAD 密码保护代理配置 cmdlet 启用或禁用自动升级。 可以使用获取 AzureAD 密码保护代理配置 cmdlet 查询当前设置。
* DC 代理服务的服务二进制文件已重命名为 AzureADPassword 保护 DCAgent.exe。
* 代理服务的服务二进制文件已重命名为 AzureADPassword 保护代理.exe。 如果第三方防火墙正在使用中，则可能需要相应地修改防火墙规则。
  * 注：如果以前的代理安装中使用了 http 代理配置文件，则需要在此升级后将其重命名（从*代理服务.exe.config*到*AzureADPassword保护代理.exe.config）。*
* 所有时间限制的功能检查都已从 DC 代理中删除。
* 小错误修复和日志记录改进。

## <a name="12650"></a>1.2.65.0

发布日期： 2019/2/1

更改：

* Server Core 现在支持 DC 代理和代理服务。 最小操作系统要求与之前保持不变：用于 DC 代理的 Windows Server 2012 和代理的 Windows Server 2012 R2。
* Register-AzureADPasswordProtectionProxy 和 Register-AzureADPasswordProtectionForest cmdlet 现在支持基于设备代码的 Azure 身份验证模式。
* Get-AzureADPasswordProtectionDCAgent cmdlet 会忽略损坏和/或无效的服务连接点。 这修复了域控制器有时会在输出中多次显示的 bug。
* Get-AzureADPasswordProtectionSummaryReport cmdlet 会忽略损坏和/或无效的服务连接点。 这修复了域控制器有时会在输出中多次显示的 bug。
* 代理 powershell 模块现在是通过 %ProgramFiles%\WindowsPowerShell\Modules 注册。 计算机的 PSModulePath 环境变量不再遭修改。
* 新增了 Get-AzureADPasswordProtectionProxy cmdlet，有助于发现林或域中的已注册代理。
* DC 代理使用 sysvol 共享中的新文件夹来复制密码策略和其他文件。

   旧文件夹位置：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   新文件夹位置：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   （此更改是为了避免误报的“孤立 GPO”警告。）

   > [!NOTE]
   > 旧文件夹和新文件夹之间不会迁移或共享任何数据。 旧版 DC 代理将继续使用旧位置，除非升级到此版本或更高版本。 当所有 DC 代理运行版本 1.2.65.0 或更高版本后，可以手动删除旧 sysvol 文件夹。

* DC 代理和代理服务现在会检测并删除各自服务连接点的受损副本。
* 每个 DC 代理会定期删除自己域中的受损和过时服务连接点，即 DC 代理和代理服务连接点。 如果检测信号时间戳已过去 7 天，DC 代理和代理服务连接点被视为过时。
* DC 代理现在会根据需要续订林证书。
* 代理服务现在会根据需要续订代理证书。
* 更新密码验证算法：在验证密码前，结合使用全局禁止密码列表和客户专用禁止密码列表（若已配置的话）。 如果给定密码包含全局禁止密码列表和客户专用禁止密码列表中的令牌，现在可能会遭拒（失败或仅审核）。 事件日志文档已更新，其中反映了这一点；请参阅[监视 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)。
* 性能和可靠性修复
* 改进了日志记录

> [!WARNING]
> 限时功能：此版本 (1.2.65.0) 中的 DC 代理服务将自 2019 年 9 月 1 日起停止处理密码验证请求。  旧版本（见以下列表）中的 DC 代理服务将自 2019 年 7 月 1 日起停止处理。 所有版本中的 DC 代理服务将在截止日期前提前两个月将 10021 个事件记录到管理事件日志中。 即将推出的 GA 版本中将删除所有时间限制。 代理服务在所有版本中都不限时，但仍应升级到最新版本，以便充分利用所有后续 bug 修复和其他改进。

## <a name="12250"></a>1.2.25.0

发行日期：2018 年 11 月 1 日

修复项：

* DC 代理和代理服务应该不再因证书信任失败而失败。
* DC 代理和代理服务为符合 FIPS 标准的计算机提供了其他修补程序。
* 代理服务现在可以在只支持 TLS 1.2 的网络环境中正常工作。
* 进行了微小的性能和稳定性修复
* 改进了日志记录

更改：

* 代理服务所需的最低 OS 级别现在是 Windows Server 2012 R2。 DC 代理服务所需的最低 OS 级别仍然是 Windows Server 2012。
* 代理服务现在需要 .NET 版本 4.6.2。
* 密码验证算法使用扩展的字符规范化表。 这可能导致在以前版本中接受的密码被拒绝。

## <a name="12100"></a>1.2.10.0

发行日期：2018 年 8 月 17 日

修复项：

* Register-AzureADPasswordProtectionProxy 和 Register-AzureADPasswordProtectionForest 现在支持多重身份验证
* Register-AzureADPasswordProtectionProxy 要求域中具有 WS2012 或更高版本的域控制器以避免加密错误。
* DC 代理服务在启动时可以更可靠地从 Azure 请求新的密码策略。
* 如果需要，DC 代理服务将每隔一小时从 Azure 请求新的密码策略，但现在将按随机选择的开始时间执行此操作。
* 如果在服务器提升为副本之前已安装于服务器上，DC 代理服务在新 DC 播发中将不再会导致无限期延迟。
* DC 代理服务现在将遵循“在 Windows Server Active Directory 上启用密码保护”配置设置
* 升级到将来的版本时，DC 代理和代理服务器安装程序现在都支持就地升级。

> [!WARNING]
> 从版本 1.1.10.3 进行就地升级不受支持并且会导致安装错误。 若要升级到版本 1.2.10 或更高版本，必须先完全卸载 DC 代理和代理服务器服务软件，然后再从头安装新版本。 必须重新注册 Azure AD 密码保护代理服务器服务。  不需要重新注册林。

> [!NOTE]
> DC 代理软件的就地升级将需要重启。

* DC 代理和代理服务器服务现在支持在配置为仅使用符合 FIPS 标准的算法的服务器上运行。
* 进行了微小的性能和稳定性修复
* 改进了日志记录

## <a name="11103"></a>1.1.10.3

发行日期：2018 年 6 月 15 日

初始的公开预览版

## <a name="next-steps"></a>后续步骤

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
