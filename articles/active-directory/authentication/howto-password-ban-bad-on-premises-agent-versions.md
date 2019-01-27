---
title: 本地 Azure AD 密码保护代理版本发行历史记录
description: 记录了版本发行和行为更改历史记录
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 1d27aa46148e0169909a7aaf05baaac18b050a34
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423657"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>预览版：Azure AD 密码保护代理版本历史记录

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

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
