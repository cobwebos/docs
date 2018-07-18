---
title: 以 AD 用户或组身份运行 Azure Service Fabric 服务 | Microsoft Docs
description: 了解如何在 Service Fabric Windows 独立群集上以 Active Directory 用户或组的身份运行服务。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 624cb54778950a8a973bcf6cefddf66cba2bd0fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206986"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>以 Active Directory 用户或组身份运行服务
在 Windows Server 独立群集上，可以使用 RunAs 策略以 Active Directory 用户或组的身份来运行服务。  默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 即使在共享托管环境中以不同帐户身份运行应用程序，也可确保运行的应用程序彼此更安全。 注意：这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。  还可以采用[组托管服务帐户 (gMSA)](service-fabric-run-service-as-gmsa.md) 的身份来运行服务。

通过使用域用户或组，可以访问域中已被授予权限的其他资源（例如文件共享）。

下面的示例演示名为 *TestUser* 的 Active Directory 用户，其域密码使用名为 *MyCert* 的证书进行了加密。 可以使用 `Invoke-ServiceFabricEncryptText` PowerShell 命令创建密码文本。 请参阅[管理 Service Fabric 应用程序中的机密](service-fabric-application-secret-management.md)获取详细信息。

用于解密密码的证书私钥必须使用带外方法（在 Azure 中通过 Azure 资源管理器）部署到本地计算机。 这样，当 Service Fabric 将服务包部署到计算机时，便能够解密密码和用户名，向 Active Directory 进行身份验证以使用这些凭据运行。

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> 如果将 RunAs 策略应用到服务，且服务清单声明使用 HTTP 协议的终结点资源，则还必须指定 SecurityAccessPolicy。  有关详细信息，请参阅[为 HTTP 和 HTTPS 终结点分配安全访问策略](service-fabric-assign-policy-to-endpoint.md)。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
有关后续步骤，请阅读以下文章：
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
