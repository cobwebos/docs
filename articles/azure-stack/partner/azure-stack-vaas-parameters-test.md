---
title: 测试验证作为 Azure Stack 的服务参数 |Microsoft Docs
description: 有关配置文件和测试的参考主题作为 Azure Stack 服务传递验证参数。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235260"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>验证作为 Azure Stack 的服务的测试参数

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

之前即服务 (VaaS) 从验证中执行任何测试套件，请选择一个解决方案和创建测试流程。

- 使用已注册 VaaS 租户凭据登录。
- 创建新的解决方案 (通过选择**将解决方案添加**) 或选择任何现有。
- 选择**启动**按钮从**测试轮次**工作流磁贴。

> [!Note]  
> 创建每个唯一的计算机集/硬件配置，你在验证，并将传递为每个生成部署的新的测试机集上的新解决方案项。

将需要输入以运行测试所需的参数**测试传递信息**页。 启动新测试通过或验证运行时提供这些参数。 大多数参数具有相同部署 Azure Stack 时提供的值。

可以输入手动中列出的值[测试参数表](#test-parameters)，或上载部署配置文件，其中包含完整的 Azure Stack 戳信息。 上传后，门户将从文件加载的值。

> [!Note]  
> 您可以搜索和通过查找并加载到在门户的配置文件键入测试参数值。

## <a name="export-the-test-parameters-using-powershell"></a>导出使用 PowerShell 的测试参数

1. 登录到 DVM 机或有权访问 Azure Stack 环境的任何计算机。
2. 打开提升的 PowerShell 窗口，并运行：

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. 上传**stampinfoproperties.json**到 VaaS 门户。

## <a name="find-the-test-parameters-in-the-configuration-file"></a>在配置文件中找到的测试参数

您还可以查找测试参数值通过打开 ECE**配置文件**。 您可以找到以下路径 DVM 计算机上的文件：  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>测试参数 

| 参数    | 说明 |
|-------------|-----------------|
| Azure Stack 内部版本                      | 部署等 1.0.170330.0 时的 azure Stack 构建或版本数。 | 
| 租户 ID                              | 在 Azure Stack 部署过程中指定的 azure 的 Active Directory 租户。 搜索**AADTenant**在该配置文件，然后选择**GUID**中的值`Id`标记。 | 
| 区域                                 | Azure Stack 部署区域。 搜索**区域**配置文件中。 | 
| 租户资源管理器                | 例如，租户 Azure 资源管理器终结点 `https://management.<ExternalFqdn>` | 
| 管理资源管理器                 | Azure 资源管理器管理终结点。 例如，`https://adminmanagement.<ExternalFqdn>` | 
| 外部 FQDN                          | 在环境外部 FQDN。 搜索**ExternalFqdn**配置文件中。 | 
| 租户用户                            | Azure Active Directory 租户管理员或者已预配或只需将用户预配服务管理员在 Azure AD 目录中。 有关预配租户帐户的详细信息，请参阅[在 Azure Active Directory 中添加新的 Azure Stack 租户帐户](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)。 此值用于测试执行租户操作，例如部署模板来预配资源 （虚拟机的存储帐户等） 并执行工作负荷。 | 
| 服务管理员用户             | 在 Azure Stack 部署过程中指定的 Azure AD 目录租户的 azure Active Directory 管理员。 搜索**AADTenant**在配置文件，然后选择中的值`UniqueName`配置文件中的标记。 | 

## <a name="checks-before-starting-the-tests"></a>开始测试前的检查

测试执行远程操作。 运行测试的计算机必须有权访问 Azure Stack 终结点。 否则该测试将无法工作。 如果本地代理上使用 VaaS，则使用计算机代理将运行。 你可以验证你的计算机通过运行以下检查有权访问 Azure Stack 点。

1. 检查可以到达基 URI。 打开命令提示符或 bash shell 中，并运行以下命令：

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 打开 web 浏览器并导航到`https://adminportal.<EXTERNALFQDN>`以检查是否可以到达 MAS 门户。

3. 使用 Azure AD 登录服务时创建测试流程提供的管理员名称和密码值。

## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
