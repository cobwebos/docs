---
title: "在 Azure 中添加、滚动更新和删除 Service Fabric 群集内使用的证书 | Microsoft 文档"
description: "介绍如何上载辅助群集证书，然后滚动更新旧的主要证书。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 829af0b685f07c2e529edf1a0ef72b741d37a14c


---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>在 Azure 中添加或删除 Service Fabric 群集的证书
我们建议你先阅读[群集安全性应用场景](service-fabric-cluster-security.md)，了解 Service Fabric 是如何使用 X.509 证书的。 在继续下一步之前，必须先了解群集证书的定义和用途。

在创建群集期间配置证书安全性时，Service Fabric 允许指定两个群集证书（主要证书和辅助证书）。 请参阅[通过门户创建 Azure 群集](service-fabric-cluster-creation-via-portal.md)或[通过 Azure Resource Manager 创建 Azure 群集](service-fabric-cluster-creation-via-arm.md)，了解详细信息。 如果通过 Resource Manager 进行部署并且只指定了一个群集证书，将使用该证书作为主要证书。 在创建群集后，可以添加一个新证书作为辅助证书。

> [!NOTE]
> 对于安全群集，始终至少需要部署一个有效的（未吊销或过期）证书（主要或辅助），否则，群集无法正常运行。 在所有有效证书过期前的 90 天，系统将针对节点生成警告跟踪和警告运行状况事件。 Service Fabric 当前不会针对此主题发送电子邮件或其他任何通知。 
> 
> 

## <a name="add-a-secondary-certificate-using-the-portal"></a>使用门户添加辅助证书
若要添加另一个证书作为辅助证书，必须将该证书上载到 Azure 密钥保管库，然后将它部署到群集中的 VM。 有关详细信息，请参阅[将证书从客户管理的密钥保管库部署到 VM](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。

1. 有关具体方法，请参阅[将证书添加到密钥保管库](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault)。
2. 登录 [Azure 门户](https://portal.azure.com/)，然后转到要向其中添加此证书的群集资源。
3. 在“**设置**”下，单击“**安全性**”，调出“群集安全性”边栏选项卡。
4. 单击此边栏选项卡顶部的“**+ 证书**”按钮，转到“**添加证书**”边栏选项卡。
5. 从下拉列表中选择“辅助证书指纹”，为上载到密钥保管库的辅助证书填写证书指纹。

> [!NOTE]
> 与执行群集创建工作流期间的不同之处在于，此处无需填充有关密钥保管库的详细信息，因为系统假设当时在此边栏选项卡中操作，已将证书部署到 VM，并且证书已在 VMSS 实例的本地证书存储中提供。
> 
> 

单击“**证书**”。 随即开始部署，“群集安全性”边栏选项卡中会显示一个蓝色状态栏。

![门户中证书指纹的屏幕截图][SecurityConfigurations_02]

部署成功完成后，可以使用主要证书或辅助证书在群集上执行管理操作。

![正在部署证书的屏幕截图][SecurityConfigurations_03]

以下屏幕截图显示完成部署后的安全性边栏选项卡外观。

![部署后的证书指纹的屏幕截图][SecurityConfigurations_08]

现在，可以使用刚刚添加的证书建立连接，在群集上执行操作。

> [!NOTE]
> 目前无法在门户中交换主要证书与辅助证书，该功能正在开发中。 只要存在有效群集证书，群集就能正常运行。
> 
> 

## <a name="add-a-secondary-certificate-and-swap-it-to-be-the-primary-using-resource-manager-powershell"></a>添加辅助证书并使用 Resource Manager Powershell 将其交换为主要证书
执行以下步骤的前提是，你熟悉 Resource Manager 的工作原理，并已使用 Resource Manager 模板至少部署了一个 Service Fabric 群集，同时已准备好你在设置此群集时使用的模板。 此外，还有一个前提就是，你可以熟练使用 JSON。

> [!NOTE]
> 如需可参考或入手的示例模板和参数，请从此 [git-repo]  (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) 下载。 
> 
> 

#### <a name="edit-your-resource-manager-template"></a>编辑 Resource Manager 模板
如果你以前就参考了 [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) 中的示例，便会发现示例 5-VM-1-NodeTypes-Secure_Step2.JSON 有所变化。 使用 5-VM-1-NodeTypes-Secure_Step1.JSON 部署安全群集

1. 打开用于部署群集的 Resource Manager 模板。
2. 添加“字符串”类型的新参数“secCertificateThumbprint”。 如果使用的 Resource Manager 模板是在创建群集时从门户下载的，或者是从快速入门模板下载的，则只需搜索该参数，就会发现它已做了定义。  
3. 找到“Microsoft.ServiceFabric/clusters”资源定义。 在属性下面找到“Certificate”JSON 标记，如以下 JSON 代码片段所示。
   
   ```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
   ``` 
4. 添加新标记“thumbprintSecondary”并为其指定值“[parameters('secCertificateThumbprint')]”。  

资源定义现在应如下所示（根据具体的模板源，有时与下面的代码片段不完全相同）。 在下面可以看到，执行的操作是指定一个新证书作为主要证书，同时将当前主要证书交换为辅助证书。  这样就可以通过一个部署步骤，将当前证书滚动更新为新证书。

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>编辑模板文件，反映前面添加的新参数
如果你以前就参考了 [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) 中的示例，便会可以开始更改示例 5-VM-1-NodeTypes-Secure.paramters_Step2.JSON 

编辑 Resource Manager 模板参数文件，为 secCertificate 添加新参数，将现有的主要证书详细信息与辅助证书交换，然后将主要证书详细信息替换为新证书详细信息。 

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### <a name="deploy-the-template-to-azure"></a>将模板部署到 Azure
1. 现在，可以将模板部署到 Azure。 请打开 Azure PS 版本 1（或更高版本）的命令提示符。
2. 登录到 Azure 帐户，选择特定的 Azure 订阅。 对于有权访问多个 Azure 订阅的用户而言，这是一个重要步骤。

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

部署模板之前先进行测试。 使用群集当前部署到的同一个资源组。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

将模板部署到该资源组。 使用群集当前部署到的同一个资源组。 运行 New-AzureRmResourceGroupDeployment 命令。 无需指定模式，因为默认值为 **incremental**。

> [!NOTE]
> 如果将 Mode 设置为 Complete，可能会无意中删除不在模板中的资源。 因此请不要在此方案中使用该模式。
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

下面是已填充数据的同一个 Powershell 命令示例。

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

部署完成后，使用新证书连接到群集，然后执行一些查询。 如果能够执行这些查询， 则可以删除旧的主要证书。 

如果使用自签名证书，请务必将它们导入本地 TrustedPeople 证书存储。

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
以下快速参考提供了用于连接到安全群集的命令 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
以下快速参考提供了用于获取群集运行状况的命令

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="remove-the-old-certificate-using-the-portal"></a>使用门户删除旧证书
下面是删除旧证书，以使群集不使用它的过程：

1. 登录 [Azure 门户](https://portal.azure.com/)，然后转到群集的安全设置。
2. 右键单击要删除的证书
3. 选择“删除”并根据提示操作。 

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## <a name="next-steps"></a>后续步骤
有关群集管理的详细信息，请阅读以下文章：

* [Service Fabric 群集升级过程和用户预期](service-fabric-cluster-upgrade.md)
* [为客户端设置基于角色的访问](service-fabric-cluster-security-roles.md)

<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png



<!--HONumber=Nov16_HO3-->


