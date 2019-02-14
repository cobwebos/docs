---
title: Azure AD Connect：什么是 ADConnectivityTool PowerShell 模块 | Microsoft Docs
description: 本文档介绍了新的 ADConnectivity PowerShell 模块
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f8b70a4b098cc7266042499300235588e5303f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164305"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>什么是 ADConnectivityTools PowerShell 模块？

ADConnectivity 工具是一个 PowerShell 模块，可在以下任一情形下使用：

- 在安装过程中，当网络连接问题阻止成功验证用户在向导中提供的 Active Directory 凭据时。
- 在 PowerShell 会话中调用函数的用户安装后。

该工具位于：C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1 

## <a name="adconnectivitytool-during-installation"></a>安装过程中的 ADConnectivityTool

在“连接目录”页的 Azure AD Connect 向导中，如果发生网络问题，ADConnectivityTool 将自动使用其函数之一来确定发生的问题。  以下任何一种都可以被视为网络问题：

- 用户提供的林名称键入错误，或该林不存在 
- 与用户提供的林相关联的域控制器中已关闭 UDP 端口 389
- 在“AD 林帐户”窗口中提供的凭据无权检索与目标林关联的域控制器
- 与用户提供的林相关联的域控制器中已关闭任一 TCP 端口 53、88 或 389 
- UDP 389 和 TCP 端口（或多个端口）都已关闭
- 无法为提供的林和\或其关联的域控制器解析 DNS

每当发现任何这些问题时，AADConnect 向导中都会显示相关的错误消息：


![错误](media/how-to-connect-adconnectivitytools/error1.png)

例如，当我们尝试在“连接目录”屏幕上添加目录时，Azure AD Connect 需要对此进行验证，并应能够通过端口 389 与域控制器进行通信。  如果不能，我们将看到上面屏幕截图中显示的错误。  

Azure AD Connect 实际上正在后台调用 `Start-NetworkConnectivityDiagnosisTools` 函数。  当凭据验证由于网络连接问题而失败时，将调用此函数。

最后，只要从向导调用工具，就会生成详细的日志文件。 该日志位于 C:\ProgramData\AADConnect\ADConnectivityTool-<date>-<time>.log

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools 安装后
安装 Azure AD Connect 后，可以使用 ADConnectivityTools PowerShell 模块中的任何函数。  

有关这些函数的参考信息，请参阅 [ADConnectivityTools 参考](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

我们将调用此函数，因为将 ADConnectivityTool.psm1 导入到 PowerShell 之后，就只能手动调用该函数。 

此函数执行与 Azure AD Connect 向导相同的逻辑，以验证提供的 AD 凭据。  此外，它还提供了有关问题和建议解决方案的更详细的解释。 

连接验证包括以下步骤：
-   获取域 FQDN（完全限制的域名）对象
-   对其进行验证，如果用户选择“创建新的 AD 帐户”，则这些凭据属于“企业管理员”组
-   获取林 FQDN 对象
-   确认至少有一个与先前获取的林 FQDN 对象关联的域是可访问的
-   验证林函数级别是否是 Windows Server 2003 或更高版本。

如果所有这些操作都成功执行，用户将能够添加目录。

如果用户在问题解决后运行此函数（或者根本不存在任何问题），则输出将指示用户返回 Azure AD Connect 向导并尝试再次插入凭据。



## <a name="next-steps"></a>后续步骤
- [Azure AD Connect：帐户和权限](reference-connect-accounts-permissions.md)
- [快速安装](how-to-connect-install-express.md)
- [自定义安装](how-to-connect-install-custom.md)
- [ADConnectivityTools 参考](reference-connect-adconnectivitytools.md)

