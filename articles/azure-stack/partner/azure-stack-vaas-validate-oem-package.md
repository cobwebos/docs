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
ms.openlocfilehash: cefa32c35df4e87d4d2b983ee8c4a16dc065e774
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160449"
---
# <a name="validate-oem-packages"></a>验证 OEM 程序包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当对已完成的解决方案验证的固件或驱动程序进行了更改时，可以测试新的 OEM 程序包。 在您的软件包经过测试，它是由 Microsoft 签名。 你的测试必须包含更新后的 OEM 扩展程序包，其中包含已通过 Windows Server 徽标和 PCS 测试的驱动程序和固件。

所有在 24 小时内完成的测试的结果都是**成功**。 如果有任何测试的结果**失败**，文件中的 bug [Microsoft 协作](https://aka.ms/collaborate)，并通过发送一封电子邮件通知 Microsoft [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

## <a name="get-your-oem-package-signed"></a>对 OEM 程序包进行签名

1. 确保已应用了当前的每月更新。 有关最新版本，请查看 [Azure Stack 操作员文档 > 概述 > 发行说明](https://docs.microsoft.com/azure/azure-stack/)中的最新版本。

    指定 Microsoft 软件更新部署到 Azure Stack 使用命名约定，例如，1803年的该值指示此更新是为 2018 年 3 月。 有关 Azure Stack 更新策略、发布节奏以及发行说明的信息，请参阅 [Azure Stack 服务策略](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)。

1. 检查系统运行状况状态由运行**Test-azurestack**为所述 [适用于 Azure Stack 运行验证测试。 在启动测试之前，请解决任何警告和错误。

2. 在[验证门户](https://azurestackvalidation.com)中，选择一个现有的解决方案。 如果尚未添加你的解决方案，请参阅[添加新的解决方案](azure-stack-vaas-validate-solution-new.md#add-a-new-solution)。

3. 在“程序包验证”磁贴上选择“启动”以启动新的工作流。

    ![程序包验证](media/image3.png)

4.  提供一个诊断连接字符串。 有关说明，请参阅[设置存储帐户](azure-stack-vaas-set-up-account.md)。

    对于每次程序包验证运行，都必须指定一个 OEM 扩展程序包。 请指定在部署 Azure Stack 时在解决方案上安装的 OEM 程序包。 有关说明，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

    为避免在输入数据时犯错，必须使用一个包含环境变量的 JSON 文件来为运行完成必需字段的输入。 有关说明，请参阅[在 Azure Stack 部署中获取配置文件](azure-stack-vaas-parameters.md)。

5. 运行测试。

6. 在所有测试都成功完成后，将你的解决方案和程序包验证的名称发送到 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) 以请求程序包签名。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
