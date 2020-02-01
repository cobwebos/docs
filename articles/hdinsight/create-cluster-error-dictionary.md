---
title: 创建群集错误字典
description: 了解如何创建群集错误字典。
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 6e5ed996a0f44bae6c37027bc01f30be85d164f9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905483"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight：群集创建错误

本文介绍了在创建群集时可能会遇到的错误的解决方法。

> [!NOTE]
> 本文中所述的前三个错误为验证错误。 当 Azure HDInsight 产品使用**CsmDocument_2_0**类时，可能会出现这种情况。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error"></a>错误

"无法访问脚本操作位置 URI：\<脚本操作 URL\>"

#### <a name="error-message"></a>错误消息

"远程服务器返回了一个错误：（404）。"

### <a name="cause"></a>原因

HDInsight 服务不能访问你在创建群集请求过程中提供的脚本操作 URL。 当服务尝试访问脚本操作时，会收到前面的错误消息。

### <a name="resolution"></a>分辨率

- 对于 HTTP 或 HTTPS URL，请尝试从 incognito 浏览器窗口中转到该 URL。
- 对于 WASB URL，请确保在请求中提供的存储帐户中存在该脚本。 另外，请确保此存储帐户的存储密钥是正确的。
- 对于 ADLS URL，请确保该脚本存在于存储帐户中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error"></a>错误

"无法访问脚本操作位置 URI： \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>错误消息

"给定的脚本 URI \<SCRIPT_URI\> 在 ADLS 中，但此群集没有 data lake storage 主体"

### <a name="cause"></a>原因

HDInsight 服务不能访问你在创建群集请求过程中提供的脚本操作 URL。 当服务尝试访问脚本操作时，会收到前面的错误消息。

### <a name="resolution"></a>分辨率

将相应的 Azure Data Lake Storage 第1代帐户添加到群集。 还要将访问 Data Lake Storage 第1代帐户的服务主体添加到群集。

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码： DeploymentDocument "CsmDocument_2_0" 验证失败

### <a name="error"></a>错误

请求中提供的 "VM 大小"\<CUSTOMER_SPECIFIED_VM_SIZE\>"无效，或"\<ROLE\>"角色不支持。 有效值为： \<VALID_VM_SIZE_FOR_ROLE\>"。

### <a name="cause"></a>原因

角色不允许您指定的虚拟机大小。 出现此错误的原因可能是 VM 大小值无法按预期工作，或者不适合计算机角色。

### <a name="resolution"></a>分辨率

错误消息将列出 VM 大小的有效值。 选择其中一个值，然后重试 "创建群集请求"。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>错误代码： InvalidVirtualNetworkId  

### <a name="error"></a>错误

"VirtualNetworkId 无效。 VirtualNetworkId "\<USER_VIRTUALNETWORKID\>" * "

### <a name="cause"></a>原因

在群集创建过程中指定的**VirtualNetworkId**值的格式不正确。

### <a name="resolution"></a>分辨率

请确保**VirtualNetworkId**和子网的值格式正确。 若要获取**VirtualNetworkId**值：

1. 转到 Azure 门户。
1. 选择虚拟网络。
1. 选择 "**属性**" 菜单项。 **ResourceID**属性值为**VirtualNetworkId**值。

下面是虚拟网络 ID 的示例：

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>错误代码： CustomizationFailedErrorCode

### <a name="error"></a>错误

"群集部署因自定义脚本操作中的错误而失败。 失败的操作： \<SCRIPT_NAME\>，请参阅 Ambari UI 以进一步调试失败。 "

### <a name="cause"></a>原因

在创建群集请求过程中提供的自定义脚本在群集成功部署后执行。 此错误代码表示在执行名为 \<SCRIPT_NAME\>的自定义脚本期间出现错误。

### <a name="resolution"></a>分辨率

因为脚本是您的自定义脚本，所以我们建议您对问题进行故障排除，并根据需要重新运行脚本。 若要对脚本失败进行故障排除，请检查/var/lib/ambari-agent/* 文件夹中的日志。 或在 Ambari UI 中打开 "**操作**" 页，然后选择**run_customscriptaction**操作以查看错误详细信息。

---

## <a name="error-codeinvaliddocumenterrorcode"></a>错误代码： InvalidDocumentErrorCode

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

查看[azure 状态](https://status.azure.com)页，了解可能会影响群集部署的任何 azure 中断。 如果没有中断，请重试群集部署。

## <a name="next-steps"></a>后续步骤

有关排查群集创建中的错误的详细信息，请参阅[排查 Azure HDInsight 的群集创建失败](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)问题。
