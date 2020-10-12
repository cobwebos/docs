---
title: Azure HDInsight 创建群集错误字典
description: 了解如何排查创建 Azure HDInsight 群集时出现的错误
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: cae8647d970020a22d59dc49b058d43fe28dd00c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816450"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight：群集创建错误

本文介绍在创建群集时可能遇到的错误的解决方法。

> [!NOTE]
> 本文所述的前三个错误是验证错误。 当 Azure HDInsight 产品使用 **CsmDocument_2_0** 类时，可能会发生这些错误。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument 'CsmDocument_2_0' 验证失败

**错误**：“无法通过 URI:\<SCRIPT ACTION URL\>”

### <a name="error-message-1"></a>错误消息 1

“远程服务器返回错误:(404)未找到。”

#### <a name="cause"></a>原因

HDInsight 服务无法访问作为“创建群集”请求的一部分提供的脚本操作 URL。 当该服务尝试访问该脚本操作时，会收到上述错误消息。

#### <a name="resolution"></a>解决方法

- 对于 HTTP 或 HTTPS URL，请尝试从 Incognito 浏览器窗口转到该 URL 进行验证。
- 对于 WASB URL，请确保该脚本在请求中指定的存储帐户中存在。 另请确保此存储帐户的存储密钥正确。
- 对于 ADLS URL，请确保该脚本在存储帐户中存在。

---

### <a name="error-message-2"></a>错误消息 2

“给定的脚本 URI \<SCRIPT_URI\> 在 ADLS 中，但此群集没有 Data Lake Storage 主体”

#### <a name="cause"></a>原因

HDInsight 服务无法访问作为“创建群集”请求的一部分提供的脚本操作 URL。 当该服务尝试访问该脚本操作时，会收到上述错误消息。

#### <a name="resolution"></a>解决方法

将相应的 Azure Data Lake Storage Gen 1 帐户添加到群集。 此外，将用于访问 Data Lake Storage Gen 1 帐户的服务主体添加到群集。

---

### <a name="error-message-3"></a>错误消息 3

“请求中提供的 VM 大小 \<CUSTOMER_SPECIFIED_VM_SIZE\> 无效，或者不支持 \<ROLE\> 角色。 有效值是：\<VALID_VM_SIZE_FOR_ROLE\>”

#### <a name="cause"></a>原因

该角色不允许指定的虚拟机大小。 此错误的可能原因是 VM 大小值不符合预期，或者不适合该计算机角色。

#### <a name="resolution"></a>解决方法

错误消息列出了 VM 大小的有效值。 请选择其中的一个值，然后重试“创建群集”请求。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>错误代码： InvalidVirtualNetworkId  

### <a name="error"></a>错误

“VirtualNetworkId 无效。 VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*”

### <a name="cause"></a>原因

在群集创建过程中指定的 **VirtualNetworkId** 值的格式不正确。

### <a name="resolution"></a>解决方法

确保 **VirtualNetworkId** 和子网值采用正确的格式。 若要获取 **VirtualNetworkId** 值：

1. 转到 Azure 门户。
1. 选择你的虚拟网络。
1. 选择“属性”菜单项。**** **ResourceID** 属性值即为 **VirtualNetworkId** 值。

下面是虚拟网络 ID 的示例：

“/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet”

---

## <a name="error-code-customizationfailederrorcode"></a>错误代码：CustomizationFailedErrorCode

### <a name="error"></a>错误

“群集部署因自定义脚本操作出错而失败。 失败的操作：\<SCRIPT_NAME\>请转到 Ambari UI 以进一步调试错误。”

### <a name="cause"></a>原因

成功部署群集后，将执行“创建群集”请求期间提供的自定义脚本。 此错误代码指示在执行名为 \<SCRIPT_NAME\> 的自定义脚本时遇到错误。

### <a name="resolution"></a>解决方法

由于这是你的自定义脚本，因此我们建议排查问题，并根据需要重新运行该脚本。 若要排查脚本错误，请检查 /var/lib/ambari-agent/* 文件夹中的日志。 或者，在 Ambari UI 中打开“操作”页，然后选择“run_customscriptaction”操作查看错误详细信息。**** ****

---

## <a name="error-codeinvaliddocumenterrorcode"></a>错误代码： InvalidDocumentErrorCode

### <a name="error"></a>错误

“数据库 \<DATABASE_NAME\> 中的 \<META_STORE_TYPE\> 元存储架构版本 \<METASTORE_MAJOR_VERSION\> 与群集版本 \<CLUSTER_VERSION\> 不兼容”

### <a name="cause"></a>原因

自定义元存储与所选的 HDInsight 群集版本不兼容。 目前，HDInsight 4.0 群集仅支持元存储版本 3.0 和更高版本，而 HDInsight 3.6 不支持元存储版本 3.0 和更高版本。

### <a name="resolution"></a>解决方法

仅使用 HDInsight 群集版本支持的元存储版本。 如果未指定自定义元存储，HDInsight 将在内部创建元存储，并在删除群集后删除此元存储。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码：FailedToConnectWithClusterErrorCode 

### <a name="error"></a>错误

“无法连接到群集管理终结点来执行缩放操作。 验证网络安全规则是否未阻止从外部访问群集，以及是否可以成功访问群集管理器 (Ambari) UI。”

### <a name="cause"></a>原因

网络安全组 (NSG) 中的某个防火墙规则阻止了群集与关键 Azure 运行状况和管理服务的通信。

### <a name="resolution"></a>解决方法

如果你打算使用网络安全组来控制网络流量，请在安装HDInsight 之前执行以下操作：

- 确定计划用于 HDInsight 的 Azure 区域。
- 确定 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
  - 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。
  - 对于网络安全组，请在端口 443 上允许来自 IP 地址的入站流量。 此配置确保 HDInsight 管理服务可以从虚拟网络外部访问群集。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>错误代码： StoragePermissionsBlockedForMsi

### <a name="error"></a>错误

“托管标识对存储帐户没有权限。 请验证是否已将“存储 Blob 数据所有者”角色分配给存储帐户的托管标识。 存储：/subscriptions/ \<Subscription ID\> /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>，托管标识：/subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>”

### <a name="cause"></a>原因

未提供管理标识所需的权限。 用户分配的托管标识没有 Azure Data Lake Storage Gen2 存储帐户的 Blob 存储参与者角色。

### <a name="resolution"></a>解决方法

1. 打开 Azure 门户。
1. 转到存储帐户。
1. 查看“访问控制(IAM)”。****
1. 确保为该用户分配了“存储 Blob 数据参与者”角色或“存储 Blob 数据所有者”角色。

有关详细信息，请参阅[在 Data Lake Storage Gen2 帐户中设置托管标识的权限](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>错误代码：InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>错误

“配置了子网 /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> 的网络安全组 /subscriptions/\<SubscriptionID\>/resourceGroups/<Resource Group name\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> 中的安全规则不允许建立所需的入站和/或出站连接。 有关详细信息，请访问[规划 Azure HDInsight 的虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)，或联系支持人员。”

### <a name="cause"></a>原因

如果网络安全组或用户定义的路由 (UDR) 控制流向 HDInsight 群集的入站流量，请确保群集能够与关键的 Azure 运行状况和管理服务通信。

### <a name="resolution"></a>解决方法

如果你打算使用网络安全组来控制网络流量，请在安装HDInsight 之前执行以下操作：

- 确定要用于 HDInsight 的 Azure 区域，并为该区域创建安全的 IP 地址列表。 有关详细信息，请参阅[运行状况和管理服务：特定区域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
- 确定 HDInsight 所需的 IP 地址。 有关详细信息，请参阅  [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
- 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。 对于网络安全组，请在端口 443 上允许来自 IP 地址的入站流量。 此配置确保 HDInsight 管理服务可以从虚拟网络外部访问群集。

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>错误代码：群集安装程序无法在一个或多个主机上安装组件

### <a name="error"></a>错误

“群集安装程序无法在一个或多个主机上安装组件。 请重试请求。”

### <a name="cause"></a>原因 

通常，此错误是出现暂时性问题或 Azure 服务中断时生成的。

### <a name="resolution"></a>解决方法

有关可能影响群集部署的任何 Azure 服务中断，请查看 [Azure 状态](https://status.azure.com)页。 如果未发生服务中断，请重试群集部署。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码：FailedToConnectWithClusterErrorCode

### <a name="error"></a>错误

无法连接到群集管理终结点。 请稍后重试。

### <a name="cause"></a>原因

尝试创建群集时，HDInsight 服务无法连接到群集

### <a name="resolution"></a>解决方法

如果使用了自定义 VNet 网络安全组 (NSG) 和用户定义路由 (UDR)，请确保群集可以与 HDInsight 管理服务通信。 有关其他信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>错误代码：由于违反策略导致部署失败：策略禁止使用资源“<Resource URI>”。 策略标识符：'[{"policyAssignment":{"name":"<Policy Name> ","id":"/providers/Microsoft.Management/managementGroups/<Management Group Name> providers/Microsoft.Authorization/policyAssignments/<Policy Name>"},"policyDefinition": <Policy Definition>

### <a name="cause"></a>原因

基于订阅的 Azure 策略可能会拒绝创建公共 IP 地址。 创建 HDInsight 群集需要两个公共 IP。

以下策略通常会影响群集创建：

* 阻止在订阅中创建 IP 地址或负载均衡器的策略。
* 阻止创建存储帐户的策略。
* 阻止删除网络资源（如 IP 地址或负载均衡器）的策略。

### <a name="resolution"></a>解决方法

创建 HDInsight 群集时删除或禁用基于订阅的 Azure Policy 分配。

---

## <a name="next-steps"></a>后续步骤

若要详细了解如何排查群集创建问题，请参阅[排查 Azure HDInsight 中的群集创建失败问题](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)。
