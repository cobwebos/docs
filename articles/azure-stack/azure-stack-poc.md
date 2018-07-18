---
title: 什么是 Azure Stack？ | Microsoft Docs
description: 使用 Azure Stack 可以在你的数据中心中运行 Azure 服务。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234880"
---
# <a name="what-is-azure-stack"></a>什么是 Azure Stack？

Microsoft Azure Stack 是一种混合云平台，用于在你的数据中心中提供 Azure 服务。 此平台设计为支持不断变化的业务需求。 Azure Stack 可以为新式应用程序（如边缘和未联网的环境）启用新方案，或者满足特定的安全和合规性要求。

为了满足你的需求，我们以两个部署选项提供 Azure Stack。

## <a name="azure-stack-integrated-systems"></a>Azure Stack 集成系统
Azure Stack 集成系统通过 Microsoft 和[硬件合作伙伴](https://azure.microsoft.com/overview/azure-stack/integrated-systems/)的合作关系提供，创建一个解决方案以提供符合云步调的创新和计算管理简便性。 由于 Azure Stack 作为集成的硬件和软件系统提供，因此你拥有你需要的灵活性和控制，以及从云中创新的能力。 Azure Stack 集成系统的大小可以是 4-12 个节点，由硬件合作伙伴和 Microsoft 共同支持。  使用 Azure Stack 集成系统可为生产工作负荷创建新方案和部署新解决方案。

## <a name="azure-stack-development-kit"></a>Azure Stack 开发工具包

Microsoft [Azure Stack 开发工具包 (ASDK)](.\asdk\asdk-what-is.md) 是 Azure Stack 的单节点部署，可用于评估和了解 Azure Stack。  你还可以使用 ASDK 作为开发人员环境以生成应用程序使用的 Api 和工具与 Azure 一致。

>[!Note]
>ASDK 并不旨在用作生产环境。

ASDK 具有以下限制：

* ASDK 与单个 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 标识提供者相关联。 可在此目录中创建多个用户，并将订阅分配给每个用户。
* 因为 Azure Stack 组件部署于一台主机计算机上，所以只有很有限的物理资源可用于租户资源。 此配置不是向缩放或性能评估。
* 网络方案非常有限，因为它的单个主机和 NIC 部署要求。

## <a name="next-steps"></a>后续步骤

- [主要功能和概念](azure-stack-key-features.md)
- [Azure Stack：Azure 的扩展 (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
