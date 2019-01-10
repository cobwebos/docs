---
title: 在 Azure Stack 中将 API 版本配置文件与 Ruby 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 Ruby 配合使用。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: df3222c361e4a8f6451326d967d574b1eb8eed1b
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157439"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Ruby 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="ruby-and-api-version-profiles"></a>Ruby 与 API 版本配置文件

用于 Azure Stack 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 Ruby SDK 中的 API 配置文件可以用来在公有云 Azure 资源和 Azure Stack 上的资源之间进行切换，实现混合云开发。

API 配置文件是资源提供程序和服务版本的组合。 可以使用 API 配置文件来组合不同的资源类型。

- 若要使用的所有服务的最新版本，请使用**最新**的 Azure SDK 汇总 gem 的配置文件。
- 若要使用与 Azure Stack 兼容的服务，请使用 Azure SDK 汇总 gem 的 **V2017_03_09** 配置文件。
- 若要使用最新**api 版本**的一种服务，使用**最新**特定 gem 的配置文件。 例如，如果你想要使用最新**api 版本**的单独的计算服务，使用**最新**的配置文件**计算**gem。
- 若要使用特定**api 版本**对于服务，使用 gem 内定义的特定 API 版本。

> [!NOTE]
> 可以在同一应用程序中组合所有选项。

## <a name="install-the-azure-ruby-sdk"></a>安装 Azure Ruby SDK

- 按照官方安装说明[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
- 按照官方安装说明[Ruby](https://www.ruby-lang.org/en/documentation/installation/)。
  - 安装时，选择**添加到 PATH 变量的 Ruby**。
  - 在出现提示时的 Ruby 安装过程中安装开发工具包。
  - 接下来，安装捆绑包，使用以下命令：  
    `Gem install bundler`
- 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 [此处](../azure-stack-subscribe-plan-provision-vm.md)提供创建订阅的说明。
- 创建服务主体并保存其 ID 和机密。 [此处](../azure-stack-create-service-principals.md)提供创建 Azure Stack 服务主体的说明。
- 确保服务主体在订阅上具有“参与者/所有者”角色。 [此处](../azure-stack-create-service-principals.md)提供如何为服务主体分配角色的说明。

## <a name="install-the-rubygem-packages"></a>Rubygem 包安装

可以直接安装 Azure Rubygem 包。

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Azure 资源管理器 Ruby SDK 处于预览状态，可能会在即将发布的版本中的重大界面更改。 次要版本中的增加可能表示重大更改。

## <a name="use-the-azuresdk-gem"></a>使用 azure_sdk gem

Gem **azure_sdk**，是 Ruby SDK 中所有受支持的 gem 的汇总。 此 gem 包含的 **最新** 配置文件支持所有服务的最新版本。 它引入了进行版本控制的  **V2017_03_09**  配置文件，该文件是为 Azure Stack 生成的。

可使用以下命令安装 azure_sdk 汇总 gem：  

```Ruby  
gem install 'azure_sdk
```

## <a name="prerequisites"></a>必备组件

若要与 Azure Stack 配合使用 Ruby Azure SDK，必须提供以下值，并将使用环境变量的值。 请参阅表后针对操作系统的说明，了解如何设置环境变量。

| 值 | 环境变量 | 说明 |
| --- | --- | --- | --- |
| 租户 ID | AZURE_TENANT_ID | Azure Stack [租户 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) 的值。 |
| 客户端 ID | AZURE_CLIENT_ID | 在本文档上一部分创建服务主体时保存的服务主体应用程序 ID。  |
| 订阅 ID | AZURE_SUBSCRIPTION_ID | [订阅 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) 用于访问 Azure Stack 中的套餐。 |
| 客户端机密 | AZURE_CLIENT_SECRET | 创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | ARM_ENDPOINT | 请参阅 [Azure Stack 资源管理器终结点](#The-azure-stack-resource-manager-endpoint)。  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack 资源管理器终结点

Microsoft Azure 资源管理器是一种管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行代码所需的信息。

 > [!NOTE]  
 > **ResourceManagerUrl**中 Azure Stack 开发工具包 (ASDK) 是：`https://management.local.azurestack.external/` **ResourceManagerUrl**集成系统中是： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > 检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 示例 JSON 文件：

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
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
若要设置的环境变量，在 Windows 命令提示符中，使用以下格式：  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS、 Linux、 和基于 Unix 的系统**基于在 Unix 系统，则可以使用以下命令：  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>现有 API 配置文件

Azure_sdk 汇总 gem 具有以下两个配置文件：

1. **V2017_03_09**  
  为 Azure Stack 生成的配置文件。 请将此配置文件用于会与 Azure Stack 最兼容的服务。
2. **最新**  
  配置文件包含所有服务的最新版本。 使用所有服务的最新版本。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要](azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API 配置文件的使用

可以使用以下代码以实例化配置文件客户端。 此参数只是 Azure Stack 或其他私有云所需要的。 默认情况下，公有云 Azure 已经有这些设置。

```Ruby  
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
```

配置文件客户端可用来访问各个资源提供程序，例如计算、 存储和网络：

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>定义 Azure Stack 环境设置函数

若要对 Azure Stack 环境的服务主体进行身份验证，定义使用的终结点`get_active_directory_settings()`。 此方法使用**ARM_Endpoint**时建立您的环境变量设置的环境变量：

```Ruby  
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
```

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

可以使用下面的示例使用 Ruby 和 Azure Stack API 配置文件创建解决方案的引用作为 GitHub 上找到：

- [使用 Ruby 管理 Azure 资源和资源组](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
- [Manage virtual machines using Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)（使用 Ruby 管理虚拟机）
- [在 Ruby 中使用模板部署启用 SSH 的 VM](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>示例资源管理器和组

若要运行示例，请确保已安装 Ruby。 如果使用 Visual Studio Code，下载的 Ruby SDK 扩展。

> [!NOTE]  
> [Manage Azure resources and resource groups with Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)（使用 Ruby 管理 Azure 资源和资源组）中提供了示例的存储库。

1. 克隆存储库：

   ```bash
   git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
   ```

2. 安装使用捆绑包的依赖项：

   ```Bash
   cd resource-manager-ruby-resources-and-groups\Hybrid\
   bundle install
   ```

3. 创建 Azure 服务主体使用 PowerShell 和检索所需的值。

   有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](../azure-stack-create-service-principals.md)。

   所需值为：
   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - 订阅 ID
   - 资源管理器终结点

   使用从已创建的服务主体检索的信息设置以下环境变量。

   - export AZURE_TENANT_ID={你的租户 ID}
   - export AZURE_CLIENT_ID={你的客户端 ID}
   - export AZURE_CLIENT_SECRET={你的客户端机密}
   - export AZURE_SUBSCRIPTION_ID={你的订阅 ID}
   - export ARM_ENDPOINT={你的 AzureStack 资源管理器 URL}

   > [!NOTE]  
   > 在 Windows 上，请使用 set 而不是 export。

4. 请确保该位置变量设置为你的 Azure Stack 位置;例如， `LOCAL="local"`。

5. 添加以下代码行，如果你使用 Azure Stack 或其他私有云定位右 active directory 终结点：

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. 在选项变量中，添加 Active Directory 设置和要使用 Azure Stack 的基 URL:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. 创建针对 Azure Stack 配置文件的配置文件客户端：

   ```ruby  
   client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
   ```

8. 若要通过 Azure Stack 进行服务主体身份验证，应使用 **get_active_directory_settings()** 来定义终结点。 此方法使用**ARM_Endpoint**时建立您的环境变量设置的环境变量：

   ```ruby  
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
   ```

9. 运行示例。

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>后续步骤

- [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
- [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
