---
title: "使用模板创建 Azure IoT 中心 (.NET) | Microsoft Docs"
description: "如何使用 Azure Resource Manager 模板和 C# 程序创建 IoT 中心。"
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
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: d973e52a6b19d6a066de1677e5707d182a815205
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>使用 Azure Resource Manager 模板创建 IoT 中心 (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

可以使用 Azure Resource Manager 以编程方式创建和管理 Azure IoT 中心。 本教程介绍如何使用 Azure Resource Manager 模板通过 C# 程序创建 IoT 中心。

> [!NOTE]
> Azure 提供了用于创建和使用资源的两个不同部署模型：[Azure Resource Manager 模型和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。  本文介绍了如何使用 Azure Resource Manager 部署模型。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 <br/>如果没有帐户，只需几分钟就能创建一个[免费帐户][lnk-free-trial]。
* 可用于存储 Azure Resource Manager 模板文件的 [Azure 存储帐][lnk-storage-account]。
* [Azure PowerShell 1.0][lnk-powershell-install] 或更高版本。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>准备 Visual Studio 项目

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板创建 Visual C# Windows 经典桌面项目。 将项目命名为 **CreateIoTHub**。

2. 在解决方案资源管理器中右键单击项目，然后单击“**管理 NuGet 包**”。

3. 在 NuGet 包管理器中，选中“包括预发行版”，并在“浏览”页上搜索 **Microsoft.Azure.Management.ResourceManager**。 选择该包，单击“安装”，在“审阅更改”中单击“确定”，并单击“我接受”以接受许可证。

4. 在 NuGet 包管理器中，搜索 **Microsoft.IdentityModel.Clients.ActiveDirectory**。  单击“**安装**”，在“**审阅更改**”中单击“**确定**”，并单击“**我接受**”以接受许可证。

5. 在 Program.cs 中，将现有 **using** 语句替换为以下代码：

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. 在 Program.cs 中，将占位符值替换为以下静态变量。 在本教程前面的介绍中，已记下 **ApplicationId**、**SubscriptionId**、**TenantId** 和 **Password**。 **Your Azure Storage account name** 是要在其中存储 Azure Resource Manager 模板文件的 Azure 存储帐户的名称。 资源组名称是创建 IoT 中心时要使用的资源组名称。 名称可以是现有的资源组或新资源组。 **部署名称**是部署的名称，例如 **Deployment_01**。

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>提交模板以创建 IoT 中心

使用 JSON 模板和参数文件在资源组中创建 IoT 中心。 还可以使用 Azure Resource Manager 模板更改现有 IoT 中心。

1. 在解决方案资源管理器中右键单击项目，单击“**添加**”，然后单击“**新建项**”。 将名为 **template.json** 的 JSON 文件添加到项目。

2. 若要在美国东部区域中添加一个标准 IoT 中心，请将“template.json”的内容替换为以下资源定义。 有关支持 IoT 中心的区域的最新列表，请参阅 [Azure 状态][lnk-status]：

    ```json
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

3. 在解决方案资源管理器中右键单击项目，单击“**添加**”，然后单击“**新建项**”。 将名为 **parameters.json** 的 JSON 文件添加到项目。

4. 将 **parameters.json** 的内容替换为以下参数信息，以便将新 IoT 中心的名称设置为 **{your initials}mynewiothub**。 IoT 中心名称必须全局唯一，因此，应包含姓名或姓名首字母缩写：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. 在**服务器资源管理器**中，连接到 Azure 订阅，并在 Azure 存储帐户中创建名为 **templates** 的容器。 在“**属性**”面板中，将 **templates** 容器的“**公共读取访问权限**”权限设置为“**Blob**”。

6. 在“**服务器资源管理器**”中，右键单击 **templates** 容器，并单击“**查看 Blob 容器**”。 单击“**上传 Blob**”按钮，选择“**parameters.json**”和“**templates.json**”这两个文件，然后单击“**打开**”，将 JSON 文件上传到 **templates** 容器。 包含 JSON 数据的 Blob 的 URL 如下：

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. 将以下方法添加到 Program.cs：

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. 将以下代码添加到 **CreateIoTHub** 方法以将模板和参数文件提交到 Azure Resource Manager：

    ```csharp
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

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>完成并运行应用程序

现在，可以调用 CreateIoTHub 方法来完成应用程序，然后生成并运行该应用程序。

1. 将以下代码添加到 **Main** 方法末尾：

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. 单击“**生成**”，并单击“**生成解决方案**”。 更正所有错误。

3. 单击“**调试**”，并单击“**开始调试**”以运行应用程序。 运行部署可能需要几分钟时间。

4. 若要验证应用程序是否添加了新的 IoT 中心，请访问 [Azure 门户][lnk-azure-portal]并查看资源列表。 或者，使用 Get-azurermresource PowerShell cmdlet。

> [!NOTE]
> 本示例应用程序将添加用于对你计费的 S1 标准 IoT 中心。 在完成后，可以通过 [Azure 门户][lnk-azure-portal]或者使用 **Remove-AzureRmResource** PowerShell cmdlet 删除该 IoT 中心。

## <a name="next-steps"></a>后续步骤
现在，已经使用包含 C# 程序的 Azure Resource Manager 模板部署了 IoT 中心，你可能想要进一步探究：

* 阅读了解 [IoT 中心资源提供程序 REST API][lnk-rest-api] 的相关功能。
* 有关 Azure Resource Manager 功能的详细信息，请参阅 [Azure Resource Manager 概述][lnk-azure-rm-overview]。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 模拟设备][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

