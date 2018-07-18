---
title: 使用 C# 和 Resource Manager 模板部署 VM | Microsoft Docs
description: 了解如何使用 C# 和 Resource Manager 模板部署 Azure VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: e57505b4bd89a79af076dc4cf132c844ae0abd1d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527818"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>使用 C# 和 Resource Manager 模板部署 Azure 虚拟机
本文介绍如何使用 C# 部署 Azure 资源管理器模板。 创建的模板会在包含单个子网的新虚拟网络中部署运行 Windows Server 的单个虚拟机。

有关虚拟机资源的详细说明，请参阅 [Azure 资源管理器模板中的虚拟机](template-description.md)。 有关模板中所有资源的详细信息，请参阅 [Azure 资源管理器模板演练](../../azure-resource-manager/resource-manager-template-walkthrough.md)。

完成这些步骤大约需要 10 分钟。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

在此步骤中，请确保已安装 Visual Studio 并已创建用于部署模板的控制台应用程序。

1. 如果尚未安装，请安装 [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。 在“工作负荷”页上选择“.NET 桌面开发”，然后单击“安装”。 在摘要中，可以看到系统已自动选择“.NET Framework 4 4.6 开发工具”。 如果已安装 Visual Studio，则可以使用 Visual Studio 启动器添加 .NET 工作负荷。
2. 在 Visual Studio 中，单击“文件” > “新建” > “项目”。
3. 在“模板” > “Visual C#”中，选择“控制台应用(.NET Framework)”，输入 *myDotnetProject* 作为项目名称，选择项目位置，然后单击“确定”。

## <a name="install-the-packages"></a>安装这些包

使用 NuGet 包可以最轻松地安装完成这些步骤所需的库。 若要在 Visual Studio 中获取所需的库，请执行以下步骤：

1. 单击“工具” > “Nuget 包管理器”，然后单击“包管理器控制台”。
2. 在控制台中键入这些命令：

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>创建文件

此步骤将创建一个用于部署资源的模板文件和一个用于向模板提供参数值的参数文件。 还创建用于执行 Azure 资源管理器操作的授权文件。

### <a name="create-the-template-file"></a>创建模板文件

1. 在解决方案资源管理器中，右键单击*myDotnetProject* > **，单击“添加** > **新建项**，然后在**Visual C# 项**中选择*文本文件*。 命名文件 CreateVMTemplate.json，然后单击“添加”。
2. 将此 JSON 代码添加到创建的文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. 保存 CreateVMTemplate.json 文件。

### <a name="create-the-parameters-file"></a>创建参数文件

若要为模板中定义的资源参数指定值，请创建包含值的参数文件。

1. 在解决方案资源管理器中，右键单击*myDotnetProject* > **，单击“添加** > **新建项**，然后在**Visual C# 项**中选择*文本文件*。 命名文件 Parameters.json，然后单击“添加”。
2. 将此 JSON 代码添加到创建的文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. 保存 Parameters.json 文件。

### <a name="create-the-authorization-file"></a>创建授权文件

在可部署模板之前，请先确保能够访问 [Active Directory 服务主体](../../resource-group-authenticate-service-principal.md)。 从服务主体中，将获取对 Azure 资源管理器请求进行身份验证的令牌。 还应记录授权文件中所需的应用程序 ID、身份验证秘钥和的租户 ID。

1. 在解决方案资源管理器中，右键单击*myDotnetProject* > **，单击“添加** > **新建项**，然后在**Visual C# 项**中选择*文本文件*。 命名文件 azureauth.properties，然后单击“添加”。
2. 添加这些授权属性：

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    将 &lt;subscription-id&gt; 替换为订阅标识符，&lt;application-id&gt; 替换为 Active Directory 应用程序标识符，&lt;authentication-key&gt; 替换为授权密钥，&lt;tenant-id&gt; 替换为租户标识符。

3. 保存 azureauth.properties 文件。
4. 在 Windows 中设置名为 AZURE_AUTH_LOCATION 的环境变量，其中包含创建的授权文件的完整路径，例如以下 PowerShell 命令可用于：

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>创建管理客户端

1. 为所创建的项目打开 Program.cs 文件，并将这些 using 语句添加到文件顶部的现有语句：

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. 若要创建管理客户端，请将以下代码添加到 Main 方法：

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>创建资源组

若要指定应用程序的值，请将代码添加到 Main 方法：

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>创建存储帐户

模板和参数是从 Azure 中的存储帐户部署的。 此步骤将创建帐户并上传文件。 

若要创建帐户，请将此代码添加到 Main 方法：

```
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>部署模板

从创建的存储帐户部署模板和参数。 

若要部署模板，请将此代码添加到 Main 方法：

```
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>删除资源

由于需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种良好的做法。 不需要单独从资源组中删除每个资源， 删除资源组，其中包含的所有资源会一并删除。 

若要删除资源组，请将以下代码添加到 Main 方法：

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>运行应用程序

控制台应用程序从头到尾完成运行大约需要五分钟时间。 

1. 若要运行控制台应用程序，请单击“开始”。

2. 在按 **Enter** 开始删除资源之前，可能需要在 Azure 门户中花几分钟时间来验证资源的创建。 单击部署状态以查看有关部署的信息。

## <a name="next-steps"></a>后续步骤
* 如果部署出现问题，后续措施是参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](../../resource-manager-common-deployment-errors.md)。
* 若要了解如何部署虚拟机及其支持的资源，请查看[使用 C# 部署 Azure 虚拟机](csharp.md)。
