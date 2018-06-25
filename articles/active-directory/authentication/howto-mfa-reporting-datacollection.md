---
title: Microsoft Azure 多重身份验证用户数据收集
description: Azure 多重身份验证借助哪些信息来对用户进行身份验证？
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: article
ms.date: 05/01/2018
ms.openlocfilehash: 2281a35d1616aa88b0c646fb96cb9f95c3272536
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264362"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Microsoft Azure 多重身份验证用户数据收集

本文档介绍在需要删除 Azure 多重身份验证服务器 (MFA) 和 Azure MFA（基于云的）收集的用户信息时，如何查找这些信息。

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>收集的信息

MFA 服务器、NPS 扩展和 Windows Server 2016 Azure MFA AD FS 适配器收集以下信息，并将其存储 90 天。

身份验证尝试（用于报告和故障排除）：

- Timestamp
- 用户名
- 名字
- 姓氏
- 电子邮件地址
- 用户组
- 身份验证方法（电话呼叫、短信、移动应用、OATH 令牌）。
- 电话呼叫模式（标准、PIN）
- 短信方向（单向、双向）
- 短信模式（OTP、OTP + PIN）
- 移动应用模式（标准、PIN）
- OATH 令牌模式（标准、PIN）
- 身份验证类型
- 应用程序名称
- 主要来电国家/地区代码
- 主要来电电话号码
- 主调来电分机号
- 身份验证的主要来电号码
- 主要呼叫结果
- 备用来电国家/地区代码
- 备用来电电话号码
- 备用来电分机号
- 身份验证的备用来电号码
- 备用呼叫结果
- 身份验证总计
- 结果总计
- 结果
- 已获得验证
- 结果
- 发起方 IP 地址
- 设备
- 设备令牌
- 设备类型
- 移动应用版本
- OS 版本。
- 结果
- 已使用“检查通知”

激活（尝试在 Microsoft Authenticator 移动应用中激活帐户）：
- 用户名
- 帐户名
- Timestamp
- 获取激活码结果
- 激活成功
- 激活错误
- 激活状态结果
- 设备名称
- 设备类型
- 应用版本
- 已启用 OATH 令牌

阻止（用于确定阻止状态和报告）：

- 阻止时间戳
- 阻止者用户名
- 用户名
- 国家/地区代码
- 电话号码
- 带格式的电话号码
- 分机
- 清理分机号
- 已阻止
- 阻止原因
- 完成时间戳
- 完成原因
- 帐户锁定
- 欺诈警报
- 未阻止欺诈警报
- 语言

绕过（用于报告）：

- 绕过时间戳
- 绕过秒
- 绕过者用户名
- 用户名
- 国家/地区代码
- 电话号码
- 带格式的电话号码
- 分机
- 清理分机号
- 绕过原因
- 完成时间戳
- 完成原因
- 已使用绕过

更改（用于同步用户对 MFA 服务器或 AAD 的更改）：

- 更改时间戳
- 用户名
- 新的国家/地区代码
- 新电话号码
- 新分机号
- 新的备用国家/地区代码
- 新的备用电话号码
- 新的备用分机号
- 新 PIN
- 需要更改 PIN
- 旧设备令牌
- 新设备令牌

## <a name="gather-data-from-mfa-server"></a>从 MFA 服务器收集数据

在 MFA 服务器 8.0 或更高版本中，管理员可以使用以下过程导出用户的所有数据：

- 登录到 MFA 服务器，导航到“用户”选项卡，选择相关的用户，然后单击“编辑”按钮。 拍摄每个选项卡的屏幕截图 (Alt-PrtScn)，为用户提供其当前 MFA 设置。
- 在 MFA 服务器的命令行中运行以下命令（请根据安装更改路径 (`C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>`)），以生成 JSON 格式的文件。
- 管理员也可以使用 Web 服务 SDK GetUserGdpr 操作作为选项，导出针对给定用户收集的所有 MFA 云服务信息，或将其合并到更大的报告解决方案。
- 在 `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` 和所有备份中搜索 “<username>”（包括引号），查找要添加或更改的用户记录的所有实例。
   - 可以通过在 MFA 服务器 UX 的“日志记录”部分的“日志文件”选项卡中取消选中“记录用户更改”，来限制（但无法消除）这些记录。
   - 如果已配置 syslog，并且在 MFA 服务器 UX 的“日志记录”部分的“Syslog”选项卡中选中了“记录用户更改”，则可以从 syslog 收集日志项。
- MultiFactorAuthSvc.log 和其他 MFA 服务器日志文件中出现的、与身份验证尝试相关的其他用户名被视为有效，并且与使用 MultiFactorAuthGdpr.exe 导出功能或 Web 服务 SDK GetUserGdpr 提供的信息重复。

## <a name="delete-data-from-mfa-server"></a>从 MFA 服务器删除数据

在 MFA 服务器的命令行中运行以下命令（请根据安装更改路径 (`C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>`)），以删除针对此用户收集的所有 MFA 云服务信息。

- 导出中包含的数据将实时删除，但完全删除有效或重复数据最长可能需要 30 天。
- 管理员也可以使用 Web 服务 SDK DeleteUserGdpr 操作作为选项，删除针对给定用户收集的所有 MFA 云服务信息，或将其合并到更大的报告解决方案。

## <a name="gather-data-from-nps-extension"></a>从 NPS 扩展收集数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出导出请求。

- MFA 信息将包含在导出中，可能需要几个小时甚至几天才能完成导出。
- AzureMfa/AuthN/AuthNOptCh、AzureMfa/AuthZ/AuthZAdminCh 和 AzureMfa/AuthZ/AuthZOptCh 事件日志中出现的用户名被视为有效，并与导出中提供的信息重复。

## <a name="delete-data-from-nps-extension"></a>从 NPS 扩展删除数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出“帐户关闭”请求，以删除针对此用户收集的所有 MFA 云服务信息。

- 完全删除这些数据最长可能需要 30 天。

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>从 Windows Server 2016 Azure MFA AD FS 适配器收集数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出导出请求。 

- MFA 信息将包含在导出中，可能需要几个小时甚至几天才能完成导出。
- AD FS 跟踪/调试事件日志（如果已启用）中出现的用户名被视为有效，并与导出中提供的信息重复。

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>从 Windows Server 2016 Azure MFA AD FS 适配器删除数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出“帐户关闭”请求，以删除针对此用户收集的所有 MFA 云服务信息。

- 完全删除这些数据最长可能需要 30 天。

## <a name="gather-data-for-azure-mfa"></a>收集 Azure MFA 的数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出导出请求。

- MFA 信息将包含在导出中，可能需要几个小时甚至几天才能完成导出。

## <a name="delete-data-for-azure-mfa"></a>删除 Azure MFA 的数据

使用 [Microsoft 隐私门户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview)发出“帐户关闭”请求，以删除针对此用户收集的所有 MFA 云服务信息。

- 完全删除这些数据最长可能需要 30 天。

## <a name="next-steps"></a>后续步骤

[MFA 服务器报告](howto-mfa-reporting.md)
