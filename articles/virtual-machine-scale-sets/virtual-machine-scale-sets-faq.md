---
title: Azure 虚拟机规模集常见问题解答 | Microsoft Docs
description: 获取有关虚拟机规模集常见问题的解答。
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 2b0f463c009d13440f6d3eb2bbbe2315ba7b13f2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895311"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure 虚拟机规模集常见问题解答

获取有关 Azure 虚拟机规模集常见问题的解答。

## <a name="top-frequently-asked-questions-for-scale-sets"></a>有关规模集的热门常见问题解答
**问：** 可在规模集中包含多少个 VM？

**答：** 一个规模集可以包含 0 到 1,000 个基于平台映像的 VM，或者 0 到 300 个基于自定义映像的 VM。 

**问：** 规模集是否支持数据磁盘？

**答：** 是的。 规模集可以定义适用于集中所有 VM 的附加数据磁盘配置。 有关详细信息，请参阅 [Azure scale sets and attached data disks](virtual-machine-scale-sets-attached-disks.md)（Azure 规模集和附加的数据磁盘）。 可用于存储数据的其他选项包括：

* Azure 文件（SMB 共享驱动器）
* OS 驱动器
* 临时驱动器（本地，不是以 Azure 存储为基础）
* Azure 数据服务（例如 Azure 表、Azure Blob）
* 外部数据服务（例如远程数据库）

**问：** 哪些 Azure 区域支持规模集？

**答：** 所有区域都支持规模集。

**问：** 如何使用自定义映像创建规模集？

**答：** 根据自定义映像 VHD 创建托管磁盘，并在规模集模板中引用该磁盘。 [下面是一个示例](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)。

**问：** 如果我将规模集容量从 20 减少到 15，将删除哪些 VM？

**答：** 将从跨更新域和容错域的规模集中均匀地删除虚拟机，以最大限度地提高可用性。 首先删除 ID 最大的 VM。

**问：** 如果将容量从 15 增加到 18，会发生什么情况？

**答：** 如果将容量增加到 18，则创建 3 个新 VM。 每增加容量一次，VM 实例 ID 就会从以前的最高值（例如 20、21、22）递增。 容错域与和更新域中的 VM 是均衡的。

**问：** 在一个规模集中使用多个扩展时，是否可以强制规定执行序列？

**答：** 不能直接强制执行，但对于 customScript 扩展，脚本可以等待另一个扩展来完成操作。 在 [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)（Azure 虚拟机规模集中的扩展序列）博客文章中可以获取有关扩展序列的其他指导。

**问：** 规模集是否适用于 Azure 可用性集？

**答：** 地区（非区域性）规模集使用*放置组*，其中每个放置组可以配置为充当具有五个容错域和五个更新域的隐式可用性集。 VM 多于 100 个的规模集跨多个放置组。 有关放置组的详细信息，请参阅[使用大型虚拟机规模集](virtual-machine-scale-sets-placement-groups.md)。 由 VM 组成的可用性集可以与由 VM 组成的规模集位于相同的虚拟网络中。 常见的配置是将控件节点 VM（经常需要独特的配置）放在可用性集中，将数据节点放在规模集中。

**问：** 规模集是否可以与 Azure 可用性区域配合使用？

**答：** 能！ 有关详细信息，请参阅[规模集区域文档](./virtual-machine-scale-sets-use-availability-zones.md)。


## <a name="autoscale"></a>自动缩放

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure 自动缩放的最佳做法是什么？

有关自动缩放的最佳做法，请参阅[自动缩放虚拟机的最佳做法](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)。

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>在哪里可以找到使用基于主机的指标执行自动缩放时的相关指标名称？

有关使用基于主机的指标执行自动缩放时的相关指标名称，请参阅 [Azure Monitor 支持的指标](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/)。

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>基于 Azure 服务总线主题和队列长度的自动缩放是否有任何示例可供参考？

是的。 有关基于 Azure 服务总线主题和队列长度的自动缩放示例，请参阅 [Azure Monitor 自动缩放常用指标](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)。

对于服务总线队列，请使用以下 JSON：

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

对于存储队列，请使用以下 JSON：

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

将示例值替换为资源的统一资源标识符 (URI)。


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>应该使用基于主机的指标还是使用诊断扩展来执行自动缩放？

可以在 VM 上创建自动缩放设置，以使用主机级指标或基于来宾 OS 的指标。

关于受支持的指标列表，请参阅 [Azure Monitor 自动缩放常用指标](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics)。 

关于虚拟机规模集的完整示例，请参阅[使用虚拟机规模集的 Resource Manager 模板的高级自动缩放配置](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)。 

此示例使用主机级 CPU 指标和消息计数指标。



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>如何对虚拟机规模集设置警报规则？

通过 PowerShell 或 Azure CLI，可以为虚拟机规模集创建指标警报。 有关详细信息，请参阅 [Azure Monitor PowerShell 快速入门示例](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules)和 [Azure Monitor 跨平台 CLI 快速入门示例](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)。

虚拟机规模集的 TargetResourceId 如下所示： 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

可以选择任何 VM 性能计数器作为要对其设置警报的指标。 有关详细信息，请参阅 [Azure Monitor 自动缩放常用指标](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)中的[基于 Resource Manager 的 Windows VM 的来宾 OS 指标](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms)和 [Linux VM 的来宾 OS 指标](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms)。

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>如何使用 PowerShell 对虚拟机规模集设置自动缩放？

若要使用 PowerShell 对虚拟机规模集设置自动缩放，请参阅博客文章 [How to add autoscale to an Azure virtual machine scale set](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/)（如何向 Azure 虚拟机规模集添加自动缩放）。




## <a name="certificates"></a>证书

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>如何安全地将证书传送到 VM？ 如何预配虚拟机规模集来运行网站，并且可以通过证书配置安全传送该网站的 SSL？ （常见的证书轮转操作几乎与配置更新操作相同。）是否有示例说明如何执行此操作？ 

为了将证书安全地传送到 VM，可以将客户证书从客户的密钥保管库直接安装到 Windows 证书存储中。

使用以下 JSON：

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

该代码支持 Windows 和 Linux。

有关详细信息，请参阅[创建或更新虚拟机规模集](https://msdn.microsoft.com/library/mt589035.aspx)。


### <a name="example-of-self-signed-certificate"></a>自签名证书的示例

1.  在密钥保管库中创建自签名证书。

    使用以下 PowerShell 命令：

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    此命令将提供 Azure 资源管理器模板的输入。

    有关如何在密钥保管库中创建自签名证书的示例，请参阅[Service Fabric 群集安全方案](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)。

2.  更改 Resource Manager 模板。

    将此属性作为虚拟机规模集资源的一部分添加到 **virtualMachineProfile**：

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>是否可以通过 Resource Manager 模板指定一个 SSH 密钥对，用于对 Linux 虚拟机规模集进行 SSH 身份验证？  

是的。 用于 **osProfile** 的 REST API 类似于标准 VM REST API。 

在模板中包括 **osProfile**：

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
[101-vm-sshkey GitHub 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json) 中使用了此 JSON 块。
 
[grelayhost.json GitHub 快速启动模板](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json)中也使用了此 OS 配置文件。

有关详细信息，请参阅[创建或更新虚拟机规模集](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)。
  

### <a name="how-do-i-remove-deprecated-certificates"></a>如何删除已弃用的证书？ 

若要删除已弃用的证书，请从保管库证书列表中删除旧证书。 在列表中留下想要在计算机上保留的所有证书。 这不会从所有虚拟机中删除此证书。 也不会向虚拟机规模集中创建的新 VM 添加证书。 

若要从现有 VM 中删除证书，需要编写一个自定义脚本扩展，从证书存储中手动删除证书。
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>如何在预配期间将现有的 SSH 公钥注入到虚拟机规模集 SSH 层？ 我想要将 SSH 公钥值存储在 Azure Key Vault 中，然后在 Resource Manager 模板中使用这些值。

如果仅向 VM 提供 SSH 公钥，则不需要将公钥放在 Key Vault 中。 公钥不是机密。
 
可以在创建 Linux VM 时以明文形式提供 SSH 公钥：

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
linuxConfiguration 元素名称 | 必选 | Type | 说明
--- | --- | --- | --- |  ---
ssh | 否 | 集合 | 指定 Linux OS 的 SSH 密钥配置
路径 | 是 | String | 指定 SSH 密钥或证书应放置到的 Linux 文件路径
keyData | 是 | String | 指定 base64 编码的 SSH 公钥

有关示例，请参阅 [101-vm-sshkey GitHub 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)。

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>添加同一个密钥保管库中的多个证书后，运行 `Update-AzureRmVmss` 时看到以下消息：
 
>Update-AzureRmVmss: 列表机密包含 /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev 的重复实例，这是不允许的。
 
如果尝试重新添加同一保管库，而不是对现有源保管库使用新保管库证书，可能会看到此消息。 如果要添加其他机密，`Add-AzureRmVmssSecret` 命令将无法正常运行。
 
若要添加同一密钥保管库中的其他机密，请更新 $vmss.properties.osProfile.secrets[0].vaultCertificates 列表。
 
关于预期的输入结构，请参阅[创建或更新虚拟机集](https://msdn.microsoft.com/library/azure/mt589035.aspx)。
 
在密钥保管库中的虚拟机规模集对象中找到该机密。 然后，将证书引用（URL 以及机密存储名称）添加到与保管库关联的列表中。

> [!NOTE] 
> 目前，不能通过使用虚拟机规模集 API 删除 VM 中的证书。
>

新的 VM 将不具有旧证书。 但是，具有证书的 VM 和已部署的 VM 将具有旧证书。
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>如果证书位于机密存储中，是否可以在不提供密码的情况下将证书推送到虚拟机规模集？

不需要在脚本中对密码进行硬编码。 可以使用运行部署脚本的权限来动态检索密码。 如果某个脚本可移动机密存储密钥保管库的证书，机密存储 `get certificate` 命令也会输出 pfx 文件的密码。
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>虚拟机规模集的 virtualMachineProfile.osProfile 的 Secrets 属性的工作原理是什么？ 使用 certificateUrl 属性指定证书的绝对 URI 时，为什么需要 sourceVault 值？ 

Windows 远程管理 (WinRM) 证书引用必须在 OS 配置文件的 Secrets 属性中存在。 

指示源保管库的目的在于强制实施访问控制列表 (ACL) 策略，该策略存在于用户的 Azure 云服务模型中。 如果源保管库未指定，无权在密钥保管库中部署或访问机密的用户可以通过计算资源提供程序 (CRP) 实现此目的。 即使是不存在的资源，它们也有 ACL。

如果提供错误的源保管库 ID 但提供有效的密钥保管库 URL，轮询操作时系统会报告错误。
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>如果将机密添加到现有虚拟机规模集，机密会注入到现有 VM 中，还是仅注入到新 VM 中？ 

证书将添加到所有 VM，包括现有的 VM。 如果虚拟机规模集的 upgradePolicy 属性设置为“手动”，对 VM 执行手动更新时，证书将添加到该 VM。
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>在 Linux VM 上，证书放在哪个位置？

要了解如何部署 Linux VM 的证书，请参阅 [Deploy certificates to VMs from a customer-managed key vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)（将证书从客户管理的密钥保管库部署到 VM）。
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>如何将新的保管库证书添加到新的证书对象？

要将保管库证书添加到现有机密，请参阅下面的 PowerShell 示例。 仅使用一个机密对象。
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>如果重置 VM 的映像，证书会发生什么情况？

如果重置 VM 的映像，则会删除证书。 重置映像会删除整个 OS 磁盘。 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>从 Key Vault 中删除证书会发生什么情况？

如果从密钥保管库中删除机密，然后对所有 VM 运行 `stop deallocate`，则再次启动这些 VM 时，将发生失败。 失败发生的原因是 CRP 需要从密钥保管库检索机密，但它无法进行检索。 在这种情况下，可以从虚拟机规模集模型中删除证书。 

CRP 组件不会持久保留客户机密。 如果对虚拟机规模集中的所有虚拟机运行 `stop deallocate`，会删除缓存。 在这种情况下，将从密钥保管库中检索机密。

扩大时不会遇到此问题，因为（单 Fabric 租户模型中的）Azure Service Fabric 中存在机密的缓存副本。
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>为什么必须如 [Service Fabric 群集安全方案](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)中所示，指定证书 URL 的准确位置 (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>)？
 
根据 Azure Key Vault 文档，在未指定版本的情况下，Get Secret REST API 应返回最新版本的机密。
 
方法 | 代码
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

请将 {secret-name} 替换为具体名称，将 {secret-version} 替换为要检索的机密的版本。 机密版本可能被排除。 在这种情况下，将检索当前的版本。
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>使用 Key Vault 时，为什么需要指定证书版本？

Key Vault 要求指定证书版本的目的是为了使用户清楚地了解哪些证书部署在其 VM 上。

如果创建了 VM，并更新了密钥保管库中的机密，则新证书不会下载到 VM。 但是，VM 看上去像是引用了该证书，并且新 VM 将获取新机密。 要避免此问题，需要引用机密的版本。

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>本团队正在开发几个证书，到时以 .cer 公钥的形式分发给大家使用。 将这些证书部署到虚拟机规模集的建议方法有哪些？

要将 .cer 公钥部署到虚拟机规模集，可以生成仅包含 .cer 文件的 .pfx 文件。 为此，请使用 `X509ContentType = Pfx`。 例如，将 .cer 文件作为 x509Certificate2 对象加载到 C# 或 PowerShell 中，然后调用该方法。 

有关详细信息，请参阅 [X509Certificate.Export 方法 (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx))。

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>我没有看到可让用户以 base64 字符串形式传入证书的选项。 其他大多数资源提供程序提供此选项。

若要模拟以 base64 字符串形式传入证书，可以在 Resource Manager 模板中提取最新版本的 URL。 在 Resource Manager 模板中包含以下 JSON 属性：

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>是否需要在密钥保管库中将证书包装在 JSON 对象中？

在虚拟机规模集和 VM 中，必须在 JSON 对象中包装证书。 

我们还支持 application/x-pkcs12 内容类型。 有关使用 application/x-pkcs12 的说明，请参阅 [Azure Key Vault 中的 PFX 证书](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/)。
 
我们目前不支持 .cer 文件。 要使用.cer 文件，请将其导出到.pfx 容器中。



## <a name="compliance-and-security"></a>符合性和安全性

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>虚拟机规模集是否符合 PCI 规范？

虚拟机规模集是 CRP 之上的一个精简 API 层。 这两个组件是 Azure 服务树中的计算平台的一部分。

从合规性角度看，虚拟机规模集是 Azure 计算平台的基础部分。 它们与 CRP 共享团队、工具、进程、部署方法、安全控制、实时 (JIT) 编译、监视、警报等。 虚拟机规模集符合支付卡行业 (PCI) 规范，因为 CRP 属于当前 PCI 数据安全标准 (DSS) 证明的一部分。

有关详细信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/Compliance/PCI)。

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>[Azure 托管服务标识](https://docs.microsoft.com/azure/active-directory/msi-overview)是否适用于虚拟机规模集？

是的。 可在 Azure 快速入门模板中查看一些示例 MSI 模板。 Linux：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux)。 Windows：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows)。


## <a name="extensions"></a>扩展

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>如何删除虚拟机规模集扩展？

若要删除虚拟机规模集扩展，请使用以下 PowerShell 示例：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
可以在 `$vmss` 中找到 extensionName 值。
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>与 Log Analytics 集成的虚拟机规模集模板是否有任何示例可供参考？

有关与 Log Analytics 集成的虚拟机规模集模板示例，请参阅 [Deploy an Azure Service Fabric cluster and enable monitoring by using Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)（部署 Azure Service Fabric 群集，并通过使用 Log Analytics 来启用监视）中的第二个示例。
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>扩展似乎在虚拟机规模集上并行运行。 这导致我的自定义脚本扩展失败。 如何进行修复？

若要了解虚拟机规模集中的扩展排序，请参阅 [Extension sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)（Azure 虚拟机规模集中的扩展排序）。
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>如何对虚拟机规模集中的 VM 重置密码？

更改规模集中 VM 密码的方式主要有两种。

- 直接更改虚拟机规模集模型。 适用于 Compute API 2017-12-01 及更高版本。

    直接更新规模集模型中的管理员凭据（例如，使用 Azure 资源浏览器、PowerShell 或 CLI）。 规模集完成更新后，所有新的 VM 将获得全新凭据。 现有 VM 只有被重置映像时才获得新凭据。 

- 使用 VM 访问扩展重置密码。

    使用以下 PowerShell 示例：
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>如何将扩展添加到虚拟机规模集中的所有 VM？

如果更新策略设置为**自动**，使用新扩展属性重新部署模板可更新所有 VM。

如果更新策略设置为“手动”，先更新扩展，然后手动更新 VM 中的所有实例。

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>如果更新与现有虚拟机规模集关联的扩展，是否会影响现有的 VM？ （即，VM 是否会不匹配虚拟机规模集模型？）或者，是否会忽略 VM？ 如果对现有计算机执行服务修复或重置映像操作，是否会执行当前在虚拟机规模集上配置的脚本，或者，是否会使用最初创建 VM 时配置的脚本？

如果更新虚拟机规模集模型中的扩展定义，且将 upgradePolicy 属性设置为“自动”，则会更新 VM。 如果 upgradePolicy 属性设置为“手动”，扩展会标记为不匹配模型。 

如果对现有 VM 执行服务修复，这种行为类似于重新启动，因此不会重新运行扩展。 如果重置映像，则类似于将 OS 驱动器替换为源映像。 在这种情况下，将运行最新模型中的任何专用设置（如扩展）。
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>如何将虚拟机规模集加入到 Active Directory 域？

若要将虚拟机规模集加入到 Active Directory (AD) 域，可以定义扩展。 

若要定义扩展，请使用 JsonADDomainExtension 属性：

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>虚拟机规模集扩展尝试安装需要重新启动的内容。 例如，"commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

如果虚拟机规模集扩展尝试安装需要重新启动的内容，可以使用 Azure 自动化所需状态配置 (Automation DSC) 扩展。 如果操作系统为 Windows Server 2012 R2，Azure 将拉入 Windows Management Framework (WMF) 5.0 安装程序，重新启动，然后继续使用该配置。 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>如何在虚拟机规模集中启用反恶意软件？

若要在虚拟机规模集中启用反恶意软件，请使用以下 PowerShell 示例：

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>我需要执行一个在专用存储帐户中托管的自定义脚本。 存储为公共存储时脚本成功运行，但尝试使用共享访问签名 (SAS) 时，脚本运行失败。 显示此消息：“缺少有效共享访问签名的强制参数”。 通过本地浏览器可以正常使用“链接+SAS”。

若要执行在私有存储帐户中托管的自定义脚本，请通过存储帐户密钥和名称来设置受保护的设置。 有关详细信息，请参阅[适用于 Windows 的自定义脚本扩展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)。


## <a name="networking"></a>网络
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>是否可以将网络安全组 (NSG) 分配给规模集，以便将其应用于集中的所有 VM NIC？

是的。 可通过在网络配置文件的 networkInterfaceConfigurations 部分中引用网络安全组，将其直接应用于规模集。 示例：

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>如何针对同一订阅和同一区域中的虚拟机规模集执行 VIP 交换？

如果有两个包含 Azure 负载均衡器前端的虚拟机规模集，并且它们处于同一订阅和区域中，可以解除分配它们的公共 IP 地址，并将公共 IP 地址分配给其他资源。 有关示例，请参阅 [VIP 交换：Azure 资源管理器中的蓝绿部署](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/)。 但这确实意味着延迟，因为要在网络一级解除分配/分配资源。 更快的做法是将 Azure 应用程序网关与两个后端池和路由规则结合使用。 也可以使用支持快速切换暂存槽和生产槽的 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)托管应用程序。
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>如何为静态专用 IP 地址分配指定专用 IP 地址范围？

IP 地址是从指定的子网中选择的。 

虚拟机规模集 IP 地址的分配方法始终为“动态”，但这并不意味着可以更改这些 IP 地址。 在这种情况下，“动态”仅意味不在 PUT 请求中指定 IP 地址。 使用子网设置静态集。 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>如何将虚拟机规模集部署到现有的 Azure 虚拟网络？ 

要将虚拟机规模集部署到现有的 Azure 虚拟网络，请参阅[将虚拟机规模集部署到现有的 Azure 虚拟网络](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)。 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>如何将虚拟机规模集中第一个 VM 的 IP 地址添加到模板的输出中？

要将虚拟机规模集中第一个 VM 的 IP 地址添加到模板的输出中，请参阅 [Azure 资源管理器：获取虚拟机规模集的专用 IP](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips)。

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>能否将规模集与加速网络结合使用？

是的。 若要使用加速网络，请在规模集的 networkInterfaceConfigurations 设置中将 enableAcceleratedNetworking 设置为 true。 例如
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>如何才能配置规模集使用的 DNS 服务器？

若要创建具有自定义 DNS 配置的虚拟机规模集，请将 dnsSettings JSON 数据包添加到规模集的 networkInterfaceConfigurations 部分中。 示例：
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>如何才能将规模集配置为向每个 VM 分配公共 IP 地址？

要创建向每个 VM 分配公共 IP 地址的虚拟机规模集，请确保 Microsoft.Compute/virtualMachineScaleSets 资源的 API 版本为 2017-03-30，并将 publicipaddressconfiguration JSON 数据包添加到规模集的 ipConfigurations 部分中。 示例：

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>能否配置与多个应用程序网关配合使用的规模集？

是的。 可以将多个应用程序网关后端地址池的资源 ID 添加到规模集网络配置文件的 ipConfigurations 部分的 applicationGatewayBackendAddressPools 列表中。

## <a name="scale"></a>缩放

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>在哪些情况下我会创建包含少于两个 VM 的虚拟机规模集？

创建包含少于两个 VM 的虚拟机规模集的原因之一为需要使用虚拟机规模集的弹性属性。 例如，可以部署不包含任何 VM 的虚拟机规模集来定义基础结构，这样就无需支付 VM 运行费。 然后，在准备好部署 VM 后，将虚拟机规模集的“容量”提高到生产实例计数。

创建包含少于两个 VM 的虚拟机规模集的另一个原因可能是，相比于使用离散 VM 的可用性集，不必担心可用性的问题。 此外，可以借助虚拟机规模集来使用可替代的无差别计算单元。 这种均一性是虚拟机规模集与可用性集的一项重要差别。 许多无状态工作负载不跟踪单个单元。 如果工作负载下降，可以减少到一个计算单元，如果工作负载上升，可以增加到多个计算单元。

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>如何更改虚拟机规模集中的 VM 数目？

若要在 Azure 门户中更改虚拟机规模集中的 VM 数，请从“虚拟机规模集属性”部分，单击“缩放”边栏选项卡，并使用滚动条。 有关更改实例计数的其他方法，请参阅[更改虚拟机规模集的实例计数](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/)。

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>如何定义达到特定阈值时触发的自定义警报？

可以在一定程度上灵活处理指定阈值的警报。 例如，可以定义自定义的 webhook。 以下 webhook 示例取自 Resource Manager 模板：

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

在此示例中，达到阈值时，警报将转到 Pagerduty.com。



## <a name="patching-and-operations"></a>修补和操作

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>如何在现有资源组中创建规模集？

尚不能通过 Azure 门户在现有资源组中创建规模集，但从 Azure 资源管理器模板部署规模集时，可以指定现有资源组。 也可以在使用 Azure PowerShell 或 CLI 创建规模集时指定现有资源组。

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>可以将规模集移至另一个资源组吗？

可以，可以将规模集资源移至新的订阅或资源组。

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>如何将虚拟机规模集更新为新映像？ 如何管理修补？

要将虚拟机规模集更新为新映像，或管理修补，请参阅[升级虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)。

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>是否可以在不更改映像的情况下，使用重置映像操作来重置 VM？ （也就是说，将 VM 重置为出厂设置而不是重置为新映像）

可以在不更改映像的情况下，使用重置映像操作来重置 VM。 但是，如果虚拟机规模集使用 `version = latest` 引用了平台映像，则调用 `reimage` 时，VM 可以更新为更高版本的 OS 映像。

有关详细信息，请参阅[管理虚拟机规模集中的所有 VM](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set)。

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>是否可以将规模集与 Azure Log Analytics 集成？

可以，可在规模集 VM 上安装 Log Analytics 扩展。 Azure CLI 示例如下：
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
可在 OMS 门户中查找所需的 workspaceId 和 workspaceKey。 在“概述”页面上，单击“设置”磁贴。 单击顶部的“相连的源”选项卡。

注意：如果规模集 _upgradePolicy_ 设置为“手动”，则需要通过对 VM 调用升级将扩展应用到集中的所有 VM。 在 CLI 中，这将为“az vmss update-instances”。

## <a name="troubleshooting"></a>故障排除

### <a name="how-do-i-turn-on-boot-diagnostics"></a>如何启用启动诊断？

若要启用启动诊断，首先，创建存储帐户。 然后，将此 JSON 块放在虚拟机规模集 **virtualMachineProfile** 中，并更新虚拟机规模集：

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

创建新 VM 时，VM 的 InstanceView 属性会显示屏幕截图的详细信息等等。 下面是一个示例：
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>虚拟机属性

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>如何在不发出多个调用的情况下获取每个 VM 的属性信息？ 例如，对于虚拟机规模集中的 100 个 VM，如何获取每个 VM 的容错域？

若要在不发出多个调用的情况下获取每个 VM 的属性信息，通过在以下资源 URI 上执行 REST API `GET`调用 `ListVMInstanceViews`：

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>是否可将不同的扩展参数传递给虚拟机规模集中的不同 VM？

不可以将不同的扩展参数传递给虚拟机规模集中的不同 VM。 但是，扩展可以根据运行它们的 VM 的唯一属性（例如计算机名）运行。 扩展还可以在 http://169.254.169.254 上查询实例元数据来获取更多关于 VM 的信息。

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>虚拟机规模集 VM 计算机名和 VM ID 为什么不是连续的？ 例如：0，1，3...

虚拟机规模集 VM 计算机名和 VM ID 不连续是因为虚拟机规模集的 **overprovision** 属性设置为默认值 **true**。 如果 overprovision 设置为 **true**，创建的 VM 数量将超过请求数量。 然后，将删除多余的 VM。 在这种情况下，虽然部署可靠性得到提高，但代价是无法遵守连续命名和连续网络地址转换 (NAT) 规则。 

可以将此属性设置为 **false**。 对于小型虚拟机规模集，不会显著影响部署可靠性。

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>删除虚拟机规模集中的 VM 与解除分配 VM 有什么区别？ 如何在这两种做法之间做出选择？

删除虚拟机规模集中的 VM 与解除分配 VM 的主要区别在于 `deallocate` 不会删除虚拟硬盘 (VHD)。 运行 `stop deallocate` 会产生存储费用。 基于以下某种原因，可能会采用其中一种做法：

- 不再想要支付计算费用，但要保留 VM 的磁盘状态。
- 想要更快速地启动一组 VM，而不是扩大虚拟机规模集。
  - 出于这种方案，可能创建了自己的自动缩放引擎，并希望以更快的速度完成端到端缩放。
- 虚拟机规模集未均匀分布在容错域或更新域。 这可能是由于有选择地删除了 VM，或者因为过度预配后，VM 被删除。 在虚拟机规模集上先运行 `stop deallocate`，然后运行 `start`，可以将 VM 均匀地分布到容错域或更新域。

