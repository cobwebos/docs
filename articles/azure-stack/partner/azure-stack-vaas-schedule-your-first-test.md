---
title: 用于验证作为 Azure Stack 门户的服务计划第一次测试 |Microsoft Docs
description: 用于验证作为 Azure Stack 门户的服务计划第一个测试。
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
ms.openlocfilehash: 0f681070df4b4b3384171c05edb3851abec2ab5c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235291"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>快速入门： 使用验证的服务门户，作为计划第一次测试

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何安排第一个包含为服务 (VaaS) 门户，以检查硬件验证测试。 本地代理必须运行验证测试之前正在验证 Azure Stack 解决方案上进行部署。

在本快速入门将执行添加你的解决方案，并运行测试。

## <a name="prerequisites"></a>必备组件

在执行本快速入门之前，需要具有：
 - 作为服务帐户验证。 有关说明，请参阅[设置为服务帐户验证](azure-stack-vaas-set-up-account.md)。  
- 在系统上安装本地代理。 有关说明，请参阅[部署本地代理和测试虚拟机](azure-stack-vaas-test-vm.md)。

## <a name="add-a-new-solution"></a>添加新的解决方案

1. 登录到[验证门户](https://azurestackvalidation.com)。

    ![登录到验证门户](media/vaas_portalsignin.png)  

2. 选择**新的解决方案**。
3. 输入解决方案的名称，然后选择**保存**。

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
4. 添加你环境的参数。 有关详细信息，请参阅[添加环境参数](#add-environmental-parameters)。
5. 添加常见的测试参数。 有关详细信息，请参阅[添加常见的测试参数](#add-common-test-parameters)。

    具体取决于测试定义中，测试可能要求您输入的值独立于通用参数，或可能会允许你重写公共参数值。
6. 单击**提交**来计划测试。

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

- [验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)  
- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
