---
title: 验证用作 Azure Stack 的服务的概述 |Microsoft Docs
description: 概述了 Azure Stack 验证即服务的常见问题。
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
ms.openlocfilehash: 56251245a23df031f3bc8fe3d36de43e194fbcc7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159667"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>什么是适用于 Azure Stack 的验证即服务？

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

作为一项服务 (VaaS) 验证是设计为解决方案合作伙伴共同工程与 Microsoft Azure Stack 产品/服务的本机 Azure 服务。 解决方案合作伙伴可以使用该服务检查其解决方案符合 Microsoft 的要求以及按预期方式使用 Azure Stack 工作。

VaaS 的主要用途如下：

- 验证新的 Azure Stack 解决方案
- 验证对 Azure Stack 软件所做的更改
- 获取在部署期间使用的进行了数字签名的解决方案合作伙伴程序包
- 预览 Azure Stack 验证辅助材料

## <a name="validate-new-azure-stack-solution"></a>验证新的 Azure Stack 解决方案

合作伙伴使用解决方案验证工作流来检查新的 Azure Stack 解决方案。 解决方案必须通过必需的硬件实验室工具包 (HKL) Azure Stack 测试组件测试。 你只需要为每个新解决方案运行两次工作流：针对最低配置和最高配置各运行一次。

有关详细信息，请参阅[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)。

## <a name="validate-changes-to-the-azure-stack-software"></a>验证对 Azure Stack 软件所做的更改

合作伙伴使用程序包验证工作流检查他们的解决方案是否可以与最新的 Azure Stack 软件更新配合工作。 最小值，包验证工作流必须在运行 Microsoft，推荐的硬件环境和上一个解决方案，修补和更新 (& U) 用于应用更新。 应当在基线内部版本中运行程序包验证。

有关详细信息，请参阅[验证来自 Microsoft 的软件更新](azure-stack-vaas-validate-microsoft-updates.md)。

## <a name="get-digitally-signed-solution-partner-packages"></a>获取进行了数字签名的解决方案合作伙伴程序包

除了验证 Azure Stack 更新之外，还可以使用程序包验证工作流检查对 OEM 自定义程序包的更新，这包括在部署 Azure Stack 软件期间使用的 Azure Stack 合作伙伴特定驱动程序、固件以及其他软件。 至少使用将受支持的最小规模的解决方案部署你在当前版本的 Azure Stack 软件上检查的程序包。 在开始测试之前，必须将更新后的程序包上传到服务。 如果测试成功，则通知 vaashelp@microsoft.com。 告诉 Azure Stack 合作伙伴，程序包已完成测试并且应当通过 Azure Stack 数字签名进行数字签名。 Microsoft 对包进行签名，并通知 Azure Stack 合作伙伴包是可供下载的门户。

有关详细信息，请参阅[验证 OEM 程序包](azure-stack-vaas-validate-oem-package.md)。

## <a name="preview-azure-stack-validation-collateral"></a>预览 Azure Stack 验证辅助材料

Microsoft 定期对新功能在 Azure Stack 中可用。 作为向市场提供这些功能的开发流程的一部分，在测试轮次工作流中将提供新的测试辅助材料。 测试轮次工作流包括了来自其他工作流的测试辅助材料，可用于执行非正式的测试。 不要使用测试轮次工作流来提交结果供审批。 请使用解决方案验证和程序包验证工作流来使你的解决方案获得正式批准。

## <a name="next-steps"></a>后续步骤

- 首先[设置验证即服务帐户](azure-stack-vaas-validate-solution-new.md)
