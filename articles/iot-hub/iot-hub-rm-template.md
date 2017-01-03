---
title: "使用 ARM 模板和 C# 创建 IoT 中心 | Microsoft 文档"
description: "遵照本教程开始使用 Azure Resource Manager 模板和 C# 程序创建 IoT 中心。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a447b40c-c728-487e-875d-db554db5adc3
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 118ab90d7886ad48e7476abd732b16b69375d214


---
# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>使用 C# 程序和 Azure Resource Manager 模板创建 IoT 中心
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>介绍
可以使用 Azure Resource Manager 以编程方式创建和管理 Azure IoT 中心。 本教程介绍如何使用 Azure Resource Manager 模板通过 C# 程序创建 IoT 中心。

> [!NOTE]
> Azure 提供了用于创建和使用资源的两个不同部署模型：[Azure Resource Manager 模型和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。  本文介绍了如何使用 Azure Resource Manager 部署模型。
> 
> 

若要完成本教程，需要以下各项：

* Microsoft Visual Studio 2015。
* 有效的 Azure 帐户。 <br/>如果你没有帐户，只需几分钟就能创建一个[免费帐户][lnk-free-trial]。
* 可用于存储 Azure Resource Manager 模板文件的 [Azure 存储帐][lnk-storage-account]。
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更高版本。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>准备 Visual Studio 项目
1. 在 Visual Studio 中，使用“**控制台应用程序**”项目模板创建一个 Visual C# Windows 项目。 将项目命名为 **CreateIoTHub**。
2. 在解决方案资源管理器中右键单击你的项目，然后单击“**管理 NuGet 包**”。
3. 在 NuGet 包管理器中，选中“包括预发行版”，然后搜索 **Microsoft.Azure.Management.ResourceManager**。 单击“**安装**”，在“**审阅更改**”中单击“**确定**”，然后单击“**我接受**”以接受许可证。
4. 在 NuGet 包管理器中，搜索 **Microsoft.IdentityModel.Clients.ActiveDirectory**。  单击“**安装**”，在“**审阅更改**”中单击“**确定**”，然后单击“**我接受**”以接受许可证。
5. 在 Program.cs 中，将现有 **using** 语句替换为以下内容：
   
    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
6. 在 Program.cs 中，将占位符值替换为以下静态变量。 在本教程前面的介绍中，你已记下 **ApplicationId**、**SubscriptionId**、**TenantId** 和 **Password**。 **Your Azure Storage account name** 是要在其中存储 Azure Resource Manager 模板文件的 Azure 存储帐户的名称。 **资源组名称**是创建 IoT 中心时要使用的资源组名称，可以是现有的资源组或新资源组。 **部署名称**是部署的名称，例如 **Deployment_01**。
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>提交 Azure Resource Manager 模板以创建 IoT 中心
使用 JSON 模板和参数文件在资源组中创建 IoT 中心。 还可以使用 Azure Resource Manager 模板更改现有 IoT 中心。

1. 在解决方案资源管理器中右键单击你的项目，单击“**添加**”，然后单击“**新建项**”。 将名为 **template.json** 的 JSON 文件添加到项目。
2. 将 **template.json** 的内容替换为以下资源定义，以在 **East US** 区域中添加一个标准 IoT 中心： 有关支持 IoT 中心的区域的最新列表，请参阅 [Azure 状态][lnk-status]：
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```
3. 在解决方案资源管理器中右键单击你的项目，单击“**添加**”，然后单击“**新建项**”。 将名为 **parameters.json** 的 JSON 文件添加到项目。
4. 将 **parameters.json** 的内容替换为以下参数信息，以便将新 IoT 中心的名称设置为 **{your initials}mynewiothub**。 IoT 中心名称必须全局唯一，因此，应包含姓名或姓名首字母缩写：
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
5. 在**服务器资源管理器**中，连接到你的 Azure 订阅，并在 Azure 存储帐户中创建名为 **templates** 的容器。 在“**属性**”面板中，将 **templates** 容器的“**公共读取访问权限**”权限设置为“**Blob**”。
6. 在“**服务器资源管理器**”中，右键单击 **templates** 容器，然后单击“**查看 Blob 容器**”。 单击“**上载 Blob**”按钮，选择“**parameters.json**”和“**templates.json**”这两个文件，然后单击“**打开**”，将 JSON 文件上载到 **templates** 容器。 包含 JSON 数据的 Blob 的 URL 如下：
   
    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. 将以下方法添加到 Program.cs：
   
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
   
    }
    ```
8. 将以下代码添加到 **CreateIoTHub** 方法以将模板和参数文件提交到 Azure Resource Manager：
   
    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```
9. 将以下代码添加到 **CreateIoTHub** 方法以显示新 IoT 中心的状态和密钥：
   
    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);
   
    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>完成并运行应用程序
现在，可以调用 **CreateIoTHub** 方法来完成应用程序，然后生成并运行该应用程序。

1. 将以下代码添加到 **Main** 方法末尾：
   
    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
2. 单击“**生成**”，然后单击“**生成解决方案**”。 更正所有错误。
3. 单击“**调试**”，然后单击“**开始调试**”以运行应用程序。 运行部署可能需要几分钟时间。
4. 若要验证应用程序中是否添加了新 IoT 中心，可以访问 [Azure 门户][lnk-azure-portal]并查看资源列表，或使用 **Get-AzureRmResource** PowerShell cmdlet。

> [!NOTE]
> 本示例应用程序将添加用于对你计费的 S1 标准 IoT 中心。 在完成后，可以通过 [Azure 门户][lnk-azure-portal]或者使用 **Remove-AzureRmResource** PowerShell cmdlet 删除该 IoT 中心。
> 
> 

## <a name="next-steps"></a>后续步骤
现在，你已经使用包含 C# 程序的 Azure Resource Manager 模板部署了 IoT 中心，你可能想要进一步探究：

* 阅读了解 [IoT 中心资源提供程序 REST API][lnk-rest-api] 的相关功能。
* 有关 Azure Resource Manager 功能的详细信息，请参阅 [Azure Resource Manager 概述][lnk-azure-rm-overview]。

若要深入了解如何开发 IoT 中心，请参阅以下内容：

* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


