---
title: 适用于 Linux 的 Azure Key Vault VM 扩展 |Microsoft Docs
description: 部署代理使用虚拟机扩展在虚拟机上自动刷新 Key Vault 证书。
services: virtual-machines-linux
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 60d5b8197e142306a51922ce0e042ed2463457d6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301225"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>适用于 Linux 的虚拟机扩展 Key Vault

Key Vault VM 扩展可自动刷新 Azure 密钥保管库中存储的证书。 具体而言，该扩展会监视存储在密钥保管库中的已观察证书的列表。  检测到更改后，扩展会检索并安装相应的证书。 当前在 Linux Vm 上发布并支持 Key Vault VM 扩展。 本文档详细介绍适用于 Linux 的 Key Vault VM 扩展的支持平台、配置和部署选项。 

### <a name="operating-system"></a>操作系统

Key Vault VM 扩展支持以下 Linux 分发版：

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Key Vault VM 代理扩展的架构。 该扩展不需要受保护的设置 - 其所有设置都被视为没有安全影响的信息。 该扩展需要受监视的密钥列表、轮询频率和目标证书存储。 尤其是在下列情况下：  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Not available on Linux e.g.: false>,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "requireInitialSync": <initial synchronization of certificates e..g: true>,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```

> [!NOTE]
> 观察到的证书 Url 的格式应为 `https://myVaultName.vault.azure.net/secrets/myCertName`。
> 
> 这是因为 `/secrets` 路径返回的是完整证书，包括私钥，而 @no__t 1 路径则不返回。 有关证书的详细信息可在此处找到：[Key Vault 证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | string |
| type | KeyVaultForLinux | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 字符串数组


## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 部署需要部署后刷新证书的一个或多个虚拟机时，模板是理想选择。 此扩展可以部署到单个 Vm 或虚拟机规模集。 架构和配置对于这两种模板类型通用。 

虚拟机扩展的 JSON 配置必须嵌套在模板的虚拟机资源片段中，具体而言，@no__t 为虚拟机模板使用-0 对象，在 @no__t 1 对象下的虚拟机规模集。

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署

可以使用 Azure PowerShell，将 Key Vault VM 扩展部署到现有虚拟机或虚拟机规模集。 

* 在 VM 上部署该扩展：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 在虚拟机规模集上部署扩展：

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可用于将 Key Vault VM 扩展部署到现有的虚拟机或虚拟机规模集。 
 
* 在 VM 上部署该扩展：
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 在虚拟机规模集上部署扩展：

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

请注意以下限制/要求：
- Key Vault 限制：
    - 必须在部署时存在 
    - 使用 MSI 为 VM/VMSS 标识设置 Key Vault 访问策略


## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure PowerShell 运行以下命令。

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
