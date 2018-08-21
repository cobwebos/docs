---
title: 验证 Azure Stack 验证作为一项服务中的原始设备制造商 (OEM) 包 |Microsoft Docs
description: 了解如何检查验证作为服务的原始设备制造商 (OEM) 包。
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
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235271"
---
# <a name="validate-oem-packages"></a>验证 OEM 包

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当已对固件或驱动程序已完成的解决方案验证的更改时，你可以测试新的 OEM 包。 在您的软件包经过测试，它是由 Microsoft 签名。 你的测试必须包含驱动程序和 Windows Server 徽标和 PC 测试已通过的固件更新后的 OEM 扩展包。

结果在 24 小时内完成所有测试**成功**。 如果有任何测试的结果**失败**，文件中的 bug [Microsoft 协作](https://aka.ms/collaborate)，并通过发送一封电子邮件通知 Microsoft [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

## <a name="get-your-oem-package-signed"></a>获取 OEM 包签名

1. 请确保已应用当前的每月更新。 有关最新版本，请参阅中的最新版本[Azure Stack 操作员文档 > 概述 > 发行说明](https://docs.microsoft.com/azure/azure-stack/)。

    指定 Microsoft 软件更新部署到 Azure Stack 使用命名约定，例如，1803年的该值指示此更新是为 2018 年 3 月。 有关 Azure Stack 更新策略，频率以及版本提供了说明，请参阅[Azure Stack 服务策略](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)。

1. 检查系统运行状况状态由运行**Test-azurestack**为所述 [适用于 Azure Stack 运行验证测试。 启动的任何测试之前解决任何警告和错误。

2. 在中[验证门户](https://azurestackvalidation.com)，选择现有的解决方案。 如果未添加你的解决方案，请参阅[添加一个新的解决方案](azure-stack-vaas-validate-solution-new.md#add-a-new-solution)。

3. 选择**启动**上**的包验证**磁贴以启动新工作流。

    ![包验证](media/image3.png)

4.  提供的诊断连接字符串。 有关说明，请参阅[设置存储帐户](azure-stack-vaas-set-up-account.md)。

    必须为运行每个包验证指定 OEM 扩展包。 指定在 Azure Stack 部署的解决方案已安装的 OEM 包。 有关说明，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

    使用环境变量的 JSON 文件必须用于完成运行后，若要避免在数据输入错误的必填字段的输入。 有关说明，请参阅[获取 Azure Stack 部署中的配置文件](azure-stack-vaas-parameters.md)。

5. 运行测试。

6. 当所有测试已成功都完成时，将发送到你的解决方案和包验证的名称[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)请求包签名。

## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
