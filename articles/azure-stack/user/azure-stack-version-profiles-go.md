---
title: 通过转到 Azure 堆栈中使用 API 版本配置文件 |Microsoft 文档
description: 了解如何通过转到 Azure 堆栈中使用 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84475302-EFC2-4C35-B4CF-60C857A96345
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 09a774e5dda71d87c3862a6152ff5d2c9468c40c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>使用转到 Azure 堆栈中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="go-and-version-profiles"></a>转到和版本配置文件

配置文件是具有来自不同服务的不同版本的不同资源类型的组合。 使用配置文件将帮助你混合和匹配不同的资源类型之间。 配置文件可以提供：

 - 通过锁定到特定的 API 版本的应用程序的稳定性。
 - 你的应用程序与 Azure 堆栈和区域的 Azure 数据中心的兼容性。

在转到 SDK 中，配置文件下有配置文件 / 路径中其版本**YYYY-月-日**格式。 现在，最新 Azure 堆栈的配置文件版本是**自 2017 年 1-03-09**。 若要从配置文件导入给定的服务，你需要从配置文件导入其相应的模块。 例如，若要导入**计算**服务中，从**自 2017 年 1-03-09**配置文件：

````go
import "github.com/Azure/azure-sdk-for-go/profi1es/2e17-e3-eg/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>安装 Azure SDK for 转到

  1. 安装 Git。 有关说明，请参阅[入门教程-安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
  2. 安装[转编程语言](https://golang.org/dl)。  
  Azure API 配置文件将需要转到 1.9 或更高版本。
  3. 通过运行以下 bash 命令安装转到 Azure SDK 及其依赖项：
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>转 SDK

你可以找到有关在 Azure 转 SDK 的详细信息：
- Azure 转 SDK:[安装 Azure SDK for 转](https://docs.microsoft.com/go/azure/azure-sdk-go-install)。
- Azure 转 SDK 是在处的 GitHub 上公开可用[azure sdk 的 go](https://github.com/Azure/azure-sdk-for-go)。

### <a name="go-autorest-dependencies"></a>转到 AutoRest 依赖关系

请转到 SDK 依赖于 Azure 转 AutoRest 模块将 REST 请求发送到 Azure 资源管理器终结点。 你将需要导入 Azure 转 AutoRest 模块依赖项从[GitHub 上的 Azure 转-AutoRest](https://github.com/Azure/go-autorest)。 你可以查找中的安装 bash 命令**安装**部分。

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>如何在 Azure 堆栈上使用转 SDK 配置文件

若要在 Azure 堆栈上运行的转到代码示例：
  1. 转到和其依赖项安装 Azure SDK。 有关说明，请参阅上一节[为转到安装 Azure SDK](#install-azure-sdk-for-go)。
  2. 从资源管理器终结点获取元数据信息。 终结点返回的 JSON 文件所需运行转到代码的信息。
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

  3. 如果不可用，创建订阅并保存更高版本使用的订阅 ID。 有关创建订阅的信息，请参阅[在 Azure 堆栈中创建服务的订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。 
  4. 创建具有"订阅"作用域的服务主体和**所有者**角色。 保存该服务主体的 ID 和密码。 有关创建 Azure 堆栈的服务主体的信息，请参阅[创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad)。 设置 Azure 堆栈环境。
  5. 从代码中的转 SDK 配置文件导入服务模块。 Azure 堆栈配置文件的当前版本是**自 2017 年 1-03-09**。 例如，若要导入从网络模块**自 2017 年 1-03-09**配置文件类型： 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. 在函数中，创建并进行身份验证客户端采用**新建**客户端函数调用。 若要创建的虚拟网络客户端，你可以使用以下代码：  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  设置`<baseURI>`到**ResourceManagerUrl**使用在步骤 2 中的值。
  设置`<subscriptionID>`到**SubscriptionID**从第三步保存的值。
  若要创建令牌，请参阅身份验证下面一节。  

  7. 使用你在上一步中创建的客户端调用 API 方法。 例如，若要使用此客户端上的上一步中创建虚拟网络： 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  有关在 Azure 堆栈上创建虚拟网络，通过转到 SDK 配置文件的完整示例，请参阅[示例](#example)。

## <a name="authentication"></a>身份验证

若要从 Azure Active Directory 使用转 SDK 获取授权者属性，请转到 AutoRest 模块安装。 这些模块应已安装了与"转到 SDK"安装;如果不是，安装[GitHub 上的身份验证包](https://github.com/Azure/go-autorest/tree/master/autorest/adal)。

授权者必须设置为资源客户端授权者。 有不同的方法来获取授权者;有关完整列表，请参阅此处。

本节提供的常用方法，以获取 Azure 堆栈上的授权者的令牌，通过使用客户端凭据：

  1. 如果可用的订阅的所有者角色的服务主体，跳过此步骤。 否则创建服务主体[说明]( https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals)并将其分配作用域为你的订阅"所有者"角色[说明]( https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)。 保存的服务主体的应用程序 ID 和机密。 

  2. 导入**adal**转 AutoRest 从代码中的包。 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. 使用从 NewOAuthConfig 方法创建 oauthConfig **adal**模块。 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  设置`<activeDirectoryEndpoint>`"loginEndpoint"的值从 ResourceManagerUrl 元数据检索属性上本文档的上一节。
  设置`<tenantID>`值与你的 Azure 堆栈租户 id。 

  4. 最后，通过从 adal 模块 NewServicePrincipalToken 方法创建服务主体的令牌。 

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
  
  设置`<activeDirectoryResourceID>`到在"访问群体"的值之一从 ResourceManagerUrl 元数据检索列表上本文档的上一节。  
  设置`<clientID>`对服务主体的应用程序 ID 保存在本文档前面部分创建服务主体时。  
  设置`<clientSecret>`对服务主体的应用程序密钥保存在本文档前面部分创建服务主体时。  

## <a name="example"></a>示例

本部分演示转到代码以在 Azure 堆栈上创建虚拟网络的示例。 有关转 SDK 的完整示例，请参阅[Azure 转 SDk 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。 Azure 堆栈示例下混合有 / 内的存储库的服务文件夹的路径。
> [!note]  
> 若要运行此示例中的代码，请使用的订阅具有**网络**资源提供程序列为**已注册**。 若要验证它，查找在 Azure 堆栈门户中，订阅并单击**资源提供程序。**

1. 导入你的代码中所需的程序包。 你应使用 Azure 堆栈上的最新的配置文件导入网络模块。 
  
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

2. 定义你的环境变量。 请注意，若要创建虚拟网络需要有一个资源组。 

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

3. 既然你已经定义你的环境变量，添加一个方法以通过使用创建身份验证令牌**adal**包。 请参阅有关上一节中的身份验证的详细信息。
  
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

4. 添加的主要方法。 Main 方法首先获取令牌，通过使用在上一步中定义的方法。 然后，它通过使用配置文件中的网络模块创建一个客户端。 最后，它会创建虚拟网络。 
  
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
