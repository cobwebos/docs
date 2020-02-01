---
title: 所有 Azure 安全中心建议的参考表
description: 本文列出了 Azure 安全中心的安全建议，这些建议可帮助保护你的资源。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2019
ms.author: memildin
ms.openlocfilehash: 3c0fb29c5a4dd73135aa8352c97df29474c36caf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904134"
---
# <a name="security-recommendations---a-reference-guide"></a>安全建议-参考指南

本文列出了在 Azure 安全中心可能会看到的建议。 环境中显示的建议取决于要保护的资源和自定义配置。

若要了解如何响应这些建议，请参阅[Azure 安全中心中的修正建议](security-center-remediate-recommendations.md)。

安全分数以您已缓解的安全中心建议的数量为基础。 若要确定首先要解决的建议的优先级，请考虑每个建议的严重性。

## <a name="recs-network"></a>网络建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应在虚拟机上应用实时网络访问控制**|应用实时（JIT）虚拟机（VM）访问控制以永久锁定对所选端口的访问权限，并使授权用户仅在有限的时间内通过 JIT 打开这些端口。<br>（相关策略：应在虚拟机上应用实时网络访问控制）|严重|N|虚拟机|
|**应启用子网级别的网络安全组**|启用网络安全组来控制子网中部署的资源的网络访问。<br>（相关策略：子网应与网络安全组关联。<br>默认情况下禁用此策略）|高/中|N|Subnet|
|**面向 Internet 的虚拟机应受网络安全组保护**|启用网络安全组来控制虚拟机的网络访问。<br>（相关策略：面向 Internet 的虚拟机应受网络安全组保护）|高/中|N|虚拟机|
|**所有网络端口应在与 VM 关联的 NSG 上受到限制**|通过限制现有允许规则的访问权限，强化面向 Internet 的 Vm 的网络安全组。<br>当任何端口打开到*所有*源（端口22、3389、5985、5986、80和1443除外）时，将触发此建议。<br>（相关策略：应限制通过面向 internet 的终结点进行访问）|严重|N|虚拟机|
|**应在面向 internet 的虚拟机上应用自适应网络强化建议**|当自适应网络强化功能查找过 NSG 的规则时，标准定价层上的客户将会看到此建议。<br>（相关策略：应在面向 internet 的虚拟机上应用自适应网络强化建议）|严重|N|虚拟机|
|**应强化 IaaS Nsg 上的 web 应用程序的规则**|强化运行 web 应用程序的虚拟机的网络安全组（NSG），其中包含对 web 应用程序端口而言过于宽松的 NSG 规则。<br>（相关策略：应强制执行 IaaS 上的 web 应用程序的 Nsg 规则）|严重|N|虚拟机|
|**应限制对应用服务的访问**|通过更改网络配置来限制对应用服务的访问，拒绝范围太大的入站流量。<br>（相关策略： [预览版]：应限制对应用服务的访问权限）|严重|N|应用服务|
|**应在虚拟机上关闭管理端口**|强化虚拟机的网络安全组，以限制对管理端口的访问。<br>（相关策略：应在虚拟机上关闭管理端口）|严重|N|虚拟机|
|**应启用 DDoS 保护标准**|启用 DDoS 保护服务标准，保护包含具有公共 Ip 的应用程序的虚拟网络。 DDoS 保护支持网络容量耗尽和协议攻击的缓解。<br>（相关策略：应启用 DDoS 保护标准）|严重|N|虚拟网络|
|**应禁用虚拟机上的 IP 转发**|禁用 IP 转发。 在虚拟机的 NIC 上启用 IP 转发后，计算机可以接收发送到其他目标的流量。 很少需要 IP 转发（例如，使用 VM 作为网络虚拟设备时），因此应由网络安全团队进行审核。<br>（相关策略： [预览版]：应禁用虚拟机上的 IP 转发）|Medium|N|虚拟机|
|**只能通过 HTTPS 访问 Web 应用程序**|为 web 应用程序启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证，并保护传输中的数据免受网络层窃听攻击。<br>（相关策略：只能通过 HTTPS 访问 Web 应用程序）|Medium|**误差**|Web 应用程序|
|**只能通过 HTTPS 访问 Function App**|为 function app 启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证，并保护传输中的数据免受网络层窃听攻击。<br>（相关策略：只能通过 HTTPS 访问 Function App）|Medium|**误差**|Function app|
|**应该启用安全传输到存储帐户**|启用到存储帐户的安全传输。 安全传输是强制你的存储帐户仅接受来自安全连接（HTTPS）的请求的选项。 使用 HTTPS 可确保服务器与服务之间的身份验证，并保护数据免遭网络层攻击（如中间人、窃听和会话劫持）的传输。<br>（相关策略：应启用安全传输到存储帐户）|严重|**误差**|存储帐户|
||||||


## <a name="recs-containers"></a>容器建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应使用基于角色的访问控制来限制对 Kubernetes Service 群集（预览版）的访问**|若要提供用户可以执行的操作的详细筛选，请使用基于角色的访问控制（RBAC）管理 Kubernetes Service 群集中的权限，并配置相关的授权策略。 有关详细信息，请参阅[Azure 基于角色的访问控制](https://docs.microsoft.com/azure/aks/concepts-identity#role-based-access-controls-rbac)。<br>（相关策略： [预览版]：基于角色的访问控制（RBAC）应在 Kubernetes Services 上使用）|Medium|N|计算资源（容器）|
|**Kubernetes 服务应升级到最新的 Kubernetes 版本（预览）**|将 Azure Kubernetes Service 群集升级到最新的 Kubernetes 版本，以便从最新的漏洞修补程序中获益。 有关特定 Kubernetes 漏洞的详细信息，请参阅[Kubernetes 标识符](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=kubernetes)。<br>（相关策略： [预览版]： Kubernetes Services 应升级到不容易受到攻击的 Kubernetes 版本）|严重|N|计算资源（容器）|
|**应通过删除不必要的应用程序特权来定义 Pod 安全策略，以减少攻击向量（预览）**|通过删除不必要的应用程序特权，定义 Pod 安全策略来减少攻击向量。 建议配置 pod 安全策略，以便盒只能访问允许访问的资源。<br>（相关策略： [预览版]： Pod 安全策略应在 Kubernetes Services 上定义）|Medium|N|计算资源（容器）|
|**只有仅授权特定的 IP 范围（预览版），才能访问 Kubernetes 服务管理 API**|仅向特定范围内的 IP 地址授予 API 访问权限，以限制对 Kubernetes 服务管理 API 的访问。 建议配置授权的 IP 范围，以便只有来自允许的网络的应用程序可以访问该群集。<br>（相关策略： [预览版]：应在 Kubernetes Services 上定义授权 IP 范围）|严重|N|计算资源（容器）|
|**应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供支持）（预览版）**|容器映像漏洞评估扫描注册表中每个推送的容器映像的安全漏洞，并显示每个映像的详细发现。 解决这些漏洞可以极大地改善您的容器的安全状况，并防范攻击。<br>（无相关策略）|严重|N|计算资源（容器）|
||||||


## <a name="recs-appservice"></a>应用服务建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**只能通过 HTTPS 访问 Web 应用程序**|仅通过 HTTPS 限制 Web 应用程序的访问权限。<br>（相关策略：）|Medium|N|应用服务|
|**只能通过 HTTPS 访问 Function App**|仅通过 HTTPS 限制函数应用的访问权限。<br>（相关策略：）|Medium|N|应用服务|
|**API 应用只能通过 HTTPS 访问**|仅通过 HTTPS 限制 API 应用的访问权限。<br>（相关策略：）|Medium|N|应用服务|
|**对于 Web 应用程序，应关闭远程调试**|如果不再需要使用 Web 应用程序，请关闭对其的调试。 远程调试需要在 Web 应用上打开入站端口。<br>（相关策略：应关闭 Web 应用程序的远程调试）|低级|**误差**|应用服务|
|**应为 Function App 关闭远程调试**|如果不再需要使用 Function App，请关闭对它的调试。 远程调试需要打开 Function App 上的入站端口。<br>（相关策略：应对 Function App 关闭远程调试）|低级|**误差**|应用服务|
|**对于 API 应用，应关闭远程调试**|如果不再需要使用 API 应用，请对其进行关闭调试。 远程调试需要在 API 应用中打开入站端口。<br>（相关策略：应关闭 API 应用的远程调试）|低级|**误差**|应用服务|
|**CORS 不应允许每个资源访问你的 Web 应用程序**|仅允许所需域与你的 web 应用程序交互。 跨源资源共享（CORS）不应允许所有域访问您的 web 应用程序。<br>（相关策略： CORS 不应允许每个资源访问你的 Web 应用程序）|低级|**误差**|应用服务|
|**CORS 不应允许每个资源访问你的 Function App**|只允许所需的域与函数应用程序交互。 跨源资源共享（CORS）不应允许所有域访问函数应用程序。<br>（相关策略： CORS 不应允许每个资源访问 Function App）|低级|**误差**|应用服务|
|**CORS 不应允许每个资源访问 API 应用**|仅允许所需域与 API 应用程序交互。 跨源资源共享（CORS）不应允许所有域访问 API 应用程序。<br>（相关策略： CORS 不应允许每个资源访问 API 应用）|低级|**误差**|应用服务|
|**应在应用服务中启用诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应在应用服务中启用诊断日志）</span>|低级|N|应用服务|
||||||


## <a name="recs-computeapp"></a>计算和应用建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应启用 Azure 流分析中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Azure 流分析中的诊断日志）|低级|**误差**|计算资源（流分析）|
|**应启用 Batch 帐户中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Batch 帐户中的诊断日志）|低级|**误差**|计算资源（批处理）|
|**应启用事件中心的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用事件中心的诊断日志）|低级|**误差**|计算资源（事件中心）|
|**逻辑应用中的诊断日志应该已启用**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用逻辑应用中的诊断日志）|低级|**误差**|计算资源（逻辑应用）|
|**应启用搜索服务中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用搜索服务中的诊断日志）|低级|**误差**|计算资源（搜索）|
|**应启用 Service Bus 中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Service Bus 中的诊断日志）|低级|**误差**|计算资源（服务总线）|
|**Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证**|仅通过 Service Fabric 中的 Azure Active Directory 执行客户端身份验证。<br>（相关策略： Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证）|严重|N|计算资源（service fabric）|
|**Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign**|Service Fabric 提供三个级别的保护（无、签名和 EncryptAndSign），用于使用主要群集证书进行节点到节点通信。 设置保护级别，以确保对所有节点到节点消息进行加密和数字签名。<br>（相关策略：应设置 Service Fabric 中 EncryptAndSign 的 ClusterProtectionLevel 属性）|严重|N|计算资源（service fabric）|
|**应从服务总线命名空间中删除除 RootManageSharedAccessKey 以外的所有授权规则**|服务总线客户端不应使用提供对命名空间中的所有队列和主题的访问权限的命名空间级别访问策略。 若要与最小特权安全模型保持一致，你应该在实体级别为队列和主题创建访问策略，以便仅提供对特定实体的访问权限。<br>（相关策略：应从服务总线命名空间中删除除 RootManageSharedAccessKey 以外的所有授权规则）|低级|N|计算资源（服务总线）|
|**应该从事件中心命名空间中删除除 RootManageSharedAccessKey 以外的所有授权规则**|事件中心客户端不应使用提供对命名空间中的所有队列和主题的访问的命名空间级别访问策略。 若要与最小特权安全模型保持一致，你应该在实体级别为队列和主题创建访问策略，以便仅提供对特定实体的访问权限。<br>（相关策略：应从事件中心命名空间中删除除 RootManageSharedAccessKey 以外的所有授权规则）|低级|N|计算资源（事件中心）|
|**应定义事件中心实体上的授权规则**|审核事件中心实体上的授权规则，以授予最小特权访问权限。<br>（无相关策略）|低级|N|计算资源（事件中心）|
|**在虚拟机上安装监视代理**|安装监视代理以在每台计算机上启用数据收集、更新扫描、基线扫描和 endpoint protection。<br>（相关策略：应在虚拟机上启用监视代理）|严重|**误差**|Machine|
|**应在你的计算机上解决监视代理运行状况问题**|若要获得完全安全中心保护，请按照故障排除指南中的说明解决计算机上的监视代理问题<br>（无相关策略）|Medium|N|Machine|
|**应在虚拟机上启用自适应应用程序控件**|启用应用程序控制来控制哪些应用程序可以在 Azure 中的 Vm 上运行。 这将有助于强化 Vm 免受恶意软件的侵害。 安全中心使用机器学习来分析每个虚拟机上运行的应用程序，并帮助您应用使用此智能的允许规则。 此功能简化了配置和维护应用程序允许规则的过程。<br>（相关策略：应在虚拟机上启用自适应应用程序控件）|严重|N|Machine|
|**在计算机上安装 endpoint protection 解决方案**|在 Windows 和 Linux 计算机上安装 endpoint protection 解决方案，以防止其受到威胁和漏洞的威胁。<br>（无相关策略）|Medium|N|Machine|
|**在虚拟机上安装 endpoint protection 解决方案**|在虚拟机上安装 endpoint protection 解决方案，以防止其受到威胁和漏洞的威胁。<br>（无相关策略）|Medium|N|Machine|
|**应为你的云服务角色更新 OS 版本**|将云服务角色的操作系统（OS）版本更新为操作系统系列可用的最新版本。<br>（无相关策略）|严重|N|Machine|
|**应在计算机上安装系统更新**|安装缺少的系统安全更新和关键更新以保护 Windows 和 Linux 虚拟机和计算机<br>（相关策略：应在计算机上安装系统更新）|严重|N|Machine|
|**应重启计算机以应用系统更新**|重新启动计算机以应用系统更新并保护计算机免受漏洞的侵害。<br>（无相关策略）|Medium|N|Machine|
|**自动化帐户变量应加密**|在存储敏感数据时启用自动加密自动化帐户变量资产。<br>（相关策略：应在自动化帐户变量上启用加密）|严重|N|计算资源（自动化帐户）|
|**磁盘加密应该应用于虚拟机**|使用适用于 Windows 和 Linux 虚拟机的 Azure 磁盘加密来加密虚拟机磁盘。 Azure 磁盘加密（ADE）利用 Windows 行业标准的 BitLocker 功能和 Linux 的 Dm-crypt 功能来提供操作系统和数据磁盘加密，以帮助保护你的数据并帮助你满足组织的安全性和合规性客户在 Azure 密钥保管库中所做的承诺。 当符合性和安全要求要求使用加密密钥（包括对临时（本地附加的临时）磁盘的加密）对数据端加密时，请使用 Azure 磁盘加密。 另外，默认情况下，使用 Azure 存储服务加密，默认情况下，使用 Azure 中的加密密钥是 Microsoft 托管密钥的 Azure 静态加密托管磁盘。 如果满足你的符合性和安全要求，你可以利用默认的托管磁盘加密来满足你的要求。<br>（相关策略：应在虚拟机上应用磁盘加密）|严重|N|Machine|
|**应将虚拟机迁移到新的 Azure 资源管理器资源**|使用 Azure 资源管理器为虚拟机提供安全增强功能，例如：更强的访问控制（RBAC）、更好的审核、基于资源管理器的部署和监管、对托管标识的访问、对密钥保管库的访问权限，基于 Azure AD 的身份验证和对标记和资源组的支持，以便更轻松地进行安全性管理。<br>（相关策略：应将虚拟机迁移到新的 Azure 资源管理器资源）|低级|N|Machine|
|**应在虚拟机上安装漏洞评估解决方案**|在虚拟机上安装漏洞评估解决方案<br>（相关策略：应在虚拟机上安装漏洞评估）|Medium|N|Machine|
|**漏洞评估解决方案应修正的漏洞**|为其部署了漏洞评估第三方解决方案的虚拟机将针对应用程序和操作系统漏洞不断地进行评估。 只要找到此类漏洞，就可以获得详细信息作为建议的一部分。<br>（相关策略：漏洞评估解决方案应修正的漏洞）|严重|N|Machine|
|**应修正计算机上安全配置中的漏洞**|修正计算机上的安全配置中的漏洞，以防止攻击。<br>（相关策略：应修正计算机上安全配置中的漏洞）|低级|N|Machine|
|**应修正容器安全配置中的漏洞**|更正安装了 Docker 的计算机上的安全配置中的漏洞，以防止攻击。<br>（相关策略：应修正容器安全配置中的漏洞）|严重|N|Machine|
|**应在你的计算机上解决 Endpoint protection 运行状况问题**|若要获得完全安全中心保护，请按照故障排除指南中的说明解决计算机上的监视代理问题。<br>（无相关策略）|Medium|N|Machine|
||||||


## <a name="recs-vmscalesets"></a>虚拟机规模集建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应启用虚拟机规模集中的诊断日志**|启用日志，并将其保留一年。 这使你可以重新创建活动跟踪以进行调查。 当发生安全事件或网络受到威胁时，这非常有用。<br>（相关策略：应启用虚拟机规模集中的诊断日志）|低级|N|虚拟机规模集|
|**应安装虚拟机规模集的系统更新**|安装缺少的系统安全更新和关键更新以保护 Windows 和 Linux 虚拟机规模集。<br>（相关策略：应安装虚拟机规模集上的系统更新）|严重|N|虚拟机规模集|
|**应修正虚拟机规模集上安全配置中的漏洞**|更正虚拟机规模集上的安全配置中的漏洞，以防止攻击。 <br>（相关策略：应修正虚拟机规模集上安全配置中的漏洞）|严重|N|虚拟机规模集|
|**应在虚拟机规模集上修正 Endpoint protection 运行状况故障**|修正虚拟机规模集上的 endpoint protection 运行状况故障，以防止其受到威胁和漏洞的威胁。<br>（无相关策略）|低级|N|虚拟机规模集|
|**Endpoint protection 解决方案应安装在虚拟机规模集上**|在虚拟机规模集上安装 endpoint protection 解决方案，以防止其受到威胁和漏洞的威胁。<br>（相关策略： Endpoint protection 解决方案应安装在虚拟机规模集上）|严重|N|虚拟机规模集|
||||||


## <a name="recs-datastorage"></a>数据和存储建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应为 SQL server 设置 Azure Active Directory 管理员**|为 SQL server 预配 Azure AD 管理员，以启用 Azure AD 身份验证。 Azure AD 身份验证可简化对数据库用户和其他 Microsoft 服务的权限管理和集中标识管理。<br>（相关策略：为 SQL server 的 Azure Active Directory 管理员审核预配|严重|N|SQL|
|**应启用 SQL server 审核**|为 Azure SQL 服务器启用审核。 （仅限 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。）<br>（相关策略：应在 SQL Server 上的高级数据安全设置上启用审核）|低级|**误差**|SQL|
|**应该启用安全传输到存储帐户**|安全传输是强制你的存储帐户仅接受来自安全连接（HTTPS）的请求的选项。 HTTPS 可确保服务器与服务之间的身份验证，并防止数据在传输过程中受到网络层攻击，如中间人、窃听和会话劫持。<br>（相关策略：应启用安全传输到存储帐户）|严重|N|存储帐户|
|**只应启用与 Redis 缓存的安全连接**|仅启用通过 SSL 连接到 Azure Cache for Redis 的连接。 使用安全连接可确保服务器与服务之间的身份验证，并保护数据免遭网络层攻击（如中间人、窃听和会话劫持）的传输。<br>（相关策略：只应启用与 Redis 缓存的安全连接）|严重|N|Redis|
|**应启用 SQL 数据库上的透明数据加密**|启用透明数据加密以保护静态数据并满足符合性要求。<br>（相关策略：应启用 SQL 数据库上的透明数据加密）|低级|**误差**|SQL|
|**应启用 Data Lake Analytics 中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Data Lake Analytics 中的诊断日志）|低级|**误差**|Data lake analytics|
|**应启用 Azure Data Lake Store 中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Azure Data Lake Store 中的诊断日志）|低级|**误差**|Data lake store|
|**应在 SQL server 上启用漏洞评估**|漏洞评估可以发现、跟踪和帮助您修正潜在的数据库漏洞。<br>（相关策略：应在 SQL server 上启用漏洞评估）|严重|**误差**|SQL|
|**应修正 SQL 数据库上的漏洞**|SQL 漏洞评估会扫描数据库中的安全漏洞，并公开最佳实践的任何偏差，如配置错误、权限过多以及未受保护的敏感数据。 解决发现的漏洞可以极大地提高数据库安全威望。<br>（相关策略：应修正 SQL 数据库上的漏洞）|严重|N|SQL|
|**应限制对具有防火墙和虚拟网络配置的存储帐户的访问权限**|在存储帐户防火墙设置中审核无限制的网络访问。 改为配置网络规则，以便仅允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以授予对来自特定 Azure 虚拟网络或公共 Internet IP 地址范围的流量的访问权限。<br>（相关策略：审核对存储帐户的不受限制的网络访问）|低级|N|存储帐户|
|**应将存储帐户迁移到新的 Azure 资源管理器资源**|使用新的 Azure 资源管理器存储帐户来提供安全增强功能，例如：更强的访问控制（RBAC）、更好的审核、基于资源管理器的部署和监管、访问托管标识、访问密钥保管库以获得机密，和对标记和资源组的基于 Azure AD 的身份验证和支持，以便更轻松地进行安全性管理。<br>（相关策略：应将存储帐户迁移到新的 Azure 资源管理器资源）|低级|N|存储帐户|
||||||


## <a name="recs-identity"></a>身份验证和访问建议

|推荐|相关策略 & 说明|severity|已启用快速修复？（[了解详细信息](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#recommendations-with-quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|**应在对订阅拥有读取权限的帐户上启用 MFA**|为具有读取权限的所有订阅帐户启用多重身份验证（MFA），以防止帐户或资源被破坏。<br>（相关策略：应在对订阅拥有读取权限的帐户上启用 MFA）|严重|N|订阅|
|**应在对订阅具有写入权限的帐户上启用 MFA**|为具有写入权限的所有订阅帐户启用多重身份验证（MFA），以防止帐户或资源被破坏。<br>（相关策略：应在对订阅具有写入权限的帐户上启用 MFA）|严重|N|订阅|
|**应在订阅上拥有所有者权限的帐户上启用 MFA**|为具有所有者权限的所有订阅帐户启用多重身份验证（MFA），以防止帐户或资源被破坏。<br>（相关策略：应在对订阅拥有所有者权限的帐户上启用 MFA）|严重|N|订阅|
|**应从订阅中删除具有读取权限的外部帐户**|从订阅中删除具有读取权限的外部帐户，以防止未受监视的访问。<br>（相关策略：应从订阅中删除具有读取权限的外部帐户）|严重|N|订阅|
|**应从订阅中删除具有写入权限的外部帐户**|从订阅中删除具有写入权限的外部帐户，以防止未受监视的访问。<br>（相关策略：应从订阅中删除具有写入权限的外部帐户）|严重|N|订阅|
|**应从订阅中删除具有所有者权限的外部帐户**|从订阅中删除具有所有者权限的外部帐户，以防止未受监视的访问。<br>（相关策略：应从订阅中删除具有所有者权限的外部帐户）|严重|N|订阅|
|**应从订阅中删除不推荐使用的具有所有者权限的帐户**|使用订阅的所有者权限删除不推荐使用的帐户。<br>（相关策略：应从订阅中删除不推荐使用的具有所有者权限的帐户）|严重|N|订阅|
|**应从订阅中删除弃用的帐户**|从订阅中删除弃用的帐户，以仅允许当前用户访问。<br>（相关策略：应从订阅中删除不推荐使用的帐户）|严重|N|订阅|
|**应该有多个所有者分配给你的订阅**|指定多个订阅所有者，以具有管理员访问冗余。<br>（相关策略：应将多个所有者分配到订阅）|严重|N|订阅|
|**应为订阅指定最多3个所有者**|指定少于三个订阅所有者，以减少被泄露的所有者造成的潜在侵害。<br>（相关策略：应为订阅指定最多3个所有者）|严重|N|订阅|
|**应启用 Key Vault 中的诊断日志**|启用日志，并将其保留一年。 这使你可以在安全事件发生或网络泄露时重新创建用于调查的活动跟踪。<br>（相关策略：应启用 Key Vault 中的诊断日志）|低级|**误差**|Key Vault|
||||||

## <a name="next-steps"></a>后续步骤
若要详细了解建议，请参阅以下内容：

* [Azure 安全中心的安全建议](security-center-recommendations.md)
* [保护您的计算机和应用程序](security-center-virtual-machine-protection.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)