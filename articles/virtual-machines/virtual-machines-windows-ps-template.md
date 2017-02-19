<properties
  pageTitle="使用 Resource Manager 模板创建 VM | Microsoft Docs"
  description="将 Resource Manager 模板与 PowerShell 配合使用，以轻松创建新的 Windows 虚拟机。"
  services="virtual-machines-windows" 
  documentationcenter=""
  author="davidmu1"
  manager="timlt"
  editor=""
  tags="azure-resource-manager"/>

<tags
  ms.assetid="19129d61-8c04-4aa9-a01f-361a09466805"
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="01/06/2017"
  ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>使用资源管理器模板创建 Windows 虚拟机

本文介绍如何使用 PowerShell 部署 Azure Resource Manager 模板。 此[模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)在包含单个子网的新虚拟网络中部署运行 Windows Server 的单个虚拟机。

有关虚拟机资源的详细说明，请参阅 [Azure Resource Manager 模板中的虚拟机](virtual-machines-windows-template-description.md)。 有关模板中所有资源的详细信息，请参阅 [Azure Resource Manager 模板演练](../resource-manager-template-walkthrough.md)。

执行本文中的步骤大约需要&5; 分钟时间。

## <a name="step-1-install-azure-powershell"></a>步骤 1：安装 Azure PowerShell
有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## <a name="step-2-create-a-resource-group"></a>步骤 2：创建资源组
必须在[资源组](../azure-resource-manager/resource-group-overview.md)中部署所有资源。

1. 获取可以创建资源的可用位置列表。
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```

2. 在所选的位置中创建资源组。 本示例演示在**美国中部**地区创建一个名为 **MyResourceGroup** 的资源组。

  ```powershell   
  New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
  ```
   
  用户应看到与此示例类似的内容：

  ```
  ResourceGroupName : myResourceGroup
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
  ```

## <a name="step-3-create-the-resources"></a>步骤 3：创建资源
部署模板，并在出现提示时提供参数值。 本示例在创建的资源组中部署 101-vm-simple-windows 模板：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
你需要提供 VM 上的管理员帐户的名称、帐户的密码和 DNS 前缀。

用户应看到与此示例类似的内容：

```
DeploymentName    : azuredeploy
ResourceGroupName : myResourceGroup
ProvisioningState : Succeeded
Timestamp         : 12/29/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                    101-vm-simple-windows/azuredeploy.json
                    ContentVersion : 1.0.0.0
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myAdminUser
                    adminPassword    SecureString
                    dnsLabelPrefix   String                     myDomain
                    windowsOSVersion String                     2016-Datacenter
Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ===========
                    hostname         String                     myDomain.centralus.cloudapp.azure.com
DeploymentDebugLogLevel :
```

> [!NOTE]
> 你也可以从本地文件部署模板和参数。 有关详细信息，请参阅[对 Azure 存储空间使用 Azure PowerShell](../storage/storage-powershell-guide-full.md)。

## <a name="next-steps"></a>后续步骤
* 如果部署出现问题，后续措施是参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)。
* 查看[使用 Azure Resource Manager 和 PowerShell 管理虚拟机](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解如何管理创建的虚拟机。




<!--HONumber=Jan17_HO2-->


