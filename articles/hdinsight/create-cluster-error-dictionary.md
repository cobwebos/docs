---
title: Azure HDInsight 创建群集 - 错误字典
description: 了解如何排除创建 Azure HDInsight 群集时出现的错误
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618900"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight：群集创建错误

本文介绍了创建群集时可能会遇到的错误的解决方法。

> [!NOTE]
> 本文中描述的前三个错误是验证错误。 当 Azure HDInsight 产品使用**CsmDocument_2_0**类时，可能会发生此类操作。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码：部署文档"CsmDocument_2_0"验证失败

### <a name="error"></a>错误

"无法访问脚本操作位置 URI：SCRIPT\<操作 URL"\>

#### <a name="error-message"></a>错误消息

远程服务器返回了一个错误：（404） 未找到。

### <a name="cause"></a>原因

HDInsight 服务无法访问您在创建群集请求中提供的脚本操作 URL。 当服务尝试访问脚本操作时，它会收到前面的错误消息。

### <a name="resolution"></a>解决方法

- 对于 HTTP 或 HTTPS URL，请通过尝试从隐身浏览器窗口转到 URL 来验证 URL。
- 对于 WASB URL，请确保脚本存在于您在请求中给出的存储帐户中。 还要确保此存储帐户的存储密钥正确。
- 对于 ADLS URL，请确保该脚本存在于存储帐户中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码：部署文档"CsmDocument_2_0"验证失败

### <a name="error"></a>错误

无法访问脚本操作位置 URI：SCRIPT_ACTION_URL" \<\>

#### <a name="error-message"></a>错误消息

给定的脚本 URI \<\> SCRIPT_URI位于 ADLS 中，但此群集没有数据湖存储主体

### <a name="cause"></a>原因

HDInsight 服务无法访问您在创建群集请求中提供的脚本操作 URL。 当服务尝试访问脚本操作时，它会收到前面的错误消息。

### <a name="resolution"></a>解决方法

将相应的 Azure 数据存储库存储第 1 代帐户添加到群集。 还将访问数据存储湖存储第 1 代帐户的服务主体添加到群集中。

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>错误代码：部署文档"CsmDocument_2_0"验证失败

### <a name="error"></a>错误

请求中提供的\<"VM\>大小"CUSTOMER_SPECIFIED_VM_SIZE" 对于角色"ROLE"\<\>无效或不支持。 有效值为：VALID_VM_SIZE_FOR_ROLE。 \<\>

### <a name="cause"></a>原因

不允许角色指定虚拟机大小。 可能发生此错误，因为 VM 大小值不按预期工作或不适合计算机角色。

### <a name="resolution"></a>解决方法

错误消息列出了 VM 大小的有效值。 选择这些值之一，然后重试创建群集请求。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>错误代码：无效虚拟网络 Id  

### <a name="error"></a>错误

"虚拟网络 Id 无效。 虚拟网络 Id\<\>' USER_VIRTUALNETWORKID "*"

### <a name="cause"></a>原因

在群集创建期间指定的**虚拟网络 Id**值格式不正确。

### <a name="resolution"></a>解决方法

确保**虚拟网络Id**和子网值格式正确。 要获取**虚拟网络 Id**值，可以：

1. 转到 Azure 门户。
1. 选择虚拟网络。
1. 选择"**属性"** 菜单项。 **资源 ID**属性值是**虚拟网络 Id**值。

下面是虚拟网络 ID 的示例：

/订阅/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/资源组/我的资源组/提供商/微软.网络/虚拟网络/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>错误代码：自定义失败错误代码

### <a name="error"></a>错误

"由于自定义脚本操作中的错误，群集部署失败。 失败操作：SCRIPT_NAME，\<\>请转到 Ambari UI 以进一步调试故障。

### <a name="cause"></a>原因

成功部署群集后，将执行在创建群集请求期间提供的自定义脚本。 此错误代码指示在执行名为\<SCRIPT_NAME\>的自定义脚本期间出现错误。

### <a name="resolution"></a>解决方法

由于脚本是自定义脚本，因此我们建议您排除故障，并在必要时重新运行该脚本。 若要排查脚本错误，请检查 /var/lib/ambari-agent/* 文件夹中的日志。 或者打开 Ambari UI 中的 **"操作"** 页，然后选择**run_customscriptaction**操作以查看错误详细信息。

---

## <a name="error-codeinvaliddocumenterrorcode"></a>错误代码：无效文档错误代码

### <a name="error"></a>错误

\<数据库\>\<\>\<\>DATABASE_NAME中的META_STORE_TYPE元存储架构版本METASTORE_MAJOR_VERSION与群集版本CLUSTER_VERSION不兼容。\> \<

### <a name="cause"></a>原因

自定义元存储与所选的 HDInsight 群集版本不兼容。 目前，HDInsight 4.0 群集仅支持 Metastore 版本 3.0 及更高版本，而 HDInsight 3.6 群集不支持 Metastore 版本 3.0 及更高版本。

### <a name="resolution"></a>解决方法

仅使用 HDInsight 群集版本支持的 Metastore 版本。 如果不指定自定义元存储，HDInsight 会在内部创建元存储，然后在群集删除后将其删除。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码：未与群集错误代码连接 

### <a name="error"></a>错误

无法连接到群集管理终结点以执行缩放操作。 验证网络安全规则是否阻止对群集的外部访问，以及群集管理器 （Ambari） UI 是否能够成功访问。

### <a name="cause"></a>原因

网络安全组 （NSG） 上的防火墙规则阻止与关键 Azure 运行状况和管理服务的群集通信。

### <a name="resolution"></a>解决方法

如果您计划使用网络安全组来控制网络流量，请在安装 HDInsight 之前执行以下操作：

- 标识你计划用于 HDInsight 的 Azure 区域。
- 标识 HDInsight 所需的 IP 地址。 有关详细信息，请参阅 [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
  - 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。
  - 对于网络安全组，允许来自 IP 地址的端口 443 上的入站流量。 此配置可确保 HDInsight 管理服务可以从虚拟网络外部到达群集。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>错误代码：存储权限阻止

### <a name="error"></a>错误

托管标识对存储帐户没有权限。 请验证是否已将“存储 Blob 数据所有者”角色分配给存储帐户的托管标识。 存储：/订阅/\<订阅\>ID /资源\<组/资源\>组名称/提供程序/Microsoft.存储/存储帐户\</存储帐户\>名称、托管标识：/订阅\</订阅\>ID /资源\<组//\>资源组名称/资源组名称/提供程序/Microsoft.托管身份\</用户分配\>Id 身份/用户托管标识名称"

### <a name="cause"></a>原因

您没有提供管理标识所需的权限。 用户分配的托管标识在 Azure 数据湖存储 Gen2 存储帐户上没有 Blob 存储参与者角色。

### <a name="resolution"></a>解决方法

1. 打开 Azure 门户。
1. 转到存储帐户。
1. 查看**访问控制 （IAM）** 下。
1. 确保用户具有存储 Blob 数据参与者角色或分配给他们的存储 Blob 数据所有者角色。

有关详细信息，请参阅[在 Data Lake Storage Gen2 帐户中设置托管标识的权限](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>错误代码：无效网络安全组安全规则

### <a name="error"></a>错误

\<网络安全组/订阅/订阅 ID/\>资源组/<资源组名称\>默认/提供商/Microsoft.网络/网络安全组/\<网络安全组名称\>中的安全规则配置了子网/订阅/订阅 ID/\<\>资源组/\<资源组名称\>RG-westeurope-vnet-tom-默认/提供商/Microsoft.Network/虚拟网络/\<虚拟网络名称\>/子网/\<子网名称\>不允许所需的入站和/或出站连接。 有关详细信息，请访问[为 Azure HDInsight 规划虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)，或联系支持人员。

### <a name="cause"></a>原因

如果网络安全组或用户定义的路由 （UDR） 控制到 HDInsight 群集的入站流量，请确保群集可以与关键的 Azure 运行状况和管理服务进行通信。

### <a name="resolution"></a>解决方法

如果您计划使用网络安全组来控制网络流量，请在安装 HDInsight 之前执行以下操作：

- 确定计划用于 HDInsight 的 Azure 区域，并为区域创建 IP 地址的安全列表。 有关详细信息，请参阅[运行状况和管理服务：特定区域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
- 识别 HDInsight 所需的 IP 地址。 有关详细信息，请参阅  [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。
- 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。 对于网络安全组，允许来自 IP 地址的端口 443 上的入站流量。 此配置可确保 HDInsight 管理服务可以从虚拟网络外部到达群集。

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>错误代码：群集设置未能在一个或多个主机上安装组件

### <a name="error"></a>错误

群集设置无法在一个或多个主机上安装组件。 请重试您的请求。

### <a name="cause"></a>原因 

通常，当存在暂时性问题或 Azure 中断时，将生成此错误。

### <a name="resolution"></a>解决方法

检查[Azure 状态](https://status.azure.com)页中是否有任何可能影响群集部署的 Azure 中断。 如果没有中断，请重试群集部署。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>错误代码：与群集错误代码连接失败

### <a name="error"></a>错误

无法连接到群集管理终结点。 请稍后重试。

### <a name="cause"></a>原因

在尝试创建群集时，HDInsight 服务无法连接到群集

### <a name="resolution"></a>解决方法

如果您使用的是自定义 VNet 网络安全组 （NSG） 和用户定义的路由 （UDR），请确保群集可以与 HDInsight 管理服务进行通信。 有关详细信息，请参阅[HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>错误代码：由于策略冲突导致部署失败："资源"<Resource URI>被策略禁止。 策略标识符：'{"策略分配"：{"名称"：""，"id"："/<Policy Name>提供商/Microsoft.管理/管理组/提供程序/Microsoft.<Management Group Name>授权/策略分配/"\"""<Policy Name>策略定义"：<Policy Definition>

### <a name="cause"></a>原因

基于订阅的 Azure 策略可能会拒绝创建公共 IP 地址。 创建 HDInsight 群集需要两个公共 IP。

以下策略通常影响群集创建：

* 禁止在订阅中创建 IP 地址或负载平衡器的策略。
* 阻止创建存储帐户的策略。
* 阻止删除网络资源（如 IP 地址或负载均衡器）的策略。

### <a name="resolution"></a>解决方法

在创建 HDInsight 群集时删除或禁用基于订阅的 Azure 策略。

---

## <a name="next-steps"></a>后续步骤

有关在群集创建中排除错误的详细信息，请参阅使用[Azure HDInsight 排除群集创建故障](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)。
