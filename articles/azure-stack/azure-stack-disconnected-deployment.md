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
ms.date: 04/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 49697a57e59b652fed4997d57bc7ae15cc596cf7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151121"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack 集成系统的 Azure 断开连接部署计划决策
在决定[如何将 Azure Stack 集成到混合云环境](azure-stack-connection-models.md)后，可以完成你的 Azure Stack 部署决策。

使用从 Azure 部署断开连接选项，可以在没有 Internet 连接的情况下部署和使用 Azure Stack。 但是，使用断开连接部署，你将受限于一个 AD FS 标识存储和基于容量的计费模型。 

适合选择此选项的情况如下所述：
- 如果存在要求你在未连接到 Internet 的环境中部署 Azure Stack 的安全性或其他限制。
- 如果希望阻止将数据（包括使用情况数据）发送到 Azure。
- 如果希望单纯将 Azure Stack 用作部署到公司 Intranet 的私有云解决方案，并且不考虑在混合方案中使用。

> [!TIP]
> 有时候，这种类型的环境也称为“潜艇方案”。

离线部署并非绝对意味着你以后不能将 Azure Stack 实例连接到 Azure 来实现混合租户 VM 方案。 它只意味着在部署期间不连接到 Azure，或者不希望使用 Azure Active Directory 作为标识存储。

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>在断开连接部署中被削弱或不可用的功能 
Azure Stack 设计为在连接到 Azure 的情况下功能最佳，因此请务必注意，在断开连接模式下，有些功能被削弱或完全不可用。 

|功能|断开连接模式的影响|
|-----|-----|
|VM 部署（带有用于配置 VM 后期部署的 DSC 扩展）|被削弱 - DSC 扩展从 Internet 查找最新 WMF。|
|VM 部署（带有用于运行 Docker 命令的 Docker 扩展）|受到影响-Docker 将检查 Internet 上的最新版本，并且此检查将失败。|
|Azure Stack 门户中的文档链接|不可用 – 链接，例如提供反馈，帮助，快速入门，等使用 Internet URL 将不起作用。|
|引用联机修正指南的警报修正/缓解|不可用 – 任何警报修正链接的使用 Internet URL 将不起作用。|
|应用商店联合 – 选择并直接从 Azure 应用商店添加库程序包的功能|受到影响-在断开连接 （不带任何 internet 连接） 模式下部署 Azure 堆栈时，你无法通过 Azure 堆栈门户下载的应用商店项。 但是，你可以使用[marketplace 联合工具](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)下载到计算机具有 internet 连接的应用商店项，然后将它们传输到 Azure 堆栈环境。|
|使用 Azure Active Directory 联合身份验证帐户管理 Azure Stack 部署|不可用 – 此功能要求连接至 Azure。 必须改用具有本地 Active Directory 实例的 AD FS。|
|应用服务|受到影响-WebApps 可能需要 Internet 访问，为更新的内容。|
|命令行接口 (CLI)|受到影响-CLI 已减少在身份验证和配置的服务主体方面的功能。|
|Visual Studio – Cloud discovery|受到影响-Cloud Discovery 或者发现不同的云，否则将不会工作。|
|Visual Studio-AD FS|受到影响-仅 Visual Studio Enterprise 支持 AD FS。
遥测|不可用 – 作为依赖于遥测数据也任何第三方库包 Azure 堆栈的遥测数据。|
|证书|需要 HTTPS 的上下文中的证书吊销列表 (CRL) 和联机证书状态协议 (OSCP) 服务不可用 – Internet 连接。|
|Key-Vault|受到影响-针对密钥保管库的常见用例是让一个应用程序读取在运行时的机密。 因此，应用程序需要目录中的一个服务主体。 在 Azure Active Directory 中，默认情况下允许常规用户（非管理员）添加服务主体。 在 AD 中（使用 ADFS）不允许。 这妨碍了端对端体验，因为用户必须始终通过目录管理员来添加任何应用程序。| 

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 产品页。
- 有关 Azure Stack 集成系统的路线图和上市区域的信息，请参阅白皮书：[Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)（Azure Stack：Azure 的扩展）。 
- 若要了解有关 Microsoft Azure 堆栈打包和定价的详细信息[下载.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)