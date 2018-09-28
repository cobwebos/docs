---
title: 使用 Java 在 Azure Stack 中使用 API 版本配置文件 |Microsoft Docs
description: 了解如何使用 Java 在 Azure Stack 中使用 API 版本配置文件。
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
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414944"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>通过 Java 在 Azure Stack 中使用 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Java SDK 为 Azure Stack 资源管理器提供了工具来帮助你构建和管理基础结构。 资源提供程序 SDK 中的包括计算、 网络、 存储、 应用服务，并[KeyVault](../../key-vault/key-vault-whatis.md)。 Java SDK 通过加载正确的模块.java 文件中的 Pom.xml 文件中包括依赖项合并的 API 配置文件。 但是，你可以添加多个配置文件作为依赖项，例如**2018年-03-01-混合**，或**最新**作为 Azure 配置文件。 使用这些依赖项加载正确的模块，以便在创建资源类型时，您将能够从你想要使用这些配置文件中选择的 API 版本。 这使您可以在 Azure 中，为 Azure Stack 开发针对最新的 API 版本时使用的最新版本。 使用 Java SDK，实现真正的混合云开发人员体验。 Java SDK 中的 API 配置文件帮助您全局 Azure 资源和 Azure Stack 中的资源之间进行切换，从而启用混合云开发。

## <a name="java-and-api-version-profiles"></a>Java 和 API 版本配置文件

将 API 配置文件是资源提供程序和 API 版本的组合。 将 API 配置文件可用于获取资源提供程序包中的每种资源类型的最新、 最稳定版本。

- 若要使用的所有服务的最新版本，请使用**最新**作为依赖项的配置文件。
    
   - 若要使用的最新的配置文件，依赖项是**com.microsoft.azure**。

   - 若要使用与 Azure Stack 兼容的服务，使用**com.microsoft.azure.profile\_2018年\_03\_01\_混合**配置文件。
    
      - 这是为了指定 Pom.xml 文件中为依赖项，会自动加载模块是否您选择的正确类从下拉列表中根据需要使用.NET。
        
          - 每个模块的顶部显示，如下所示：         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - 依赖关系出现，如下所示：
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - 若要使用特定资源提供程序中的资源类型特定的 API 版本，使用 intellisense 通过定义的特定 API 版本。

请注意，可以组合使用所有相同的应用程序中的选项。

## <a name="install-the-azure-java-sdk"></a>安装 Azure Java SDK

使用以下步骤来安装 Java SDK:

1.  请按照官方说明安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

2.  按照官方安装说明[Java SDK](http://zulu.org/download/)) 和[Maven](https://maven.apache.org/)。 正确的版本是 Java 开发人员工具包版本 8。 正确的 Apache Maven 是版本 3.0 或更高版本。 JAVA_HOME 环境变量必须设置为 Java 开发工具包的安装位置，若要完成本快速入门。 有关详细信息，请参阅[通过 Java 和 Maven 创建第一个函数](../../azure-functions/functions-create-first-java-maven.md)。

3.  若要安装正确的依赖项包，请在 Java 应用程序中打开 Pom.xml 文件。 添加依赖项，如下面的代码中所示：

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  需要安装的包依赖于你想要使用的配置文件版本。 配置文件版本的包名称为：
    
   - **com.microsoft.azure.profile\_2018年\_03\_01\_混合**
   - **com.microsoft.azure**
      - **latest**

5.  如果不可用，创建订阅并保存以供将来使用的订阅 ID。 有关如何创建订阅的说明，请参阅[在 Azure Stack 中创建产品/服务](../azure-stack-subscribe-plan-provision-vm.md)。

6.  创建服务主体并保存客户端 ID 和客户端机密。 有关如何创建适用于 Azure Stack 的服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问](../azure-stack-create-service-principals.md)。 请注意，客户端 ID 也称为应用程序 ID 时创建服务主体。

7.  请确保服务主体上你的订阅具有参与者/所有者角色。 有关如何向服务主体分配角色的说明，请参阅[提供对 Azure Stack 的应用程序访问](../azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>必备组件

若要使用.NET 的 Azure SDK 的 Azure Stack，必须提供以下值，并将使用环境变量的值。 若要设置环境变量，请参阅下表为操作系统的说明进行操作。

| 值                     | 环境变量 | 说明                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 租户 ID                 | TENANT_ID            | Azure Stack 的值[<span class="underline">租户 ID</span>](../azure-stack-identity-overview.md)。                                                          |
| 客户端 ID                 | CLIENT_ID             | 在本文档上一部分创建服务主体时保存的服务主体应用程序 ID。                                                                                              |
| 订阅 ID           | SUBSCRIPTION_ID      | [<span class="underline">订阅 ID</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions)是如何访问产品/服务在 Azure Stack 中。                |
| 客户端机密             | CLIENT_SECRET        | 创建服务主体时保存的服务主体应用程序机密。                                                                                                                                   |
| 资源管理器终结点 | 终结点              | 请参阅[ <span class="underline">Azure Stack 资源管理器终结点</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)。 |
| 位置                  | RESOURCE_LOCATION    | 本地 Azure stack                                                                                                                                                                                                |

若要查找在 Azure Stack 的租户 ID，请按照的说明执行操作[此处](../azure-stack-csp-ref-operations.md)。 若要设置环境变量，请执行以下操作：

### <a name="microsoft-windows"></a>Microsoft Windows

若要在 Windows 命令提示符中设置环境变量，请使用以下格式：

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS、 Linux 和基于 Unix 的系统

在基于 Unix 的系统，可以使用以下命令：

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>现有的 API 配置文件

1.  **com.microsoft.azure.profile\_2018年\_03\_01\_混合**： 构建于 Azure Stack 的最新配置文件。 使用服务的此配置文件是最，只要是 1808年戳记上与 Azure Stack 兼容或更多。

2.  **com.microsoft.azure.profile\_2017年\_03\_09\_配置文件**： 如果您是在低于 1808年生成标记上，使用此配置文件。

3.  **com.microsoft.azure**： 包含的所有服务的最新版本的配置文件。 使用所有服务的最新版本。

有关 Azure Stack 和 API 的配置文件的详细信息，请参阅[摘要的 API 配置文件](../user/azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API 配置文件使用情况

下面的代码对 Azure Stack 上的服务主体进行身份验证。 它将创建一个令牌由租户 ID 和身份验证基，它是特定于 Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

这使您可以使用 API 配置文件依赖关系部署成功地向 Azure Stack 的应用程序。

## <a name="define-azure-stack-environment-setting-functions"></a>定义 Azure Stack 环境设置函数

若要注册 Azure Stack 云与正确的终结点，请使用以下代码：

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

`getActiveDirectorySettings`下面的代码中调用从元数据终结点中检索终结点。 它指出从进行调用的环境变量：

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

可以使用.NET 和 Azure Stack API 配置文件创建解决方案以引用方式使用以下 GitHub 示例：

  - [管理资源组](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [管理存储帐户](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [管理虚拟机](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>示例单元测试项目 

1.  克隆存储库使用以下命令：
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  创建 Azure 服务主体并分配角色以访问订阅。 有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](../azure-stack-create-service-principals.md)。

3.  检索所需的以下值：
    
   1.  租户 ID
   2.  客户端 ID
   3.  客户端机密
   4.  订阅 ID
   5.  资源管理器终结点
   6.  资源位置

4.  设置以下环境变量使用检索到使用命令提示符创建从服务主体中的信息：
    
   1. 导出 TENANT_ID = {你的租户 id}
   2. 导出 CLIENT_ID = {你的客户端 id}
   3. 导出 CLIENT_SECRET = {你的客户端密码}
   4. 导出 SUBSCRIPTION_ID = {你的订阅 id}
   5. 导出 ARM_ENDPOINT = {Azure Stack 资源管理器 URL}
   6. 导出 RESOURCE_LOCATION = {的 Azure Stack 位置}

   在 Windows 中，使用**设置**而不是**导出**。

5.  使用`getactivedirectorysettings`代码以检索 arm 元数据终结点并使用 HTTP 客户端设置的终结点信息。

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  在 pom.xml 文件中，添加以下用于 Azure Stack 2018-03-01-混合配置文件的依赖关系。 此依赖项将安装与此配置文件关联的计算、 网络、 存储、 密钥保管库和应用服务资源提供程序的模块。
    
   请注意，您可以使用最新的配置文件目标 Azure:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  在命令提示符中的打开来设置环境变量，输入以下行：
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md)
- [支持的配置文件的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)
