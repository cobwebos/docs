---
title: "什么是 Azure Stack？ | Microsoft Docs"
description: "Azure 堆栈可以在你的数据中心中运行 Azure 服务。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 68d1e1752f934e61bbb60c0c934a80b564896a36
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="what-is-azure-stack"></a>什么是 Azure Stack？

Microsoft Azure Stack 是一种混合云平台，通过它可从组织的数据中心提供 Azure 服务。  Azure Stack旨在启用新方案的现代应用程序中重要的方案，如边缘和连接断开的环境或满足特定的安全和合规性要求。  Azure Stack可以以两个部署选项，以满足你需求提供。

## <a name="azure-stack-integrated-systems"></a>Azure Stack 集成系统
Azure Stack 集成系统由 Microsoft 与[硬件合作伙伴](https://azure.microsoft.com/overview/azure-stack/integrated-systems/)共同提供，它创建了一个解决方案，实现了云进度创新与管理简化之间的平衡。  因为 Azure Stack 作为硬件和软件的集成系统提供，你可以获得适度的灵活性和控制，同时又能采用云环境中的创新。  Azure Stack 集成系统的大小可以是 4-12 个节点，由硬件合作伙伴和 Microsoft 共同支持。  使用 Azure Stack集成系统为自己的生产工作负荷实现新方案。    

## <a name="azure-stack-development-kit"></a>Azure Stack 开发工具包
Microsoft Azure Stack 开发工具包是单节点部署的 Azure Stack，可用于评估和了解 Azure Stack。  你还可以使用 Azure Stack 开发工具包作为开发人员环境，其中你可以使用与 Azure 一致的 Api 和开发工具。  Azure Stack 开发工具包不能用作生产环境。

Azure Stack 开发工具包具有以下限制：
* Azure Stack 开发工具包与单个 Azure Active Directory 或 Active Directory 联合身份验证服务标识提供程序相关联。 可在此目录中创建多个用户，并将订阅分配给每个用户。
* 如果在单个计算机上部署所有组件，租户资源可用的物理资源可能有限。 此配置不适用于规模或性能评估。
* 网络方案根据单一主机/NIC 的要求受到限制。  

## <a name="next-steps"></a>后续步骤
[主要功能和概念](azure-stack-key-features.md)

[Azure 堆叠： Azure (pdf) 的扩展](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

