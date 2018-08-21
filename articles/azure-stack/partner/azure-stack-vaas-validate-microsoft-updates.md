---
title: 验证 microsoft Azure Stack 的验证作为一项服务中的软件更新 |Microsoft Docs
description: 了解如何从 Microsoft 作为服务的验证的软件更新进行验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235290"
---
# <a name="validate-software-updates-from-microsoft"></a>从 Microsoft 的软件更新进行验证

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布到 Azure Stack 软件更新。 这些更新提供给 Azure Stack 联合工程合作伙伴提前进行公开，以便它们可以针对其解决方案对更新进行验证，并向 Microsoft 提供反馈。

## <a name="test-an-existing-solution"></a>测试现有的解决方案

1. 登录到[验证门户](https://azurestackvalidation.com)。

2. 选择现有的解决方案从 Microsoft 更新已部署，以及选择**启动**上**包验证**磁贴。

    ![包验证](media/image3.png)

3. 输入验证名称。

4. 给 OEM 包的解决方案安装在部署时输入的 URL。 包存储在 Azure blob 服务中使用的 URL。 有关详细信息，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

5. 选择**上传**添加你的部署配置文件。 请参阅[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)有关上传你的部署配置文件信息。

6. 然后必须将部署配置文件自定义使用正确的环境的参数文件，请参阅[环境的参数](azure-stack-vaas-parameters.md#environment-parameters)有关其他详细信息。

    > [!Note]   
    > 可以通过添加常见的测试参数来进一步自定义部署配置文件。 有关详细信息，请参阅[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)

7. 必须手动输入的用户名和密码的租户用户、 服务管理员和云管理员。

8. 提供用于存储诊断日志的 Azure 存储 blob 的 URL。 有关详细信息，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

    > [!Note]  
    > 可以输入描述性标记来标记工作流。

10. 选择**提交**保存工作流。

解决方案工作流在大约 24 小时内运行。 在计划测试添加到链接或指令。 清除在工具中。

查找运行的监视验证的进度的详细信息，请参阅[监视测试](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
