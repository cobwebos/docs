---
title: "使用 C# 和 Resource Manager 模板部署 VM | Microsoft Docs"
description: "了解如何使用 C# 和 Resource Manager 模板部署 Azure VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: e04b6871a119c9022aeb42a8e3063a224ff50db0


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>使用 C# 和 Resource Manager 模板部署 Azure 虚拟机
使用资源组和模板，可以统一管理针对应用程序提供支持的所有资源。 本文说明如何使用 Visual Studio 和 C# 设置身份验证、创建模板，然后使用创建的模板部署 Azure 资源。

首先需确保已完成以下设置步骤：

* 安装 [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* 验证是否安装了 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* 获取[身份验证令牌](../resource-group-authenticate-service-principal.md)
* 使用 [Azure PowerShell](../resource-group-template-deploy.md)、[Azure CLI](../resource-group-template-deploy-cli.md) 或 [Azure 门户](../resource-group-template-deploy-portal.md)创建一个资源组。

完成这些步骤大约需要 30 分钟。

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>步骤 1：创建 Visual Studio 项目、模板文件和参数文件
### <a name="create-the-template-file"></a>创建模板文件
使用 Azure Resource Manager 模板可以统一部署和管理 Azure 资源。 该模板是资源与关联的部署参数的 JSON 描述。

在 Visual Studio 中执行以下步骤：

1. 单击“文件” > “新建” > “项目”。
2. 在“模板” > “Visual C#”中，选择“控制台应用程序”，输入项目的名称和位置，然后单击“确定”。
3. 在解决方案资源管理器中右键单击项目名称，然后单击“添加” > “新建项”。
4. 单击“Web”，选择“JSON 文件”，输入 *VirtualMachineTemplate.json* 作为名称，然后单击“添加”。
5. 在 VirtualMachineTemplate.json 文件的左括号和右括号中，添加所需的架构元素和所需的 contentVersion 元素：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [参数](../resource-group-authoring-templates.md#parameters)并不总是必需的，但在部署模板时，它们提供了输入值的一种方法。 在 ContentVersion 元素后面添加 parameters 元素及其子元素：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. 可以在模板中使用[变量](../resource-group-authoring-templates.md#variables)指定可能经常发生变化的值或需要通过组合参数值创建的值。 在 parameters 节的后面添加 variables 元素：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. 接下来将在模板中定义[资源](../resource-group-authoring-templates.md#resources)，例如虚拟机、虚拟网络和存储帐户。 在 variables 节的后面添加 resources 节：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. 保存创建的模板文件。

### <a name="create-the-parameters-file"></a>创建参数文件
若要为模板中定义的资源参数指定值，请创建一个参数文件，其中包含部署模板时要使用的值。 在 Visual Studio 中执行以下步骤：

1. 在解决方案资源管理器中右键单击项目名称，然后单击“添加” > “新建项”。
2. 单击“Web”，选择“JSON 文件”，在“名称”中输入 *Parameters.json*，然后单击“添加”。
3. 打开 parameters.json 文件，然后添加以下 JSON 内容：
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > 本文创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅 [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像）。
   > 
   > 
4. 保存创建的参数文件。

## <a name="step-2-install-the-libraries"></a>步骤 2：安装库
使用 NuGet 包是安装完成本教程所需的库的最简单方法。 需要使用 Azure 资源管理库和 Azure Active Directory 身份验证库来创建资源。 若要在 Visual Studio 中获取这些库，请执行以下步骤：

1. 在解决方案资源管理器中右键单击项目名称，然后依次单击“管理 NuGet 包”和“浏览”。
2. 在搜索框中键入 *Active Directory*，单击“Active Directory 身份验证库”包旁边的“安装”，然后根据说明安装该包。
3. 在页面顶部，选择“包括预发行版”。 在搜索框中键入 *Microsoft.Azure.Management.ResourceManager*，单击“Microsoft Azure 资源管理库”旁边的“安装”，然后根据说明安装该包。

现在，你可以开始使用这些库来创建应用程序了。

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>步骤 3：创建用于对请求进行身份验证的凭据
现已创建 Azure Active Directory 应用程序并安装身份验证库。 接下来，请将应用程序信息格式化为凭据，用于对发往 Azure Resource Manager 的请求进行身份验证。

1. 打开你为项目创建的 Program.cs 文件，然后在该文件的顶部添加以下 using 语句：
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. 将以下方法添加到 Program 类，以获取创建凭据所需的令牌：
   
     private static async Task<AuthenticationResult> GetAccessTokenAsync()   {
   
       var cc = new ClientCredential("{client-id}", "{client-secret}");
       var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
       var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
       if (token == null)
       {
         throw new InvalidOperationException("Could not get the token.");
       }
       return token;
     }
   
   将 {client-id} 替换为 Azure Active Directory 应用程序的标识符，将 {client-secret} 替换为 AD 应用程序的访问密钥，并将 {tenant-id} 替换为你的订阅的租户标识符。 可以通过运行 Get-AzureRmSubscription 找到租户 ID。 可以使用 Azure 门户找到访问密钥。
3. 若要创建凭据，请将以下代码添加到 Program.cs 文件中的 Main 方法：
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. 保存 Program.cs 文件。

## <a name="step-4-deploy-the-template"></a>步骤 4：部署模板
此步骤使用前面创建的资源组，但你也可以使用 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 和 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 类来创建资源组。

1. 将变量添加到 Program 类的 Main 方法，指定前面创建的资源的名称、部署名称和订阅标识符：
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";
   
    将 groupName 的值替换为资源组的名称。 将 deploymentName 的值替换为要用于部署的名称。 可以通过运行 Get-AzureRmSubscription 查找订阅标识符。
2. 将以下方法添加到 Program 类，以使用你定义的模板将资源部署到资源组：
   
        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }
   
    如果想要从存储帐户部署模板，可将 Template 属性替换为 TemplateLink 属性。
3. 若要调用刚刚添加的方法，请将以下代码添加到 Main 方法：
   
        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>步骤 5：删除资源
由于你需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种良好的做法。 你不需要单独从资源组中删除每个资源， 删除资源组，其中包含的所有资源会一并删除。

1. 若要删除资源组，请将以下方法添加到 Program 类：
   
     public static async void DeleteResourceGroupAsync(
   
       TokenCredentials credential,
       string groupName,
       string subscriptionId)
     {
   
       Console.WriteLine("Deleting resource group...");
       var resourceManagementClient = new ResourceManagementClient(credential)
         { SubscriptionId = subscriptionId };
       await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
     }
2. 若要调用刚刚添加的方法，请将以下代码添加到 Main 方法：
   
     DeleteResourceGroupAsync(
   
       credential,
       groupName,
       subscriptionId);
     Console.ReadLine();

## <a name="step-6-run-the-console-application"></a>步骤 6：运行控制台应用程序
1. 若要运行控制台应用程序，请在 Visual Studio 中单击“启动”，然后使用用于订阅的相同凭据登录到 Azure AD。
2. 在显示“已接受”状态之后按 **Enter**。
   
   控制台应用程序从头到尾完成运行大约需要五分钟时间。 在按 Enter 开始删除资源之前，你可能需要在 Azure 门户中花几分钟时间来验证资源的创建。
3. 若要查看资源的状态，请在 Azure 门户中浏览到“审核日志”：
   
    ![在 Azure 门户中浏览审核日志](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>后续步骤
* 如果部署出现问题，请查看[使用 Azure 门户对资源组部署进行故障排除](../resource-manager-troubleshoot-deployments-portal.md)。
* 查看[使用 Azure Resource Manager 和 PowerShell 管理虚拟机](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解如何管理创建的虚拟机。




<!--HONumber=Nov16_HO3-->


