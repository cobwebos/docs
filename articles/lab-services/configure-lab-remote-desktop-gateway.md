---
title: 配置一个实验，用于在 Azure 开发测试实验室中使用远程桌面网关 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中配置实验室，使用远程桌面网关，以确保实验室 Vm 的安全访问，而无需公开 RDP 端口。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078997"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>若要使用远程桌面网关的 Azure 开发测试实验室中配置在实验室
在 Azure 开发测试实验室中，可以配置实验室，以确保安全访问实验室虚拟机 (Vm) 的远程桌面网关，而无需公开 RDP 端口。 实验室提供了一个中心位置，实验室用户查看并连接到他们有权访问的所有虚拟机。 **Connect**按钮**虚拟机**页创建可打开以连接到计算机的特定于计算机的 RDP 文件。 可以进一步自定义并通过将实验室连接到远程桌面网关的安全 RDP 连接。 

这种方法是更安全，因为实验室用户直接向网关计算机进行身份验证，或者可以使用已加入域的网关计算机上的公司凭据来连接到其计算机。 实验室还支持使用令牌身份验证网关计算机，使用户可以连接到其实验室虚拟机，而无需向 internet 公开 RDP 端口。 本文将指导示例如何设置令牌身份验证用于连接到实验室计算机的实验室。

## <a name="architecture-of-the-solution"></a>解决方案的体系结构

![解决方案的体系结构](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. [获取 RDP 文件内容](/rest/api/dtl/virtualmachines/getrdpfilecontents)当选中调用操作**Connect** button.1。 
1. 获取 RDP 文件内容操作调用`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`请求的身份验证令牌。
    1. `{gateway-hostname}` 是网关主机名上指定**实验室设置**实验室在 Azure 门户中的页。 
    1. `{lab-machine-name}` 是你正在尝试连接的名称。
    1. `{port-number}` 是需要在其的连接的端口。 通常，此端口是 3389。 如果 VM 使用实验室[共享 IP](devtest-lab-shared-ip.md)功能在开发测试实验室中，该端口将会不同。
1. 远程桌面网关将推迟从调用`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`到 Azure 函数以生成身份验证令牌。 开发测试实验室服务会自动请求标头中包括的函数密钥。 功能键是保存在实验室的密钥保管库。 若要显示为该机密的名称**网关令牌机密**上**实验室设置**实验室的页。
1. Azure 函数应返回对网关计算机基于证书的令牌身份验证的令牌。  
1. 获取 RDP 文件内容操作，然后返回完成的 RDP 文件，包括身份验证信息。
1. 打开使用您首选的 RDP 连接程序的 RDP 文件。 请记住，并非所有 RDP 连接程序都支持令牌身份验证。 身份验证令牌具有到期日期，由 function app 设置。 令牌过期之前，请与实验室 VM 的连接。
1. 后远程桌面网关计算机进行身份验证令牌中的 RDP 文件，将连接转接到实验室计算机。

### <a name="solution-requirements"></a>解决方案要求
若要使用开发测试实验室令牌身份验证功能，有几个配置要求的网关计算机、 域名服务 (DNS) 和函数。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>远程桌面网关计算机的要求
- 若要处理 HTTPS 流量在网关计算机上必须安装 SSL 证书。 如果只有一台计算机，证书必须为网关服务器场或计算机本身的 FQDN 的负载均衡器的匹配的完全限定的域名 (FQDN)。 通配符 SSL 证书不起作用。  
- 安装网关计算机上的签名证书。 通过使用创建的签名证书[创建 SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)脚本。
- 安装[可插入身份验证](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)远程桌面网关的支持令牌身份验证模块。 此类模块的一个示例是`RDGatewayFedAuth.msi`附带所[System Center Virtual Machine Manager (VMM) 映像](/system-center/vmm/install-console?view=sc-vmm-1807)。 有关 System Center 的详细信息，请参阅[System Center 文档](https://docs.microsoft.com/system-center/)并[定价详细信息](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 网关服务器可以处理对发出的请求`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`。

    网关的主机名是网关服务器场的负载均衡器的 FQDN 或计算机本身的 FQDN，如果只有一台计算机。 `{lab-machine-name}`想要连接，实验室计算机的名称和`{port-number}`是将其进行连接的端口。  默认情况下，此端口是 3389。  但是，如果使用虚拟机[共享 IP](devtest-lab-shared-ip.md)功能在开发测试实验室中，该端口将会不同。
- [应用程序路由请求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模块的 Internet 信息服务器 (IIS) 可用于重定向`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`给 azure 函数，后者处理请求以获取令牌身份验证的请求。


## <a name="requirements-for-azure-function"></a>Azure 函数的要求
Azure 函数处理请求的格式与`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}`和返回同一个基于身份验证令牌签名证书安装在网关计算机上。 `{function-app-uri}`是用来访问该函数的 uri。 功能键自动将传入的请求标头。 示例函数，请参阅[ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>网络的要求

- 有关与网关计算机上安装的 SSL 证书关联的 FQDN 的 DNS 必须将流量定向到网关计算机或网关计算机场的负载均衡器。
- 如果实验室计算机使用的专用 Ip，必须从网关计算机实验室计算机，可以通过共享同一个虚拟网络或使用对等互连的虚拟网络的网络路径。

## <a name="configure-the-lab-to-use-token-authentication"></a>配置在实验室以使用令牌身份验证 
本部分演示如何配置为使用支持令牌身份验证的远程桌面网关计算机的实验室。 本部分中未介绍如何设置远程桌面网关场本身。 该信息，请参阅[示例，以便创建远程桌面网关](#sample-to-create-a-remote-desktop-gateway)本文末尾部分。 

更新实验室设置之前，请将存储已成功执行该函数返回在实验室的密钥保管库中的身份验证令牌所需的密钥。 您可以进入函数键值**管理**函数在 Azure 门户中的页。 有关如何将机密保存在密钥保管库的详细信息，请参阅[添加到密钥保管库机密](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)。 保存以供将来使用的机密的名称。

若要查找的实验室的密钥保管库 ID，请运行以下 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

配置实验室以使用令牌身份验证通过使用以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
1. 从实验室列表中，选择你**实验室**。
1. 在实验室的页上，选择**配置和策略**。
1. 在左侧菜单中**设置**部分中，选择**实验室设置**。
1. 在中**远程桌面**部分中，输入完全限定的域名 (FQDN) 或远程桌面服务网关计算机的 IP 地址或为场**网关主机名**字段。 此值必须与网关计算机上使用的 SSL 证书的 FQDN 匹配。

    ![在实验室设置中的远程桌面选项](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在中**远程桌面**部分中，对于**网关令牌**机密，输入前面创建的机密的名称。 此值不是机密的函数密钥本身，而保留的函数密钥的实验室的密钥保管库中的名称。

    ![在实验室设置中的网关令牌机密](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **保存**更改。

    > [!NOTE] 
    > 通过单击**保存**，表示你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)并[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果函数是首选配置通过自动化实验室，请参阅[集 DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)若要设置的示例 PowerShell 脚本**网关主机名**和**网关令牌机密**设置。 [Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)还提供了一个 Azure 资源管理器模板可创建或更新与实验室**网关主机名**和**网关令牌机密**设置。

## <a name="configure-network-security-group"></a>配置网络安全组
若要进一步保护实验室，网络安全组 (NSG) 可以添加到实验室虚拟机使用虚拟网络。 有关说明如何设置 NSG，请参阅[创建、 更改或删除网络安全组](../virtual-network/manage-network-security-group.md)。

下面是一个示例 NSG，只允许通信，它们首先将通过网关来访问实验室计算机。 此规则中的源是单个网关计算机的 IP 地址或网关计算机前的负载均衡器的 IP 地址。

![网络安全组-规则](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>若要创建远程桌面网关的示例

> [!NOTE] 
> 通过使用示例模板，你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)并[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)提供了几个示例来帮助安装程序使用开发测试实验室中使用令牌身份验证和远程桌面网关所需的资源。 这些示例包括网关计算机、 实验室设置和函数应用的 Azure 资源管理器模板。

按照以下步骤来设置远程桌面网关服务器场的示例解决方案。

1. 创建一个签名证书。  运行[创建 SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存指纹、 密码和 Base64 编码的创建的证书。
2. 获取 SSL 证书。 你控制的域必须处于与 SSL 证书关联的 FQDN。 保存指纹、 密码和 Base64 编码此证书。 若要获取使用 PowerShell 的指纹，请使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    若要获取使用 PowerShell 的 Base64 编码，请使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 从下载文件[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)。

    该模板要求对几个其他资源管理器模板和相关的资源，请访问相同的基 URI 的访问。 从所有文件都复制[ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth.msi 到存储帐户中的 blob 容器。  
4. 部署**azuredeploy.json**从[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)。 该模板采用以下参数：
    - adminUsername – 必需。  网关计算机的管理员用户名。
    - adminPassword-必需。 网关计算机的管理员帐户的密码。
    - instanceCount – 若要创建的网关计算机的数目。  
    - alwaysOn – 指示是否要或不处于热状态保留创建的 Azure Functions 应用。 当用户第一次尝试连接到其实验室 VM，但它确实具有成本影响时，保持 Azure Functions 应用会避免延迟。  
    - 令牌生存期 – 创建的令牌将处于有效的时间长度。 格式为 hh: mm:。
    - sslCertificate-Base64 编码的网关计算机的 SSL 证书。
    - sslCertificatePassword – 网关计算机的 SSL 证书的密码。
    - sslCertificateThumbprint-SSL 证书的本地证书存储中的标识的证书指纹。
    - signCertificate-Base64 编码的网关计算机的证书进行签名。
    - signCertificatePassword – 网关计算机的证书进行签名的密码。
    - signCertificateThumbprint-为签名证书的本地证书存储中的标识的证书指纹。
    - _artifactsLocation – 可以找到所有支持资源的 URI 位置。 此值必须是完全限定的 UIR，而非相对路径。
    - _artifactsLocationSasToken – 用于访问支持资源，如果位置为 Azure 存储帐户的共享访问签名 (SAS) 令牌。

    可以通过使用以下命令中使用 Azure CLI 部署模板：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    下面是参数的说明：

    - 可以通过运行获取 {存储帐户的终结点} `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`。  {存储的帐户名称} 是包含已上传的文件的存储帐户的名称。  
    - {容器-name} 是 {存储的帐户名称} 保存已上传的文件的容器的名称。  
    - 可以通过运行获取 {sas-令牌} `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`。 
        - {存储的帐户名称} 是包含已上传的文件的存储帐户的名称。  
        - {容器-name} 是 {存储的帐户名称} 保存已上传的文件的容器的名称。  
        - {Utc 的到期日期} 是日期，采用 UTC，SAS 令牌将过期且不再可以使用 SAS 令牌访问存储帐户。

    记录 gatewayFQDN 和 gatewayIP 从模板部署输出的值。 您还需要保存新创建的函数，可在中的函数密钥的值[Function app 设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)选项卡。
5. 配置 DNS 以便从上一步，该 FQDN 的 SSL 证书将定向到 gatewayIP 的 IP 地址。

    创建远程桌面网关服务器场并进行相应的 DNS 更新后，已准备好通过开发测试实验室的实验室中使用。 **网关主机名**并**网关令牌机密**设置必须配置为使用你部署的网关计算机。 

    > [!NOTE]
    > 如果实验室计算机使用的专用 Ip，必须从网关计算机实验室计算机，可以通过共享同一个虚拟网络或使用对等互连的虚拟网络的网络路径。

    网关和实验室配置后，实验室用户单击时创建的连接文件**Connect**将自动包括使用令牌身份验证连接所需信息。     

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关远程桌面服务的详细信息：[远程桌面服务文档](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


