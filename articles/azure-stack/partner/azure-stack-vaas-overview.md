---
title: 验证用作 Azure Stack 的服务的概述 |Microsoft Docs
description: Azure Stack 验证作为服务的已知问题的概述。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235210"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>验证用作 Azure Stack 的服务是什么？

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

作为一项服务 (VaaS) 验证是设计为解决方案合作伙伴共同工程与 Microsoft Azure Stack 产品/服务的本机 Azure 服务。 解决方案合作伙伴可以使用该服务检查其解决方案符合 Microsoft 的要求以及按预期方式使用 Azure Stack 工作。

VaaS 的主要用途是：

- 验证新的 Azure Stack 解决方案
- 验证对 Azure Stack 软件更改
- 获取包部署过程中使用的数字签名的解决方案合作伙伴
- 预览 Azure Stack 验证辅助资料

## <a name="validate-new-azure-stack-solution"></a>验证新的 Azure Stack 解决方案

合作伙伴使用解决方案验证工作流来检查新的 Azure Stack 解决方案。 该解决方案必须通过所需的硬件实验室工具包 (HKL) Azure Stack 测试组件测试。 只需运行两次每个新的解决方案的工作流： 一次的最小和最大配置。

有关详细信息，请参阅[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)。

## <a name="validate-changes-to-the-azure-stack-software"></a>验证对 Azure Stack 软件更改

合作伙伴使用包验证工作流检查他们的解决方案，适用于最新的 Azure Stack 软件更新。 最小值，包验证工作流必须在运行 Microsoft，推荐的硬件环境和上一个解决方案，修补和更新 (& U) 用于应用更新。 应在基线版本上运行包验证。

有关详细信息，请参阅[来自 Microsoft 的软件更新进行验证](azure-stack-vaas-validate-microsoft-updates.md)。

## <a name="get-digitally-signed-solution-partner-packages"></a>获取合作伙伴的包的数字签名的解决方案

除了验证 Azure Stack 的更新，你可以使用包验证工作流检查对 OEM 自定义包，其中包括 Azure Stack 特定于合作伙伴的驱动程序、 固件和其他 Azure Stack 的部署过程中使用的软件更新软件。 部署要检查当前版本的最少的最小大小解决方案将支持在使用 Azure Stack 软件的包。 必须将更新后的包上载到服务之前启动测试。 如果测试成功，通知vaashelp@microsoft.com。 包已完成测试，应使用 Azure Stack 数字签名进行数字签名，请告知 Azure Stack 合作伙伴。 Microsoft 对包进行签名，并通知 Azure Stack 合作伙伴包是可供下载的门户。

有关详细信息，请参阅[验证 OEM 包](azure-stack-vaas-validate-oem-package.md)。

## <a name="preview-azure-stack-validation-collateral"></a>预览 Azure Stack 验证辅助资料

Microsoft 定期对新功能在 Azure Stack 中可用。 作为向市场提供这些功能的开发过程的一部分，新的测试附件可测试通过工作流中。 测试通过工作流包括从其他工作流以允许非官方测试执行的测试附件。 不使用测试通过工作流提交以供审核的结果。 使用解决方案验证和包验证工作流以获取官方批准你的解决方案。

## <a name="next-steps"></a>后续步骤

- 首先，和[设置您的验证作为服务帐户](azure-stack-vaas-validate-solution-new.md)
