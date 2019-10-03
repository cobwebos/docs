---
title: 使用企业安全性套餐的 Azure HDInsight 体系结构
description: 了解如何通过企业安全性套餐规划 Azure HDInsight 安全性。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e7983c4da4803965dabaa6a471fbea8a2fba5229
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810941"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>在 HDInsight 中使用企业安全性套餐

标准 Azure HDInsight 群集是单用户群集。 适用于大多数使用小型应用程序团队来构建大数据工作负荷的公司。 每个用户可以按需创建专用群集，并在不再需要时将其销毁。 

许多企业已转向这样一种模型：群集由 IT 团队管理，并由多个应用程序团队共享。 这些较大型企业需要在 Azure HDInsight 中实现对每个群集的多用户访问。

HDInsight 以托管方式依赖于常用的标识提供者 - Active Directory。 通过将 HDInsight 与 [Azure Active Directory 域服务 (Azure AD DS)](../../active-directory-domain-services/overview.md) 相集成，可以使用你的域凭据来访问群集。 

HDInsight 中的虚拟机 (VM) 将加入你提供的域。 因此，在 HDInsight 上运行的所有服务（Apache Ambari、Apache Hive 服务器、Apache Ranger、Apache Spark Thrift 服务器等）都可以为经身份验证的用户无缝运行。 然后，管理员可以使用 Apache Ranger 创建强大的授权策略，以针对群集中的资源提供基于角色的访问控制。

## <a name="integrate-hdinsight-with-active-directory"></a>将 HDInsight 与 Active Directory 集成

开源 Apache Hadoop 依赖于 Kerberos 协议来提供身份验证和安全性。 因此，使用企业安全性套餐 (ESP) 的 HDInsight 群集节点加入到由 Azure AD DS 管理的域。 将为群集上的 Hadoop 组件配置 Kerberos 安全性。 

自动创建以下内容：

- 每个 Hadoop 组件的服务主体
- 加入域的每台计算机的计算机主体
- 每个群集的组织单位 (OU)，用于存储这些服务和计算机主体

概而言之，需要在环境中设置以下项：

- 一个 Active Directory 域（由 Azure AD DS 管理）。 **域名必须39个字符或更少才能使用 Azure HDInsight。**
- 在 Azure AD DS 中启用的安全 LDAP (LDAPS)。
- HDInsight 虚拟网络和 Azure AD DS 虚拟网络之间的正常网络连接（如果为这两者选择不同的虚拟网络）。 HDInsight 虚拟网络中的 VM 应通过虚拟网络对等互连与 Azure AD DS 连接。 如果 HDInsight 和 Azure AD DS 部署在同一虚拟网络中，则会自动提供此连接，不需要执行进一步操作。

## <a name="set-up-different-domain-controllers"></a>设置不同的域控制器
HDInsight 当前仅支持将 Azure AD DS 用作群集用于与 Kerberos 进行通信的主域控制器。 但是，也可以使用其他复杂的 Active Directory 设置，只要该设置能启用 Azure AD DS 进行 HDInsight 访问。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务
[Azure AD DS](../../active-directory-domain-services/overview.md) 提供与 Windows Server Active Directory 完全兼容的托管域。 Microsoft 负责采用高度可用的 (HA) 设置来管理、修补和监视域。 你可以部署群集，而不用担心如何维护域控制器。 

用户、组和密码将从 Azure AD 进行同步。 利用从 Azure AD 实例到 Azure AD DS 的单向同步，用户可以使用相同的企业凭据登录到群集。 

有关详细信息，请参阅[使用 Azure AD DS 配置使用 ESP 的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)。

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>IaaS VM 上的本地 Active Directory 或 Active Directory

如果域具有本地 Active Directory 示例或更复杂的 Active Directory 设置，则可以使用 Azure AD Connect 将这些标识同步到 Azure AD。 然后可在该 Active Directory 租户上启用 Azure AD DS。 

由于 Kerberos 依赖于密码哈希，因此必须[在 Azure AD DS 上启用密码哈希同步](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。 

如果使用 Active Directory 联合身份验证服务 (AD FS) 进行联合身份验证，则必须启用密码哈希同步。（有关建议的设置，请参阅[此视频](https://youtu.be/qQruArbu2Ew)。）密码哈希同步在 AD FS 基础结构失败时可以帮助进行灾难恢复，并且它还有助于提供泄漏凭据保护。 有关详细信息，请参阅[使用 Azure AD Connect 同步启用密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)。 

在 IaaS VM 上单独使用本地 Active Directory 或 Active Directory 而不使用 Azure AD 和 Azure AD DS，这是使用 ESP 的 HDInsight 群集不支持的配置。

如果使用了联合身份验证并且密码哈希已正确同步，但是遇到了身份验证失败，请检查是否为 PowerShell 服务主体启用了云密码身份验证。 如果没有，则必须为你的 Azure AD 租户设置[主领域发现 (HRD) 策略](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)。 若要检查和设置 HRD 策略，请执行以下操作：

1. 安装预览版[Azure AD PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)。

   ```powershell
   Install-Module AzureAD
   ```

2. 使用全局管理员（租户管理员）凭据连接。
   
   ```powershell
   Connect-AzureAD
   ```

3. 检查是否已创建了 Microsoft Azure PowerShell 服务主体。

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. 如果它不存在，请创建服务主体。

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. 创建策略并将其附加到此服务主体。

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId
    
    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>后续步骤

* [配置 ESP HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [为具有 ESP 的 HDInsight 群集配置 Apache Hive 策略](apache-domain-joined-run-hive.md)
* [管理 ESP HDInsight 群集](apache-domain-joined-manage.md) 
