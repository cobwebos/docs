---
title: 以 gMSA 帐户身份运行 Azure Service Fabric 服务 | Microsoft Docs
description: 了解如何在 Service Fabric Windows 独立群集上以 gMSA 身份运行服务。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: cd36d52df24610af1d2cb2d9e6e41f33bb7ea5fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>以组托管服务帐户身份运行服务
使用 Azure Service Fabric，可以保护群集中以不同用户帐户运行的应用程序。 即使在共享托管环境中以不同帐户身份运行应用程序，也可确保运行的应用程序彼此更安全。 默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 对于 Windows Server 独立群集，可使用 RunAs 策略以[组托管服务帐户 (gMSA)](service-fabric-run-service-as-ad-user-or-group.md) 或 Active Directory 用户或组的身份来运行服务。 注意：这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。 使用 gMSA 时，没有密码或加密密码存储在 `Application Manifest` 中。

以下示例演示如何创建一个名为 *svc-Test$* 的 gMSA 帐户；如何将该托管服务帐户部署到群集节点；以及如何配置用户主体。

先决条件：
- 域需要 KDS 根密钥。
- 域必须位于 Windows Server 2012 或更高功能级别上。

1. 让 Active Directory 域管理员使用 `New-ADServiceAccount` commandlet 创建组托管服务帐户，并确保 `PrincipalsAllowedToRetrieveManagedPassword` 包括所有 service fabric 群集节点。 `AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必须是唯一的。

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 在每个 Service Fabric 群集节点（例如，`SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`）上，安装并测试 gMSA。
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 配置用户主体，并配置 RunAsPolicy 以引用用户。
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> 如果将 RunAs 策略应用到服务，且服务清单使用 HTTP 协议声明终结点资源，则必须指定 SecurityAccessPolicy。  有关详细信息，请参阅[为 HTTP 和 HTTPS 终结点分配安全访问策略](service-fabric-assign-policy-to-endpoint.md)。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
有关后续步骤，请阅读以下文章：
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
