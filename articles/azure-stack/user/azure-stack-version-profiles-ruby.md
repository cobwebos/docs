---
title: 使用 Ruby Azure 堆栈中的 API 版本配置文件 |Microsoft 文档
description: 了解如何使用 Ruby Azure 堆栈中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 98d285bf8ec92e7b6baf709d4796ce2f4420c4f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>使用 Ruby Azure 堆栈中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="ruby-and-api-version-profiles"></a>Ruby 和 API 版本配置文件

Ruby SDK Azure 堆栈资源管理器提供了工具来帮助你构建和管理你的基础结构。 SDK 中的资源提供程序包括计算、 虚拟网络和存储与 Ruby 语言使用。 Ruby SDK 中的 API 配置文件启用混合云开发，通过帮助你全局 Azure 资源和 Azure 堆栈上的资源之间切换。

API 配置文件是资源提供程序和服务版本的组合。 API 配置文件可用于将不同的资源类型组合。

 - 要使用的所有服务的最新版本，使用**最新**的 Azure SDK 汇总 gem 的配置文件。
 - 若要使用 Azure 堆栈与兼容的服务，使用**V2017_03_09**的 Azure SDK 汇总 gem 的配置文件。
 - 若要使用最新的 api 版本的服务，使用**最新**特定 gem 的配置文件。 例如，如果你想要使用最新的 api 版本的单独的计算服务，则使用**最新**配置文件的**计算**gem。
 - 若要使用的服务特定的 api 版本，请使用 gem 内部定义的特定 API 版本。

> [!note] 
> 你可以组合所有相同的应用程序中的选项。

## <a name="install-the-azure-ruby-sdk"></a>安装 Azure Ruby SDK

 - 请按照官方说明安装[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
 - 请按照官方说明安装[Ruby](https://www.ruby-lang.org/en/documentation/installation/)。
    - 安装时选择**添加到 PATH 变量的 Ruby**
    - 在出现提示时的 Ruby 安装过程中安装开发人员工具包。
    - 接下来，安装捆绑包使用以下命令：  
      `Gem install bundler`
 - 如果不可用，创建订阅并保存更高版本使用的订阅 ID。 若要创建订阅的说明[此处](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。 
 - 创建服务主体并保存其 ID 和密码。 如何创建 Azure 堆栈的服务主体是[此处](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。 
 - 请确保你的服务主体将参与者/所有者角色对你的订阅。 说明如何将角色分配给服务主体是[此处](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。

## <a name="install-the-rubygem-packages"></a>安装 rubygem 包

你可以直接安装 azure rubygem 包。

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

请注意 Azure 资源管理器 Ruby SDK 处于预览状态，并且将可能有即将发布的版本中的重大界面将会更改。 次要版本中的增加的号可能指示重大更改。

## <a name="usage-of-the-azuresdk-gem"></a>Azure_sdk gem 的使用情况

Gem，azure_sdk，是 Ruby SDK 中所有受支持的 gem 的汇总。 此 gem 组成**最新**支持的所有服务的最新版本的配置文件。 它引入了版本控制的配置文件**V2017_03_09** Azure 堆栈为生成的配置文件。

你可以使用以下命令安装 azure_sdk 汇总 gem:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>先决条件

若要与 Azure 堆栈使用 Ruby Azure SDK，必须提供以下值，并将使用环境变量的值。 在你的操作系统上设置环境变量的表后，请参阅的说明。 

| 值 | 环境变量 | 说明 | 
| --- | --- | --- | --- |
| 租户 ID | AZURE_TENANT_ID | Azure 堆栈的值[租户 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview)。 |
| 客户端 ID | AZURE_CLIENT_ID | 服务主体的应用程序 ID 保存在本文档前面部分创建服务主体时。  |
| 订阅 ID | AZURE_SUBSCRIPTION_ID | [订阅 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions)是如何访问提供 Azure 堆栈中。 |
| 客户端机密 | AZURE_CLIENT_SECRET | 服务主体的应用程序机密保存时创建服务主体。 |
| 资源管理器终结点 | ARM_ENDPOINT | 请参阅[Azure Stack 资源管理器 endpoin](#The-azure-stack-resource-manager-endpoint)。  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure 堆栈资源管理器终结点

Microsoft Azure 资源管理器是一个管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以处理这些任务，作为一个组，而不是单独进行，在单个操作中。

你可以从资源管理器终结点获取元数据信息。 终结点返回的 JSON 文件与运行你的代码所需的信息。
  > [!note]  
  > **ResourceManagerUrl**中 Azure 堆栈开发工具包 (ASDK) 是： `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl**在集成的系统都是： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > 若要检索所需的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  示例 JSON 文件：

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>设置环境变量

**Microsoft Windows**  
若要设置环境变量中，在 Windows 命令提示符下，使用以下格式：  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS、 Linux 和基于 Unix 的系统**  
在基于 Unix 的系统，你可以如使用命令：  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>现有 API 配置文件

Azure_sdk 汇总 gem 具有以下两个配置文件：

1. **V2017_03_09**  
  为 Azure 堆栈生成的配置文件。 使用此服务的配置文件才能与 Azure 堆栈最兼容。
2. **最新**  
  配置文件包含的所有服务的最新版本。 使用最新版本的所有服务。

有关 Azure 堆栈和 API 的配置文件的详细信息，请参阅[摘要的 API 配置文件](azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API 配置文件使用情况

以下行用于实例化配置文件客户端。 此参数才是 Azure 堆栈或其他私有云所需的。 默认情况下，全局 Azure 已有这些设置。

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

配置文件客户端可用来访问各个资源提供程序，如计算、 存储和网络。

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>定义 AzureStack 环境设置函数

若要对 Azure 堆栈环境的服务主体进行身份验证，定义使用的终结点**get_active_directory_settings()**。 此方法使用**ARM_Endpoint**时建立你的环境变量设置的环境变量。

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

你可以使用下面的示例位于 GitHub repositoreis 作为使用 Ruby 和 Azure 堆栈 API 的配置文件创建解决方案的引用：

 - [使用 Ruby 管理 Azure 资源和资源组](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [使用 Ruby 管理虚拟机](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [在 Ruby 中使用模板部署启用 SSH 的 VM](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>示例资源管理器和组

若要运行示例，请确保你已安装 Ruby。 如果你使用的 Visual Studio Code，作为扩展以及下载 Ruby SDK。 

> [!note]  
> 获取在示例存储库"[管理 Azure 资源和资源组与 Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)"。

1. 克隆存储库。

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. 安装使用捆绑包的依赖关系。

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. 创建使用 PowerShell 的 Azure 服务主体和检索所需的值。 

  有关创建服务主体的说明，请参阅[使用 Azure PowerShell 使用证书创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。

  所需的值有：
  - 租户 ID
  - 客户端 ID
  - 客户端机密
  - 订阅 ID
  - 资源管理器终结点

  设置以下环境变量使用检索到您创建从服务主体中的信息。

  - 导出 AZURE_TENANT_ID = {你的租户 id}
  - 导出 AZURE_CLIENT_ID = {你的客户端 id}
  - 导出 AZURE_CLIENT_SECRET = {你的客户端密码}
  - 导出 AZURE_SUBSCRIPTION_ID = {你的订阅 id}
  - 导出 ARM_ENDPOINT = {你 AzureStack 资源管理器 url}

  > [!note]  
  > 在 Windows 上，而不是导出中使用组。

4. 请确保位置变量设置为你 AzureStack 的位置。 例如本地 ="local"

5. 添加以下代码行中，如果你使用 Azure 堆栈或其他私有云以面向右 active directory 终结点。

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. 内的选项变量中，添加 active directory 设置和要使用 Azure 堆栈的基 URL。 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. 创建面向 Azure 堆栈配置文件的配置文件客户端：

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. 若要对 Azure 堆栈的服务主体进行身份验证，终结点应使用定义**get_active_directory_settings()**。 此方法使用**ARM_Endpoint**时建立你的环境变量设置的环境变量。

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. 运行示例。

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
