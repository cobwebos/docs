---
title: 通过 Azure CLI 和 Python 在 Azure Stack Edge Pro 设备 GPU 上部署 VM
description: 介绍如何使用 Azure CLI 和 Python 在 Azure Stack Edge Pro GPU 设备上创建和管理虚拟机 (VM)。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: c27f6ef47b8e4db83ceb63e308e318803800f8a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890714"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>使用 Azure CLI 和 Python 在 Azure Stack Edge Pro GPU 设备上部署 VM

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

本教程介绍如何使用 Azure 命令行接口 (CLI) 和 Python 在 Azure Stack Edge Pro 设备上创建和管理 VM。

## <a name="vm-deployment-workflow"></a>VM 部署工作流

下图演示了此部署工作流。

![VM 部署工作流](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

下面是此部署工作流的基本摘要信息：

1. 连接到 Azure 资源管理器
2. 创建资源组
3. 创建存储帐户
4. 将 blob URI 添加到 hosts 文件
5. 安装证书
6. 上传 VHD
7. 基于 VHD 创建托管磁盘
8. 基于映像托管磁盘创建 VM 映像
9. 基于之前创建的资源创建 VM
10. 创建 VNet
11. 使用 VNet 子网 ID 创建 VNIC

有关工作流图表的详细说明，请参阅[使用 Azure PowerShell 在 Azure Stack Edge Pro 设备上部署 VM](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。 有关如何连接到 Azure 资源管理器的信息，请参阅[使用 Azure PowerShell 连接到 Azure 资源管理器](azure-stack-edge-j-series-connect-resource-manager.md)。

## <a name="prerequisites"></a>必备条件

在开始使用 Azure CLI 和 Python 在 Azure Stack Edge Pro 设备上创建和管理 VM 之前，你需要确保已完成以下步骤中列出的先决条件：

1. 已完成 Azure Stack Edge Pro 设备上的网络设置，如[步骤 1：配置 Azure Stack Edge Pro 设备](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)中所述。

2. 已启用计算网络接口。 此网络接口 IP 用于为 VM 部署创建虚拟交换机。 下列步骤将为你演示此过程：

    1. 转到“计算”。 选择将用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    2. 在网络接口上启用计算。 Azure Stack Edge Pro 创建并管理与该网络接口相对应的虚拟交换机。

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. 你已在 Azure Stack Edge Pro 设备和客户端的受信任存储中创建并安装了所有证书。 按照[步骤 2：创建和安装证书](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)中所述过程操作。

4. 你已为 Azure Stack Edge Pro 设备创建了 Base-64 编码的 .cer 证书（PEM 格式）。 此证书已作为签名链上传到设备，并安装到了客户端上受信任的根存储中。 还需要 pem 格式的此证书，这样 Python 才能在此客户端上工作。

    使用 `certutil` 命令将此证书转换为 pem 格式。 必须在包含证书的目录中运行此命令。

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    下面显示了命令用法的示例：

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    稍后，你还会将此 pem 添加到 Python 存储。

5. 你已在设备的本地 Web UI 的“网络”页中分配了设备 IP。 需要将此 IP 添加到：

    - 客户端上的主机文件，或
    - DNS 服务器配置
    
    > [!IMPORTANT]
    > 建议你修改终结点名称解析的 DNS 服务器配置。

    1. 以管理员身份启动“记事本”（保存文件需要管理员权限），然后打开位于 `C:\Windows\System32\Drivers\etc` 的 hosts 文件 。
    
        ![Windows 资源管理器 hosts 文件](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. 将以下条目添加到 hosts 文件，替换为设备的适当值：
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. 使用下图作为参考。 保存 **hosts** 文件。

        ![记事本中的 hosts 文件](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [下载此过程中使用的 Python 脚本](https://aka.ms/ase-vm-python)。

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>步骤 1：在客户端上设置 Azure CLI/Python

### <a name="verify-profile-and-install-azure-cli"></a>验证配置文件并安装 Azure CLI

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. 在客户端上安装 Azure CLI。 此示例已安装 Azure CLI 2.0.80。 若要验证 Azure CLI 的版本，请运行 `az --version` 命令。

    下面是上述命令的输出示例：

    ```powershell
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    如果没有 Azure CLI，请下载并[在 Windows 上安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。 可以使用 Windows 命令提示符或通过 Windows PowerShell 运行 Azure CLI。

2. 记下 CLI 的 Python 位置。 需要使用该位置确定 Azure CLI 的受信任的根证书存储的位置。

3. 若要运行本文中使用的示例脚本，你将需要以下 Python 库版本：

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    若要安装版本，请运行以下命令：

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    以下示例输出显示了 Haikunator 的安装：

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    以下示例输出显示了 `msrestazure` 的 pip 安装： 
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>信任 Azure Stack Edge Pro CA 根证书

1. 在计算机上找到证书位置。 该位置可能因安装 `az cli` 的位置而异。 以管理员身份运行 Windows PowerShell。 切换到指向 `az cli` 安装 Python 的位置的路径：`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`。

    若要获取证书位置，请键入以下命令：

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    cmdlet 返回证书位置，如下所示：  
        
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    记下该位置，因为稍后会用到 - `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. 信任 Azure Stack Edge Pro CA 根证书，方法是将其追加到现有 Python 证书。 需要提供指向之前保存 PEM 证书的位置的路径。

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    示例路径为“C:\VM-scripts\rootteam3device.pem”
    
    然后在 Windows PowerShell 中键入以下一系列命令：

    ```powershell
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
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>连接到 Azure Stack Edge Pro

1. 通过运行 `az cloud register` 命令注册 Azure Stack Edge Pro 环境。

    在某些情况下，直接出站 Internet 连接通过代理或防火墙进行路由，从而强制进行 SSL 拦截。 在这些情况下，az cloud register 命令可能会失败并出现错误，例如\"无法从云中获取终结点。\"若要解决此错误，请在 Windows PowerShell 中设置以下环境变量：

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. 为 Azure 资源管理器终结点的脚本设置环境变量、创建资源的位置以及指向源 VHD 所在位置的路径。 资源的位置在所有 Azure Stack Edge Pro 设备上都是固定的，并且设置为 `dbelocal`。 还需要指定地址前缀和专用 IP 地址。 以下所有环境变量的值都取决于你的值，但 `AZURE_RESOURCE_LOCATION` 除外，它应该硬编码为 `"dbelocal"`。

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. 注册环境。 在运行 az cloud register 时使用以下参数：

    | 值 | 说明 | 示例 |
    | --- | --- | --- |
    | 环境名称 | 要尝试连接到的环境的名称 | 提供一个名称，例如 `aze-environ` |
    | 资源管理器终结点 | 该 URL 为 `https://Management.<appliancename><dnsdomain>`。 <br> 若要获取此 URL，请转到设备的本地 Web UI 中的“设备”页。 |例如，`https://management.team3device.teatraining1.com`。  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    下面显示了上述命令的用法示例：
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. 使用以下命令设置活动环境：

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    下面显示了上述命令的用法示例：

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. 使用 `az login` 命令登录到 Azure Stack Edge Pro 环境。 你可以以用户或[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)身份登录到 Azure Stack Edge Pro 环境。

   若要以用户身份登录，请按照以下步骤操作：

   可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果你的帐户已启用多重身份验证，则必须采用后者。

   下面显示了 `az login` 用法的示例：
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   使用 login 命令后，系统将提示你输入密码。 提供 Azure 资源管理器密码。

   下面显示了提供密码后成功登录的输出示例：  
   
   ```powershell
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   请记下 `id` 和 `tenantId` 值，因为这些值分别对应于 Azure 资源管理器订阅 ID 和 Azure 资源管理器租户 ID，后面的步骤中将用到它们。
       
   以下环境变量需要设置为作为服务主体运行：

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Azure 资源管理器客户端 ID 是硬编码的。 Azure 资源管理器租户 ID 和 Azure 资源管理器订阅 ID 均存在于你先前运行的 `az login` 命令的输出中。 Azure 资源管理器客户端密码是你设置的 Azure 资源管理器密码。

   有关详细信息，请参阅 [Azure 资源管理器密码](azure-stack-edge-j-series-set-azure-resource-manager-password.md)。

5. 将配置文件更改为 2019-03-01 混合版本。 若要更改配置文件版本，请运行以下命令：

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    下面显示了 `az cloud update` 用法的示例：

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>步骤 2：创建 VM

系统将向你提供用于创建 VM 的 Python 脚本。 根据你是以用户身份登录还是设置为服务主体，该脚本会相应地获取输入并创建 VM。

1. 从安装了 Python 的同一目录运行该 Python 脚本。

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. 运行脚本时，上传 VHD 需要 20-30 分钟。 若要查看上传操作的进度，可以使用 Azure 存储资源管理器或 AzCopy。

    下面是成功运行脚本的输出示例。 该脚本将在一个资源组中创建所有资源，使用这些资源来创建 VM，最后删除该资源组，包括它创建的所有资源。

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>后续步骤

[Linux 虚拟机的常见 Az CLI 命令](../virtual-machines/linux/cli-manage.md)