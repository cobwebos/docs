---
title: 了解 Azure Stack 的安全控制措施 | Microsoft Docs
description: 向服务管理员介绍应用于 Azure Stack 的安全控制措施
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.openlocfilehash: a3bd314a1df3c45c76b2e3a5acb31c1474d0fdf5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009474"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure Stack 基础结构安全局势

*适用于：Azure Stack 集成系统*

安全考虑因素与合规性是使用混合云的主要推动因素。 Azure Stack 针对这些方案而设计。因此，在采用 Azure Stack 时，必须知道现有的控制措施。

两个安全局势层在 Azure Stack 中共存。 第一层是 Azure Stack 基础结构，其中包含 Azure 资源管理器最多的硬件组件。 第一层包括管理员门户和租户门户。 第二个层包含的工作负荷创建、 部署和管理的租户。 第二层包括虚拟机和应用服务网站等项。

## <a name="security-approach"></a>安全方法

Azure Stack 的安全状况旨在防范新式威胁，并符合主要合规标准的要求。 因此，Azure Stack 基础结构的安全局势构建在两个支柱之上：

 - **假设性违规**  
我们从假设系统已被入侵的情况出发，将重点放在检测入侵并限制其影响上，而不只是尽量防止攻击。 
 - **默认情况下强制写入**  
由于基础结构在定义完善的硬件和软件，Azure Stack 上运行*启用、 配置和验证所有的安全功能*默认情况下。

由于 Azure Stack 是以集成系统的形式交付的，因此 Azure Stack 基础结构的安全局势由 Microsoft 定义。 如同在 Azure 中一样，租户需负责定义其租户工作负荷的安全局势。 本文档提供有关 Azure Stack 基础结构安全局势的基础知识。

## <a name="data-at-rest-encryption"></a>静态数据加密
所有 Azure Stack 基础结构和租户静态数据都以 Bitlocker 加密。 这种加密可以防范 Azure Stack 存储组件的实物遗失或失窃。 

## <a name="data-in-transit-encryption"></a>传输中数据加密
Azure Stack 基础结构组件使用以 TLS 1.2 加密的通道进行通信。 加密证书由基础结构自行管理。 

所有外部基础结构终结点（例如 REST 终结点或 Azure Stack 门户）都支持使用 TLS 1.2 进行安全通信。 对于这些终结点，必须提供来自第三方或企业证书颁发机构的加密证书。 

尽管可对这些外部终结点使用自签名证书，但 Microsoft 强烈建议不要使用此类证书。 

## <a name="secret-management"></a>机密管理
Azure Stack 基础结构使用许多机密（例如密码）来运行。 其中的大多数机密会自动轮换，因为它们是每隔 24 小时轮换一次的组托管服务帐户。

对于剩余的不属于组托管服务帐户的机密，可以使用特权终结点中的脚本以手动方式轮换。

## <a name="code-integrity"></a>代码完整性
Azure Stack 使用最新的 Windows Server 2016 安全功能。 其中一个是 Windows Defender Device Guard，也不能提供应用程序允许列表，可确保只有经过授权的 Azure 堆栈基础结构内的代码运行。 

经过授权的代码是由 Microsoft 或 OEM 合作伙伴签名的代码，包含在 Microsoft 定义的策略中指定的允许软件列表内。 换而言之，只能执行已批准在 Azure Stack 基础结构中运行的软件。 系统会阻止任何执行未经授权代码的企图并生成审核。

Device Guard 策略也会阻止第三方代理或软件在 Azure Stack 基础结构中运行。

## <a name="credential-guard"></a>Credential Guard
Azure Stack 中的另一个 Windows Server 2016 安全功能是 Windows Defender Credential Guard，它可用于防止 Azure Stack 基础结构凭据遭到“传递哈希”和“传递票证”攻击。

## <a name="antimalware"></a>反恶意软件
Azure Stack 中的每个组件（Hyper-V 主机和虚拟机）受到 Windows Defender Antivirus 的保护。

在联网场景中，防病毒定义和引擎更新每天应用多次。 在离线场景中，反恶意软件更新作为 Azure Stack 的每月更新的一部分应用。 有关详细信息，请参阅[在 Azure Stack上更新 Windows Defender Antivirus](azure-stack-security-av.md)

## <a name="constrained-administration-model"></a>受约束的管理模型
Azure Stack 中的管理是使用三个入口点来控制的，其中每个入口点有特定的用途： 
1. [管理员门户](azure-stack-manage-portals.md)针对日常管理操作提供点击式体验。
2. Azure 资源管理器通过 PowerShell 和 Azure CLI 使用的 REST API 公开管理员门户的所有管理操作。 
3. 对于特定的低级操作（例如数据中心集成或支持方案），Azure Stack 公开一个称作[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 终结点。 此终结点只公开一组已添加到允许列表的 cmdlet，并且经常接受审核。

## <a name="network-controls"></a>网络控制措施
Azure Stack 基础结构随附多个网络访问控制列表 (ACL) 层。 ACL 可防止用户对基础结构组件进行未经授权的访问，并将基础结构通信限制为基础结构在运行时需要访问的路径。 

在三个层中实施网络 ACL：
1.  机架顶部交换机
2.  软件定义的网络
3.  主机和 VM 操作系统防火墙

## <a name="next-steps"></a>后续步骤

- [了解如何轮换 Azure Stack 中的应用机密](azure-stack-rotate-secrets.md)
