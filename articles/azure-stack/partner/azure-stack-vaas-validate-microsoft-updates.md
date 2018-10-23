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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648769"
---
# <a name="validate-software-updates-from-microsoft"></a>验证来自 Microsoft 的软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布到 Azure Stack 软件更新。 这些更新提供给 Azure Stack 联合工程合作伙伴提前进行公开，以便它们可以针对其解决方案对更新进行验证，并向 Microsoft 提供反馈。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>应用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>创建工作流

更新验证使用相同的工作流作为**包验证**。 按照的说明[创建包验证工作流](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow)。

## <a name="run-tests"></a>运行测试

更新验证使用相同的工作流作为**包验证**。 按照的说明[执行包验证测试](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)。

不需要请求更新验证签名的包。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
