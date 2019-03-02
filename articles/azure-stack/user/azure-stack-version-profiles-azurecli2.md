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
ms.date: 02/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: fe5e998b919a3e2a876ef943424bd7161b71b5d4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241198"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Azure CLI 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以按照在本文中设置了 Azure 命令行接口 (CLI) 来管理 Azure Stack 开发工具包 (ASDK) 资源从 Linux、 Mac 和 Windows 客户端平台的步骤。

## <a name="prepare-for-azure-cli"></a>对于 Azure CLI 准备

你将需要在开发计算机上使用 Azure CLI 的 Azure Stack CA 根证书。 该证书用于通过 CLI 管理资源。

 - **Azure Stack CA 根证书**如果从外部 ASDK 工作站使用 CLI，则需要。  

 - **虚拟机别名终结点**提供在部署 VM 时以单个参数形式引用映像发布者、产品/服务、SKU 和版本的别名，如“UbuntuLTS”或“Win2012Datacenter”。  

以下部分介绍如何获取这些值。

### <a name="export-the-azure-stack-ca-root-certificate"></a>导出 Azure Stack CA 根证书

如果使用的集成的系统，不需要导出 CA 根证书。 您需要导出 ASDK 上的 CA 根证书。

若要导出 PEM 格式的 ASDK 根证书：

1. [在 Azure Stack 中创建 Windows VM](azure-stack-quick-windows-portal.md)。

2. 登录到计算机，打开提升的 PowerShell 提示符，然后运行以下脚本：

      ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }
      
    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
```

3. 将证书复制到本地计算机。


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>设置虚拟机别名终结点

您可以设置一个可公开访问的终结点承载虚拟机别名文件。 虚拟机别名文件是一个 JSON 文件，提供映像的公用名称。 在部署 VM 作为一个参数，Azure CLI 时，将使用名称。

1. 如果发布自定义映像，请记下发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果它是从 marketplace 映像，可以通过查看信息```Get-AzureVMImage```cmdlet。  

2. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

4. 在 Azure Stack 中创建存储帐户。 完成后，创建 blob 容器。 将访问策略设置为“公开”。  

3. 将 JSON 文件上传到新容器。 完成后，你可以查看 blob 的 URL。 选择 blob 名称，然后从 blob 属性中选择该 URL。

### <a name="install-or-ugrade-cli"></a>安装或将 CLI

登录到开发工作站并安装 CLI。 Azure Stack 需要 Azure CLI 2.0 版或更高版本。 API 配置文件的最新版本需要当前版本的 CLI。  可以使用中所述的步骤来安装 CLI[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)一文。 若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

```azurecli
az --version
```

应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。

### <a name="install-python-on-windows"></a>在 Windows 上安装 Python

1. 安装[在系统上的 Python 3](https://www.python.org/downloads/)。

2. 将 PIP 升级。 PIP 是 Python 包管理器。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```PowerShell  
    python -m pip install --upgrade pip
    ```

3. 安装**certifi**模块。 [Certifi](https://pypi.org/project/certifi/)模块和用于验证 TLS 主机的标识时验证 SSL 证书的可信度的根证书的集合。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```PowerShell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>在 Linux 上安装 Python

1. Ubuntu 16.04 映像附带了 Python 2.7 和 Python 3.5，默认情况下安装。 可以通过运行以下命令来验证你的 Python 3 版本：

    ```bash  
    python3 --version
    ```

2. 将 PIP 升级。 PIP 是 Python 包管理器。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 安装**certifi**模块。 [Certifi](https://pypi.org/project/certifi/)用于验证 TLS 主机的标识时验证 SSL 证书的可信度是根证书的集合。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>在 macOS 上安装 Python

1. 安装[在系统上的 Python 3](https://www.python.org/downloads/)。 对于 Python 3.7 发行版本，Python.org 提供两个下载二进制安装程序选项。 默认变体是仅限 64 位，适用于 macOS 10.9 (Mavericks) 和更高版本的系统。 打开终端，并键入以下命令检查你的 python 版本：

    ```bash  
    python3 --version
    ```

2. 将 PIP 升级。 PIP 是 Python 包管理器。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 安装**certifi**模块。 [Certifi](https://pypi.org/project/certifi/)模块和用于验证 TLS 主机的标识时验证 SSL 证书的可信度的根证书的集合。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

本部分将引导你完成设置 CLI 如果你使用 Azure AD 作为标识管理服务，并将 Windows 计算机上使用 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

若要信任 Azure Stack CA 根书，请将它附加到现有的 Python 证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于安装的 Python 的 OS 和版本。

2. 将它追加到现有的 Python 证书信任 Azure Stack CA 根证书。

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

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

1. 运行 `az cloud register` 命令注册 Azure Stack 环境。

    在某些情况下，通过代理或防火墙，这会强制执行 SSL 截获路由直接出站 internet 连接。 在这些情况下，`az cloud register`命令可能会失败并出现错误，如"无法从云中获取终结点"。 若要解决此错误，可以设置以下环境变量：

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

    通过指定名称注册你的环境。 指定的名称之后的环境`-n`切换。 使用`AzureStackUser`针对用户环境。 如果您是操作员，指定`AzureStackAdmin`。

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2018-03-01-hybrid**。 你将需要使用 Azure CLI 的最新版本。
 
1. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)的形式登录到 Azure Stack 环境。 

  - 以用户身份登录： 

    可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

    ```azurecli
    az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
    ```

    > [!NOTE]
    > 如果您的用户帐户已启用多重身份验证，则可以使用`az login`命令而无需提供`-u`参数。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

  - 以服务主体身份登录： 
    
    在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](azure-stack-create-service-principals.md)，并为其分配角色。 接下来，使用以下命令登录：

    ```azurecli  
    az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
    ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

本部分将引导你完成设置 CLI 如果你使用 Active Directory 联合身份验证服务 (AD FS) 作为标识管理服务，并将 Windows 计算机上使用 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 打开命令提示符处或提升的 PowerShell 提示符，并键入以下命令：

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于安装的 Python 的 OS 和版本。

2. 将它追加到现有的 Python 证书信任 Azure Stack CA 根证书。

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

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

1. 运行 `az cloud register` 命令注册 Azure Stack 环境。

    在某些情况下，通过代理或防火墙，这会强制执行 SSL 截获路由直接出站 internet 连接。 在这些情况下，`az cloud register`命令可能会失败并出现错误，如"无法从云中获取终结点"。 若要解决此错误，可以设置以下环境变量：

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

    通过指定名称注册你的环境。 指定的名称之后的环境`-n`切换。 使用`AzureStackUser`针对用户环境。 如果您是操作员，指定`AzureStackAdmin`。

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2018-03-01-hybrid**。 你将需要使用 Azure CLI 的最新版本。

1. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)的形式登录到 Azure Stack 环境。 

  - 以用户身份登录： 

    可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

    ```azurecli
    az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
    ``

    > [!NOTE]
    > If your user account has multi-factor authentication enabled, you can use the `az login` command without providing the `-u` parameter. Running this command gives you a URL and a code that you must use to authenticate.

  - Sign in as a *service principal*: 
    
    Prepare the .pem file to be used for service principal login.

    On the client machine where the principal was created, export the service principal certificate as a pfx with the private key located at `cert:\CurrentUser\My`; the cert name has the same name as the principal.

    Convert the pfx to pem (use the OpenSSL utility).

    Sign in to the CLI:
  
    ```azurecli  
    az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
    ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

本部分将引导你完成设置 CLI 如果使用 Azure AD 作为标识管理服务，并使用 CLI 在 Linux 计算机上。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

将它追加到现有的 Python 证书信任 Azure Stack CA 根证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 将需要具有 pip 和 certifi[安装模块](#install-python-on-linux)。 可在 bash 提示符下使用以下 Python 命令：

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    记下证书位置，例如 `~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于操作系统以及安装的 Python 版本。

2. 证书使用的路径运行以下 bash 命令。

  - 对于远程 Linux 计算机：

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - 对于 Azure Stack 环境中的 Linux 计算机：

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下步骤连接到 Azure Stack：

1. 运行 `az cloud register` 命令注册 Azure Stack 环境。 在某些情况下，通过代理或防火墙，这会强制执行 SSL 截获路由直接出站 internet 连接。 在这些情况下，`az cloud register`命令可能会失败并出现错误，如"无法从云中获取终结点"。 若要解决此错误，可以设置以下环境变量：

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 通过指定名称注册你的环境。 指定的名称之后的环境`-n`切换。 使用`AzureStackUser`针对用户环境。 如果您是操作员，指定`AzureStackAdmin`。

      ```azurecli  
      az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

3. 设置活动环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2018-03-01-hybrid**。 你将需要使用 Azure CLI 的最新版本。

5. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)的形式登录到 Azure Stack 环境。 

    * 以用户身份登录：

    可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

    > [!NOTE]
    > 如果您的用户帐户已启用多重身份验证，则可以使用`az login`命令而无需提供`-u`参数。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。
   
    * 以登录*服务主体*
    
    在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](azure-stack-create-service-principals.md)，并为其分配角色。 接下来，使用以下命令登录：

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
    az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

本部分将引导你完成设置 CLI 如果使用 Active Directory 联合身份验证服务 (AD FS) 作为你的管理服务，并使用 CLI 在 Linux 计算机上。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

将它追加到现有的 Python 证书信任 Azure Stack CA 根证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 将需要具有 pip 和 certifi[安装模块](#install-python-on-linux)。 可在 bash 提示符下使用以下 Python 命令：

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    记下证书位置，例如 `~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于操作系统以及安装的 Python 版本。

2. 证书使用的路径运行以下 bash 命令。

  - 对于远程 Linux 计算机：

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - 对于 Azure Stack 环境中的 Linux 计算机：

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下步骤连接到 Azure Stack：

1. 运行 `az cloud register` 命令注册 Azure Stack 环境。 在某些情况下，通过代理或防火墙，这会强制执行 SSL 截获路由直接出站 internet 连接。 在这些情况下，`az cloud register`命令可能会失败并出现错误，如"无法从云中获取终结点"。 若要解决此错误，可以设置以下环境变量：

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 通过指定名称注册你的环境。 指定的名称之后的环境`-n`切换。 使用`AzureStackUser`针对用户环境。 如果您是操作员，指定`AzureStackAdmin`。

      ```azurecli  
      az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

3. 设置活动环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2018-03-01-hybrid**。 你将需要使用 Azure CLI 的最新版本。

5. 使用 `az login` 命令登录到 Azure Stack 环境。 可以用户身份或以[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)的形式登录到 Azure Stack 环境。 

6. 登录： 

  *  作为**用户**设备代码中使用 web 浏览器：  

    ```azurecli  
    az login --use-device-code
    ```

    > [!NOTE]  
    >运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

  * 作为服务主体：
        
    准备要用于服务主体登录的 .pem 文件。

      * 在创建主体的客户端计算机上，使用私钥（位于 `cert:\CurrentUser\My` 证书名称与主体名称相同）将服务主体证书导出为 pfx。
  
      * 将 pfx 转换为 pem（使用 OpenSSL 实用工具）。

    登录到 CLI：

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create \
  -n MyResourceGroup -l local
```
如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知问题

在 Azure Stack 中使用 CLI 时存在一些已知的问题：

 - Azure Stack 尚不支持 CLI 交互模式，例如 `az interactive`命令。
 - 若要获取 Azure Stack 中可用的虚拟机映像列表，请使用 `az vm image list --all` 命令，而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应只返回 Azure Stack 环境中可用的映像。
 - Azure 中可用的虚拟机映像别名可能不适用于 Azure Stack。 使用虚拟机映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
- [为 Azure Stack 用户启用 Azure CLI（操作员）](../azure-stack-cli-admin.md)
- [管理用户权限](azure-stack-manage-permissions.md) 
