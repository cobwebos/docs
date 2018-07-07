---
title: 使用 CLI 连接到 Azure Stack | Microsoft Docs
description: 了解如何使用跨平台命令行接口 (CLI) 管理和部署 Azure Stack 上的资源
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 1b59409e43a23dd63a6697a44a20df079a751516
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866852"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Azure CLI 2.0 配合使用

可以按照这篇文章，若要将 Azure 命令行接口 (CLI) 设置为从 Linux、 Mac 和 Windows 客户端平台管理 Azure Stack 开发工具包资源中的步骤。

## <a name="install-cli"></a>安装 CLI

登录到开发工作站并安装 CLI。 Azure Stack 需要 Azure CLI 2.0 版。 可以使用[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 一文中所述的步骤来安装它。 若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

```azurecli
az --version
```

应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。

## <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

1. 从 [Azure Stack 运营商](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate)获取 Azure Stack CA 根证书，并信任该证书。 若要信任 Azure Stack CA 根书，请将它附加到现有的 Python 证书。

2. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 需要安装 [pip](https://pip.pypa.io) 和 [certifi](https://pypi.org/project/certifi/) 模块。 可在 bash 提示符下使用以下 Python 命令：

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于安装的 Python 的 OS 和版本。

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>设置开发计算机在云中的路径

如果从 Azure Stack 环境中创建的 Linux 计算机运行 CLI，请结合证书的路径运行以下 bash 命令。

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>设置开发计算机在云外部的路径

如果从计算机运行 CLI**外部**Azure Stack 环境：  

1. 必须[与 Azure Stack 建立 VPN 连接](azure-stack-connect-azure-stack.md)。

2. 复制从 Azure Stack 运营商获取的 PEM 证书，并记下文件的位置 (PATH_TO_PEM_FILE)。

3. 根据开发工作站的 OS 运行以下命令。

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

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>获取虚拟机别名终结点

在使用 CLI 创建虚拟机之前，用户必须联系 Azure Stack 运营商，并获取虚拟机别名终结点 URI。 例如，Azure 使用以下 URI：https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json。 云管理员应使用 Azure Stack 市场中提供的映像，为 Azure Stack 设置类似的终结点。 用户需将终结点 URI 传递给 `az cloud register` 命令的 `endpoint-vm-image-alias-doc` 参数，如以下部分所示。 
   

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

2. 使用以下命令设置活动环境。

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

3. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects)的形式登录到 Azure Stack 环境。 

   * 以用户身份登录：可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > 如果用户帐户已启用多重身份验证，则可以使用不带 `-u` 参数的 `az login command` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。
   
   * 以服务主体的形式登录：在登录之前，请通过 [Azure 门户](azure-stack-create-service-principals.md)或 CLI 创建一个服务主体，并为其分配角色。 接下来，使用以下命令登录：

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create \
  -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知问题
在 Azure Stack 中使用 CLI 时，必须注意一些已知问题：

 - Azure Stack 尚不支持 CLI 交互模式（例如 `az interactive` 命令）。
 - 若要获取 Azure Stack 中可用的虚拟机映像列表，请使用 `az vm images list --all` 命令，而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应只返回 Azure Stack 环境中可用的映像。
 - Azure 中可用的虚拟机映像别名可能不适用于 Azure Stack。 使用虚拟机映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

## <a name="next-steps"></a>后续步骤

[使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)

[为 Azure Stack 用户启用 Azure CLI（操作员）](..\azure-stack-cli-admin.md)

[管理用户权限](azure-stack-manage-permissions.md)