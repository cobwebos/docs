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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 90c18af0f846061c102f1daf1b84a332aaec1dc6
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235298"
---
# <a name="validate-a-new-azure-stack-solution"></a>验证新的 Azure Stack 解决方案

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何可以使用解决方案验证工作流进行认证新的 Azure Stack 解决方案。

Azure Stack 解决方案是硬件物料清单 (BoM) 的已共同协商好与 Microsoft，并通过了 Windows Server 徽标认证要求。 当已对硬件会导致解决方案被归类为 BoM 的更改时，还可以使用解决方案验证工作流*新*。 如果有疑问什么会触发**新**或**重新认证**的一种解决方案与联系[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

若要验证你的解决方案，两次运行工作流。 运行它一次*最小日志*支持的配置。 在其中运行的第二次*最大*配置。 Microsoft 认证解决方案，当这两种配置通过所有测试。

本快速入门可帮助你在添加解决方案并运行测试的过程继续。

## <a name="add-a-new-solution"></a>添加新的解决方案

1. 登录到[验证门户](https://azurestackvalidation.com)。
2. 选择**新的解决方案**。
3. 输入解决方案并选择的名称**保存**。

## <a name="create-a-solution-validation-workflow"></a>创建解决方案验证工作流

1. 选择解决方案名称。
2. 选择**管理**上**解决方案验证**磁贴。

    ![解决方案验证](media/image2.png)

## <a name="create-a-solution-workflow"></a>创建解决方案工作流

1. 选择**新解决方案验证**。
2. 键入验证的名称。
3. 选择**最小**或**最大**。  
    - **最低**  
    该解决方案支持的最小数目的节点配置。  
    - **最大值**  
    解决方案配置了受支持的最大节点数。
4. 选择**上传**以及如何将您的部署配置文件。 这是可选步骤。 此外可以按照下一节中的步骤来添加测试参数。

    > [!note]  
    > 您可以创建你的配置文件通过将参数添加环境参数中常见的测试参数部分在界面中。 您可以检索正在验证 Azure Stack 部署中的服务生成的文件。 有关说明，请参阅[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)。

5. 添加你环境的参数。 有关详细信息，请参阅[添加环境参数](#add-environmental-parameters)。
6. 添加常见的测试参数。 有关详细信息，请参阅[添加常见的测试参数](#add-common-test-parameters)。

    具体取决于测试定义中，测试可能要求您输入的值独立于通用参数，或可能会允许你重写公共参数值。

7. 单击**提交**来计划测试。

## <a name="add-environmental-parameters"></a>添加环境参数

添加以下环境的参数：

| 测试阶段信息 | 需要 | 说明 |
| --- | --- | --- | --- |
| Azure Stack 内部版本 | 需要 | Azure Stack 内部版本 （例如 20170501.1） 数字值必须是有效的 Azure Stack 生成号或版本，例如，1.0.170330.9 |
| 租户 ID | 需要 | Active Directory 租户 id。 这必须是一个 GUID (例如 ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| 区域 | 需要 | Azure Stack 部署区域 |
| 租户资源管理器终结点 | 需要 | 租户 Azure 资源管理器操作 （例如终结点 https://management.loc-ext.domain.com) |
| 管理资源管理器终结点 | 不是必需 | 租户 Azure 资源管理器操作 （例如终结点 https://management.loc-ext.domain.com) |
| 外部 FQDN | 不是必需 | 外部完全限定的域名作为后缀用于终结点。 （例如 local.azurestack.external 或 redmond.contoso.com） |
| 节点数 | 需要 | 在解决方案中的节点数。 |

## <a name="add-common-test-parameters"></a>添加常见的测试参数

添加以下常见的测试参数：

| 测试阶段信息 | 需要 | 说明 |
| --- | --- | --- |
| 租户用户名 | 需要 | 租户用户名 (例如tenant@contoso.onmicrosoft.com) |
| 租户密码 | 需要 | 租户密码。 |
| 服务管理员用户名 | 不是必需 | 租户用户名 (例如tenant@contoso.onmicrosoft.com) |
| 服务管理员密码 | 不是必需 | 服务管理员用户名 (例如serviceadmin@contoso.onmicrosoft.com) |
| 云管理员用户名 | 不是必需 | Azure Stack 域管理员帐户 (例如，contoso\cloudadmin) |
| 云管理员密码 | 不是必需 | |
|  诊断连接字符串 | 不是必需 | 到 Azure 存储帐户的诊断日志将复制到测试执行期间的 SAS URI。 请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。 <br><br>值**诊断连接字符串**流通用参数将由服务存储和在工作流中使用此参数的所有测试计划时提供。 如果 SAS URL 的到期 30 天内，系统会提示为新的 SAS URL 的常见参数上。 |
| 标记-名称 | 不是必需 |  可以输入描述性标记来标记工作流。 这是标记的名称。 |
| 标记的值 | 不是必需 | 可以输入描述性标记来标记工作流。 这是标记的值。 |

## <a name="next-steps"></a>后续步骤

- [重新预约或取消一个测试](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。