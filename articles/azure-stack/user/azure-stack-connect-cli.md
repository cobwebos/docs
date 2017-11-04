---
title: "连接到 Azure CLI 堆栈 |Microsoft 文档"
description: "了解如何使用跨平台命令行界面 (CLI) 来管理和部署 Azure 堆栈上的资源"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: 5ef64e727615d17ae550efbc7ea427936d7d4c3b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>安装和配置 CLI 用于 Azure 堆栈

在本文中，我们指导你完成使用 Azure 命令行界面 (CLI) 来管理 Azure 堆栈开发工具包资源从 Linux 和 Mac 客户端平台的过程。 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure 堆栈 CA 根证书导出

如果你使用 CLI 从在 Azure 堆栈开发工具包环境内运行的虚拟机，Azure 堆栈根证书以便可以直接检索它已安装在虚拟机中。 如果从外部开发工具包工作站使用 CLI，你必须从开发工具包导出 Azure 堆栈 CA 根证书并将其添加到你的开发工作站 （外部 Linux 或 Mac 平台） 的 Python 证书存储。 

若要导出 PEM 格式中的 Azure 堆栈根证书，请登录到你的开发工具包，并运行以下脚本：

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Certificate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>安装 CLI

接下来，登录到你的开发工作站和安装 CLI。 Azure 堆栈需要 2.0 版的 Azure CLI。 你可以通过使用中所述的步骤安装它，[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)文章。 若要验证安装是否成功，请打开终端或命令提示符窗口并运行以下命令：

```azurecli
az --version
```

你应看到 Azure CLI 和在计算机安装其他依赖库的版本。

## <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure 堆栈 CA 根证书

若要信任 Azure 堆栈 CA 根证书，请将其追加到现有的 Python 证书。 如果你从在 Azure 堆栈环境内创建的 Linux 计算机中运行 CLI，运行以下 bash 命令：

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

如果你从 Azure Sack 环境外的计算机运行 CLI，你必须先设置[VPN 连接到 Azure 堆栈](azure-stack-connect-azure-stack.md)。 现在将复制到你的开发工作站上前面导出的 PEM 证书，并运行以下命令，具体取决于你开发工作站的 OS。

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

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
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>设置虚拟机别名终结点

用户可以使用 CLI 创建虚拟机之前，应设置可公开访问的终结点包含虚拟机映像别名的云管理员联系并将其与云注册此终结点。 `endpoint-vm-image-alias-doc`中的参数`az cloud register`命令用于此目的。 云管理员必须将映像下载到 Azure 堆栈应用商店之前它们将其添加到映像别名终结点。
   
例如，Azure 使用以下 URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json。 云管理员应该类似的终结点用于 Azure 堆栈使用设置 Azure 堆栈应用商店中可用的映像。

## <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下步骤连接到 Azure 堆栈：

1. 通过运行来注册你的 Azure 堆栈环境`az cloud register`命令。
   
   a. 若要注册*云管理*环境中，使用：

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. 若要注册*用户*环境中，使用：

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. 通过使用以下命令设置活动的环境。

   a. 有关*云管理*环境中，使用：

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. 有关*用户*环境中，使用：

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. 更新你的环境配置为使用 Azure 堆栈的特定 API 版本配置文件。 若要更新的配置，运行以下命令：

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. 通过登录到你的 Azure 堆栈环境`az login`命令。 你可以登录到 Azure 堆栈环境以用户或作为[服务主体](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)。 

   * 以登录*用户*： 你可以指定的用户名和密码直接内的`az login`命令或通过使用浏览器进行身份验证。 您需要做后者，如果你的帐户已启用多因素身份验证。

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > 如果你的用户帐户已启用多因素身份验证，则可以使用`az login command`而无需提供`-u`参数。 运行命令提供一个 URL，必须使用进行身份验证的代码。
   
   * 以登录*服务主体*： 在登录之前[创建服务主体通过 Azure 门户](azure-stack-create-service-principals.md)或 CLI 并将其分配一个角色。 现在，通过使用以下命令登录：

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>测试连接

现在，我们具有所有内容安装，让我们使用 CLI 创建 Azure 堆栈中的资源。 例如，你可以创建应用程序的资源组并添加虚拟机。 使用以下命令创建名为"MyResourceGroup"的资源组：

```azurecli
az group create \
  -n MyResourceGroup -l local
```

如果成功创建资源组前, 一个命令将输出新创建的资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)

[管理用户权限](azure-stack-manage-permissions.md)

