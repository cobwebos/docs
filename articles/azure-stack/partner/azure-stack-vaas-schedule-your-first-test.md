---
title: 用于验证作为 Azure Stack 门户的服务计划第一次测试 |Microsoft Docs
description: 通过适用于 Azure Stack 的验证即服务门户来计划你的第一次测试。
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
ms.openlocfilehash: 8ff78f46283d3ee4baa7310aa56bcdabde85f2cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159616"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>快速入门：使用验证即服务门户来计划你的第一次测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何通过验证即服务 (VaaS) 门户计划你的第一次测试来检查硬件。 在运行验证测试之前，必须在 Azure Stack 解决方案上部署本地代理。

在本快速入门中，你将添加解决方案并运行测试。

## <a name="prerequisites"></a>必备组件

在根据本快速入门进行操作之前，需要具有以下各项：
 - 一个验证即服务帐户。 有关说明，请参阅[设置验证即服务帐户](azure-stack-vaas-set-up-account.md)。  
- 系统上安装的本地代理。 有关说明，请参阅[部署本地代理并测试虚拟机](azure-stack-vaas-test-vm.md)。

## <a name="add-a-new-solution"></a>添加新的解决方案

1. 登录到[验证门户](https://azurestackvalidation.com)。

    ![登录到验证门户](media/vaas_portalsignin.png)  

2. 选择“新建解决方案”。
3. 为解决方案输入名称并选择“保存”。

## <a name="create-a-solution-validation-workflow"></a>创建解决方案验证工作流

1. 选择解决方案名称。
2. 在“解决方案验证”磁贴上选择“管理”。

    ![解决方案验证](media/image2.png)

## <a name="create-a-solution-workflow"></a>创建解决方案工作流

1. 选择“新建解决方案验证”。
2. 键入验证的名称。
3. 选择“最低”或“最高”。  
    - **最低**  
    为解决方案配置受支持的最少数目的节点。  
    - **最高**  
    为解决方案配置受支持的最多数目的节点。
4. 添加环境参数。 有关详细信息，请参阅[添加环境参数](#add-environmental-parameters)。
5. 添加常用测试参数。 有关详细信息，请参阅[添加常用测试参数](#add-common-test-parameters)。

    根据测试定义，测试可能要求你独立于常用参数输入值，也可能允许你覆盖常用参数值。
6. 单击“提交”来计划测试。

## <a name="add-environmental-parameters"></a>添加环境参数

添加以下环境参数：

| 测试轮次信息 | 需要 | 说明 |
| --- | --- | --- | --- |
| Azure Stack 内部版本 | 需要 | Azure Stack 内部版本号（例如 20170501.1）值必须是有效的 Azure Stack 内部版本号或版本，例如 1.0.170330.9 |
| 租户 ID | 需要 | Active Directory 租户 ID。 这必须是一个 GUID（例如 ECA23256-6BA0-4F27-8E4D-AFB02F088363） |
| 区域 | 需要 | Azure Stack 部署区域 |
| 租户资源管理器终结点 | 需要 | 租户 Azure 资源管理器操作的终结点（例如 https://management.loc-ext.domain.com)） |
| 管理员资源管理器终结点 | 不是必需 | 租户 Azure 资源管理器操作的终结点（例如 https://management.loc-ext.domain.com)） |
| 外部 FQDN | 不是必需 | 用作终结点后缀的外部完全限定的域名。 （例如 local.azurestack.external 或 redmond.contoso.com） |
| 节点数 | 需要 | 解决方案中的节点数。 |

## <a name="add-common-test-parameters"></a>添加常用测试参数

添加以下常用测试参数：

| 测试轮次信息 | 需要 | 说明 |
| --- | --- | --- |
| 租户用户名 | 需要 | 租户用户名（例如 tenant@contoso.onmicrosoft.com） |
| 租户密码 | 需要 | 租户的密码。 |
| 服务管理员用户名 | 不是必需 | 租户用户名（例如 tenant@contoso.onmicrosoft.com） |
| 服务管理员密码 | 不是必需 | 服务管理员用户名（例如 serviceadmin@contoso.onmicrosoft.com） |
| 云管理员用户名 | 不是必需 | Azure Stack 域管理员帐户（例如 contoso\cloudadmin） |
| 云管理员密码 | 不是必需 | |
|  诊断连接字符串 | 不是必需 | 在执行测试期间要将诊断日志复制到的 Azure 存储帐户的 SAS URI。 请参阅[创建 Azure 存储 blob 来存储日志](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。 <br><br>**诊断连接字符串**常用参数的值将由服务进行存储，并且在调度时提供给工作流中使用此参数的所有测试。 如果 SAS URL 在 30 天内失效，则会在常用参数页面上提示你输入新的 SAS URL。 |
| 标记 - 名称 | 不是必需 |  可以输入的用来标记工作流的描述性标记。 这是标记的名称。 |
| 标记 - 值 | 不是必需 | 可以输入的用来标记工作流的描述性标记。 这是标记的值。 |

## <a name="next-steps"></a>后续步骤

- [验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)  
- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
