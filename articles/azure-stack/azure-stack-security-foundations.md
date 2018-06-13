---
title: 了解 Azure 堆栈的安全控制 |Microsoft 文档
description: 作为服务管理员，了解有关应用于 Azure 堆栈的安全控件
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
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843691"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure 堆栈基础结构安全状况

*适用于：Azure Stack 集成系统*

安全注意事项和法规遵从性要求包括使用混合云的主要驱动程序。 Azure 堆栈旨在用于这些情况下，而且，务必时采用 Azure 堆栈了解已经存在的控件。

在 Azure 堆栈，有两个安全态势层共存。 第一层包含 Azure 堆栈基础结构，这是由从硬件组件一直到 Azure 资源管理器，并包含管理员和租户门户。 第二个层包含的工作负荷的租户创建、 部署和管理，包括的任务等虚拟机或应用程序服务的网站。  

## <a name="security-approach"></a>安全方法
Azure 堆栈旨在提供安全状况来抵御现代威胁，并生成以满足从主要法规遵从性标准的要求。 因此，Azure 堆栈基础结构的安全状况基于两个重要元素：

 - **假定漏洞。**  
系统已受到侵犯假设从开始，专注于*检测和限制的违反情况的影响*而不是只想要防止攻击。 
 - **默认情况下强制写入。**  
由于基础结构上定义完善的硬件和软件，将运行我们*启用、 配置和验证所有的安全功能*默认情况下。



由于已将 Azure 堆栈发送作为一个集成系统，由 Microsoft 定义 Azure 堆栈基础结构的安全状况。 就像在 Azure 中，租户负责定义其租户工作负载的安全状况。 本文档提供有关 Azure 堆栈基础结构的安全状况的基础知识。

## <a name="data-at-rest-encryption"></a>在 rest 加密的数据
使用 Bitlocker 的其余部分进行加密所有 Azure 堆栈的基础结构和租户数据。 这种加密可防止物理丢失或被盗 Azure 堆栈存储组件。 

## <a name="data-in-transit-encryption"></a>在传输过程加密的数据
Azure 堆栈的基础结构组件进行通信使用 TLS 1.2 使用加密的通道。 自助基础结构管理加密证书。 

所有外部的基础结构终结点，如 REST 终结点或 Azure 堆栈门户中，支持 TLS 1.2 进行安全通信。 加密证书，从第三方或企业证书颁发机构，必须提供这些终结点。 

虽然自签名的证书可以用于这些外部终结点，Microsoft 强烈建议不要使用它们。 

## <a name="secret-management"></a>密钥管理
Azure 堆栈的基础结构使用多种机密，如的密码，来正常。 因为它们被旋转每 24 小时的组托管服务帐户，其中的大多数自动通常情况下，旋转。

不包含特权终结点中的脚本可以手动旋转组托管服务帐户的剩余机密信息。

## <a name="code-integrity"></a>代码完整性
Azure 堆栈将使用最新的 Windows Server 2016 的安全功能。 其中一个是 Windows Defender Device Guard，也不能提供应用程序允许列表，可确保只有经过授权的 Azure 堆栈基础结构内的代码运行。 

已授权的代码签名由 Microsoft 或 OEM 合作伙伴，并且它包含在由 Microsoft 定义策略中指定的允许软件的列表。 换而言之，可以执行已经过审核可以运行 Azure 堆栈基础结构中的软件。 阻止任何尝试执行未授权的代码和生成审核。

Device Guard 策略还可以防止运行在 Azure 堆栈基础结构中的第三方代理或软件。

## <a name="credential-guard"></a>凭据保护
Azure 堆栈中的另一个 Windows Server 2016 安全功能是 Windows Defender 凭据保护，用于防止传递哈希和传递票证攻击中的 Azure 堆栈基础结构凭据。

## <a name="antimalware"></a>反恶意软件
Azure 堆栈 （HYPER-V 主机和虚拟机） 中的每个组件进行保护，Windows Defender 防病毒软件。

## <a name="constrained-administration-model"></a>约束的管理模型
Azure 堆栈中的管理控制通过使用三个入口点，每个都有特定用途： 
1. [管理员门户](azure-stack-manage-portals.md)提供针对每日的管理操作的点，请单击体验。
2. Azure 资源管理器公开管理员门户通过 REST API，通过 PowerShell 和 Azure CLI 使用的所有管理的操作。 
3. 对于特定的低级别操作，例如数据中心集成或支持方案，Azure 堆栈公开 PowerShell 终结点调用[特权终结点](azure-stack-privileged-endpoint.md)。 此终结点公开只有列入允许一组 cmdlet，并很大程度审核。

## <a name="network-controls"></a>网络控件
Azure 堆栈的基础结构附带的网络访问控制 List(ACL) 的多个层。 防止未经授权的访问的基础结构组件和限制与仅的路径以其能够正常运行所需的基础结构通信的 Acl。 

在三个层中实施网络 Acl:
1.  机架顶部的切换
2.  软件定义网络
3.  主机和 VM 操作系统防火墙 


