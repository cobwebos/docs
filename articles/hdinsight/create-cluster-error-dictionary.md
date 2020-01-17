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
ms.openlocfilehash: ee9ed5374b12c3130d952770a4be6a759e37339a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156856"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight：群集创建错误

本文介绍了在创建群集时可能会遇到的错误的解决方法。

> [!NOTE]
> 本文中所述的前三个错误为验证错误。 They can occur when an Azure HDInsight product uses the **CsmDocument_2_0** class.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error code: DeploymentDocument 'CsmDocument_2_0' failed the validation

### <a name="error"></a>错误

"Script Action location cannot be accessed URI:\<SCRIPT ACTION URL\>"

#### <a name="error-message"></a>错误消息

"The remote server returned an error: (404) Not Found."

### <a name="cause"></a>原因

The HDInsight service can't access the script action URL that you provided as part of the Create Cluster request. The service receives the preceding error message when it tries to access the script action.

### <a name="resolution"></a>分辨率

- For an HTTP or HTTPS URL, verify the URL by trying to go to it from an incognito browser window.
- For a WASB URL, be sure that the script exists in the storage account that you give in the request. Also make sure that the storage key for this storage account is correct.
- For an ADLS URL, be sure that the script exists in the storage account.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error code: DeploymentDocument 'CsmDocument_2_0' failed the validation

### <a name="error"></a>错误

"Script Action location cannot be accessed URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>错误消息

"The given script URI \<SCRIPT_URI\> is in ADLS, but this cluster has no data lake storage principal"

### <a name="cause"></a>原因

The HDInsight service can't access the script action URL that you provided as part of the Create Cluster request. The service receives the preceding error message when it tries to access the script action.

### <a name="resolution"></a>分辨率

Add the corresponding Azure Data Lake Storage Gen 1 account to the cluster. Also add the service principal that accesses the Data Lake Storage Gen 1 account to the cluster.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Error code: DeploymentDocument 'CsmDocument_2_0' failed the validation

### <a name="error"></a>错误

"VM size '\<CUSTOMER_SPECIFIED_VM_SIZE\>' provided in the request is invalid or not supported for role '\<ROLE\>'. Valid values are: \<VALID_VM_SIZE_FOR_ROLE\>."

### <a name="cause"></a>原因

The virtual machine size that you specified isn't allowed for the role. This error might occur because the VM size value doesn't work as expected or isn't suitable for the computer role.

### <a name="resolution"></a>分辨率

The error message lists the valid values for the VM size. Select one of these values and retry the Create Cluster request.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Error code: InvalidVirtualNetworkId  

### <a name="error"></a>错误

"The VirtualNetworkId is not valid. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*"

### <a name="cause"></a>原因

The **VirtualNetworkId** value that you specified during cluster creation isn't in the correct format.

### <a name="resolution"></a>分辨率

Make sure that the **VirtualNetworkId** and subnet values are in the correct format. To get the **VirtualNetworkId** value:

1. 转到 Azure 门户。
1. Select your virtual network.
1. Select the **Properties** menu item. The **ResourceID** property value is the **VirtualNetworkId** value.

Here's an example of a virtual network ID:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Error code: CustomizationFailedErrorCode

### <a name="error"></a>错误

"Cluster deployment failed due to an error in the custom script action. Failed Actions: \<SCRIPT_NAME\>, Please go to Ambari UI to further debug the failure."

### <a name="cause"></a>原因

The custom script that you provided during the Create Cluster request is executed after the cluster is deployed successfully. This error code indicates that an error arose during execution of the custom script named \<SCRIPT_NAME\>.

### <a name="resolution"></a>分辨率

Because the script is your custom script, we recommend that you troubleshoot the problem and rerun the script if necessary. To troubleshoot the script failure, examine the logs in the /var/lib/ambari-agent/* folder. Or open the **Operations** page in the Ambari UI and then select the **run_customscriptaction** operation to view the error details.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Error code: InvalidDocumentErrorCode

### <a name="error"></a>错误

"\<META_STORE_TYPE\> 元存储架构版本 \<METASTORE_MAJOR_VERSION\> \<DATABASE_NAME\> \<CLUSTER_VERSION\>"

### <a name="cause"></a>原因

自定义元存储与所选 HDInsight 群集版本不兼容。 目前，HDInsight 4.0 群集仅支持元存储版本3.0 和更高版本，而 HDInsight 3.6 群集不支持元存储版本3.0 和更高版本。

### <a name="resolution"></a>分辨率

仅使用 HDInsight 群集版本支持的元存储版本。 如果未指定自定义元存储，HDInsight 将在内部创建元存储，然后在删除群集时将其删除。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码： FailedToConnectWithClusterErrorCode 

### <a name="error"></a>错误

"无法连接到群集管理终结点以执行缩放操作。 验证网络安全规则未阻止对群集的外部访问，并且群集管理器（Ambari） UI 可以成功访问。

### <a name="cause"></a>原因

网络安全组（NSG）上的防火墙规则阻止了与关键 Azure 运行状况和管理服务的群集通信。

### <a name="resolution"></a>分辨率

如果你计划使用网络安全组来控制网络流量，请在安装 HDInsight 之前执行以下操作：

- 标识你计划用于 HDInsight 的 Azure 区域。
- 标识 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
  - 为计划安装 HDInsight 的子网创建或修改网络安全组。
  - 对于网络安全组，允许来自 IP 地址的端口443上的入站流量。 此配置可确保 HDInsight 管理服务可以从虚拟网络外部访问群集。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>错误代码： StoragePermissionsBlockedForMsi  

### <a name="error"></a>错误

"托管标识没有存储帐户的权限。 请验证是否已将 "存储 Blob 数据所有者" 角色分配给存储帐户的托管标识。 存储：/subscriptions/\<订阅 ID\>/resourceGroups/\< 资源组名称\>/providers/Microsoft.Storage/storageAccounts/\<存储帐户名称\>、托管标识：/subscriptions/\<订阅 ID\>/resourceGroups//\< 资源组名称\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<用户托管标识名称\>"

### <a name="cause"></a>原因

你没有提供管理身份所需的权限。 用户分配的托管标识在 Azure Data Lake Storage Gen2 存储帐户上没有 Blob 存储参与者角色。

### <a name="resolution"></a>分辨率

1. 打开 Azure 门户。
1. 转到存储帐户。
1. 查看 "**访问控制（IAM）** "。
1. 请确保 "存储 Blob 数据参与者" 角色或 "存储 Blob 数据所有者" 角色具有对订阅的用户分配的托管标识的 "已分配" 访问权限。

有关详细信息，请参阅在[Data Lake Storage Gen2 帐户上设置托管标识的权限](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>错误代码： InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>错误

"网络安全组中的安全规则/subscriptions/\<SubscriptionID\>/resourceGroups/< 资源组名称\> 默认值/提供者/networkSecurityGroups/\<网络安全组名称\> 配置了子网/subscriptions/\<SubscriptionID\>/resourceGroups/\<资源组名称\> RG-westeurope-vnet-tomtom-default/providers/virtualNetworks/\<虚拟网络名称\>/subnets/\<子网名称\> 不允许所需的入站和/或出站连接。 有关详细信息，请访问[规划 Azure HDInsight 的虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)或联系支持人员。

### <a name="cause"></a>原因

如果网络安全组或用户定义的路由（Udr）控制到 HDInsight 群集的入站流量，请确保群集可以与关键 Azure 运行状况和管理服务通信。

### <a name="resolution"></a>分辨率

如果你计划使用网络安全组来控制网络流量，请在安装 HDInsight 之前执行以下操作：

- 标识计划用于 HDInsight 的 Azure 区域，并为区域的 IP 地址创建一个安全列表。 有关详细信息，请参阅[运行状况和管理服务：特定区域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
- 确定 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
- 为计划安装 HDInsight 的子网创建或修改网络安全组。 对于网络安全组，允许来自 IP 地址的端口443上的入站流量。 此配置可确保 HDInsight 管理服务可以从虚拟网络外部访问群集。
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>错误代码：群集安装程序无法在一个或多个主机上安装组件

###  <a name="error"></a>错误

"群集安装程序无法在一个或多个主机上安装组件。 请重试你的请求。 "

### <a name="cause"></a>原因 

通常，当发生暂时性问题或 Azure 中断时，会生成此错误。

### <a name="resolution"></a>分辨率

查看[azure 状态](https://status.azure.com/status)页，了解可能会影响群集部署的任何 azure 中断。 如果没有中断，请重试群集部署。

## <a name="next-steps"></a>后续步骤

有关排查群集创建中的错误的详细信息，请参阅[排查 Azure HDInsight 的群集创建失败](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)问题。
