---
title: 验证 Azure Stack 验证作为一项服务中的 Microsoft 软件更新 |Microsoft Docs
description: 了解如何从作为服务的验证与 Microsoft 的软件更新进行验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: bcb56789b2781cd1d081af8c112222a1f1269a74
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304884"
---
# <a name="validate-software-updates-from-microsoft"></a>验证来自 Microsoft 的软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布到 Azure Stack 软件更新。 这些更新提供给 Azure Stack coengineering 合作伙伴。 中的公开可用的提前提供了更新。 可以针对你的解决方案检查更新，并向 Microsoft 提供反馈。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>应用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>创建工作流

更新验证使用相同的工作流作为**解决方案验证**。

## <a name="run-tests"></a>运行测试

1. 更新验证使用相同的工作流作为**解决方案验证**。 

2. 按照的说明[运行解决方案验证测试](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)。 请改为选择以下测试：
    - 每月的 Azure Stack 更新验证
    - 云模拟引擎

你不需要请求更新验证签名的包。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)