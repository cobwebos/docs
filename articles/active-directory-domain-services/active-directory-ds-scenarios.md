---
title: "Azure Active Directory 域服务：部署方案 | Microsoft 文档"
description: "Azure AD 域服务的部署方案"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: fc1aa4d778a6232258470fc9a58a98906c86ec7b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>部署方案和用例
本部分探讨一些可以受益于 Azure Active Directory (AD) 域服务的部署方案和用例。

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>安全、轻松管理 Azure 虚拟机
可以使用 Azure Active Directory 域服务，以简化的方式管理 Azure 虚拟机。 可将 Azure 虚拟机加入托管域，这样，便可以使用企业 AD 凭据登录。 这种方法有助于避免凭据管理所带来的不便，例如，维护每个 Azure 虚拟机上的本地管理员帐户。

还可以使用组策略来管理和保护已加入托管域的服务器虚拟机。 可将所需的安全基准应用到 Azure 虚拟机，根据企业安全指导原则锁定这些虚拟机。 例如，可以使用组策略管理功能来限制可在这些虚拟机上启动的应用程序类型。

![以简化的方式管理 Azure 虚拟机](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

由于服务器和其他基础结构的服役期限已到，Contoso 打算将目前托管在本地的许多应用程序迁移到云中。 其当前 IT 标准强制要求托管企业应用程序的服务器必须加入域，并使用组策略进行管理。 Contoso 的 IT 管理员偏向于将 Azure 中部署的虚拟机加入域，以便简化管理。 因此，管理员和用户可以使用其企业凭据登录到域。 同时，可以根据所需的安全基准，使用组策略配置计算机。 Contoso 不希望在 Azure 中部署、监视和管理域控制器，以保护 Azure 虚拟机。 因此，Azure AD 域服务十分适合此用例。

**部署注意事项**

采用此部署方案时，请考虑以下要点：

* Azure AD 域服务提供的托管域默认提供一个扁平的组织单位 (OU) 结构。 所有已加入域的计算机驻留在一个扁平的 OU 中。 但是，可以选择创建自定义的 OU。
* Azure AD 域服务针对每个用户和计算机容器使用内置 GPO 形式的简单组策略。 可以创建自定义 GPO，并将其目标设为自定义 OU。
* Azure AD 域服务支持基本 AD 计算机对象架构。 无法扩展计算机对象的架构。

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>将使用 LDAP 绑定身份验证的本地应用程序即时转移到 Azure 基础结构服务
![LDAP 绑定](./media/active-directory-domain-services-scenarios/ldap-bind.png)

多年前，Contoso 从一家 ISV 采购了一个本地应用程序。 该应用程序目前已被 ISV 置于维护模式，对于 Contoso 来说，请求对该应用程序进行更改将代价不菲，让人知难而退。 此应用程序有一个基于 Web 的前端，该前端使用 Web 表单收集用户凭据，并向企业 Active Directory 执行 LDAP 绑定来验证用户的身份。 Contoso 希望将此应用程序迁移到 Azure 基础结构服务。 最好的情况是无需进行任何更改，该应用程序就能如常运行。 此外，用户应该能够使用其现有的企业凭据进行身份验证，且无需重新为用户培训，告诉他们如何以不同的方式操作。 换句话说，最终用户应该不知道应用程序的运行位置，并且察觉不到应用程序已迁移。

**部署注意事项**

采用此部署方案时，请考虑以下要点：

* 确保应用程序无需在目录中修改/写入数据。 不支持对 Azure AD 域服务提供的托管域进行 LDAP 写入访问。
* 无法直接针对托管域更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 这些更改会自动同步，并出现在托管域中。

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>将使用 LDAP 读取权限访问目录的本地应用程序即时转移到 Azure 基础结构服务
大约十年前，Contoso 开发了一个本地业务线 (LOB) 应用程序。 此应用程序可感知目录，在设计上可配合 Windows Server AD 使用。 该应用程序使用 LDAP（轻量目录访问协议）从 Active Directory 读取有关用户的信息/属性。 该应用程序不会修改属性，也不会在目录中写入数据。 Contoso 想要将此应用程序迁移到 Azure 基础结构服务，并淘汰目前托管此应用程序的已过时本地硬件。 无法重新编写该应用程序，让其使用现代目录 API，例如基于 REST 的 Azure AD 图形 API。 因此，即时转移选项可以满足需要，因为它可以在不修改代码或重新编写应用程序的情况下，将应用程序迁移到云中运行。

**部署注意事项**

采用此部署方案时，请考虑以下要点：

* 确保应用程序无需在目录中修改/写入数据。 不支持对 Azure AD 域服务提供的托管域进行 LDAP 写入访问。
* 确保应用程序不需要自定义/扩展的 Active Directory 架构。 Azure AD 域服务不支持架构扩展。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>将本地服务或后台程序应用程序迁移到 Azure 基础结构服务
某些应用程序包含多个层，其中有一个层需要对后端层（如数据库层）执行经身份验证的调用。 Active Directory 服务帐户通常用于这些用例。 可将这些应用程序即时转移到 Azure 基础结构服务，使用 Azure AD 域服务来满足这些应用程序的标识需求。 可以选择使用已从本地目录同步到 Azure AD 的同一个服务帐户。 或者，可以先创建自定义 OU，然后在该 OU 中单独创建一个服务帐户来部署此类应用程序。

![使用 WIA 的服务帐户](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso 有一个定制的软件保管库应用程序，其中包含 Web 前端、SQL 服务器和后端 FTP 服务器。 Contoso 使用服务帐户的 Windows 集成身份验证在 FTP 服务器中对 Web 前端进行身份验证。 该 Web 前端设置为以服务帐户的身份运行。 后端服务器配置为通过服务帐户授权访问 Web 前端。 Contoso 不希望在云中部署域控制器虚拟机用于将此应用程序转移到 Azure 基础结构服务。 Contoso 的 IT 管理员可以将托管 Web 前端、SQL 服务器和 FTP 服务器的服务器部署到 Azure 中的虚拟机， 并将这些计算机加入 Azure AD 域服务托管域。 然后，便可以在本地目录中使用同一个服务帐户实现应用身份验证。 此服务帐户已同步到 Azure AD 域服务托管域，并且可供使用。

**部署注意事项**

采用此部署方案时，请考虑以下要点：

* 确保应用程序使用用户名/密码进行身份验证。 Azure AD 域服务不支持基于证书/智能卡的身份验证。
* 无法直接针对托管域更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 这些更改会自动同步，并出现在托管域中。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows Server 远程桌面服务部署
可以使用 Azure AD 域服务向 Azure 中部署的远程桌面服务器提供托管 AD 域服务。

有关此部署方案的详细信息，请参阅如何[将 Azure AD 域服务与 RDS 部署集成](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds)。


## <a name="domain-joined-hdinsight-clusters-preview"></a>已加入域的 HDInsight 群集（预览版）
可以设置已加入启用了 Apache Ranger 的 Azure AD 域服务托管域的 Azure HDInsight 群集。 通过 Apache Ranger 创建并应用 Hive 策略，并允许数据科学家等用户使用基于 ODBC 的工具（如 Excel、Tableau）连接到 Hive。Microsoft 致力于将其他工作负荷（如 HBase、Spark 和 Storm）尽快添加到已加入域的 HDInsight。

有关此部署方案的详细信息，请参阅如何[配置已加入域的 HDInsight 群集](../hdinsight/domain-joined/apache-domain-joined-configure.md)
