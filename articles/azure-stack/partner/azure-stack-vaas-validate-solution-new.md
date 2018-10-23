---
title: 验证新的 Azure Stack 解决方案 |Microsoft Docs
description: 了解如何验证新的 Azure Stack 解决方案作为服务的验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 777609b89bc08cd61489d2c3a3669ec07ccbc372
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646996"
---
# <a name="validate-a-new-azure-stack-solution"></a>验证新的 Azure Stack 解决方案

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用**解决方案验证**来验证新的 Azure Stack 解决方案的工作流。

Azure Stack 解决方案是具有共同达成一致 Microsoft 和合作伙伴之间在符合 Windows Server 徽标认证要求后的硬件清单的清单 (BoM)。 当已对硬件 BoM 的更改时，必须 recertified 解决方案。 有关何时再次验证解决方案的更多问题，请在 team [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

若要验证你的解决方案，请运行两次解决方案验证工作流。 针对受支持的“最低”配置运行一次。 运行它的第二次*最常*支持的配置。 如果两种配置都通过了全部测试，Microsoft 将为解决方案颁发证明。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>创建解决方案验证工作流

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. 选择**启动**上**解决方案验证**磁贴。

    ![解决方案验证工作流磁贴](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. 选择**解决方案配置**。
    - **最小**： 使用支持的最小数目的节点配置解决方案。
    - **最大**： 的解决方案配置与受支持的最大节点数。
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![解决方案验证信息](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后，无法修改环境的参数。

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将重定向到测试摘要页。

## <a name="execute-solution-validation-tests"></a>执行解决方案验证测试

在中**解决方案验证测试摘要**页中，将看到的完成验证所需的测试的列表。

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![计划解决方案验证测试](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)