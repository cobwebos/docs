---
title: 使用 CLI 连接到 Azure Stack | Microsoft Docs
description: 了解如何使用跨平台命令行接口 (CLI) 管理和部署 Azure Stack 上的资源
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 15354cd7472e7cffb7a40ca431bc23eb65b9a9a9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845888"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Azure CLI 配合使用

可以按照本文中的步骤设置 Azure 命令行接口 (CLI)，以从 Linux、Mac 和 Windows 客户端平台管理 Azure Stack 开发工具包资源。

## <a name="install-cli"></a>安装 CLI

登录到开发工作站并安装 CLI。 Azure Stack 需要 Azure CLI 2.0 版或更高版本。 可以使用中所述的步骤来安装该版本[安装 Azure CLI](/cli/azure/install-azure-cli)一文。 若要验证安装是否成功，请打开一个终端或命令提示符窗口，并运行以下命令：

```azurecli
az --version
```

应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。

## <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

1. 从 [Azure Stack 运营商](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate)获取 Azure Stack CA 根证书，并信任该证书。 若要信任 Azure Stack CA 根书，请将它附加到现有的 Python 证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 需要安装 [pip](https://pip.pypa.io) 和 [certifi](https://pypi.org/project/certifi/) 模块。 可在 bash 提示符下使用以下 Python 命令：

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    请记下证书的位置;例如， `~/lib/python3.5/site-packages/certifi/cacert.pem`。 你的特定路径取决于你的操作系统和已安装的 Python 版本。

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>设置开发计算机在云中的路径

如果从 Azure Stack 环境中创建的 Linux 计算机运行 CLI，请结合证书的路径运行以下 bash 命令。

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>设置开发计算机在云外部的路径

如果从 Azure Stack 环境外部的计算机运行 CLI:  

1. 设置[到 Azure Stack 的 VPN 连接](azure-stack-connect-azure-stack.md)。
1. 从 Azure Stack 操作员的 PEM 证书复制并记下的文件 (PATH_TO_PEM_FILE) 的位置。
1. 在以下部分中，具体取决于开发工作站上的操作系统中运行命令。

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting required information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>获取虚拟机别名终结点

使用 CLI 创建虚拟机之前，必须联系 Azure Stack 操作员并获取虚拟机别名终结点 URI。 例如，Azure 使用以下 URI： `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`。 云管理员应使用 Azure Stack 市场中提供的映像，为 Azure Stack 设置类似的终结点。 必须通过终结点 URI 的`endpoint-vm-image-alias-doc`参数`az cloud register`命令下, 一节中所示。 
  
## <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下步骤连接到 Azure Stack：

1. 运行 `az cloud register` 命令注册 Azure Stack 环境。
   
    a. 若要注册云管理环境，请使用：

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    b. 若要注册用户环境，请使用：

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. 若要在多租户环境中注册*用户*，请使用：

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. 若要在 AD FS 环境中注册用户，请使用：

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. 使用以下命令设置活动环境。
   
    a. 对于云管理环境，请使用：

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

    b. 对于用户环境，请使用：

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack 1808 生成前的版本，则必须使用 API 版本配置文件**2017年-03-09-profile**而不是 API 版本配置文件**2018年-03-01-混合**。

1. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)的形式登录到 Azure Stack 环境。 

    * Azure AD 环境
      * 以用户身份登录：您可以指定的用户名和密码直接内的`az login`命令，或使用浏览器进行身份验证。 如果你的帐户已启用多重身份验证，您必须执行后一种操作：

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > 如果用户帐户已启用多重身份验证，则可以使用不带 `-u` 参数的 `az login command` 命令。 运行此命令提供一个 URL，必须使用进行身份验证的代码。
   
      * 以服务主体身份登录：在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](azure-stack-create-service-principals.md)，并为其分配角色。 接下来，使用以下命令登录：

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * AD FS 环境

        * 将 Web 浏览器与设备代码配合使用，以用户身份登录：  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

        * 以服务主体身份登录：
        
          1. 准备要用于服务主体登录的 .pem 文件。

            * 在客户端计算机主体的创建，服务主体将证书导出为 pfx 私钥与位于`cert:\CurrentUser\My`; 名称具有相同的名称作为主体的证书。
        
            * 将 pfx 转换为 pem （使用 OpenSSL 实用程序）。

          2.  登录到 CLI：
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>测试连接

使用完成所有设置，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create \
  -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知问题

在 Azure Stack 中使用 CLI 时，是已知问题：

 - CLI 交互模式;例如，`az interactive`命令，在 Azure Stack 中尚不支持。
 - 若要获取 Azure Stack 中可用的虚拟机映像列表，请使用 `az vm image list --all` 命令，而不是 `az vm image list` 命令。 指定`--all`选项可确保响应返回仅可在 Azure Stack 环境中的映像。
 - Azure 中可用的虚拟机映像别名可能不适用于 Azure Stack。 使用虚拟机映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
- [为 Azure Stack 用户启用 Azure CLI（操作员）](../azure-stack-cli-admin.md)
- [管理用户权限](azure-stack-manage-permissions.md) 