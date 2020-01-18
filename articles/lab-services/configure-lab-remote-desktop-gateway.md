---
title: 在 Azure 开发测试实验室中配置实验室以使用远程桌面网关
description: 了解如何使用远程桌面网关在 Azure 开发测试实验室中配置实验室，以确保安全访问实验室 Vm，而无需公开 RDP 端口。
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170285"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>将 Azure 开发测试实验室中的实验室配置为使用远程桌面网关
在 Azure 开发测试实验室中，可以为实验室配置远程桌面网关，以确保无需公开 RDP 端口即可安全访问实验室虚拟机（Vm）。 实验室为实验室用户提供了一个中心位置，用于查看并连接到他们有权访问的所有虚拟机。 "**虚拟机**" 页上的 "**连接**" 按钮创建计算机特定的 RDP 文件，您可以打开该文件以连接到计算机。 可以通过将实验室连接到远程桌面网关，进一步自定义和保护 RDP 连接。 

此方法更加安全，因为实验室用户直接向网关计算机进行身份验证，或者可以使用已加入域的网关计算机上的公司凭据连接到其计算机。 实验室还支持对网关计算机使用令牌身份验证，该身份验证允许用户连接到实验室虚拟机，而无需向 internet 公开 RDP 端口。 本文介绍如何设置使用令牌身份验证连接到实验室计算机的实验室的示例。

## <a name="architecture-of-the-solution"></a>解决方案的体系结构

![解决方案的体系结构](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 当你选择 "**连接**" 按钮时，将调用 "[获取 RDP 文件内容](/rest/api/dtl/virtualmachines/getrdpfilecontents)" 操作。 
1. "获取 RDP 文件内容" 操作会调用 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 以请求身份验证令牌。
    1. `{gateway-hostname}` 是 Azure 门户中实验室的 "**实验室设置**" 页上指定的网关主机名。 
    1. `{lab-machine-name}` 是你要尝试连接的计算机的名称。
    1. `{port-number}` 是需要建立连接的端口。 通常，此端口为3389。 如果实验室 VM 正在使用开发测试实验室中的[共享 IP](devtest-lab-shared-ip.md)功能，则该端口将有所不同。
1. 远程桌面网关会将 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 的调用推迟到 Azure 函数，以生成身份验证令牌。 开发测试实验室服务自动在请求标头中包含功能键。 功能密钥将保存在实验室的密钥保管库中。 在实验室的 "**实验室设置**" 页上显示为 "**网关令牌机密**" 的机密名称。
1. Azure 函数应针对网关计算机返回基于证书的令牌身份验证的令牌。  
1. 然后，"获取 RDP 文件内容" 操作返回完整的 RDP 文件，其中包括身份验证信息。
1. 使用首选的 RDP 连接程序打开 RDP 文件。 请记住，并非所有 RDP 连接程序都支持令牌身份验证。 身份验证令牌有一个过期日期，由 function app 设置。 在令牌过期之前，建立到实验室 VM 的连接。
1. 远程桌面网关计算机在 RDP 文件中对令牌进行身份验证后，该连接将转发到实验室计算机。

### <a name="solution-requirements"></a>解决方案要求
若要使用开发测试实验室令牌身份验证功能，网关计算机、域名服务（DNS）和函数有一些配置要求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>远程桌面网关计算机的要求
- SSL 证书必须安装在网关计算机上才能处理 HTTPS 通信。 如果只有一台计算机，则证书必须与网关场的负载均衡器的完全限定的域名（FQDN）或计算机本身的 FQDN 相匹配。 通配符 SSL 证书不起作用。  
- 在网关计算机上安装的签名证书。 使用[Create-SigningCertificate](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)脚本创建签名证书。
- 安装支持远程桌面网关令牌身份验证的可[插入身份验证](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模块。 此类模块的一个示例是[System Center Virtual Machine Manager （VMM）映像](/system-center/vmm/install-console?view=sc-vmm-1807)附带 `RDGatewayFedAuth.msi`。 有关 System Center 的详细信息，请参阅[System center 文档](https://docs.microsoft.com/system-center/)和[定价详细](https://www.microsoft.com/cloud-platform/system-center-pricing)信息。  
- 网关服务器可以处理对 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`发出的请求。

    网关主机名是网关场的负载均衡器的 FQDN 或计算机本身的 FQDN （如果只有一个计算机）。 `{lab-machine-name}` 是你要尝试连接的实验室计算机的名称，而 `{port-number}` 是建立连接所用的端口。  默认情况下，此端口为3389。  但是，如果虚拟机正在开发测试实验室中使用[共享 IP](devtest-lab-shared-ip.md)功能，则该端口将有所不同。
- Internet Information Server （IIS）的[应用程序路由请求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模块可用于将 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 请求重定向到 azure 函数，该模块处理请求以获取用于身份验证的令牌。


## <a name="requirements-for-azure-function"></a>Azure function 的要求
Azure 函数处理 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 格式的请求，并根据安装在网关计算机上的相同签名证书返回身份验证令牌。 `{function-app-uri}` 是用于访问函数的 uri。 自动在请求的标头中传递此功能键。 有关示例函数，请参阅[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>网络要求

- 与在网关计算机上安装的 SSL 证书关联的 FQDN 的 DNS 必须将流量定向到网关计算机或网关计算机场的负载均衡器。
- 如果实验室计算机使用专用 Ip，则必须从网关计算机到实验室计算机的网络路径，方法是共享同一虚拟网络或使用对等互连虚拟网络。

## <a name="configure-the-lab-to-use-token-authentication"></a>将实验室配置为使用令牌身份验证 
本部分介绍如何将实验室配置为使用支持令牌身份验证的远程桌面网关计算机。 本部分不介绍如何设置远程桌面网关场本身。 有关此信息，请参阅本文末尾的[创建远程桌面网关的示例](#sample-to-create-a-remote-desktop-gateway)部分。 

更新实验室设置之前，请在实验室的密钥保管库中存储成功执行函数以返回身份验证令牌所需的密钥。 可以在 Azure 门户的函数的 "**管理**" 页中获取功能密钥值。 有关如何在密钥保管库中保存机密的详细信息，请参阅[将机密添加到 Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)。 保存机密名称供以后使用。

若要查找实验室的密钥保管库的 ID，请运行以下 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用以下步骤将实验室配置为使用令牌身份验证：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中，选择**实验室**。
1. 在实验室页面上，选择 "**配置和策略**"。
1. 在左侧菜单的 "**设置**" 部分中，选择 "**实验室设置**"。
1. 在 "**远程桌面**" 部分中，输入 "**网关主机**名" 字段的完全限定的域名（FQDN）或 IP 地址。 此值必须与网关计算机上使用的 SSL 证书的 FQDN 相匹配。

    ![实验室设置中的远程桌面选项](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在 "**远程桌面**" 部分中，为 "**网关令牌**机密" 输入先前创建的密钥的名称。 此值不是功能键本身，而是实验室的密钥保管库中包含功能密钥的密钥的名称。

    ![实验室设置中的网关令牌机密](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **保存**变化.

    > [!NOTE] 
    > 单击 "**保存**" 即表示你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)和[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果首选通过自动化配置实验室，请参阅[Set-DevTestLabGateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) for PowerShell 脚本示例，以设置**网关主机名**和**网关令牌机密**设置。 [Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)还提供 azure 资源管理器模板，该模板使用**网关主机名**和**网关令牌机密**设置创建或更新实验室。

## <a name="configure-network-security-group"></a>配置网络安全组
为了进一步保护实验室，可向实验室虚拟机使用的虚拟网络添加网络安全组（NSG）。 有关如何设置 NSG 的说明，请参阅[创建、更改或删除网络安全组](../virtual-network/manage-network-security-group.md)。

下面是一个示例 NSG，只允许第一次通过网关到达实验室计算机的流量。 此规则中的源是单个网关计算机的 IP 地址，或网关计算机前面的负载均衡器的 IP 地址。

![网络安全组-规则](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>创建远程桌面网关的示例

> [!NOTE] 
> 使用示例模板即表示你同意[远程桌面网关的许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 有关远程网关的详细信息，请参阅[欢迎使用远程桌面服务](https://aka.ms/rds)和[部署远程桌面环境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab)提供了几个示例，可帮助设置使用令牌身份验证和具有开发测试实验室的远程桌面网关所需的资源。 这些示例包括用于网关计算机、实验室设置和函数应用的 Azure 资源管理器模板。

按照以下步骤设置远程桌面网关场的示例解决方案。

1. 创建签名证书。  运行[Create-SigningCertificate](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存所创建证书的指纹、密码和 Base64 编码。
2. 获取 SSL 证书。 与 SSL 证书关联的 FQDN 必须适用于你控制的域。 保存此证书的指纹、密码和 Base64 编码。 若要使用 PowerShell 获取指纹，请使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    若要使用 PowerShell 获取 Base64 编码，请使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 下载[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)中的文件。

    该模板需要访问同一基本 URI 上的一些其他资源管理器模板和相关资源。 将[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth 中的所有文件复制到存储帐户中的 blob 容器。  
4. 从[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)部署**azuredeploy.json** 。 模板采用以下参数：
    - adminUsername –必需。  网关计算机的管理员用户名。
    - adminPassword –必需。 网关计算机的管理员帐户的密码。
    - instanceCount-要创建的网关计算机数。  
    - alwaysOn-指示是否保持创建的 Azure Functions 应用处于热状态。 当用户首次尝试连接到其实验室 VM 时，保留 Azure Functions 应用将避免延迟，但会产生成本影响。  
    - 令牌生存期-创建的令牌生效的时间长度。 格式为 HH： MM： SS。
    - sslCertificate-网关计算机的 SSL 证书的 Base64 编码。
    - sslCertificatePassword-网关计算机的 SSL 证书的密码。
    - sslCertificateThumbprint-SSL 证书的本地证书存储中的标识证书指纹。
    - signCertificate-用于网关计算机签名证书的 Base64 编码。
    - signCertificatePassword-用于为网关计算机签名证书的密码。
    - signCertificateThumbprint-签名证书的本地证书存储中的标识证书指纹。
    - _artifactsLocation –可在其中找到所有支持资源的 URI 位置。 此值必须是完全限定的 UIR，而不是相对路径。
    - _artifactsLocationSasToken –用于访问支持资源的共享访问签名（SAS）令牌（如果该位置是 Azure 存储帐户）。

    可以使用以下命令通过 Azure CLI 来部署模板：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    以下是参数的说明：

    - 可以通过运行 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`获取 {存储帐户终结点}。  {存储帐户名称} 是包含已上传的文件的存储帐户的名称。  
    - {Container-name} 是包含你上载的文件的 {存储帐户名称} 中的容器的名称。  
    - 可以通过运行 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`获取 {sas 令牌}。 
        - {存储帐户名称} 是包含已上传的文件的存储帐户的名称。  
        - {Container-name} 是包含你上载的文件的 {存储帐户名称} 中的容器的名称。  
        - {Utc 过期日期} 是指 SAS 令牌过期的日期，并且不能再使用 SAS 令牌来访问该存储帐户。

    记录模板部署输出中的 gatewayFQDN 和 gatewayIP 的值。 还需要保存新创建函数的 "函数密钥" 的值，该函数可在 "[函数应用设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)" 选项卡中找到。
5. 配置 DNS，以便 SSL 证书的 FQDN 定向到上一步骤中 gatewayIP 的 IP 地址。

    创建远程桌面网关场并进行相应的 DNS 更新后，开发测试实验室中的实验室便可使用它。 **网关主机名**和**网关令牌机密**设置必须配置为使用部署的网关计算机。 

    > [!NOTE]
    > 如果实验室计算机使用专用 Ip，则必须从网关计算机到实验室计算机的网络路径，方法是共享同一虚拟网络或使用对等互连虚拟网络。

    配置网关和实验室后，当实验室用户单击该**连接**时创建的连接文件将自动包含使用令牌身份验证进行连接所需的信息。     

## <a name="next-steps"></a>后续步骤
请参阅以下文章，详细了解远程桌面服务：[远程桌面服务文档](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


