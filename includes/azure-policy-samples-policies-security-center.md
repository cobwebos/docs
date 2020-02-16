---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 439803f9a2f66044ec35044189c05fdb1c6f168e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170265"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[\[预览版\] 应在虚拟机上启用漏洞评估](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |监视 Azure 安全中心漏洞评估在虚拟机上检测到的漏洞 |AuditIfNotExists、Disabled |1.0.0-preview |
|[只多只为订阅指定 3 个所有者](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为订阅提供安全联系人电子邮件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |输入电子邮件地址，以便在 Azure 安全中心检测到资源泄露时接收通知 |AuditIfNotExists、Disabled |1.0.0 |
|[应为订阅提供安全联系人电话号码](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |输入电话号码，以便在 Azure 安全中心检测到资源泄露情况时收到通知 |AuditIfNotExists、Disabled |1.0.0 |
|[应该限制通过面向 Internet 的终结点进行访问](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |Azure 安全中心已识别到网络安全组的某些入站规则过于宽松。 入站规则不应允许从“任何”或“Internet”范围进行访问。 这有可能使得攻击者能够轻松地将你的资源定为攻击目标。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在虚拟机上启用自适应应用程序控制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_AppWhitelistMonitoring_Audit.json) |通过 Azure 安全中心监视可能的应用程序允许列表配置 |AuditIfNotExists、Disabled |1.0.0 |
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |1.0.0 |
|[应该对订阅启用 Log Analytics 监视代理的自动预配](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |启用 Log Analytics 监视代理的自动预配，以便收集安全数据 |AuditIfNotExists、Disabled |1.0.0 |
|[应启用 DDoS 防护标准版](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |应为属于应用程序网关且具有公共 IP 子网的所有虚拟网络启用 DDoS 保护标准。 |AuditIfNotExists、Disabled |1.0.0 |
|[应从订阅中删除弃用的帐户](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |应从订阅中删除弃用的帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |1.0.0 |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在虚拟机上应用磁盘加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |建议通过 Azure 安全中心监视未启用磁盘加密的 VM |AuditIfNotExists、Disabled |1.0.0 |
|[应启用高严重性警报的电子邮件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |启用向安全联系人发送电子邮件安全警报，使他们能够收到来自 Microsoft 的安全警报电子邮件。 这可以确保适当的人员能够意识到任何潜在安全问题，并降低风险 |AuditIfNotExists、Disabled |1.0.0 |
|[应启用向订阅所有者发送高严重性警报的电子邮件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |启用向订阅所有者发送电子邮件安全警报，使他们能够收到来自 Microsoft 的安全警报电子邮件。 这可以确保他们意识到任何潜在安全问题，并及时降低风险 |AuditIfNotExists、Disabled |1.0.0 |
|[在订阅中启用 Azure 安全中心](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Register_To_Azure_Security_Center_Deploy.json) |识别不受 Azure 安全中心 (ASC) 监视的现有订阅。 不受 ASC 监视的订阅将注册到免费定价层。 已由 ASC 监视的订阅（免费或标准层）被视为合规。 若要注册新建的订阅，请打开合规性选项卡，选择相关的不合规分配，并创建修正任务。 需要使用安全中心监视一个或多个新订阅时，请重复此步骤。 |deployIfNotExists |1.0.0 |
|[应在虚拟机规模集上安装终结点保护解决方案](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |1.0.0 |
|[应从订阅中删除拥有所有者权限的外部帐户](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。 |AuditIfNotExists、Disabled |1.0.0 |
|[应从订阅中删除拥有读取权限的外部帐户](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |应从订阅中删除拥有读取特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |1.0.0 |
|[应从订阅中删除具有写入权限的外部帐户](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |应从订阅中删除拥有写入特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |1.0.0 |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnVirtualMachines_Audit.json) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 若要详细了解如何使用 NSG 控制流量，请访问 https://aka.ms/nsg-doc |AuditIfNotExists、Disabled |1.0.0 |
|[应在虚拟机上应用实时网络访问控制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |1.0.0 |
|[应关闭虚拟机上的管理端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。 |AuditIfNotExists、Disabled |1.0.0 |
|[应对订阅中拥有写入权限的帐户启用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |1.0.0 |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |1.0.0 |
|[应选择安全中心标准定价层](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |标准定价层为网络和虚拟机启用威胁检测，在 Azure 安全中心提供威胁情报、异常检测和行为分析 |Audit、Disabled |1.0.0 |
|[子网应与网络安全组关联](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在虚拟机规模集上安装系统更新](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |审核是否缺少系统安全更新和关键更新，为了确保 Windows 和 Linux 虚拟机规模集的安全，应安装这些更新。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在计算机上安装系统更新](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |建议通过 Azure 安全中心监视服务器上缺失的安全系统更新 |AuditIfNotExists、Disabled |1.0.0 |
|[应为订阅分配了多个所有者](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |建议指定多个订阅所有者，这样才会有管理员访问冗余。 |AuditIfNotExists、Disabled |1.0.0 |
|[应修正容器安全配置中的漏洞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |1.0.0 |
|[应修复计算机上安全配置中的漏洞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |1.0.0 |
|[应修复虚拟机规模集上安全配置中的漏洞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |1.0.0 |
|[应修复 SQL 数据库中的漏洞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |监视漏洞评估扫描结果并提供如何补救数据库漏洞的相关建议。 |AuditIfNotExists、Disabled |1.0.0 |
|[应通过漏洞评估解决方案修复漏洞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |建议在 Azure 安全中心监视漏洞评估解决方案检测到的漏洞和没有漏洞评估解决方案的 VM。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在与 VM 关联的网络安全组上限制 Web 端口](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedWebApps_Audit.json) |Azure 安全中心已发现某些虚拟机正在运行 Web 应用程序，并且与这些虚拟机关联的 NSG 对 Web 应用程序端口过于宽松 |AuditIfNotExists、Disabled |1.0.0 |
