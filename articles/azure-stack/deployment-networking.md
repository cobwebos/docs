---
title: Azure 堆栈部署网络流量 |Microsoft 文档
description: 本指南介绍了 Azure 堆栈部署网络过程有关的期望内容。
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656138"
---
# <a name="about-deployment-network-traffic"></a>有关部署网络流量
了解网络流量期间 Azure 堆栈的流动方式部署十分重要确保成功部署。 本文将指导你预期的网络流量通过提供希望得到什么了解在部署过程。

此图显示所有组件和连接的部署过程中涉及：

![Azure 堆栈部署网络拓扑](media/deployment-networking/figure1.png)

> [!NOTE]
> 本指南介绍了连接部署的要求，若要了解有关其他部署方法，请参阅[Azure 堆栈部署连接模型](azure-stack-connection-models.md)。

### <a name="the-deployment-vm"></a>部署 VM
Azure 堆栈解决方案包括一组用于承载 Azure 堆栈组件的服务器和额外的服务器称为硬件生命周期主机 (HLH)。 此服务器可用于部署和管理你的解决方案的生命周期，并在部署过程中承载部署 VM (DVM)。

## <a name="deployment-requirements"></a>部署要求
部署开始之前，有一些可由你的 OEM 以确保部署成功完成身份验证的最低要求。 了解这些要求将帮助你准备环境并确保验证成功，这些是：

-   [证书](azure-stack-pki-certs.md)
-   [Azure 订阅](https://azure.microsoft.com/free/?b=17.06)
-   Internet 访问权限
-   DNS
-   NTP

> [!NOTE]
> 本文重点介绍的最后三个要求。 前两个的详细信息，请参阅上面的链接。

## <a name="deployment-network-traffic"></a>部署网络流量
DVM 从 BMC 网络的 ip 配置，并且需要网络访问 internet。 尽管不是所有 BMC 网络组件都需要外部路由或访问 Internet，但 Ip 利用此网络从某些特定于 OEM 组件可能还需要它。

在部署期间，DVM 进行身份验证对 Azure Active Directory，(Azure AD) 使用从你的订阅的 Azure 帐户。 为了做到这一点，DVM 需要 internet 访问权限的特定端口和 Url 列表。 你可以查找中的完整列表[发布终结点](azure-stack-integrate-endpoints.md)文档。 DVM 将利用 DNS 服务器转发到外部 Url 发出的内部组件的 DNS 请求。 内部 DNS 将转发到部署之前 OEM 提供的 DNS 转发器地址对这些请求。 同样适用于的 NTP 服务器，则需要可靠的时间服务器来维护的所有 Azure 堆栈组件的一致性和时间同步。

仅为出站 DVM 在部署过程所需 Internet 访问权限，在部署过程进行任何入站的调用。 请记住，它使用其 IP 作为源，并且该 Azure 堆栈不支持的代理配置。 因此，如有必要，你需要提供的透明代理或 NAT，访问 internet。 部署完成后，Azure 和 Azure 堆栈之间的所有通信都都通过外部网络使用公共 Vip。

Azure 堆栈交换机上的网络配置包含访问控制列表 (Acl)，以限制某些网络源和目标之间的流量。 DVM 是具有无限访问权限; 的唯一组件即使 HLH 受到严格限制。 可以向你的 OEM 询问有关自定义选项，以简化管理和从你的网络的访问。 由于这些 Acl，务必避免在部署时更改 DNS 和 NTP 服务器地址。 如果这样做，你将需要重新配置的所有解决方案交换机。

完成部署后，将继续直接使用由系统的组件提供的 DNS 和 NTP 服务器地址。 例如，如果部署完成后，你可以检查 DNS 请求，源将从 DVM IP 到外部网络范围中的地址。

Azure 堆栈成功部署后，你的 OEM 合作伙伴可以将 DVM 用于其他部署后任务。 但是，当完成所有部署任务和后期部署配置时，OEM 应能移除和从 HLH 删除 DVM。

## <a name="next-steps"></a>后续步骤
[验证 Azure 注册](azure-stack-validate-registration.md)
