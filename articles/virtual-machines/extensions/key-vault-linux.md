---
title: 适用于 Linux 的 Azure Key Vault VM 扩展
description: 部署一个代理，该代理使用虚拟机扩展在虚拟机上执行 Key Vault 证书自动刷新操作。
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: e653adfd7a148cea7bfb1ecfdbbf386eff0c3e86
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723317"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>适用于 Linux 的 Key Vault 虚拟机扩展

通过 Key Vault 虚拟机 (VM) 扩展，可自动刷新 Azure Key Vault 上存储的证书。 具体来说，该扩展会监视 Key Vault 中存储的一列已观察到的证书。  检测到出现更改后，该扩展会检索并安装相应的证书。 Key Vault VM 扩展由 Microsoft 发布并支持，目前在 Linux VM 上提供。 本文档详细介绍适用于 Linux 的 Key Vault VM 扩展支持的平台、配置和部署选项。 

### <a name="operating-system"></a>操作系统

Key Vault VM 扩展支持以下 Linux 发行版：

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>支持的证书内容类型

- PKCS #12
- PEM

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 Key Vault VM 代理扩展的架构。 该扩展不需要受保护的设置 - 其所有设置都被视为没有安全影响的信息。 该扩展需要受监视的密钥列表、轮询频率和目标证书存储。 具体来说：  
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
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
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
> 你观察到的证书 URL 应采用 `https://myVaultName.vault.azure.net/secrets/myCertName` 格式。
> 
> 这是因为 `/secrets` 路径会返回完整证书（包括私钥），但 `/certificates` 路径不会。 有关证书的详细信息，可参阅此处：[Key Vault 证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| 发布者 | Microsoft.Azure.KeyVault | 字符串 |
| type | KeyVaultForLinux | 字符串 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 字符串 |
| certificateStoreName | MY | 字符串 |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine | 字符串 |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 字符串数组


## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 部署需要部署后刷新证书的一个或多个虚拟机时，模板是理想选择。 可将该扩展部署到单个 VM 或虚拟机规模集。 架构和配置对于这两种模板类型通用。 

必须将虚拟机扩展的 JSON 配置嵌套在该模板的虚拟机资源片段中，具体来说是嵌套在虚拟机模板的 `"resources": []` 对象，如果是虚拟机规模集，则嵌套在 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 对象下。

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
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
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
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 若要在虚拟机规模集上部署扩展：

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可使用Azure CLI 将 Key Vault VM 扩展部署到现有虚拟机或虚拟机规模集。 
 
* 在 VM 上部署该扩展：
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 若要在虚拟机规模集上部署扩展：

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
  - 必须使用托管标识为 VM/VMSS 标识设置 Key Vault 访问策略。 请参阅如何[使用托管标识提供 Key Vault 身份验证](../../key-vault/managed-identity.md)


## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

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
