---
title: 测试验证作为 Azure Stack 的服务参数 |Microsoft Docs
description: 关于验证即服务 Azure Stack 的配置文件和测试轮次参数的参考主题。
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
ms.openlocfilehash: 9f042779e3463f0d75dc6327b3553156edbf162a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162217"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>验证即服务 Azure Stack 的测试参数

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

在通过验证即服务 (VaaS) 执行任何测试套件之前，请选择一个解决方案并创建一个测试轮次。

- 使用已注册的 VaaS 租户凭据进行登录。
- 创建一个新的解决方案（通过选择“添加解决方案”）或选择任何现有解决方案。
- 从“测试轮次”工作流磁贴中选择“启动”按钮。

> [!Note]  
> 为你要验证的每个唯一计算机集/硬件配置创建一个新的解决方案条目，并为该计算机集上的每个生成部署创建一个新的测试轮次。

你需要在“测试轮次信息”页面上输入运行测试所需的参数。 在启动新的测试轮次或验证运行时需提供这些参数。 大多数参数具有你在部署 Azure Stack 时提供的相同值。

可以手动输入[测试参数表](#test-parameters)中列出的值，也可以上传包含完整 Azure Stack 戳记信息的部署配置文件。 上传后，门户将从该文件中加载值。

> [!Note]  
> 可以通过查找配置文件并将其加载到门户中来搜索并键入测试参数值。

## <a name="export-the-test-parameters-using-powershell"></a>使用 PowerShell 导出测试参数

1. 登录到 DVM 计算机或任何有权访问 Azure Stack 环境的计算机。
2. 打开一个提升的 PowerShell 窗口并运行以下命令：

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. 将 **stampinfoproperties.json** 上传到 VaaS 门户。

## <a name="find-the-test-parameters-in-the-configuration-file"></a>在配置文件中查找测试参数

还可以通过打开 ECE **配置文件**来查找测试参数。 可以在 DVM 计算机上的以下路径中找到该文件：  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>测试参数 

| 参数    | 说明 |
|-------------|-----------------|
| Azure Stack 内部版本                      | 已部署的 Azure Stack 内部版本或版本号，例如 1.0.170330.0 | 
| 租户 ID                              | 在部署 Azure Stack 期间指定的 Azure Active Directory 租户。 在配置文件中搜索 **AADTenant** 并选择 `Id` 标记中的 **GUID** 值。 | 
| 区域                                 | Azure Stack 部署区域。 在配置文件中搜索 **Region**。 | 
| 租户资源管理器                | 租户 Azure 资源管理器终结点，例如 `https://management.<ExternalFqdn>`。 | 
| 管理员资源管理器                 | Azure 资源管理器管理终结点。 例如，`https://adminmanagement.<ExternalFqdn>` | 
| 外部 FQDN                          | 环境的外部 FQDN。 在配置文件中搜索 **ExternalFqdn**。 | 
| 租户用户                            | 之前已预配的或者需要由 Azure AD Directory 中的服务管理员预配的 Active Directory 租户管理员。 有关预配租户帐户的详细信息，请参阅[在 Azure Active Directory 中添加新的 Azure Stack 租户帐户](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)。 测试使用此值来执行租户级操作，例如，部署模板来预配资源（VM、存储帐户，等等）并执行工作负荷。 | 
| 服务管理员用户             | 在部署 Azure Stack 期间指定的 Azure AD Directory 租户的 Azure Active Directory 管理员。 在配置文件中搜索 **AADTenant** 并选择配置文件中的 `UniqueName` 标记中的值。 | 

## <a name="checks-before-starting-the-tests"></a>启动测试前的检查

这些测试执行远程操作。 运行测试的计算机必须有权访问 Azure Stack 终结点。 否则，测试将无法工作。 如果你在本地代理上使用 VaaS，请使用将运行代理的计算机。 可以通过运行以下检查来验证计算机是否有权访问 Azure Stack 终结点。

1. 检查是否可以访问基本 URI。 打开一个 CMD 提示符或 bash shell，然后运行以下命令：

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 打开 Web 浏览器并导航到 `https://adminportal.<EXTERNALFQDN>` 以检查是否可以访问 MAS 门户。

3. 使用创建测试轮次时提供的 Azure AD 服务管理员用户名和密码值进行登录。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
