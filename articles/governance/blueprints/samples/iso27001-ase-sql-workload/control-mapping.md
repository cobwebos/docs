---
title: 示例-ISO 27001 蓝图-控件映射
description: ISO 27001 蓝图示例控件映射。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c062759938652518ac3cafff64973050554ca19d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579693"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Azure 蓝图 ISO 27001 蓝图示例的控件映射

以下文章详细介绍了如何将 Azure 蓝图 ISO 27001 ASE/SQL 工作负荷蓝图示例映射到 ISO 27001 控件。 有关控件的详细信息，请参阅[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

以下映射到所**ISO 27001: 2013**控件。 使用右侧导航直接跳转到特定的控件映射。 许多映射控件都与实现[Azure 策略](../../../policy/overview.md)计划。 若要查看完整的计划，请打开**策略**在 Azure 门户并选择**定义**页。 然后，找到并选择**[预览版]审核 ISO 27001: 2013 控制和部署特定的 VM 扩展以支持审核要求**内置策略计划。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 责任分离

具有只有一个 Azure 订阅所有者不允许管理冗余。 相反，具有 Azure 订阅的所有者过多会增加通过遭到入侵的所有者帐户受到破坏的可能性。 此蓝图可帮助您通过将分配两个维护适当数量的 Azure 订阅所有者[Azure 策略](../../../policy/overview.md)审核的 Azure 订阅的所有者数量的定义。 管理订阅所有者权限可以帮助您实现相应职责分离。

- [预览版]: Audit minimum number of owners for subscription
- [预览]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 访问网络和网络服务

Azure 实现[基于角色的访问控制](../../../../role-based-access-control/overview.md)(RBAC) 来管理谁有权访问 Azure 资源。 此蓝图可帮助你控制对 Azure 资源的分配七个存储帐户访问[Azure 策略](../../../policy/overview.md)定义。 这些策略审核资源类型的使用和配置，可能会更多权限资源的访问权限。
了解资源的违反了这些策略可帮助你采取纠正措施以确保 Azure 资源的访问权限仅限于授权用户。

- [预览]: Deploy VM extension to audit Linux VM accounts with no passwords
- [预览]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  密码s
- [预览]: Audit Linux VM accounts with no passwords
- [预览]: Audit Linux VM allowing remote connections from accounts with no passwords
- 审核经典存储帐户的使用
- 审核经典虚拟机的使用
- 审核未使用托管磁盘的 VM

## <a name="a922-user-access-provisioning"></a>A.9.2.2 用户访问的预配

Azure 实现[基于角色的访问控制](../../../../role-based-access-control/overview.md)(RBAC) 来管理谁有权访问 Azure 资源。 此蓝图分配三个[Azure 策略](../../../policy/overview.md)定义要审核的使用[Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) SQL 服务器的身份验证并[Service Fabric](../../../../service-fabric/service-fabric-overview.md)。 使用 Azure Active Directory 身份验证，简化的权限管理和集中式的标识管理的数据库用户和其他 Microsoft 服务。 此蓝图还将分配的 Azure 策略定义要审核的自定义 RBAC 规则使用。 了解自定义 RBAC 规则的实现可以帮助您验证需求和正确实现，如自定义 RBAC 规则是容易出错。

- 审核确认已为 SQL Server 预配了 Azure Active Directory 管理员
- 审核确认已在 Service Fabric 中使用 Azure Active Directory，用于实施客户端身份验证
- 审核自定义 RBAC 规则的使用

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特权的访问权限

此蓝图可帮助你限制并可以通过分配四个控制特权的访问权限[Azure 策略](../../../policy/overview.md)定义要审核的外部帐户的所有者和/或编写使用所有者的权限以及帐户和/或写入权限没有启用多重身份验证。

- [预览]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [预览]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [预览]: Audit external accounts with owner permissions on a subscription
- [预览]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 信息管理的机密的身份验证的用户

此蓝图分配三个[Azure 策略](../../../policy/overview.md)定义审核不具有启用多重身份验证的帐户。 多重身份验证来帮助保护帐户安全，即使一条身份验证信息遭到破坏。 通过监视帐户，而启用多重身份验证，可以标识可能会更有可能遭到入侵的帐户。 此蓝图还将两个审核 Linux VM 的 Azure 策略定义分配密码的文件权限设置不正确时的警报。 此安装程序允许你采取纠正措施以确保身份验证器不会受到影响。

- [预览]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [预览]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [预览]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [预览]: Deploy VM extension to audit Linux VM passwd file permissions
- [预览]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 审阅的用户访问权限

Azure 实现[基于角色的访问控制](../../../../role-based-access-control/overview.md)(RBAC) 向可帮助你管理 Azure 中拥有资源的访问权限的人员。 使用 Azure 门户，你可以查看谁有权访问 Azure 资源和他们的权限。 此蓝图分配四个[Azure 策略](../../../policy/overview.md)定义要审核应优先供审核，其中包括折旧前的帐户和使用提升的权限的外部帐户的帐户。

- [预览]: Audit deprecated accounts on a subscription
- [预览]: Audit deprecated accounts with owner permissions on a subscription
- [预览]: Audit external accounts with owner permissions on a subscription
- [预览]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 删除或调整的访问权限

Azure 实现[基于角色的访问控制](../../../../role-based-access-control/overview.md)(RBAC) 向可帮助你管理 Azure 中拥有资源的访问权限的人员。 使用[Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md)和 RBAC，可以更新用户角色，以反映组织的更改。 需要时，帐户可以阻止登录 （或删除），后者立即删除对 Azure 资源的访问权限。 此蓝图分配两个[Azure 策略](../../../policy/overview.md)定义要审核折旧前应考虑删除的帐户。

- [预览]: Audit deprecated accounts on a subscription
- [预览]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 密码管理系统

此蓝图可帮助你通过将分配 10 强制使用强密码[Azure 策略](../../../policy/overview.md)审核不强制执行最小长度和其他密码要求的 Windows Vm 的定义。 虚拟机中的密码强度策略冲突的感知可帮助你采取纠正措施以确保 VM 的所有用户帐户的密码符合策略。

- [预览]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [预览]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [预览]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [预览]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [预览]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [预览]: Audit Windows VM enforces password complexity requirements
- [预览]: Audit Windows VM maximum password age 70 days
- [预览]: Audit Windows VM minimum password age 1 day
- [预览]: Audit Windows VM passwords must be at least 14 characters
- [预览]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>有关使用的加密控制 A.10.1.1 策略

此蓝图，帮助您强化您的加密控件使用的策略分配 13 [Azure 策略](../../../policy/overview.md)定义来强制实施特定的加密控制和审核使用弱加密设置。
了解 Azure 资源可能加密配置未优化可以帮助您采取纠正措施以确保已根据您的信息安全策略配置的资源。 具体而言，此蓝图分配的策略要求加密的 blob 存储帐户和 data lake 存储帐户;需要对 SQL 数据库; 透明数据加密审核存储帐户、 SQL 数据库、 虚拟机磁盘和自动化帐户的变量; 缺少加密审核不安全连接到存储帐户、 函数应用、 Web 应用、 API 应用和 Redis 缓存中;审核弱的虚拟机密码加密;总结和审核未加密的 Service Fabric 通信。

- [预览]: Audit HTTPS only access for a Function App
- [预览]: Audit HTTPS only access for a Web Application
- [预览]: Audit HTTPS only access for an API App
- [预览]: Audit missing blob encryption for storage accounts
- [预览]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  加密n
- [预览]: Audit Windows VM should not store passwords using reversible encryption
- [预览]: Monitor unencrypted SQL database in Azure Security Center
- [预览]: Monitor unencrypted VM Disks in Azure Security Center
- 审核确认已启用自动化帐户变量加密功能
- 审核确认仅启用了到 Redis 缓存的安全连接
- 审核确认指向存储帐户的传输的安全性
- 审核确认 Service Fabric 中的 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 审核透明数据加密状态

## <a name="a1241-event-logging"></a>A.12.4.1 事件日志记录

此蓝图可帮助你确保系统事件记录通过分配七[Azure 策略](../../../policy/overview.md)审核日志设置 Azure 资源上的定义。 如果虚拟机不会将日志发送到指定的 log analytics 工作区还审核具有分配的策略。

- [预览版]：审核依赖项代理部署的 VM 映像 (OS) 未列出
- [预览版]：审核在 VMSS 的 VM 映像 (OS) 未列出的依赖项代理部署
- [预览版]：审核日志分析代理部署的 VM 映像 (OS) 未列出
- [预览版]：VMSS 的 VM 映像 (OS) 未列出中的审核日志分析代理部署
- [预览]: Monitor unaudited SQL database in Azure Security Center
- 审核诊断设置
- 审核 SQL 服务器级别审核设置

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 管理的技术漏洞

此蓝图可帮助你通过将分配五个管理信息系统漏洞[Azure 策略](../../../policy/overview.md)监视缺少系统更新、 操作系统漏洞、 SQL 漏洞和虚拟机的定义安全漏洞。 这些见解提供有关已部署资源的安全状态的实时信息，可帮助您修正措施的优先级。

- [预览]: Monitor missing Endpoint Protection in Azure Security Center
- [预览]: Monitor missing system updates in Azure Security Center
- [预览]: Monitor OS vulnerabilities in Azure Security Center
- [预览]: Monitor SQL vulnerability assessment results in Azure Security Center
- [预览]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>软件安装的 A.12.6.2 限制

自适应应用程序控制是在 Azure 安全中心可帮助你控制的应用程序可以在位于 Azure 中的 Vm 上运行的解决方案。 此蓝图分配的 Azure 策略定义，用于监视对允许的应用程序集进行更改。 软件安装的限制可以帮助你减少引入的软件漏洞的可能性。

- [预览]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 网络控制

此蓝图可帮助你管理和控制网络通过将分配[Azure 策略](../../../policy/overview.md)监视规则宽松的网络安全组的定义。 规则过于宽松，可能会允许非预期的网络访问权限，并且应进行评审。

- [预览]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 信息传输策略和过程

蓝图可帮助你确保与 Azure 服务的信息传输是安全的通过将分配两个[Azure 策略](../../../policy/overview.md)定义审核不安全连接到存储帐户和 Redis 缓存。

- 审核确认仅启用了到 Redis 缓存的安全连接
- 审核确认指向存储帐户的传输的安全性

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 保护应用程序服务事务

此蓝图可帮助你通过将三个分配保护信息系统资产[Azure 策略](../../../policy/overview.md)监视未受保护的终结点、 应用程序和存储帐户的定义。 终结点和不受防火墙和无限制地访问存储帐户的应用程序可以允许在信息系统内所含的信息进行意外的访问。

- [预览]: Monitor unprotected network endpoints in Azure Security Center
- [预览]: Monitor unprotected web application in Azure Security Center
- 审核对存储帐户的不受限的网络访问

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 报告信息安全漏洞

此蓝图可帮助你通过分配五个保持识别用于系统漏洞[Azure 策略](../../../policy/overview.md)监视漏洞、 修补程序状态和 Azure 安全中心中的恶意软件警报的定义。 Azure 安全中心提供了报表功能，使你能够在已部署的 Azure 资源的安全状态的实时见解。

- [预览]: Monitor missing Endpoint Protection in Azure Security Center
- [预览]: Monitor missing system updates in Azure Security Center
- [预览]: Monitor OS vulnerabilities in Azure Security Center
- [预览]: Monitor SQL vulnerability assessment results in Azure Security Center
- [预览]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>后续步骤

现在，已学习了 ISO 27001 应用服务环境 / SQL 数据库工作负荷蓝图示例控件映射，请访问以下文章，了解有关体系结构以及如何部署此示例的信息：

> [!div class="nextstepaction"]
> [ISO 27001 应用服务环境 / SQL 数据库工作负荷蓝图-概述](./index.md)
> [ISO 27001 应用服务环境 / SQL 数据库工作负荷蓝图-部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。