---
title: 验证 microsoft Azure Stack 的验证作为一项服务中的软件更新 |Microsoft Docs
description: 了解如何使用验证即服务验证来自 Microsoft 的软件更新。
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
ms.openlocfilehash: 6ef8c0486a694ac44c53375b24893812b10343e4
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158477"
---
# <a name="validate-software-updates-from-microsoft"></a>验证来自 Microsoft 的软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布到 Azure Stack 软件更新。 这些更新提供给 Azure Stack 联合工程合作伙伴提前进行公开，以便它们可以针对其解决方案对更新进行验证，并向 Microsoft 提供反馈。

## <a name="test-an-existing-solution"></a>测试现有的解决方案

1. 登录到[验证门户](https://azurestackvalidation.com)。

2. 选择在其中部署了来自 Microsoft 的更新的一个现有解决方案，从“程序包验证”磁贴中选择“启动”。

    ![程序包验证](media/image3.png)

3. 输入验证名称。

4. 输入在部署时在解决方案上安装的 OEM 程序包的 URL。 请使用 Azure blob 服务中存储的程序包的 URL。 有关详细信息，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

5. 选择“上传”以添加部署配置文件。 有关上传部署配置文件的信息，请参考[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)。

6. 必须使用正确的环境参数文件来自定义部署配置文件，有关更多详细信息，请参阅[环境参数](azure-stack-vaas-parameters.md#environment-parameters)。

    > [!Note]   
    > 可以通过添加常用测试参数来进一步自定义部署配置文件。 有关详细信息，请参阅 [Azure Stack 验证即服务的工作流常用参数](azure-stack-vaas-parameters.md)。

7. 必须手动输入租户用户、服务管理员和云管理员的用户名和密码。

8. 提供用于存储诊断日志的 Azure 存储 blob 的 URL。 有关详细信息，请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。

    > [!Note]  
    > 可以输入的用来标记工作流的描述性标记。

10. 选择“提交”以保存工作流。

解决方案工作流将运行大约 24 小时。 在计划测试时请添加链接或说明。 工具中的清除

有关监视验证运行进度的详细信息，请参阅[监视测试](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
