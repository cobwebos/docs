---
title: Azure AD Connect：ADSync 服务帐户 | Microsoft Docs
description: 本主题介绍 ADSync 服务帐户并提供有关此类帐户的最佳做法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9614def5310bdc6fa8c6f37d7cdcc0a5f081a96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360293"
---
# <a name="adsync-service-account"></a>ADSync 服务帐户
Azure AD Connect 会安装一个本地服务用于协调 Active Directory 与 Azure Active Directory 之间的同步。  Microsoft Azure AD 同步同步服务（ADSync）在本地环境中的服务器上运行。  默认会在“快速”安装中设置该服务的凭据，不过，用户也可以根据组织的安全要求自定义凭据。  这些凭据不会用于连接到本地林或 Azure Active Directory。

选择 ADSync 服务帐户是在安装 Azure AD Connect 之前要做出的一项重要规划决策。  安装后尝试更改凭据会导致服务无法启动、无法访问同步数据库，以及无法在连接的目录（Azure 和 AD DS）中进行身份验证。  在还原原始凭据之前无法进行同步。

## <a name="the-default-adsync-service-account"></a>默认的 ADSync 服务帐户

在成员服务器上运行时，AdSync 服务将在虚拟服务帐户 (VSA) 的上下文中运行。  由于产品限制，在域控制器上安装时会创建一个自定义服务帐户。  如果使用“快速”设置时该服务帐户不能满足组织的安全要求，请选择“自定义”选项来部署 Azure AD Connect。  然后选择符合组织要求的服务帐户选项。

>[!NOTE]
>在域控制器上安装时，默认服务帐户的格式为 Domain\AAD_InstallationIdentifier。  此帐户的密码是随机生成的，这为恢复和密码轮换带来了重大挑战。  Microsoft 建议在域控制器上执行初始安装期间自定义服务帐户，以使用独立帐户或组托管服务帐户 (sMSA/gMSA)

|Azure AD Connect 的位置|已创建服务帐户|
|-----|-----|
|成员服务器|NT SERVICE\ADSync|
|域控制器|Domain\AAD_74dc30c01e80（参阅注释）|

## <a name="custom-adsync-service-accounts"></a>自定义 ADSync 服务帐户
Microsoft 建议在虚拟服务帐户或者独立或组托管服务帐户的上下文中运行 ADSync 服务。  域管理员还可以选择创建一个根据具体组织安全要求预配的服务帐户。   若要自定义安装期间使用的服务帐户，请在“快速设置”页上选择如下所示的“自定义”选项。   提供了以下选项：

- 默认帐户– Azure AD Connect 将按上文所述预配服务帐户
- 托管服务帐户–使用管理员预配的独立或组 MSA
- 域帐户–使用管理员预配的域服务帐户

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>诊断 ADSync 服务帐户更改
安装后更改 ADSync 服务的凭据会导致服务无法启动、无法访问同步数据库，以及无法在连接的目录（Azure 和 AD DS）中进行身份验证。  为数据库授予对新 ADSync 服务帐户的访问权限并不足以从此问题恢复。 在还原原始凭据之前无法进行同步。

无法启动时，ADSync 服务将在事件日志中发出错误级消息。  该消息的内容根据使用的是内置数据库 (localdb) 还是完整 SQL 而异。  下面是可能显示的事件日志条目示例。

### <a name="example-1"></a>示例 1

找不到 AdSync 服务加密密钥，或者已重新创建这些密钥。  在更正此问题之前无法进行同步。

解决此问题如果更改了 AdSync 服务登录凭据，则 Microsoft Azure AD 同步加密密钥将不可访问。  如果更改了凭据，请使用“服务”应用程序将登录帐户改回到其原始配置值（例如 NT SERVICE\AdSync），并重启该服务。  这可以使 AdSync 服务立即恢复正常运行。

有关详细信息，请参阅以下[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

### <a name="example-2"></a>示例 2

由于无法与本地数据库 (localdb) 建立连接，该服务无法启动。

解决此问题如果更改了 AdSync 服务登录凭据，则 Microsoft Azure AD 同步服务将失去访问本地数据库提供程序的权限。  如果更改了凭据，请使用“服务”应用程序将登录帐户改回到其原始配置值（例如 NT SERVICE\AdSync），并重启该服务。  这可以使 AdSync 服务立即恢复正常运行。

有关详细信息，请参阅以下[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

其他详细信息：提供程序返回了以下错误信息：
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
