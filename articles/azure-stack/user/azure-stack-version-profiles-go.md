---
title: 在 Azure Stack 中将 API 版本配置文件与 GO 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 GO 配合使用。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: f865d08f742ebd1072b80a95960609e6ae5f4a82
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098402"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Go 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="go-and-version-profiles"></a>Go 和版本配置文件

配置文件结合了不同服务的不同版本的不同资源类型。 使用配置文件有助于混合和匹配不同的资源类型。 配置文件可提供以下优势：

- 通过锁定到特定的 API 版本提供应用程序稳定性。
- 提供应用程序与 Azure Stack 和区域性 Azure 数据中心的兼容性。

在 Go SDK 中，配置文件可用于配置文件路径下，其版本采用**年-月-日**格式。 现在，最新的 Azure Stack API 配置文件版本是**2017年-03-09**。 若要从配置文件导入给定的服务，请从配置文件导入其相应的模块。 例如，若要导入**计算**服务从**2017年-03-09**配置文件，请使用以下代码：

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>安装 Azure SDK for Go

1. 安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
2. 安装 [Go 编程语言](https://golang.org/dl)。 适用于 Azure 的 API 配置文件需要 Go 1.9 或更高版本。
3. 运行以下 bash 命令，安装 Go Azure SDK 及其依赖项：

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Go SDK

您可以通过以下链接找到有关 Azure GO SDK 的详细信息：

- [安装 Azure SDK for Go](/azure/azure-sdk-go-install) 中的“Azure Go SDK”。
- Azure Go SDK，在 GitHub 上公开可用[azure sdk 的 go](https://github.com/Azure/azure-sdk-for-go)存储库。

### <a name="go-autorest-dependencies"></a>Go-AutoRest 依赖项

Go SDK 依赖于 Azure **Go-autorest**模块将 REST 请求发送到 Azure 资源管理器终结点。 必须导入 Azure **Go-autorest**从模块依赖项[Azure Go-autorest GitHub 上](https://github.com/Azure/go-autorest)。 可以在 **Install** 节中找到 install bash 命令。

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>如何在 Azure Stack 上使用 Go SDK 配置文件

若要在 Azure Stack 上运行的 Go 代码示例，请按照下列步骤：

1. 安装 Azure SDK for Go 及其依赖项。 有关说明，请参阅上一节[安装 Azure SDK for Go](#install-azure-sdk-for-go)。
2. 从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行 Go 代码所需的信息。

   > [!NOTE]  
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

3. 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的信息，请参阅[在 Azure Stack 中创建产品/服务](../azure-stack-subscribe-plan-provision-vm.md)。

4. 创建服务主体**订阅**作用域和**所有者**角色。 保存服务主体 ID 和机密。 有关创建 Azure Stack 的服务主体的信息，请参阅[创建服务主体](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)。 现在已设置 Azure Stack 环境。

5. 从你的代码中的 Go SDK 配置文件导入服务模块。 最新版本的 Azure Stack 配置文件为 **2017-03-09**。 例如，若要导入网络模块从**2017年-03-09**配置文件类型，请使用以下代码：

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. 在函数中，创建并进行身份验证的客户端**新建**客户端函数调用。 若要创建虚拟网络客户端，可使用以下代码：  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   设置`<baseURI>`到**ResourceManagerUrl**使用在步骤 2 中的值。 设置`<subscriptionID>`到**SubscriptionID**步骤 3 中保存的值。

   若要创建令牌，请参阅以下部分。  

7. 使用在上一步骤中创建的客户端调用 API 方法。 例如，若要使用在上一步中的客户端创建虚拟网络，请参阅下面的示例：

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

使用 Go SDK 配置文件在 Azure Stack 上创建虚拟网络的完整示例，请参阅[示例](#example)。

## <a name="authentication"></a>身份验证

若要获取**Authorizer**属性从 Azure Active Directory 使用 Go SDK 中，安装**Go-autorest**模块。 这些模块应已安装了与"Go SDK"安装;如果没有，请安装[从 GitHub 的身份验证包](https://github.com/Azure/go-autorest/tree/master/autorest/adal)。

Authorizer 必须设置为资源客户端的授权者。 有不同的方式来获取授权者令牌在 Azure Stack 上使用的客户端凭据：

1. 如果可以使用订阅中具有所有者角色的某个服务主体，请跳过此步骤。 否则创建[服务主体](azure-stack-create-service-principals.md)并将其分配的"所有者"角色[作用域为你的订阅](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role)。 保存服务主体应用程序 ID 和机密。

2. 导入**adal**在代码中的从 Go-autorest 包。

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. 创建**oauthConfig**通过使用 NewOAuthConfig 方法从**adal**模块。

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   设置`<activeDirectoryEndpoint>`的值`loginEndpoint`属性从`ResourceManagerUrl`上本文档的上一节中检索元数据。 设置`<tenantID>`值为 Azure Stack 租户 id。

4. 最后，通过使用创建服务主体令牌`NewServicePrincipalToken`方法从**adal**模块：

   ```go
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
   ```

    设置`<activeDirectoryResourceID>`从"audience"列表中的值之一**ResourceManagerUrl**本文上一部分中检索元数据。
    设置`<clientID>`为服务主体应用程序 ID 时保存的这篇文章在上一部分中创建服务主体。
    设置`<clientSecret>`到本文上一部分中创建服务主体时保存的服务主体的应用程序机密。

## <a name="example"></a>示例

此示例演示在 Azure Stack 创建虚拟网络的 Go 代码示例。 Go SDK 的完整示例，请参阅[Azure Go SDK 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。 可用的存储库的服务文件夹中的混合路径下 azure Stack 示例。

> [!NOTE]  
> 若要在此示例中运行的代码，请使用的订阅具有**网络**资源提供程序列为**已注册**。 若要验证，订阅在 Azure Stack 门户中，查找并选择**资源提供程序。**

1. 导入在代码中所需的包。 使用 Azure Stack 上的最新的配置文件导入网络模块：

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. 定义环境变量。 若要创建虚拟网络，必须具有一个资源组。

   ```go
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
   ```

3. 现在，已经定义环境变量，添加一个方法以使用创建的身份验证令牌**adal**包。 有关身份验证的详细信息，请参阅上一节。

   ```go
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
   ```

4. 添加`main`方法。 `main`方法首先使用在上一步中定义的方法来获取令牌。 然后，它通过使用配置文件中的网络模块创建客户端。 最后创建虚拟网络。

   ```go
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
   ```

## <a name="next-steps"></a>后续步骤

- [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
- [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
