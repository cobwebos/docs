---
title: 创建群集错误字典
description: 了解如何创建群集错误字典。
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483058"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight：群集创建错误

本文介绍创建群集时遇到的错误的解决方法。 

> [!NOTE]
> 由于 HDInsight 产品使用 CsmDocument_2_0 类时出现验证错误，下面的列表中的前三个错误发生。



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**错误**：无法访问脚本操作位置 URI：\<脚本操作 URL\>

**错误消息： "远程服务器返回了一个错误：（404）找不到"。**

### <a name="cause"></a>原因
无法从 HDInsight 服务访问作为创建群集请求的一部分提供的脚本操作 URL。 我们在尝试访问脚本操作时收到 "ErrorMessage"。

### <a name="resolution"></a>分辨率 
  - 对于 http/https URL，可以通过尝试从 incognito 浏览器窗口打开 URL 进行验证。 
  - 对于 WASB URL，请确保该脚本位于请求中提供的存储帐户中。 请确保此存储帐户的存储密钥准确无误。 
  - 对于 ADLS URL，请确保该脚本存在于存储帐户中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**错误**：无法访问脚本操作位置 URI： \<脚本操作 URL\>

**错误消息：给定的脚本 URI \<脚本 URI\> 在 ADLS 中，但此群集没有 data lake 存储主体**

### <a name="cause"></a>原因
无法从 HDInsight 服务访问作为创建群集请求的一部分提供的脚本操作 URL。 我们在尝试访问脚本操作时收到 "ErrorMessage"。 

### <a name="resolution"></a>分辨率

请确保将相应的 Azure Data Lake Store 第1代帐户添加到群集。 用于访问 Azure Data Lake Store 第1代帐户的服务主体也添加到群集中。 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**错误**：请求中提供的 vm 大小 "\<客户指定的 vm 大小\>" 无效或不支持角色 "\<role\>"。 有效值为： \<ROLE\>的有效 VM 大小。

### <a name="cause"></a>原因
角色不允许使用客户指定的 VM 大小。 这可能是真的，因为 VM 大小值未按预期工作，或者不适合计算机角色。 

### <a name="resolution"></a>分辨率
错误消息将列出 VM 大小的有效值。 选择其中一个有效值，然后重试创建群集请求。 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>错误代码： InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**错误**： VirtualNetworkId 无效。 VirtualNetworkId "\<USER_VIRTUALNETWORKID\>" *

### <a name="cause"></a>原因
在群集创建过程中指定的**VirtualNetworkId**值的格式不正确。 

### <a name="resolution"></a>分辨率
请确保**VirtualNetworkId**和子网的格式正确。 若要获取**VirtualnetworkId**值，请在 Azure 门户中，选择你的虚拟网络，然后在菜单上选择 "**属性**"。 **ResourceID**属性是**VirtualNetworkId**值。 

虚拟网络 ID 的示例如下： 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>错误代码： CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**错误**：群集部署因自定义脚本操作中的错误而失败。 失败的操作： \<SCRIPT_NAME\>，请参阅 Ambari UI 以进一步调试失败。

### <a name="cause"></a>原因
成功部署群集后，将执行创建群集请求过程中提供的用户自定义脚本。 此错误代码指示在执行此带有名称 \<SCRIPT_NAME\>的自定义脚本时遇到错误。   

### <a name="resolution"></a>分辨率
因为这是用户的自定义脚本，所以用户应该对问题进行故障排除，并根据需要重新运行脚本。 若要对脚本失败进行故障排除，请检查/var/lib/ambari-agent/* 文件夹中的日志。 或者，打开 Ambari UI 中的 "操作" 页，然后选择**run_customscriptionaction**操作以查看错误详细信息。 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>错误代码： InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**错误**：数据库 METASTORE_MAJOR_VERSION\> \<中 \<META_STORE_TYPE\> 元存储架构版本 \<DATABASE_NAME\> 与群集版本 \<CLUSTER_VERSION 不兼容\>

### <a name="cause"></a>原因
自定义元存储与所选 HDInsight 群集版本不兼容。 目前，HDInsight 4.0 群集仅支持元存储版本3。*x*和 HDInsight 3.6 不支持元存储版本3。*x*或更高版本。 

### <a name="resolution"></a>分辨率
请确保仅使用每个 HDInsight 群集版本支持的元存储版本。 请注意，如果未指定自定义元存储，HDInsight 将在内部创建元存储。 但是，在删除群集时，将自动删除此元存储。 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码： FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**错误**：无法连接到群集管理终结点以执行缩放操作。 验证网络安全规则未阻止对群集的外部访问，并且群集管理器（Ambari） UI 可以成功访问。

### <a name="cause"></a>原因
你的网络安全组（NSG）上有防火墙规则，该规则阻止了与关键 Azure 运行状况和管理服务的群集通信。 

### <a name="resolution"></a>分辨率
如果你计划使用 **网络安全组** 来控制网络流量，请在安装 HDInsight 之前执行以下操作： 
  - 标识你计划用于 HDInsight 的 Azure 区域。 
  - 标识 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。 
    - 为计划安装 HDInsight 的子网创建或修改网络安全组。 
    - **网络安全组：** 允许来自 IP 地址的端口 **443** 上的 **入站** 流量。 这可以确保 HDInsight 管理服务可以从虚拟网络外部访问群集。 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>错误代码： StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**错误**：托管标识没有存储帐户的权限。 请验证是否已将 "存储 Blob 数据所有者" 角色分配给存储帐户的托管标识。 存储：/subscriptions/\<订阅 ID\>/resourceGroups/\< 资源组名称\>/providers/Microsoft.Storage/storageAccounts/\<存储帐户名称\>、托管标识：/subscriptions/\<订阅 ID\>/resourceGroups//\< 资源组名称\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<用户托管标识名称\>

### <a name="cause"></a>原因
未提供所需的权限来**管理标识。** **用户分配的托管标识**在 ADLS Gen2 存储帐户上没有 Blob 存储参与者角色。 

### <a name="resolution"></a>分辨率

打开 Azure 门户，中转到你的存储帐户，在 "**访问控制（IAM）** " 下查看，并确保 "存储 Blob 数据参与者" 或 "存储 Blob 数据所有者" 角色具有 "分配" 权限，可访问该订阅的 **用户分配的托管标识**。 有关详细信息，请参阅在[Data Lake Storage Gen2 帐户上设置托管标识的权限](hdinsight-hadoop-use-data-lake-storage-gen2.md)。 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>错误代码： InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**错误**：网络安全组中的安全规则/Subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> 默认/提供程序/networkSecurityGroups/\<网络安全组名称\> 配置了子网/Subscriptions/\<SubscriptionID >\/resourceGroups/\<资源组名称 > RG-Westeurope/tomtom/\/虚拟网络名称 >\/子网/\<子网名称\> 
不允许所需的入站和/或出站连接。 有关详细信息，请访问为[Azure HDInsight 计划虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)，或联系支持人员。 * *

### <a name="cause"></a>原因
如果使用网络安全组（Nsg）或用户定义的路由（Udr）来控制到 HDInsight 群集的入站流量，则必须确保群集可以与关键 Azure 运行状况和管理服务通信。

### <a name="resolution"></a>分辨率
如果你计划使用 **网络安全组** 来控制网络流量，请在安装 HDInsight 之前执行以下操作： 
  - 标识你计划用于 HDInsight 的 Azure 区域，并为你所在区域的 IP 地址创建一个安全列表：[运行状况和管理服务：特定区域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
  - 确定 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。 
  - 为计划安装 HDInsight 的子网创建或修改网络安全组。 **网络安全组**：在端口 **443**上允许来自 IP 地址的入站流量 。 这将确保 HDInsight 管理服务可以从虚拟网络外部访问群集。
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>错误代码：群集安装程序无法在一个或多个主机上安装组件

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**错误**：群集安装程序无法在一个或多个主机上安装组件。 请重试请求。 

### <a name="cause"></a>原因 
通常，在出现暂时性问题或发生 Azure 中断时，会生成此错误 

### <a name="resolution"></a>分辨率

查看[azure 状态](https://status.azure.com/status)页，了解可能会影响群集部署的任何可能的 azure 中断。 如果没有任何中断，请重试群集部署。 

## <a name="next-steps"></a>后续步骤

若要深入了解如何排查群集创建问题，请参阅[排查 Azure HDInsight 的群集创建失败](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)问题。
