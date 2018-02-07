---
title: "有关 Azure 堆栈的断开连接的 azure 部署决策集成系统 |Microsoft 文档"
description: "确定部署规划 Azure 堆栈 Azure 连接的多节点部署的决策。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: afea4b23aca322321b4096cdfdc9d30b087a2f30
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure 断开连接的部署规划 Azure 堆栈的决策集成系统
您已决定后[如何将将 Azure 堆栈集成到你的混合云环境](azure-stack-deployment-decisions.md)，然后才能最终完成 Azure 堆栈的部署决策。

使用断开连接从 Azure 的部署选项，可以部署和使用 Azure 堆栈未连接到 Internet。 但是，通过断开连接的部署，你仅限于一个 AD FS 标识存储区和基于容量的计费模型。 

如果选择此选项你：
- 具有安全或要求你在未连接到 Internet 的环境中部署 Azure 堆栈的其他限制。
- 要阻止 （包括使用情况数据） 的数据被发送到 Azure。
- 想要使用 Azure 堆栈单纯用作私有云解决方案部署到企业 Intranet，并不感兴趣的混合方案。

> [!TIP]
> 有时，此类型的环境是也称为"潜水艇方案"。

断开连接的部署并不严格意味着，你不能更高版本 Azure 堆栈实例为连接到 Azure 的混合租户 VM 方案。 这意味着你在部署过程中没有与 Azure 的连接，或不想使用 Azure Active Directory 作为标识存储。 但是，如果你想要连接到 Azure 后部署，而不考虑你想要使用作为标识存储，则应选择连接到 Azure 的部署选项。 

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>障碍的用户或中不可用的断开连接部署的功能 
Azure 堆栈旨在时连接到 Azure，因此务必要注意有障碍的用户或完全不可用在断开连接模式下的某些功能和功能的工作效率最佳。 

|功能|处于断开模式的影响|
|-----|-----|
|使用 DSC 扩展配置 VM 后期部署的 VM 部署|受到影响-DSC 扩展查找 Internet 到最新的 WMF。|
|使用 Docker 扩展运行 Docker 命令的 VM 部署|受到影响-Docker 将检查 Internet 上的最新版本，并且此检查将失败。|
|在 Azure 门户中堆栈的文档链接|不可用 – 链接，例如提供反馈，帮助，快速入门，等使用 Internet URL 将不起作用。|
|警报修正/缓解引用联机修正指南|不可用 – 任何警报修正链接的使用 Internet URL 将不起作用。|
|应用商店联合 – 选择并直接从 Azure 应用商店添加库程序包的功能|不可用 – 此功能要求连接到 Azure 和 Azure Active Directory 帐户。|
|使用 Azure Active Directory 联合身份验证帐户来管理 Azure 堆栈部署|不可用 – 此功能要求连接至 Azure。 必须改为使用 AD FS 与本地 Active Directory 实例。|
|资源提供程序，如 WebApps 和 SQL|不可用的资源提供程序，如 WebApps 和 SQL 需要对内容进行 Internet 访问。|
|命令行接口 (CLI)|受到影响-CLI 已减少在身份验证和配置的服务主体方面的功能。|
|Visual Studio – Cloud discovery|受到影响-Cloud Discovery 或者发现不同的云，否则将不会工作。|
|Visual Studio – AD FS|受到影响-仅 Visual Studio Enterprise 支持 AD FS。
遥测|不可用 – 作为依赖于遥测数据也任何第三方库包 Azure 堆栈的遥测数据。|
|证书|需要 HTTPS 的上下文中的证书吊销列表 (CRL) 和联机证书状态协议 (OSCP) 服务不可用 – Internet 连接。|
|密钥保管库|受到影响-针对密钥保管库的常见用例是让一个应用程序读取在运行时的机密。 此应用程序需要该目录中的服务主体。 在 Azure Active Directory，正则用户 （非管理员） 都是默认情况下允许添加服务主体。 在 AD 中 （使用 ADFS） 它们不是。 因为其中一个必须始终通过目录管理员以添加任何应用程序，这会在端到端体验会障碍。| 

## <a name="learn-more"></a>了解详细信息
- 有关用例、 购买、 合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure 堆栈](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure 堆栈信息的路线图和地域可用性集成的系统，请参阅白皮书： [Azure 堆栈： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure 堆栈打包和定价的详细信息[下载.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)