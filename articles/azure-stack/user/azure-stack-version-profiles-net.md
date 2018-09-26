---
title: 通过.NET SDK 在 Azure Stack 中使用 API 版本配置文件 |Microsoft Docs
description: 了解如何使用.NET 在 Azure Stack 中使用 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184987"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>通过在 Azure Stack 中的.NET 中使用 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

.NET SDK 为 Azure Stack 资源管理器提供了工具来帮助你构建和管理基础结构。 资源提供程序 SDK 中的包括计算、 网络、 存储、 应用服务，并[KeyVault](../../key-vault/key-vault-whatis.md)。 .NET SDK 包含 14 NuGet 包必须在每次下载到你项目的解决方案以合并配置文件信息。 但是，专门下载哪些资源提供程序将为 2018年-03-01-混合或 2017年-03-09-配置文件使用以便为你的应用程序优化内存。 每个包都包括资源提供程序、 相应的 API 版本，以及它所属的 API 配置文件。 在.NET SDK 中的 API 配置文件帮助您全局 Azure 资源和 Azure Stack 上的资源之间进行切换，从而启用混合云开发。

## <a name="net-and-api-version-profiles"></a>.NET 和 API 版本配置文件

将 API 配置文件是资源提供程序和 API 版本的组合。 将 API 配置文件可用于获取资源提供程序包中的每种资源类型的最新、 最稳定版本。

-   要使用的所有服务的最新版本，请使用**最新**包的配置文件。 此配置文件属于**Microsoft.Azure.Management** NuGet 包。

-   若要使用与 Azure Stack 兼容的服务，使用**Microsoft.Azure.Management.Profiles.hybrid\_2018年\_03\_01。*ResourceProvider*。 0.9.0-preview.nupkg**或**Microsoft.Azure.Management.Profiles.hybrid\_2017年\_03\_09。*ResourceProvider*。 0.9.0-preview.nupkg**包。

    -   有两个包的每个配置文件每个资源提供程序。

    -   絋粄**ResourceProvider**更高版本的 NuGet 包的部分更改为正确的访问接口。

-   若要使用最新的 API 版本的服务，使用**最新**特定 NuGet 包的配置文件。 例如，如果你想要使用**最新 API**计算服务，仅使用版本**最新**的配置文件**计算**包。 **最新**配置文件属于**Microsoft.Azure.Management** NuGet 包。

-   若要使用特定资源提供程序中的资源类型特定的 API 版本，使用包中定义的特定 API 版本。

请注意，可以组合使用所有相同的应用程序中的选项。

## <a name="install-the-azure-net-sdk"></a>安装 Azure.NET SDK

1.  安装 Git。 有关说明，请参阅[入门-安装 Git][]。

2.  若要安装正确的 NuGet 包，请参阅[查找和安装包][]。

3.  需要安装的包依赖于你想要使用的配置文件版本。 配置文件版本的包名称是：

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018年\_03\_01。*ResourceProvider*。 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017年\_03\_09。*ResourceProvider*。 0.9.0-preview.nupkg**

4.  若要安装 Visual Studio Code 的正确的 NuGet 包，请参阅以下链接以下载[NuGet 包管理器的说明][]。

5.  如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 若要创建订阅的说明，请参阅[在 Azure Stack 中创建产品/服务][]。

6.  创建服务主体并保存客户端 ID 和客户端机密。 有关如何创建适用于 Azure Stack 的服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限][]。 请注意，客户端 ID 也称为应用程序 ID 时创建服务主体。

7.  请确保服务主体上你的订阅具有参与者/所有者角色。 有关如何向服务主体分配角色的说明，请参阅[提供对 Azure Stack 的应用程序访问权限][]。

## <a name="prerequisites"></a>必备组件

若要使用.NET 的 Azure SDK 的 Azure Stack，必须提供以下值，并将使用环境变量的值。 若要设置环境变量，请参阅下表为操作系统的说明进行操作。

| 值                     | 环境变量   | 说明                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID                 | AZURE_TENANT_ID       | Azure Stack 的值[*租户 ID*][]。                                                                          |
| 客户端 ID                 | AZURE_CLIENT_ID       | 在本文上一部分中创建服务主体时保存主体的应用程序 ID 的服务。 |
| 订阅 ID           | AZURE_SUBSCRIPTION_ID | [*订阅 ID* ][]是如何访问产品/服务在 Azure Stack 中。                                                      |
| 客户端机密             | AZURE_CLIENT_SECRET   | 创建服务主体时保存服务主体的应用程序机密。                                      |
| 资源管理器终结点 | ARM_ENDPOINT           | 请参阅[ *Azure Stack 资源管理器终结点*][]。                                                                    |

若要查找在 Azure Stack 租户 ID，请按照的说明操作[此处](../azure-stack-csp-ref-operations.md)。 若要设置环境变量，请执行以下操作：

### <a name="microsoft-windows"></a>Microsoft Windows

若要在 Windows 命令提示符中设置环境变量，请使用以下格式：

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS、 Linux 和基于 Unix 的系统

在基于 Unix 的系统，可以使用以下命令：

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack 资源管理器终结点

Microsoft Azure 资源管理器是一种管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行代码所需的信息。

请注意以下事项：

- Azure Stack 开发工具包 (ASDK) 中的 **ResourceManagerUrl** 为： https://management.local.azurestack.external/

- **ResourceManagerUrl**在集成系统是：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`来检索所需的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

示例 JSON 文件：

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>现有的 API 配置文件

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018年\_03\_01。*ResourceProvider*。 0.9.0-preview.nupkg**： 为 Azure Stack 构建最新的配置文件。 使用服务的此配置文件是最，只要是 1808年戳记上与 Azure Stack 兼容或更多。

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017年\_03\_09。*ResourceProvider*。 0.9.0-preview.nupkg**： 如果您是在低于 1808年生成标记上，使用此配置文件。

3.  **最新**： 包含的所有服务的最新版本的配置文件。 使用所有服务的最新版本。 此配置文件属于**Microsoft.Azure.Management** NuGet 包。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要][]。

## <a name="azure-net-sdk-api-profile-usage"></a>Azure.NET SDK API 配置文件使用情况

下面的代码应该用于实例化配置文件客户端。 此参数只是 Azure Stack 或其他私有云所需要的。 默认情况下，公有云 Azure 已经有这些设置。

下面的代码需要对 Azure Stack 上的服务主体进行身份验证。 它创建的令牌由租户 ID 和身份验证基，它是特定于 Azure Stack。

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

这样，便可以使用 API 配置文件 NuGet 包来部署你成功地向 Azure Stack 的应用程序。

## <a name="define-azure-stack-environment-setting-functions"></a>定义 Azure Stack 环境设置函数

若要对 Azure Stack 环境的服务主体进行身份验证，请使用以下代码：

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

这会替代初始化服务客户端向 Azure Stack 进行身份验证。

## <a name="samples-using-api-profiles"></a>使用 API 配置文件示例

可以使用下面的示例作为创建使用.NET 和 Azure Stack API 配置文件的解决方案的参考在 GitHub 存储库中找到。

-   [为虚拟机、 vNet、 资源组和存储帐户的测试项目][]
-   使用.NET 管理虚拟机

### <a name="sample-unit-test-project"></a>示例单元测试项目 

1.  克隆存储库使用以下命令：

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  创建 Azure 服务主体并分配角色以访问订阅。 有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 使用证书创建服务主体][]。

3.  检索所需的以下值：

    1.  租户 ID
    2.  客户端 ID
    3.  客户端机密
    4.  订阅 ID
    5.  资源管理器终结点

4.  设置以下环境变量使用的服务主体使用命令提示符创建从检索的信息：

    1.  export AZURE_TENANT_ID={你的租户 ID}
    2.  export AZURE_CLIENT_ID={你的客户端 ID}
    3.  export AZURE_CLIENT_SECRET={你的客户端机密}
    4.  export AZURE_SUBSCRIPTION_ID={你的订阅 ID}
    5.  导出 ARM_ENDPOINT = {Azure Stack 资源管理器 URL}

   在 Windows 中，使用**设置**而不是**导出**。

5.  请确保位置变量设置为你的 Azure Stack 位置。 例如，本地 ="local"。

6.  设置将允许你对 Azure Stack 进行身份验证的自定义登录凭据。 请注意，在此示例中的授权文件夹中包含代码的这一部分。

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  如果使用 Azure Stack 来重写初始化服务客户端向 Azure Stack 进行身份验证，请添加以下代码。 请注意该代码部分已包含在此示例中的授权文件夹中。

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  使用 NuGet 包管理器，搜索"2018年-03-01-混合"，并安装与此配置文件相关的计算、 网络、 存储、 密钥保管库和应用服务资源提供程序的包。

2.  在.cs 文件中每个任务，设置适用于 Azure Stack 所需的参数。 一个示例，如下所示为任务`CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  右键单击每个任务并选择**运行测试**。

    1.  在侧窗格窗口上的绿色复选标记警报创建每个任务已成功根据给定的参数。 请检查你的 Azure Stack 订阅以确保已成功创建资源。

    2.  有关如何运行单元测试的详细信息，请参阅[使用测试资源管理器运行单元测试。][]

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md)
- [支持的配置文件的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)

  [入门-安装 Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [查找和安装包]: /nuget/tools/package-manager-ui
  [NuGet 包管理器的说明]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [在 Azure Stack 中创建产品/服务]: ../azure-stack-subscribe-plan-provision-vm.md
  [提供对 Azure Stack 的应用程序访问权限]: ../azure-stack-create-service-principals.md
  [* 租户 ID *]: ../azure-stack-identity-overview.md
  [* 订阅 ID *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* Azure Stack 资源管理器终结点 *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API 配置文件的摘要]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [为虚拟机、 vNet、 资源组和存储帐户的测试项目]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [使用 Azure PowerShell 使用证书创建服务主体]: ../azure-stack-create-service-principals.md
  [使用测试资源管理器运行单元测试。]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
