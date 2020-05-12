---
title: 澳大利亚政府 ISM PROTECTED 蓝图示例概述
description: 澳大利亚政府 ISM PROTECTED 蓝图示例的概述。 此蓝图示例帮助客户评估特定的 ISM PROTECTED 控制措施。
ms.date: 03/10/2020
ms.topic: sample
ms.openlocfilehash: e2f64943e9db207feb3f59a905cad994553a6cd6
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595352"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>澳大利亚政府 ISM PROTECTED 蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图澳大利亚政府 ISM PROTECTED 蓝图示例如何映射到 ISM PROTECTED 控制措施。 有关控制措施的详细信息，请参阅 [ISM PROTECTED](https://www.cyber.gov.au/ism)。

下面是到 ISM PROTECTED 控制措施的映射  。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择“ **\[预览\]：** 审核澳大利亚政府 ISM PROTECTED 控制措施，并部署特定 VM 扩展来支持审核要求”内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略可以帮助你[评估控件的符合性](../../../policy/how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性  仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。
> 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md)。


## <a name="location-constraints"></a>位置约束

此蓝图通过分配以下 Azure Policy 定义，帮助你将所有资源和资源组的部署位置限制为“澳大利亚中部”、“澳大利亚中部 2”、“澳大利亚东部”和“澳大利亚东南部”：

- 允许的位置（已硬编码为“澳大利亚中部”、“澳大利亚中部 2”、“澳大利亚东部”和“澳大利亚东南部”）
- 资源组允许的位置（已硬编码为“澳大利亚中部”、“澳大利亚中部 2”、“澳大利亚东部”和“澳大利亚东南部”）

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>人员安全性指导原则 - 对系统及其资源的访问

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 可以唯一标识有权访问系统及其资源的人员

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 仅限在履行其职责时需要访问系统、应用程序和数据存储库的人员进行标准访问

- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者
- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 首次请求时验证对系统、应用程序和数据存储库的特权访问，并且每年一次或以更高的频率重新验证

- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 仅限在履行其职责时需要访问系统、应用程序和数据存储库的人员进行特权访问

- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者
- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM
- 应在虚拟机上应用实时网络访问控制

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 严格控制共享用户帐户的使用，并且可以唯一标识使用此类帐户的人员

- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 为特权用户分配一个专用特权帐户，专门用于执行需要特权访问权限的任务

- 显示其中的“管理员”组包含任意指定成员的 Windows VM 的审核结果
- 部署先决条件来审核管理员组包含任意指定成员的 Windows VM

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 在人员不再具有合法访问要求的当日删除或暂停对系统、应用程序和数据存储库的访问权限

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 向人员授予对系统的临时访问权限时，实施有效的安全控制措施将其限制为只能访问履行其职责所需的信息

- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>系统强化指导原则 - 操作系统强化

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 将最新版本 (N) 或 N-1 版本的操作系统用于标准操作环境 (SOE)

- 应在计算机上安装系统更新
- 应在虚拟机规模集上安装系统更新

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 删除或禁用不需要的操作系统帐户、软件、组件、服务和功能

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 在所有服务器上实施应用程序白名单解决方案，以将可执行文件、软件库、脚本和安装程序的执行限定于已批准的集

- 应在虚拟机上启用自适应应用程序控制

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 在工作站和服务器上实施防病毒软件，并在其中进行以下配置：启用基于签名的检测并将其设置为较高级别、启用基于试探法的检测并将其设置为较高级别、至少每日检查检测签名是否为最新并对其进行更新、为所有固定磁盘和可移动媒体配置自动和常规扫描

- 应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>系统强化指导原则 - 身份验证强化

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 先对用户进行身份验证，然后再向其授予对系统及其资源的访问权限

- 审核对存储帐户的不受限的网络访问
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证
- \[预览\]：显示允许通过没有密码的帐户进行远程连接的 Linux VM 中的审核结果
- \[预览\]：部署先决条件，以审核允许通过没有密码的帐户进行远程连接的 Linux VM
- \[预览\]：显示具有不使用密码的帐户的 Linux VM 中的审核结果
- \[预览\]：部署必备组件以审核其中的帐户没有密码的 Linux VM

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 使用多重身份验证对标准用户进行身份验证

- 应在对订阅拥有读取权限的帐户上启用 MFA

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 使用多重身份验证对所有特权用户和任何其他信任位置进行身份验证

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 用于单重身份验证的通行短语至少包含 14 个字符以满足复杂性要求，且最好包含 4 个随机字

- \[预览\]：显示“安全设置 - 帐户策略”中 Windows VM 配置的审核结果
- \[预览\]：部署必备组件以审核“安全设置 - 帐户策略”中的 Windows VM 配置

## <a name="guidelines-for-system-management---system-administration"></a>系统管理指导原则 - 系统管理

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 每当用户执行特权操作时，都使用多重身份验证对其进行身份验证

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 仅允许来自用来管理系统和应用程序的网络区域的管理流量

- 应在虚拟机上应用实时网络访问控制
- 应当为 API 应用禁用远程调试
- 应当为函数应用禁用远程调试
- 应当为 Web 应用程序禁用远程调试

## <a name="guidelines-for-system-management---system-patching"></a>系统管理指导原则 - 系统修补

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 在供应商、独立的第三方、系统管理员或用户识别出应用程序和驱动程序中评估为极端风险的安全漏洞后，在 48 小时内修补、更新或缓解这些安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 在供应商、独立的第三方、系统管理员或用户识别出应用程序和驱动程序中评估为高风险的安全漏洞后，在两周内修补、更新或缓解这些安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 在供应商、独立的第三方、系统管理员或用户识别出应用程序和驱动程序中评估为中低风险的安全漏洞后，在一个月内修补、更新或缓解安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 在供应商、独立的第三方、系统管理员或用户识别出操作系统和固件中评估为极端风险的安全漏洞后，在 48 小时内修补、更新或缓解这些安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 在供应商、独立的第三方、系统管理员或用户识别出操作系统和固件中评估为高风险的安全漏洞后，在两周内修补、更新或缓解这些安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 在供应商、独立的第三方、系统管理员或用户识别出操作系统和固件中评估为中低风险的安全漏洞后，在一个月内修补、更新或缓解这些安全漏洞

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞
- SQL Server 的漏洞评估设置应包含用来接收扫描报告的电子邮件地址

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>系统管理指导原则 - 数据备份和还原

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 至少每日一次备份重要信息、软件和配置设置

- 审核没有配置灾难恢复的虚拟机

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>系统监视指导原则 - 事件日志记录和审核

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 实施集中式日志记录工具，并将系统配置为在每个事件发生后，尽快将事件日志保存到集中式日志记录工具中

- Azure 订阅应有用于活动日志的日志配置文件

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 为操作系统记录以下事件：访问重要数据和进程、应用程序崩溃和任何错误消息、尝试使用特权、更改帐户、更改安全策略、更改系统配置、域名系统 (DNS) 和超文本传输协议 (HTTP) 请求、尝试访问数据和系统资源失败、服务失败和重启、系统启动和关闭、将数据传输到外部媒体、用户或组管理、使用特权

- \[预览\]：审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VMSS 中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- \[预览\]：审核 VM 的 Log Analytics 工作区 — 报告不匹配
- 审核诊断设置

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 为数据库记录以下事件：访问特别重要的信息、添加新用户（尤其是特权用户）、包含注释的任何查询、包含多个嵌入查询的任何查询、任何查询或数据库警报或失败、尝试提升特权、成功或不成功的访问尝试、更改数据库结构、更改用户角色或数据库权限、数据库管理员操作、数据库登录和注销、修改数据、使用可执行命令

- 应在 SQL 服务器上启用高级数据安全性
- 审核诊断设置
- 应在 SQL 托管实例上启用高级数据安全性

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>系统监视指导原则 - 漏洞管理

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 在部署系统之前或者对系统进行重大更改之后，由具有相关技能的适当人员执行漏洞评估和渗透测试，并且此类测试必须至少每年进行一次，或者按系统所有者指定的频率进行

- 应该修复 SQL 数据库中的漏洞
- 应对 SQL Server 启用漏洞评估
- 应对 SQL 托管实例启用漏洞评估
- \[预览\]：应在虚拟机上启用漏洞评估
- 应该修复虚拟机规模集上安全配置中的漏洞
- 应该通过漏洞评估解决方案修复漏洞
- 应该修复计算机上安全配置中的漏洞
- 应该修复容器安全配置中的漏洞

## <a name="guidelines-for-database-systems-management---database-servers"></a>数据库系统管理指导原则 - 数据库服务器

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 使用全磁盘加密对数据库服务器的硬盘进行加密

- 应在虚拟机上启用磁盘加密
- 应在 SQL 数据库上启用透明数据加密

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 对在数据库服务器与 Web 应用程序之间传送的信息进行加密

- 应该启用只能通过安全方式连接到 Redis 缓存
- 应该启用安全传输到存储帐户
- 显示未使用安全通信协议的 Windows Web 服务器中的审核结果
- 部署先决条件，以便审核未使用安全通信协议的 Windows Web 服务器

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>数据库系统管理指导原则 - 数据库管理系统软件

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 禁用、重命名默认数据库管理员帐户或更改其通行短语

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 数据库管理员具有唯一且可标识的帐户

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 不在不同的数据库之间共享数据库管理员帐户

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 数据库管理员帐户专用于执行管理任务，标准数据库帐户用于与数据库进行常规用途的交互

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 数据库管理员访问权限限定于所定义的角色，而不是具有默认管理权限或所有权限的帐户

- 应该为 SQL 服务器预配 Azure Active Directory 管理员

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>有关使用加密的指导原则 - 加密基本要求

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 用于静态数据的加密软件实施全磁盘加密或部分加密，在部分加密的情况下，访问控制只允许写入到已加密的分区

- 应在虚拟机上启用磁盘加密

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>有关使用加密的指导原则 - 传输层安全性

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 仅使用最新版本的 TLS

- 应在 API 应用中使用最新的 TLS 版本
- 应在 Web 应用中使用最新的 TLS 版本
- 应在函数应用中使用最新的 TLS 版本
- 部署先决条件，以便审核未使用安全通信协议的 Windows Web 服务器
- 显示未使用安全通信协议的 Windows Web 服务器中的审核结果

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>数据传输和内容筛选指导原则 - 内容筛选

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 使用多个不同的扫描引擎对所有内容执行防病毒扫描

- 应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展
- 应在虚拟机规模集上安装 Endpoint Protection 解决方案
- 监视 Azure 安全中心 Endpoint Protection 的缺失情况

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>数据传输和内容筛选指导原则 - Web 应用程序开发

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 专门使用 HTTPS 提供所有 Web 应用程序内容

- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 API 应用
- 只能通过 HTTPS 访问 Web 应用程序
- 应该启用只能通过安全方式连接到 Redis 缓存

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 为 Web 应用程序实施基于 Web 浏览器的安全控制，以帮助保护 Web 应用程序及其用户

- CORS 不应允许所有资源访问 Web 应用程序

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>网络管理指导原则 - 网络设计和配置

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 在网络上实施网络访问控制，以防止未经授权的网络设备进行连接

- 审核对存储帐户的不受限的网络访问

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 实施网络访问控制，以将网段内部和网段之间的流量限制为仅限于业务所需的帐户

- 面向 Internet 的虚拟机应使用网络安全组进行保护
- 审核对存储帐户的不受限的网络访问
- 应在面向 Internet 的虚拟机上应用自适应网络强化建议

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>网络管理指导原则 - 联机服务的服务连续性

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 与服务提供商讨论拒绝服务攻击预防和缓解策略，具体包括：提供商承受拒绝服务攻击的能力、拒绝服务攻击可能导致客户产生的任何成本、在遭到拒绝服务攻击期间通知客户或关闭其联机服务的阈值、在遭到拒绝服务攻击期间采取的预先批准的措施，以及与上游提供商议定的、在上游尽量远的位置阻止恶意流量的拒绝服务攻击预防安排

- 应启用 DDoS 防护标准版


> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。 

## <a name="next-steps"></a>后续步骤

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。