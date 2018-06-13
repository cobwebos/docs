---
title: 部署在 Azure 堆栈中的受保护的 Service Fabric 群集 |Microsoft 文档
description: 了解如何部署 Azure 堆栈中的受保护的 Service Fabric 群集
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33883019"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>部署在 Azure 堆栈中的 Service Fabric 群集

使用**Service Fabric 群集**Azure 应用商店来部署在 Azure 堆栈中的受保护的 Service Fabric 群集中的项。 

有关使用 Service Fabric 的详细信息，请参阅[的 Azure 服务 Frabric 概述](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)和[Service Fabric 群集安全方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)，Azure 文档中。

## <a name="prerequisites"></a>先决条件

以下部署所需的 Service Fabric 群集：
1. **群集证书**  
   这是你将添加到 KeyVault 时部署 Service Fabric 的 X.509 服务器证书。 
   - **CN**上此证书必须匹配完全限定域名名称 (FQDN) 的创建 Service Fabric 群集。 
   - 证书格式必须是 PFX，因为公钥和私钥的密钥是必需。 
   请参阅[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)用于创建此服务器端证书。

    > [!NOTE]  
    > 出于测试目的，可以使用 x.509 服务器证书的自签名的证书就地。 自签名的证书不需要匹配群集的 FQDN。

2.  **管理客户端证书**这是客户端将使用对 Service Fabric 群集，可以自签名进行身份验证的证书。 请参阅[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)用于创建此客户端证书。

3.  **以下各项 Azure 堆栈应用商店中必须可用：**
     - **Windows Server 2016** – 该模板使用 Windows Server 2016 映像来创建群集。  
     - **客户脚本扩展**-从 Microsoft 虚拟机扩展。  
     - **PowerShell 所需阶段配置**-从 Microsoft 虚拟机扩展。


## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密
若要部署 Service Fabric 群集，必须指定正确的 KeyVault*机密标识符*或 Service Fabric 群集的 URL。 Azure 资源管理器模板采用作为输入 KeyVault，，然后在 Service Fabric 群集的安装过程中检索群集证书。 

> [!IMPORTANT]  
> 你必须使用 PowerShell 将机密添加到 KeyVault，以用于 Service Fabric。 不要使用门户。  

使用以下脚本创建 KeyVault 并添加*群集证书*到它。 (请参阅[先决条件](#prerequisites)。)运行脚本之前，查看示例脚本，然后更新指定的参数，以匹配你的环境。 此脚本还会输出需要向 Azure 资源管理器模板提供的值。 

> [!TIP]  
> 该脚本可以成功之前，必须有公共提供，其中包含用于计算、 网络、 存储和 KeyVault 的服务。 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


有关详细信息，请参阅[管理使用 PowerShell 的 Azure 堆栈上的 KeyVault](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell)。

## <a name="deploy-the-marketplace-item"></a>部署应用商店项目

1. 在用户门户中，转到**新建** > **计算** > **Service Fabric 群集**。 

   ![选择 Service Fabric 群集](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. 对于每个页中，如*基础知识*，填写部署窗体。 如果你不确定的一个值，则使用默认值。 选择**确定**前进到下一步的页面：

   ![基本](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. 上*网络设置*页上，你可以指定为你的应用程序打开特定端口：

   ![网络设置](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. 上*安全*页上，添加你从获取的值[创建 Azure KeyVault](#add-a-secret-to-key-vault)并上载机密。

   有关*管理客户端证书指纹*，输入的指纹*管理客户端证书*。 (请参阅[先决条件](#prerequisites)。)
   
   - 源密钥保管库： 指定整个*keyVault id*从脚本结果的字符串。 
   - 群集证书 URL： 指定从整个 URL*机密 Id*从脚本结果。 
   - 群集证书指纹： 指定*群集证书指纹*从脚本结果。
   - 管理客户端证书指纹： 指定*管理客户端证书指纹*你已经创建了系统必备组件。 

   ![脚本输出](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![安全](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. 完成该向导，然后选择**创建**部署 Service Fabric 群集。



## <a name="access-the-service-fabric-cluster"></a>访问 Service Fabric 群集
你可以通过使用 Service Fabric 资源管理器或 Service Fabric PowerShell 访问 Service Fabric 群集。


### <a name="use-service-fabric-explorer"></a>使用 Service Fabric 资源管理器
1.  验证 Web 浏览器有权访问您管理的客户端证书，以及可以向你的 Service Fabric 群集进行身份验证。  

    a. 打开 Internet Explorer 并转到**Internet 选项** > **内容** > **证书**。
  
    b. 在证书，选择**导入**启动*证书导入向导*，然后单击**下一步**。 上*导入的文件*页上，单击**浏览**，然后选择**管理客户端证书**提供给 Azure 资源管理器模板。
        
       > [!NOTE]  
       > 此证书不是之前已添加到 KeyVault 群集证书。  

    c. 确保"个人信息交换"— — 在文件资源管理器窗口的扩展下拉菜单中选择。  

       ![个人信息交换](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. 上*证书存储区*页上，选择**个人**，然后完成向导。  
       ![证书存储](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. 若要查找你的 Service Fabric 群集的 FQDN:  

    a. 请转到资源组与 Service Fabric 的群集，并找到*公共 IP 地址*资源。 选择与要打开的公共 IP 地址关联的对象*公共 IP 地址*边栏选项卡。  

      ![公用 IP 地址](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. 在公共 IP 地址的边栏选项卡，FQDN 显示为*DNS 名称*。  

      ![DNS 名](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. 若要查找 Service Fabric 资源管理器，以及客户端连接终结点的 URL，请查看模板部署的结果。

4. 在浏览器中，转到 https://*FQDN*: 19080。 替换*FQDN*替换为你在步骤 2 中的 Service Fabric 群集的 FQDN。   
   如果你已使用自签名的证书，你将获得的连接不是安全的警告。 若要继续到网站，选择**更多信息**，，然后**转到网页**。 

5. 若要对站点进行身份验证必须选择要使用的证书。 选择**更多选择**，选取适当的证书，然后单击**确定**连接到 Service Fabric 资源管理器。 

   ![验证](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>使用 Service Fabric PowerShell

1. 安装*Microsoft Azure Service Fabric SDK*从[准备开发环境，在 Windows 上的](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools)Azure Service Fabric 文档中。  

2. 安装完成后，配置系统环境变量，以确保 Service Fabric cmdlet 可从 PowerShell。  
    
    a. 转到**控制面板** > **系统和安全** > **系统**，然后选择**高级系统设置**.  
    
      ![控制面板](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. 上**高级**选项卡*系统属性*，选择**环境变量**。  

    c. 有关*系统变量*，编辑**路径**并确保**c:\\Program Files\\Microsoft Service Fabric\\bin\\构造\\Fabric.Code**位于的环境变量列表的顶部。  

      ![环境变量列表](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. 更改环境变量的顺序之后, 重新启动 PowerShell，然后运行以下的 PowerShell 脚本，以访问 Service Fabric 群集:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > 没有任何*https://* 之前在脚本中群集的名称。 端口 19000 是必需的。
 
