---
title: "Azure Service Fabric 安全性清单 | Microsoft Docs"
description: "本文介绍了一组 Azure Service Fabric 安全性清单。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: d0441f1e96e94352d4112ec387058b47074d8b0b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="azure-service-fabric-security-checklist"></a>Azure Service Fabric 安全性清单
本文提供的易用清单有助于保护 Azure Service Fabric 环境。

## <a name="introduction"></a>介绍
Azure Service Fabric 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。 Service Fabric 还解决了开发和管理云应用程序中的重大难题。 开发人员和管理员不需解决复杂的基础结构问题，只需专注于实现苛刻的任务关键型工作负荷，即那些可缩放、可靠且易于管理的工作负荷。

## <a name="checklist"></a>清单
以下清单有助于确保在管理和配置 Azure Service Fabric 解决方案过程中未忽略任何重要问题。


|清单类别| 说明 |
| ------------ | -------- |
|[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>访问控制可让群集管理员针对不同的用户组限制特定群集操作的访问权限，使群集更加安全。</li><li>管理员对管理功能（包括读取/写入功能）拥有完全访问权限。 </li><li>   默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。</li></ul>|
|[X.509 证书和 Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>运行生产工作负荷的群集中使用的[证书](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates)应使用正确配置的 Windows Server 证书服务进行创建，或者从已批准的[证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 获取。</li><li>切勿在生产环境中使用通过 [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx) 等工具创建的[临时或测试证书](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development)。 </li><li>可以使用[自签名证书](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security)，但只应使用它来测试群集，而不应在生产环境中使用。</li></ul>|
|[群集安全性](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>群集安全性方案包括节点到节点安全性、客户端到节点安全性、[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles)。</li></ul>|
|[群集身份验证](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>在群集联合的情况下对[节点到节点的通信](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md)进行身份验证。 </li></ul>|
|[服务器身份验证](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>在管理客户端对[群集管理终结点](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-portal)进行身份验证。</li></ul>|
|[应用程序安全性](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>加密和解密应用程序配置值。</li><li> 在复制期间跨节点加密数据。</li></ul>|
|[群集证书](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>需要使用此证书来保护群集节点之间的通信。</li><li>  在 Thumbprint 部分设置主证书的指纹，在 ThumbprintSecondary 变量中设置辅助证书的指纹。</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>当客户端尝试连接到此群集时，系统会向客户端提供此证书。 可以使用两个不同的服务器证书（一个主证书和一个辅助证书）进行升级。</li></ul>|
|ClientCertificateThumbprints| <ul><li>这是需要在经过身份验证的客户端上安装的一组证书。 </li></ul>|
|ClientCertificateCommonNames| <ul><li>在 CertificateCommonName 中设置第一个客户端证书的通用名称。 CertificateIssuerThumbprint 是此证书的颁发者的指纹。 </li></ul>|
|ReverseProxyCertificate| <ul><li>如果想要保护[反向代理](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy)，可以选择指定此证书。 </li></ul>|
|密钥保管库| <ul><li>用于管理 Azure 中 Service Fabric 群集的证书。  </li></ul>|


## <a name="next-steps"></a>后续步骤
- [Service Fabric 群集升级过程和用户预期](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-upgrade)
- [在 Visual Studio 中管理 Service Fabric 应用程序](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-manage-application-in-visual-studio)。
- [Service Fabric 运行状况模型简介](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-health-introduction)。
