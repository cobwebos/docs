---
title: Azure Stack 集成系统的 Azure 断开连接部署决策 | Microsoft Docs
description: 确定多节点 Azure Stack Azure 连接部署的部署计划决策。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 4481bcf7a794423f98f45e4a21a139dbe4c32b4f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210775"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack 集成系统的 Azure 断开连接部署计划决策
您已决定后[如何将将 Azure Stack 集成到混合云环境](azure-stack-connection-models.md)，可以完成 Azure Stack 部署决策。

无需连接到 Internet 即可部署和使用 Azure Stack。 但是，使用断开连接部署，你将受限于一个 AD FS 标识存储和基于容量的计费模型。 多租户模式需要 Azure Active Directory (Azure AD) 的使用，因为断开连接部署不支持多租户模式。 

适合选择此选项的情况如下所述：
- 如果存在要求你在未连接到 Internet 的环境中部署 Azure Stack 的安全性或其他限制。
- 如果希望阻止将数据（包括使用情况数据）发送到 Azure。
- 如果希望单纯将 Azure Stack 用作部署到公司 Intranet 的私有云解决方案，并且不考虑在混合方案中使用。

> [!TIP]
> 有时，此类型的环境也称为*潜艇方案*。

断开连接的部署不会限制你稍后将 Azure Stack 实例连接到 Azure 的混合租户 VM 方案。 这意味着您在部署期间未连接到 Azure 或不想使用 Azure AD 作为标识存储。

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>在断开连接部署中被削弱或不可用的功能 
Azure Stack 设计为在连接到 Azure 的情况下功能最佳，因此请务必注意，在断开连接模式下，有些功能被削弱或完全不可用。 

|Feature|断开连接模式的影响|
|-----|-----|
|VM 部署（带有用于配置 VM 后期部署的 DSC 扩展）|被削弱 - DSC 扩展从 Internet 查找最新 WMF。|
|VM 部署（带有用于运行 Docker 命令的 Docker 扩展）|被削弱-Docker 将检查 Internet 以确定最新版本，并且此检查将失败。|
|Azure Stack 门户中的文档链接|不可用-链接，例如提供反馈，帮助，快速入门中，Internet URL 不起作用的使用。|
|引用联机修正指南的警报修正/缓解|不可用 – 任何警报修正链接的使用 Internet URL 不起作用。|
|Marketplace – 选择并直接从 Azure Marketplace 添加库包的功能|被削弱-在 Azure Stack 部署在断开连接模式下 （不带任何 Internet 连接） 时，不能使用 Azure Stack 门户下载 marketplace 项。 但是，可以使用[市场联合工具](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item)将市场项下载到有 Internet 连接的计算机，然后再将这些项转移到 Azure Stack 环境。|
|使用 Azure Active Directory 联合身份验证帐户管理 Azure Stack 部署|不可用-此功能要求连接到 Azure。 必须改用具有本地 Active Directory 实例的 AD FS。|
|应用服务|被损坏 - WebApps 可能需要访问 Internet 以获取更新的内容。|
|命令行接口 (CLI)|被削弱-CLI 减少了在身份验证和预配服务主体的方面的功能。|
|Visual Studio – Cloud discovery|被削弱-Cloud Discovery 将发现不同的云，或将无法工作。|
|Visual Studio – AD FS|被削弱-只有 Visual Studio Enterprise 支持 AD FS。
遥测|不可用-适用于 Azure Stack 作为依赖于遥测数据以及任何第三方库包的遥测数据。|
|证书|需要 HTTPS 的上下文中的证书吊销列表 (CRL) 和联机证书状态协议 (OSCP) 服务不可用-Internet 连接。|
|Key-Vault|被削弱-Key Vault 的一个常见用例是让读取机密在运行时应用程序。 因此，应用程序需要目录中的一个服务主体。 在 Azure Active Directory 中，默认情况下允许常规用户（非管理员）添加服务主体。 在 AD 中（使用 ADFS）不允许。 这妨碍了端对端体验，因为用户必须始终通过目录管理员来添加任何应用程序。| 

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 产品页。
- 有关 Azure Stack 集成系统的路线图和上市区域的信息，请参阅白皮书：[Azure Stack：Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 打包和定价的详细信息[下载此.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)
