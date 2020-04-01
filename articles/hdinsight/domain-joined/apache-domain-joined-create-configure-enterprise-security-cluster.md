---
title: 创建、配置企业安全包群集 - Azure
description: 了解如何在 Azure HDInsight 中创建和配置企业安全包群集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: fb3484d013314897ea2e9157b642d8f2b85dcd60
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437634"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中创建和配置企业安全包群集

Azure HDInsight 的企业安全包 （ESP） 允许您访问 Azure 中的 Apache Hadoop 群集的基于 Active 目录的身份验证、多用户支持和基于角色的访问控制。 HDInsight ESP 群集使遵守严格公司安全策略的组织能够安全地处理敏感数据。

本指南演示如何创建启用 ESP 的 Azure HDInsight 群集。 它还演示如何创建启用活动目录和域名系统 （DNS） 的 Windows IaaS VM。 使用本指南配置必要的资源，允许本地用户登录到启用 ESP 的 HDInsight 群集。

您创建的服务器将充当*实际*本地环境的替换。 您将使用它进行设置和配置步骤。 稍后，您将在您自己的环境中重复这些步骤。

本指南还将通过使用密码哈希与 Azure 活动目录 （Azure AD） 同步来帮助您创建混合标识环境。 本指南补充了[HDInsight 中的 ESP](apache-domain-joined-architecture.md)使用 。

在您自己的环境中使用此过程之前：

* 设置活动目录和 DNS。
* 启用 Azure AD。
* 将本地用户帐户同步到 Azure AD。

![Azure AD 体系结构图](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>创建本地环境

在本节中，您将使用 Azure 快速入门部署模板创建新 VM、配置 DNS 以及添加新的 Active Directory 林。

1. 转到快速入门部署模板，[使用新的活动目录林创建 Azure VM。](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. 选择 **"部署到 Azure**"。
1. 登录到 Azure 订阅。
1. 在 **"使用新的 AD 林"页上创建 Azure VM**时，提供以下信息：

    |properties | 值 |
    |---|---|
    |订阅|选择要部署资源的订阅。|
    |资源组|选择 **"创建新**"，然后输入名称`OnPremADVRG`|
    |位置|选择一个位置。|
    |管理用户名|`HDIFabrikamAdmin`|
    |管理员密码|输入密码。|
    |域名|`HDIFabrikam.com`|
    |Dns 前缀|`hdifabrikam`|

    保留其余默认值。

    ![使用新的 Azure AD 林林创建 Azure VM 的模板](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. 查看**条款和条件**，然后选择**我同意上述条款和条件**。
1. 选择 **"购买**"，然后监视部署并等待它完成。 部署大约需要 30 分钟才能完成。

## <a name="configure-users-and-groups-for-cluster-access"></a>为群集访问配置用户和组

在本节中，您将在本指南结束时创建有权访问 HDInsight 群集的用户。

1. 使用远程桌面连接到域控制器。
    1. 从 Azure 门户导航到**资源组** > **PremADVRG** > **adVM** > **连接**。
    1. 从**IP 地址**下拉列表中，选择公共 IP 地址。
    1. 选择 **"下载 RDP 文件**"，然后打开该文件。
    1. 用作`HDIFabrikam\HDIFabrikamAdmin`用户名。
    1. 输入您为管理员帐户选择的密码。
    1. 选择“确定”  。

1. 从域控制器**服务器管理器**仪表板，导航到**工具** > **活动目录用户和计算机**。

    ![在服务器管理器仪表板上，打开活动目录管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 创建两个新用户 **：HDIAdmin**和**HDIUser**。 这两个用户将登录到 HDInsight 群集。

    1. 从 **"活动目录用户和计算机"** 页面，右键`HDIFabrikam.com`单击 ，然后导航**到"新** > **用户**"。

        ![创建新的活动目录用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 在新**对象 - 用户**页上，输入`HDIUser`**名字**和**用户登录名**。 其他字段将自动填充。 然后选择 **"下一步**"。

        ![创建第一个管理员用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在显示的弹出窗口中，输入新帐户的密码。 选择**密码永远不会过期**，然后在弹出消息**确定**。
    1. 选择 **"下一步**"，然后**完成**以创建新帐户。
    1. 重复上述步骤以创建用户`HDIAdmin`。

        ![创建第二个管理员用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 创建全局安全组。

    1. 从**活动目录用户和计算机**，右键单击`HDIFabrikam.com`，然后导航到**新** > **组**。

    1. 在`HDIUserGroup`**"组名称**文本"框中输入。

    1. 选择“确定”  。

    ![创建新的活动目录组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![创建新对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 将成员添加到**HDIUserGroup**。

    1. 右键单击**HDIUser**并选择"**添加到组..."。**
    1. 在 **"输入对象名称以选择**文本"框中，输入`HDIUserGroup`。 然后在弹出窗口中选择 **"确定**"，然后再次**选择"确定**"。
    1. 对**HDIAdmin**帐户重复上述步骤。

        ![将成员 HDIUser 添加到组 HDIUser 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

现在，您已经创建了活动目录环境。 您添加了两个用户和一个可以访问 HDInsight 群集的用户组。

用户将与 Azure AD 同步。

### <a name="create-an-azure-ad-directory"></a>创建 Azure AD 目录

1. 登录到 Azure 门户。
1. 选择 **"创建资源和**类型`directory`"。 选择**Azure 活动目录** > **创建**。
1. 在 **"组织名称"** 下，输入`HDIFabrikam`。
1. 在**初始域名**下，输入`HDIFabrikamoutlook`。
1. 选择“创建”  。

    ![创建 Azure AD 目录](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>创建自定义域

1. 从新的**Azure 活动目录**中，**在"管理**"下，选择**自定义域名**。
1. 选择 **= 添加自定义域**。
1. 在 **"自定义域名**"下`HDIFabrikam.com`，输入 ，然后选择 **"添加域**"。
1. 然后完成[将 DNS 信息添加到域注册商](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)。

![创建自定义域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>创建组

1. 从新的**Azure 活动目录**中，**在"管理**"下，选择**组**。
1. 选择 **= 新组**。
1. 在**组名**文本框中，输入`AAD DC Administrators`。
1. 选择“创建”  。

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租户

现在，您将配置 Azure AD 租户，以便可以将用户和组从本地活动目录实例同步到云。

创建活动目录租户管理员。

1. 登录到 Azure 门户并选择 Azure AD 租户**HDIFabrikam**。

1. 导航到**管理** > **用户** > **新用户**。

1. 为新用户输入以下详细信息：

    **标识**

    |properties |说明 |
    |---|---|
    |用户名|在文本框中输入 `fabrikamazureadmin`。 从域名下拉列表中，选择`hdifabrikam.com`|
    |名称| 输入 `fabrikamazureadmin`。|

    **密码**
    1. 选择 **"让我创建密码**"。
    1. 输入您选择的安全密码。

    **组和角色**
    1. 选择**选定的 0 个组**。
    1. 选择**AAD DC 管理员**，然后**选择**。

    ![Azure AD 组对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. 选择**用户**。
    1. 选择**全局管理员**，然后**选择**。

    ![Azure AD 角色对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. 选择“创建”  。

1. 然后让新用户登录到 Azure 门户，提示其更改密码。 在配置 Microsoft Azure 活动目录连接之前，您需要执行此操作。

## <a name="sync-on-premises-users-to-azure-ad"></a>将本地用户同步到 Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>配置 Microsoft Azure 活动目录连接

1. 从域控制器下载[Microsoft Azure 活动目录连接](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 打开您下载的可执行文件，并同意许可条款。 选择“继续”****。

1. 选择 **"使用快速设置**"。

1. 在"**连接到 Azure AD"** 页上，输入 Azure AD 全局管理员的用户名和密码。 使用配置活动`fabrikamazureadmin@hdifabrikam.com`目录租户时创建的用户名。 然后选择 **"下一步**"。

    !["连接到 Azure AD"页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 在 **"连接到活动目录域服务**"页上，输入企业管理员帐户的用户名和密码。 使用您之前`HDIFabrikam\HDIFabrikamAdmin`创建的用户名及其密码。 然后选择 **"下一步**"。

   !["连接到 Azure AD"页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在**Azure AD 登录配置**页上，选择 **"下一步**"。
   !["Azure AD 登录配置"页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. 在 **"准备配置"** 页上，选择 **"安装**"。

   !["准备配置"页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. 在“配置完成”页面上，选择“退出”********。
   !["配置完成"页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 同步完成后，确认您在 IaaS 目录中创建的用户已同步到 Azure AD。
   1. 登录到 Azure 门户。
   1. 选择**Azure 活动目录** > **HDIFabrikam** > **用户**。

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识，可用于配置 Azure AD 域服务 （Azure AD DS）。 有关详细信息，请参阅使用[Azure 门户创建、列出、删除或将角色分配给用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登录到 Azure 门户。
1. 选择 **"创建资源和**类型`managed identity`"。 选择**用户分配的托管标识** > **创建**。
1. 对于**资源名称**，请输入`HDIFabrikamManagedIdentity`。
1. 选择订阅。
1. 在 **"资源组**"下，选择 **"新建"** 并输入`HDIFabrikam-CentralUS`。
1. 在 **"位置**"下，选择**美国中部**。
1. 选择“创建”  。

![创建新的用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

按照以下步骤启用 Azure AD DS。 有关详细信息，请参阅使用[Azure 门户启用 Azure AD DS。](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)

1. 创建虚拟网络以承载 Azure AD DS。 运行以下 PowerShell 代码。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登录到 Azure 门户。
1. 选择 **"创建资源**"，输入`Domain services`，然后选择**Azure AD 域服务** > **创建**。
1. 在**基础知识**页上：
    1. 在**目录名称**下，选择您创建的 Azure AD 目录 **：HDIFabrikam**。
    1. 对于**DNS 域名**，请输入*HDIFabrikam.com*。
    1. 选择订阅。
    1. 指定资源组**HDIFabrikam-中央美国**。 对于**位置**，请选择**美国中部**。

        ![Azure AD DS 基本详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. 在 **"网络"** 页上，选择使用 PowerShell 脚本创建的网络 **（HDIFabrikam-VNET）** 和子**网 （AADDS 子网**）。 或者选择 **"新建**"以立即创建虚拟网络。

    !["创建虚拟网络"步骤](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在 **"管理员"组**页上，您应该会看到一条通知，指出已创建名为**AAD DC 管理员的**组来管理此组。 如果需要，可以修改此组的成员身份，但在这种情况下，您无需更改它。 选择“确定”  。

    ![查看 Azure AD 管理员组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在 **"同步**"页上，通过选择 **"全部** > **确定**"来启用完全同步。

    ![启用 Azure AD DS 同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. 在 **"摘要"** 页上，验证 Azure AD DS 的详细信息，然后选择 **"确定**"。

    !["启用 Azure AD 域服务"的摘要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

启用 Azure AD DS 后，本地 DNS 服务器在 Azure AD VM 上运行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD DS 虚拟网络

使用以下步骤配置 Azure AD DS 虚拟网络 **（HDIFabrikam-AADDSVNET）** 以使用自定义 DNS 服务器。

1. 查找自定义 DNS 服务器的 IP 地址。
    1. 选择`HDIFabrikam.com`Azure AD DS 资源。
    1. 在“管理”下，选择“属性”********。
    1. 在**虚拟网络上的 IP 地址**下查找 IP 地址。

    ![查找 Azure AD DS 的自定义 DNS IP 地址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 配置**HDIFabrikam-AADDSVNET**以使用自定义 IP 地址 10.0.0.4 和 10.0.0.5。

    1. 在 **"设置"** 下，选择**DNS 服务器**。
    1. 选择**自定义**。
    1. 在文本框中，输入第一个 IP 地址 *（10.0.0.4*）。
    1. 选择“保存”。 
    1. 重复这些步骤以添加其他 IP 地址 *（10.0.0.5*）。

在我们的方案中，我们将 Azure AD DS 配置为使用 IP 地址 10.0.0.4 和 10.0.0.5，在 Azure AD DS 虚拟网络上设置相同的 IP 地址：

![自定义 DNS 服务器页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>保护 LDAP 流量

轻量级目录访问协议 （LDAP） 用于读取和写入 Azure 活动目录。 通过使用安全套接字层 （SSL） 或传输层安全 （TLS） 技术，可以使 LDAP 流量保密和安全。 您可以通过安装格式正确的证书通过 SSL （LDAPS） 启用 LDAP。

有关安全 LDAP 的详细信息，请参阅[为 Azure AD DS 托管域配置 LDAPS。](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)

在本节中，您将创建自签名证书、下载证书并为**HDIFabrikam** Azure AD DS 托管域配置 LDAPS。

以下脚本为**HDIFabrikam**创建证书。 证书保存在*本地计算机*路径中。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 任何创建有效的公钥加密标准 （PKCS） \#10 请求的实用程序或应用程序都可用于形成 TLS/SSL 证书请求。

验证证书是否安装在计算机**的个人**存储中：

1. 启动微软管理控制台 （MMC）。
1. 添加在本地计算机上管理证书的**证书**管理单元。
1. 扩展**证书（本地计算机）** > **个人** > **证书**。 **个人**存储中应存在新证书。 此证书颁发给完全限定的主机名。

    ![验证本地证书创建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右侧的窗格中，右键单击您创建的证书。 指向**所有任务**，然后选择 **"导出**"。

1. 在 **"导出私钥**"页上，选择 **"是"，导出私钥**。 将导入密钥的计算机需要私钥来读取加密的消息。

    ![证书导出向导的导出私钥页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 在 **"导出文件格式"** 页上，保留默认设置，然后选择 **"下一步**"。
1. 在 **"密码"** 页上，键入私钥的密码。 对于**加密**，请选择**三重DES-SHA1**。 然后选择 **"下一步**"。
1. 在"**文件导出"** 页上，键入导出的证书文件的路径和名称，然后选择 **"下一步**"。 文件名必须具有 .pfx 扩展名。 此文件在 Azure 门户中配置为建立安全连接。
1. 为 Azure AD DS 托管域启用 LDAPS。
    1. 在 Azure 门户中，选择`HDIFabrikam.com`域 。
    1. 在 **"管理**"下，选择 **"安全 LDAP"。**
    1. 在 **"安全 LDAP"** 页上，**在"安全 LDAP**"下，选择**启用**。
    1. 浏览您在计算机上导出的 .pfx 证书文件。
    1. 输入证书密码。

    ![启用安全 LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 现在，您已经启用了 LDAPS，通过启用端口 636 确保可以访问的。
    1. 在**HDIFabrikam-CentralUS**资源组中，选择网络安全组**AADDS-HDIFabrikam.com-NSG**。
    1. 在 **"设置"** 下，选择 **"入站安全规则** > **添加**"。
    1. 在 **"添加入站安全规则"** 页上，输入以下属性，然后选择 **"添加**：

        | properties | 值 |
        |---|---|
        | 源 | Any |
        | 源端口范围 | * |
        | 目标 | Any |
        | 目标端口范围 | 636 |
        | 协议 | Any |
        | 操作 | Allow |
        | Priority | \<所需数字> |
        | 名称 | Port_LDAP_636 |

    !["添加入站安全规则"对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikam 托管标识**是用户分配的托管标识。 为托管标识启用 HDInsight 域服务参与者角色，该标识将允许读取、创建、修改和删除域服务操作。

![创建用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>创建启用 ESP 的 HDInsight 群集

此步骤需要以下先决条件：

1. **在美国西部**的地方创建新的资源组*HDIFabrikam-WestUS* 。
1. 创建将承载支持 ESP 的 HDInsight 群集的虚拟网络。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在承载 Azure AD DS （`HDIFabrikam-AADDSVNET`） 的虚拟网络与将承载启用 ESP 的 HDInsight 群集`HDIFabrikam-HDIVNet`（） 的虚拟网络之间创建对等关系。 使用以下 PowerShell 代码对两个虚拟网络进行对等。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 创建新的 Azure 数据存储湖存储 Gen2 帐户，称为**Hdigen2 存储**。 使用用户托管的身份**HDIFabrikam 管理标识**配置帐户。 有关详细信息，请参阅将[Azure 数据存储湖存储第 2 代与 Azure HDInsight 群集结合](../hdinsight-hadoop-use-data-lake-storage-gen2.md)使用。

1. 在**HDIFabrikam-AADDSVNET**虚拟网络上设置自定义 DNS。
    1. 转到 Azure 门户>**资源组** > **，在 PremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS 服务器**上。
    1. 选择 **"自定义"** 并输入*10.0.0.4*和*10.0.0.5*。
    1. 选择“保存”。 

        ![为虚拟网络保存自定义 DNS 设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 创建新的启用 ESP 的 HDInsight Spark 群集。
    1. 选择 **"自定义"（大小、设置、应用）。**
    1. 输入**基础知识**的详细信息（第 1 节）。 确保**群集类型**为**Spark 2.3 （HDI 3.6）。** 确保**资源组**为**HDIFabrikam-中央美国**。

    1. 对于**安全与网络**（第 2 节），请填写以下详细信息：
        * 在 **"企业安全包**"下，选择 **"已启用**"。
        * 选择**群集管理员用户**并选择您创建的本地管理员用户的**HDIAdmin**帐户。 单击“选择”。
        * 选择**群集访问组** > **HDIUser 组**。 将来添加到此组的任何用户都将能够访问 HDInsight 群集。

            ![选择群集访问组 HDIUser 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成群集配置的其他步骤，并验证**群集摘要**上的详细信息。 选择“创建”  。

1. 登录到 在 中`https://CLUSTERNAME.azurehdinsight.net`新创建的群集的 Ambari UI。 使用管理员用户名`hdiadmin@hdifabrikam.com`及其密码。

    ![阿帕奇 Ambari UI 登录窗口](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 在群集仪表板中，选择 **"角色**"。
1. 在 **"角色"** 页上，**在"将角色分配给这些**角色"下，在**群集管理员**角色旁边输入组*hdiuser 组*。 

    ![将群集管理员角色分配给 hdiuser 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 打开安全外壳 （SSH） 客户端并登录到群集。 使用您在本地活动目录实例中创建的**hdiuser。**

    ![使用 SSH 客户端登录到群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果可以使用此帐户登录，则已正确配置 ESP 群集以与本地活动目录实例同步。

## <a name="next-steps"></a>后续步骤

阅读[一本关于使用 ESP 的 Apache Hadoop 安全性的介绍](hdinsight-security-overview.md)。
