---
title: "使用 C# 创建和管理 Azure 虚拟机 |Microsoft Docs"
description: "使用 C# 和 Azure Resource Manager 部署虚拟机及其所有支持资源。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>使用 C# 创建和管理 Azure 中的 Windows VM #

[Azure 虚拟机](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) 需要多个支持性 Azure 资源。 本文介绍如何使用 C# 创建、管理和删除 VM 资源。 你将学习如何：

> [!div class="checklist"]
> * 创建 Visual Studio 项目
> * 安装包
> * 创建凭据
> * 创建资源
> * 执行管理任务
> * 删除资源
> * 运行应用程序

完成这些步骤大约需要 20 分钟。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 如果尚未安装，请安装 [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。 在“工作负荷”页上选择“.NET 桌面开发”，然后单击“安装”。 在摘要中，可以看到系统已自动选择“.NET Framework 4 4.6 开发工具”。 如果已安装 Visual Studio，则可以使用 Visual Studio 启动器添加 .NET 工作负荷。
2. 在 Visual Studio 中，单击“文件” > “新建” > “项目”。
3. 在“模板” > “Visual C#”中，选择“控制台应用(.NET Framework)”，输入 *myDotnetProject* 作为项目名称，选择项目位置，然后单击“确定”。

## <a name="install-the-package"></a>安装包

使用 NuGet 包可以最轻松地安装完成这些步骤所需的库。 若要在 Visual Studio 中获取所需的库，请执行以下步骤：

1. 单击“工具” > “Nuget 包管理器”，然后单击“包管理器控制台”。
2. 在控制台中键入此命令：

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>创建凭据

在开始此步骤之前，请确保能够访问 [Active Directory 服务主体](../../azure-resource-manager/resource-group-create-service-principal-portal.md)。 还应该记录稍后步骤需要的应用程序 ID、身份验证秘钥和的租户 ID。

### <a name="create-the-authorization-file"></a>创建授权文件

1. 在解决方案资源管理器中，右键单击“myDotnetProject” > “添加” > “新建项”，然后在“Visual C# 项”中选择“文本文件”。 命名文件 azureauth.properties，然后单击“添加”。
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
4. 在 Windows 中设置名为 AZURE_AUTH_LOCATION 的环境变量，其中包含创建的授权文件的完整路径。 例如，以下 PowerShell 命令可用于：

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>创建管理客户端

1. 为所创建的项目打开 Program.cs 文件，然后将这些 using 语句添加到文件顶部的现有语句：

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>创建资源

### <a name="create-the-resource-group"></a>创建资源组

必须在[资源组](../../azure-resource-manager/resource-group-overview.md)中包含所有资源。

若要指定应用程序的值并创建资源组，请将以下代码添加到 Main 方法：

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>创建可用性集

[可用性集](tutorial-availability-sets.md)可以方便你维护应用程序所使用的虚拟机。

若要创建可用性集，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>创建公共 IP 地址

与虚拟机通信需要[公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

若要创建虚拟机的公共 IP 地址，请将以下代码添加到 Main 方法：
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>创建虚拟网络

虚拟机必须是[虚拟网络](../../virtual-network/virtual-networks-overview.md)的子网。

若要创建子网和虚拟网络，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>创建网络接口

虚拟机需要使用网络接口在虚拟网络上通信。

若要创建网络接口，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>创建虚拟机

创建所有支持的资源后，可以创建虚拟机。

若要创建虚拟机，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> 本教程创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅 [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像）。
> 
>

如果要使用现有磁盘而不是商城映像，请使用以下代码：

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>执行管理任务

在虚拟机生命周期中，你可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，建议创建代码来自动执行重复或复杂的任务。

如需对虚拟机执行任何操作，需获取一个虚拟机实例：

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

若要获取有关虚拟机的信息，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>停止 VM

可以停止虚拟机并保留其所有设置但继续支付其费用，也可以停止虚拟机并将其解除分配。 解除分配某个虚拟机也会解除分配与其关联的所有资源，并停止该虚拟机的计费。

若要停止虚拟机而不解除分配虚拟机，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

如果要解除分配虚拟机，请将 PowerOff 调用更改为以下代码：

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>启动 VM

若要启动虚拟机，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>重设 VM 大小

决定虚拟机大小时应考虑部署的诸多方面。 有关详细信息，请参见 [VM 大小](sizes.md)。  

若要更改虚拟机大小，请将以下代码添加到 Main 方法：

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>将数据磁盘添加到 VM

若要将数据磁盘添加到虚拟机，请将以下代码添加到 Main 方法，以添加大小为 2 GB 的数据磁盘，以及为 0 的 LUN 和 ReadWrite 缓存类型：

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>删除资源

由于需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种良好的做法。 如果要删除虚拟机和所有支持资源，只需删除资源组。

若要删除资源组，请将以下代码添加到 Main 方法：

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>运行应用程序

控制台应用程序从头到尾完成运行大约需要五分钟时间。 

1. 若要运行控制台应用程序，请单击“开始”。

2. 在按 **Enter** 开始删除资源之前，可能需要在 Azure 门户中花几分钟时间来验证资源的创建。 单击部署状态以查看有关部署的信息。

## <a name="next-steps"></a>后续步骤
* 参考 [Deploy an Azure Virtual Machine using C# and a Resource Manager template](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 C# 和 Resource Manager 模板部署 Azure 虚拟机）中的信息，利用模板创建虚拟机。
* 详细了解如何使用[适用于 .NET 的 Azure 库](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet)。


