---
title: Azure AD Connect：ADSync 服务帐户 |Microsoft Docs
description: 本主题描述了 ADSync 服务帐户，并提供有关帐户的最佳做法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478714"
---
# <a name="adsync-service-account"></a>ADSync 服务帐户
Azure AD Connect 将安装在本地服务负责协调 Active Directory 和 Azure Active Directory 之间的同步。  Microsoft Azure AD Sync 同步服务 (ADSync) 在你的本地环境中的服务器上运行。  该服务的凭据在 Express 安装中默认情况下设置，但可以自定义以满足你组织安全要求。  这些凭据不用于连接到本地林或 Azure Active Directory。

选择 ADSync 服务帐户是一项重要的规划决策，以便在安装 Azure AD Connect 之前。  任何尝试更改的凭据之后安装将导致服务无法启动，, 无法访问的同步数据库，但未通过使用你已连接的目录 （Azure 和 AD DS） 进行身份验证。  还原原始凭据之前，会发生不同步。

## <a name="the-default-adsync-service-account"></a>默认 ADSync 服务帐户

当成员服务器上运行，AdSync 服务运行的虚拟服务帐户 (VSA) 的上下文中。  由于产品存在的限制，域控制器上安装时创建一个自定义服务帐户。  如果快速设置服务帐户不符合你组织安全要求，请通过选择自定义选项来部署 Azure AD Connect。  然后选择符合组织的要求的服务帐户选项。

>[!NOTE]
>在域控制器上安装时的默认服务帐户的 Domain\AAD_InstallationIdentifier 形式。  此帐户的密码随机生成的并显示恢复和密码轮换的重大挑战。  Microsoft 建议在使用任一独立或组托管服务帐户的域控制器上的初始安装过程中自定义的服务帐户 (sMSA / gMSA)

|Azure AD Connect 位置|创建服务帐户|
|-----|-----|
|成员服务器|NT SERVICE\ADSync|
|域控制器|Domain\AAD_74dc30c01e80 (see note)|

## <a name="custom-adsync-service-accounts"></a>自定义 ADSync 服务帐户
Microsoft 建议运行 ADSync 服务的虚拟服务帐户或独立的上下文中或组托管服务帐户。  您的域管理员还可以选择创建预配以满足你特定组织安全要求的服务帐户。   若要自定义安装过程中使用的服务帐户，请选择以下快速设置页面上的自定义选项。   提供了以下选项：

- 默认帐户，Azure AD Connect 将预配的服务帐户，如上文所述
- 托管服务帐户 – 使用独立或组 MSA 由您的管理员预配
- 域帐户，使用域服务预配由您的管理员帐户

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>诊断 ADSync 服务帐户更改
无法访问的同步数据库，但未通过使用你已连接的目录 （Azure 和 AD DS） 进行身份验证后安装将导致服务无法启动，请, 更改 ADSync 服务的凭据。  授予数据库访问新的 ADSync 服务帐户权限不足，无法恢复从该问题。 还原原始凭据之前，会发生不同步。

ADSync 服务将错误级别消息发出到事件日志时无法启动。  消息的内容根据完整的 SQL 的内置数据库 (localdb) 是否正在使用会有所不同。  以下是可能出现的事件日志条目的示例。

### <a name="example-1"></a>示例 1

AdSync 服务加密密钥未找到，并且已重新创建。  解决此问题之前，不会发生同步。

故障排除此问题 Microsoft Azure AD 同步加密密钥将变为不可访问，如果更改的 AdSync 服务登录凭据。  如果已更改的凭据，使用服务应用程序的登录帐户更改回其最初配置的值 （例如。 NT SERVICE\AdSync) 并重新启动服务。  这将立即还原 AdSync 服务的正确的操作。

请参阅以下[一文](https://go.microsoft.com/fwlink/?linkid=2086764)有关进一步信息。

### <a name="example-2"></a>示例 2

该服务无法启动，因为无法建立到本地数据库 (localdb) 的连接。

故障排除此问题 Microsoft Azure AD Sync 服务将会丢失访问本地数据库提供程序，如果更改的 AdSync 服务登录凭据的权限。  如果已更改的凭据使用服务应用程序的登录帐户更改回其最初配置的值 （例如。 NT SERVICE\AdSync) 并重新启动服务。  这将立即还原 AdSync 服务的正确的操作。

请参阅以下[一文](https://go.microsoft.com/fwlink/?linkid=2086764)有关进一步信息。

提供程序返回以下错误信息的其他详细信息：
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。