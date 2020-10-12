---
title: 配置 BYOS (为探查器 & 自带存储) Snapshot Debugger
description: 配置 BYOS (为探查器 & 自带存储) Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 04/14/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 719f0cfa0a1f80568acf3231ce3ffab441e5f6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117383"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>配置自带存储 (BYOS) 用于 Application Insights Profiler 和 Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>什么是携带你自己的存储 (BYOS) ，为什么我需要它？ 
当你使用 Application Insights Profiler 或 Snapshot Debugger 时，你的应用程序生成的项目将通过公共 Internet 上传到 Azure 存储帐户。 这些帐户由 Microsoft 付费和控制，以便进行处理和分析。 Microsoft 控制这些项目的静态加密和生存期管理策略。

使用自带存储时，这些项目将被上传到您控制的存储帐户中。 这意味着，你可以控制 "静态加密" 策略、"生存期管理策略" 和 "网络访问"。 但是，你将负责与该存储帐户关联的成本。

> [!NOTE]
> 如果启用 "专用" 链接，则需要自带存储。 有关 Application Insights 的专用链接的详细信息， [请参阅文档。](../platform/private-link-security.md)
>
> 如果你正在启用 Customer-Managed 密钥，则需要自带存储。 有关 Application Insights Customer-Managed 密钥的详细信息，[请参阅文档。](../platform/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>如何访问我的存储帐户？
1. 在虚拟机或应用服务中运行的代理会将项目 (配置文件、快照和符号) 上传到帐户中的 blob 容器。 此过程涉及到联系 Application Insights Profiler 或 Snapshot Debugger 服务，以获取 SAS (共享访问签名) 令牌连接到存储帐户中的新 blob。
1. Application Insights Profiler 或 Snapshot Debugger 服务将分析传入的 blob，并将分析结果和日志文件写回 blob 存储。 根据可用计算能力，此过程可能在上载后随时发生。
1. 查看探查器跟踪或快照调试器分析时，服务将从 blob 存储提取分析结果。

## <a name="prerequisites"></a>必备条件
* 请确保在与 Application Insights 资源相同的位置中创建存储帐户。 例如： 如果你的 Application Insights 资源位于美国西部2，则你的存储帐户还必须在美国西部2中。 
* 通过访问控制 (IAM) UI，将存储帐户中的 "存储 Blob 数据参与者" 角色授予 AAD 应用程序 "诊断服务可信存储访问
* 如果启用了 "专用链接"，请配置其他设置，以允许从你的虚拟网络连接到受信任的 Microsoft 服务。 

## <a name="how-to-enable-byos"></a>如何启用 BYOS

### <a name="create-storage-account"></a>创建存储帐户
如果未在 Application Insights 资源的同一位置) ，请创建全新的存储帐户 (。
如果 Application Insights 资源 `West US 2` ，则存储帐户必须位于中 `West US 2` 。

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>向存储帐户授予诊断服务的访问权限
BYOS 存储帐户将链接到 Application Insights 资源。 每个 Application Insights 资源只能有一个存储帐户，且两者必须位于同一位置。 你可以使用具有多个 Application Insights 资源的同一存储帐户。

首先，需要向 Application Insights Profiler 和 Snapshot Debugger 服务授予对存储帐户的访问权限。 若要授予访问权限，请 `Storage Blob Data Contributor` `Diagnostic Services Trusted Storage Access` 通过存储帐户中的访问控制 (IAM) 页将角色添加到名为的 AAD 应用程序，如图1.0 所示。 

步骤： 
1. 单击 "添加角色分配" 部分中的 "添加" 按钮 
1. 选择“存储 Blob 数据参与者”角色 
1. 选择 "分配访问权限" 部分中的 "Azure AD 用户、组或服务主体"。 
1. 搜索 & 选择 "诊断服务受信任的存储访问" 应用 
1. 保存更改

_ ![ 图 1.0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _图 1.0_ 

添加角色后，它将显示在 "角色分配" 部分下，如下图1.1 所示。 
_ ![ 图 1.1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _图 1.1_ 

如果还使用的是专用链接，则需要使用另外一个配置，以允许从你的虚拟网络连接到受信任的 Microsoft 服务。 请参阅 [存储网络安全文档](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>将你的存储帐户链接到你的 Application Insights 资源
若要将 BYOS 配置为代码级诊断 (Profiler/调试器) ，有三个选项：

* 使用 Azure PowerShell Cmdlet
* 使用 Azure 命令行界面 (CLI) 
* 使用 Azure 资源管理器模板

#### <a name="configure-using-azure-powershell-cmdlets"></a>使用 Azure PowerShell Cmdlet 进行配置

1. 请确保已安装 Az PowerShell 4.2.0 或更高版本。

    若要安装 Azure PowerShell，请参阅 [官方 Azure PowerShell 文档](/powershell/azure/install-az-ps)。

1. 安装 Application Insights PowerShell 扩展。
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. 用 Azure 帐户登录
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    有关如何登录的详细信息，请参阅 [AzAccount 文档](/powershell/module/az.accounts/connect-azaccount)。

1. 删除链接到 Application Insights 资源的上一个存储帐户。

    模式：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    例如：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. 将存储帐户与 Application Insights 资源连接起来。
    
    模式：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    例如：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>使用 Azure CLI 进行配置

1. 请确保已安装 Azure CLI。

    若要安装 Azure CLI，请参阅 [官方 Azure CLI 文档](/cli/azure/install-azure-cli)。

1. 安装 Application Insights CLI 扩展。
    ```powershell
    az extension add -n application-insights
    ```

1. 将存储帐户与 Application Insights 资源连接起来。

    模式：
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    例如：
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    预期输出：
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > 若要在链接的存储帐户上执行更新 Application Insights 资源，请参阅 [APPLICATION INSIGHTS CLI 文档](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)。

#### <a name="configure-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板进行配置

1. 在) 上创建一个具有以下内容的 Azure 资源管理器模板文件 ( # B0。
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 运行以下 PowerShell 命令，以将以前的模板部署 (创建链接存储帐户) 。

    模式：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    例如：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. 在 PowerShell 控制台中出现提示时提供以下参数：
    
    |           参数           |                                说明                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | 要启用 BYOS 的 Application Insights 资源的名称。            |
    | storage_account_name          | 要用作 BYOS 的存储帐户资源的名称。 |
    
    预期输出：
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. 启用代码级别诊断 (探查器/调试器) 通过 Azure 门户所需的工作负荷。  (应用服务 > Application Insights) _ ![ 图 2.0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _图 2.0_

## <a name="troubleshooting"></a>疑难解答
### <a name="template-schema-schema_uri-isnt-supported"></a>不支持模板架构 "{schema_uri}"。
* 请确保模板的 `$schema` 属性有效。 它必须遵循以下模式： `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* 请确保模板的位于 `schema_version` 有效的值中： `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` 。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>找不到位置 "{location}" 的已注册资源提供程序。
* 请确保资源的 `apiVersion` `microsoft.insights/components` 为 `2015-05-01` 。
* 请确保资源的 `apiVersion` `linkedStorageAccount` 为 `2020-03-01-preview` 。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>存储帐户位置应与 AI 组件位置匹配。
* 请确保 Application Insights 资源的位置与存储帐户相同。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

有关常规探查器故障排除，请参阅 [探查器疑难解答文档](profiler-troubleshooting.md)。

若要进行常规 Snapshot Debugger 故障排除，请参阅 [Snapshot Debugger 疑难解答文档](snapshot-debugger-troubleshoot.md)。 

## <a name="faqs"></a>常见问题解答
* 如果我启用了 Profiler 或快照，然后启用了 BYOS，则我的数据将迁移到我的存储帐户中吗？
    _不能。_

* 是否 BYOS 使用静态加密和 Customer-Managed 密钥？
    _是的，为实现精确，BYOS 是启用了探查器/调试器并 Customer-Manager 键的必备组件。_

* BYOS 是否会在与 Internet 隔离的环境中工作？
    _是的。事实上，BYOS 是对隔离网络方案的要求。_

* Customer-Managed 键和专用链接都已启用时，是否会 BYOS？ 
    _是的，可以这样做。_

* 如果我启用了 BYOS，可以使用诊断服务存储帐户来存储收集的数据吗？ 
    _是的，你现在可以，但目前不支持从你的 BYOS 迁移数据。_

* 启用 BYOS 后，是否会接管 it （存储和网络）的所有相关成本？ 
    _是_
