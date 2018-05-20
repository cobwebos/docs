---
title: 在 Azure Stack 中将 API 版本配置文件与 GO 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 GO 配合使用。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd2d0c46c0829a73d32c96b506b9f2111eda3c84
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Go 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="go-and-version-profiles"></a>Go 和版本配置文件

配置文件结合了不同服务的不同版本的不同资源类型。 使用配置文件有助于混合和匹配不同的资源类型。 配置文件可以：

 - 通过锁定到特定的 API 版本提供应用程序稳定性。
 - 提供应用程序与 Azure Stack 和区域性 Azure 数据中心的兼容性。

在 Go SDK 中，配置文件在 profiles/ 路径下提供，其版本采用 **YYYY-MM-DD** 格式。 目前，最新的 Azure Stack 配置文件版本为 **2017-03-09**。 若要从配置文件导入给定的服务，需要从配置文件导入其相应的模块。 例如，若要从 **2017-03-09** 配置文件导入**计算**服务：

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>安装 Azure SDK for Go

  1. 安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
  2. 安装 [Go 编程语言](https://golang.org/dl)。  
  Azure 的 API 配置文件需要 Go 1.9 或更高版本。
  3. 运行以下 bash 命令，安装 Go Azure SDK 及其依赖项：
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO SDK

可在以下位置找到有关 Azure GO SDK 的详细信息：
- [安装 Azure SDK for Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install) 中的“Azure Go SDK”。
- GitHub 上公开发布了 Azure Go SDK，网址为 [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)。

### <a name="go-autorest-dependencies"></a>Go-AutoRest 依赖项

GO SDK 依赖 Azure Go-AutoRest 模块将 REST 请求发送到 Azure 资源管理器终结点。 需要从 [GitHub 上的 Azure Go-AutoRest](https://github.com/Azure/go-autorest) 导入 Azure Go-AutoRest 模块依赖项。 可以在 **Install** 节中找到 install bash 命令。

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>如何在 Azure Stack 上使用 GO SDK 配置文件

在 Azure Stack 上运行 Go 代码的示例：
  1. 安装 Azure SDK for Go 及其依赖项。 有关说明，请参阅上一部分[安装 Azure SDK for Go](#install-azure-sdk-for-go)。
  2. 从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行 Go 代码所需的信息。

  > [!Note]  
  > Azure Stack 开发工具包 (ASDK) 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`  
  > 集成系统中的 **ResourceManagerUrl** 为：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > 检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

  3. 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的信息，请参阅[在 Azure Stack 中创建产品/服务的订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。 
  4. 创建具有“订阅”范围和“所有者”角色的服务主体。 保存服务主体 ID 和密码。 有关为 Azure Stack 创建服务主体的信息，请参阅[创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad)。 已设置 Azure Stack 环境。
  5. 在代码中从 Go SDK 配置文件导入服务模块。 最新版本的 Azure Stack 配置文件为 **2017-03-09**。 例如，若要从 **2017-03-09** 配置文件类型导入网络模块： 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. 在函数中，使用 **New** Client 函数调用创建客户端并对其进行身份验证。 若要创建虚拟网络客户端，可使用以下代码：  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  将 `<baseURI>` 设置为在步骤 2 中使用的 **ResourceManagerUrl** 值。
  将 `<subscriptionID>` 设置为在步骤 3 中保存的 **SubscriptionID** 值。
  若要创建令牌，请参阅下面的“身份验证”部分。  

  7. 使用在上一步骤中创建的客户端调用 API 方法。 例如，若要上一步骤中的客户端创建虚拟网络： 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  有关使用 Go SDK 配置文件在 Azure Stack 中创建虚拟网络的完整示例，请参阅[示例](#example)。

## <a name="authentication"></a>身份验证

若要使用 Go SDK 从 Azure Active Directory 获取 Authorizer 属性，请安装 Go-AutoRest 模块。 这些模块应已通过“Go SDK”安装完成安装；如果尚未安装，请安装 [GitHub 上的身份验证包](https://github.com/Azure/go-autorest/tree/master/autorest/adal)。

Authorizer 必须设置为资源客户端的授权者。 可通过不同的方法获取 Authorizer；此处提供了完整列表。

本部分介绍一种使用客户端凭据在 Azure Stack 上获取授权者令牌的常用方法：

  1. 如果可以使用订阅中具有所有者角色的某个服务主体，请跳过此步骤。 否则，请创建服务主体[说明]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)，并为其分配范围限定于订阅[说明]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)的“所有者”角色。 保存服务主体应用程序 ID 和机密。 

  2. 在代码中从 Go-AutoRest 导入 **adal** 包。 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. 使用 NewOAuthConfig 方法从 **adal** 模块创建 oauthConfig。 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  将 `<activeDirectoryEndpoint>` 设置为在本文上一部分中检索的 ResourceManagerUrl 元数据中的“loginEndpoint”属性值。
  将 `<tenantID>` 值设置为 Azure Stack 租户 ID。 

  4. 最后，使用 NewServicePrincipalToken 方法从 adal 模块创建服务主体令牌。 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  将 `<activeDirectoryResourceID>` 设置为在本文上一部分中检索的 ResourceManagerUrl 元数据中的“对象”列表值之一。  
  将 `<clientID>` 设置为在本文上一部分中创建服务主体时保存的服务主体应用程序 ID。  
  将 `<clientSecret>` 设置为在本文上一部分中创建服务主体时保存的服务主体应用程序机密。  

## <a name="example"></a>示例

本部分演示用于在 Azure Stack 中创建虚拟网络的 Go 代码示例。 有关 Go SDK 的完整示例，请参阅 [Azure Go SDk 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。 可从该存储库的服务文件夹中的 hybrid/ 路径下获取 Azure Stack 示例。

> [!Note]  
> 若要运行此示例中的代码，请验证使用的订阅是否具有列为“已注册”的“网络”资源提供程序。 若要验证，请在 Azure Stack 门户中找到该订阅，然后单击“资源提供程序”。

1. 在代码中导入所需的包。 应该使用 Azure Stack 上最新的可用配置文件来导入网络模块。 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. 定义环境变量。 若要创建虚拟网络，你需要有一个资源组。 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. 定义环境变量后，使用 **adal** 包添加用于创建身份验证令牌的方法。 请参阅上一部分中有关身份验证的详细信息。
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. 添加 main 方法。 main 方法首先使用上一步骤中定义的方法获取令牌。 然后，该方法使用配置文件中的网络模块创建客户端。 最后创建虚拟网络。 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>后续步骤
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
