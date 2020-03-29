---
title: 将实验室配置为在 Azure 开发人员测试实验室中使用远程桌面网关
description: 了解如何使用远程桌面网关在 Azure DevTest 实验室中配置实验室，以确保安全访问实验室 VM，而无需公开 RDP 端口。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88daecdf4490ffd4eef45e6cd664a16f86bad113
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170285"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>在 Azure 开发人员测试实验室中配置实验室以使用远程桌面网关
在 Azure DevTest 实验室中，可以为实验室配置远程桌面网关，以确保安全访问实验室虚拟机 （VM），而无需公开 RDP 端口。 该实验室为您的实验室用户提供了一个中心位置，以便查看和连接到他们有权访问的所有虚拟机。 **"虚拟机**"页上的 **"连接**"按钮将创建一个特定于计算机的 RDP 文件，您可以打开该文件以连接到计算机。 通过将实验室连接到远程桌面网关，您可以进一步自定义 RDP 连接并保护 RDP 连接。 

此方法更安全，因为实验室用户直接对网关计算机进行身份验证，或者可以使用域加入的网关计算机上的公司凭据连接到其计算机。 该实验室还支持对网关计算机使用令牌身份验证，该网关计算机允许用户连接到其实验室虚拟机，而无需将 RDP 端口暴露给 Internet。 本文介绍了如何设置使用令牌身份验证连接到实验室计算机的实验室的示例。

## <a name="architecture-of-the-solution"></a>解决方案的体系结构

![解决方案的体系结构](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 当您选择"**连接**"按钮时，将调用["获取 RDP 文件内容"](/rest/api/dtl/virtualmachines/getrdpfilecontents)操作。 
1. 获取 RDP 文件内容操作调用`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`以请求身份验证令牌。
    1. `{gateway-hostname}`是在 Azure 门户中的**实验室的"实验室设置"** 页上指定的网关主机名。 
    1. `{lab-machine-name}`是您尝试连接的计算机的名称。
    1. `{port-number}`是需要进行连接的端口。 通常这个端口是3389。 如果实验室 VM 在 DevTest 实验室中使用[共享 IP](devtest-lab-shared-ip.md)功能，则端口将不同。
1. 远程桌面网关将调用从`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`Azure 函数延迟到 Azure 函数以生成身份验证令牌。 DevTest Labs 服务会自动在请求标头中包含功能密钥。 功能键将保存在实验室的密钥保管库中。 该机密的名称在实验室的 **"实验室设置"** 页上显示为**网关令牌密钥**。
1. Azure 函数应返回令牌，用于对网关计算机进行基于证书的令牌身份验证。  
1. 然后，获取 RDP 文件内容操作返回完整的 RDP 文件，包括身份验证信息。
1. 使用首选 RDP 连接程序打开 RDP 文件。 请记住，并非所有 RDP 连接程序都支持令牌身份验证。 身份验证令牌确实具有由函数应用设置的到期日期。 在令牌过期之前与实验室 VM 建立连接。
1. 远程桌面网关计算机对 RDP 文件中的令牌进行身份验证后，连接将转发到实验室计算机。

### <a name="solution-requirements"></a>解决方案要求
要使用 DevTest Labs 令牌身份验证功能，网关计算机、域名服务 （DNS） 和功能有一些配置要求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>远程桌面网关计算机的要求
- 必须在网关计算机上安装 SSL 证书才能处理 HTTPS 流量。 如果只有一台计算机，证书必须匹配网关服务器场的负载均衡器的完全限定域名 （FQDN） 或计算机本身的 FQDN。 通配符 SSL 证书不起作用。  
- 安装在网关计算机上的签名证书。 使用[Create-签名证书.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)脚本创建签名证书。
- 安装支持远程桌面网关令牌身份验证[的可插拔身份验证](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模块。 这种模块的一个示例是`RDGatewayFedAuth.msi`[系统中心虚拟机管理器 （VMM） 映像](/system-center/vmm/install-console?view=sc-vmm-1807)附带的一个示例。 有关系统中心的详细信息，请参阅[系统中心文档](https://docs.microsoft.com/system-center/)和[定价详细信息](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 网关服务器可以处理对`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`发出的请求。

    网关主机名是网关服务器场负载均衡器的 FQDN，或者只有一台计算机时计算机本身的 FQDN。 是`{lab-machine-name}`您尝试连接的实验室计算机的名称，`{port-number}`也是将在其上进行连接的端口。  默认情况下，此端口为 3389。  但是，如果虚拟机在 DevTest 实验室中使用[共享 IP](devtest-lab-shared-ip.md)功能，则端口将不同。
- Internet 信息服务器 （IIS）[的应用程序路由请求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模块可用于将请求重定向`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`到 azure 函数，该函数处理获取令牌进行身份验证的请求。


## <a name="requirements-for-azure-function"></a>Azure 函数的要求
Azure 函数处理具有 的`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}`格式的请求，并基于网关计算机上安装的相同签名证书返回身份验证令牌。 是`{function-app-uri}`用于访问函数的 uri。 函数键将自动在请求的标头中传递。 有关示例函数，请参阅[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>网络要求

- 与网关计算机上安装的 SSL 证书关联的 FQDN 的 DNS 必须将流量定向到网关计算机或网关计算机服务器场的负载均衡器。
- 如果实验室计算机使用专用 IP，则必须有一个网络路径，从网关计算机到实验室计算机，通过共享同一虚拟网络或使用对等虚拟网络。

## <a name="configure-the-lab-to-use-token-authentication"></a>将实验室配置为使用令牌身份验证 
本节演示如何将实验室配置为使用支持令牌身份验证的远程桌面网关计算机。 本节不介绍如何设置远程桌面网关服务器场本身。 有关此信息，请参阅本文末尾的示例[以创建远程桌面网关](#sample-to-create-a-remote-desktop-gateway)部分。 

在更新实验设置之前，请存储成功执行函数以在实验室密钥保管库中返回身份验证令牌所需的密钥。 您可以在 Azure 门户中的函数**的"管理**"页中获取函数键值。 有关如何在密钥保管库中保存机密的详细信息，请参阅[向密钥保管库添加机密](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)。 保存机密的名称以供以后使用。

要查找实验室密钥保管库的 ID，运行以下 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用以下步骤将实验室配置为使用令牌身份验证：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择“所有服务”****，并从列表中选择“开发测试实验室”****。
1. 从实验室列表中，选择您的**实验室**。
1. 在实验室的页面上，选择 **"配置"和"策略**"。
1. 在左侧菜单中，在 **"设置"** 部分中，选择 **"实验室设置**"。
1. 在 **"远程桌面**"部分中，输入远程桌面服务网关计算机或服务器场的**网关主机**字段的完全限定域名 （FQDN） 或 IP 地址。 此值必须与网关计算机上使用的 SSL 证书的 FQDN 匹配。

    ![实验室设置中的远程桌面选项](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在 **"远程桌面**"部分中，对于**网关令牌**密钥，请输入前面创建的秘密的名称。 此值不是函数键本身，而是实验室密钥保管库中保存函数键的机密的名称。

    ![实验室设置中的网关令牌密钥](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **保存**变化。

    > [!NOTE] 
    > 通过单击 **"保存**"，即表示您同意[远程桌面网关的许可证条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)并[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果首选通过自动化配置实验室，请参阅[Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)了解用于设置**网关主机名**和**网关令牌密钥**设置的示例 PowerShell 脚本。 [Azure 开发人员测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)还提供 Azure 资源管理器模板，该模板使用**网关主机名**和**网关令牌密钥**设置创建或更新实验室。

## <a name="configure-network-security-group"></a>配置网络安全组
为了进一步保护实验室，可以将网络安全组 （NSG） 添加到实验室虚拟机使用的虚拟网络中。 有关如何设置 NSG 的说明，请参阅[创建、更改或删除网络安全组](../virtual-network/manage-network-security-group.md)。

下面是一个仅允许首先通过网关的流量到达实验室计算机的 NSG 示例。 此规则中的源是单个网关计算机的 IP 地址，或网关计算机前面的负载均衡器的 IP 地址。

![网络安全组 - 规则](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>创建远程桌面网关的示例

> [!NOTE] 
> 通过使用示例模板，您同意[远程桌面网关的许可证条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)并[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 开发人员测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)提供了一些示例，以帮助使用 DevTest Labs 设置使用令牌身份验证和远程桌面网关所需的资源。 这些示例包括用于网关计算机、实验室设置和功能应用的 Azure 资源管理器模板。

按照以下步骤为远程桌面网关服务器场设置示例解决方案。

1. 创建签名证书。  运行[创建签名证书.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存所创建证书的指纹、密码和 Base64 编码。
2. 获取 SSL 证书。 与 SSL 证书关联的 FQDN 必须针对您控制的域。 保存此证书的指纹、密码和 Base64 编码。 要使用 PowerShell 获取指纹，请使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    要使用 PowerShell 获取 Base64 编码，请使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 从[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)下载文件。

    该模板需要访问同一基础 URI 中的一些其他资源管理器模板和相关资源。 将所有文件从[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth.msi 复制到存储帐户中的 blob 容器。  
4. 从[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)部署**azuredeploy.json。** 该模板采用以下参数：
    - 管理员用户名 = 必需。  网关计算机的管理员用户名。
    - 管理员密码 = 必需。 网关计算机管理员帐户的密码。
    - 实例计数 = 要创建的网关计算机数。  
    - 始终打开 = 指示是否将创建的 Azure 函数应用保持在温暖状态。 保留 Azure 函数应用将避免用户首次尝试连接到其实验室 VM 时的延迟，但它确实会产生成本问题。  
    - 令牌生存期 = 创建令牌将有效的时间长度。 格式为 HH：MM：SS。
    - ssl 证书 – 网关计算机 SSL 证书的 Base64 编码。
    - ssl 证书密码 – 网关计算机的 SSL 证书的密码。
    - ssl 证书指纹 - SSL 证书的本地证书存储中的标识证书指纹。
    - 签名证书 – 用于网关计算机签名证书的 Base64 编码。
    - 签名证书密码 – 网关计算机签名证书的密码。
    - 签名证书指纹 - 签名证书的本地证书存储中的标识证书指纹。
    - _artifactsLocation = URI 位置，可在其中找到所有支持资源。 此值必须是完全限定的 UIR，而不是相对路径。
    - _artifactsLocationSasToken = 共享访问签名 （SAS） 令牌用于访问支持资源（如果位置是 Azure 存储帐户）。

    可以使用以下命令使用 Azure CLI 部署模板：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    以下是参数的说明：

    - 可以通过运行`az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`获取 [存储帐户终结点]。  [存储-acct 名称] 是存储帐户的名称，该帐户保存您上载的文件。  
    - [容器名称] 是保存您上载的文件的 [存储-acct 名称] 中的容器的名称。  
    - 可以通过运行`az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`获得 [sas-token]。 
        - [存储-acct 名称] 是存储帐户的名称，该帐户保存您上载的文件。  
        - [容器名称] 是保存您上载的文件的 [存储-acct 名称] 中的容器的名称。  
        - [utc 过期日期] 是 SAS 令牌将过期的日期，SAS 令牌不能再用于访问存储帐户。

    从模板部署输出中记录网关FQDN和网关IP的值。 您还需要为新创建的函数保存函数键的值，可在["功能应用设置"](../azure-functions/functions-how-to-use-azure-function-app-settings.md)选项卡中找到。
5. 配置 DNS，以便 SSL 证书的 FQDN 从上一步直接到网关IP的 IP 地址。

    创建远程桌面网关服务器场并进行适当的 DNS 更新后，开发人员测试实验室中的实验室就可以使用它了。 网关**主机名**和**网关令牌密钥**设置必须配置为使用已部署的网关计算机。 

    > [!NOTE]
    > 如果实验室计算机使用专用 IP，则必须有从网关计算机到实验室计算机的网络路径，无论是通过共享同一虚拟网络还是使用对等虚拟网络。

    配置网关和实验室后，实验室用户单击**Connect**时创建的连接文件将自动包含使用令牌身份验证进行连接所需的信息。     

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关远程桌面服务：[远程桌面服务文档](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


