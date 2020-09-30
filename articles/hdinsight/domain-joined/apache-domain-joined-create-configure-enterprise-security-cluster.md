---
title: 创建和配置企业安全性套餐群集 - Azure
description: 了解如何在 Azure HDInsight 中创建和配置企业安全性套餐群集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: c0e35b94b4322d9273e5793c85792eb2bbd34d05
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536063"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中创建和配置企业安全性套餐群集

对于 Azure 中的 Apache Hadoop 群集，你可以通过用于 Azure HDInsight 的企业安全性套餐 (ESP) 来访问基于 Active Directory 的身份验证、多用户支持以及基于角色的访问控制。 遵循严格的企业安全策略的组织可以使用 HDInsight ESP 群集安全地处理敏感数据。

本指南介绍了如何创建启用了 ESP 的 Azure HDInsight 群集。 此外还介绍了如何创建在其上启用了 Active Directory 和域名系统 (DNS) 的 Windows IaaS VM。 可以使用本指南来配置必要的资源，以允许本地用户登录到启用了 ESP 的 HDInsight 群集。

你创建的服务器将充当实际本地环境的替代项。 可以将它用于设置和配置步骤。 稍后你将在自己的环境中重复这些步骤。

还可以参照本指南，通过将密码哈希同步与 Azure Active Directory (Azure AD) 配合使用来创建混合标识环境。 本指南是对[在 HDInsight 中使用 ESP](apache-domain-joined-architecture.md) 的补充。

在你自己的环境中使用此过程之前，请执行以下操作：

* 设置 Active Directory 和 DNS。
* 启用 Azure AD。
* 将本地用户帐户同步到 Azure AD。

![Azure AD 体系结构图](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>创建本地环境

在本部分中，你将使用 Azure 快速入门部署模板来创建新的 VM、配置 DNS 以及添加新的 Active Directory 林。

1. 请转到快速入门部署模板，以便[使用新的 Active Directory 林创建 Azure VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)。

1. 选择“部署到 Azure”。
1. 登录到 Azure 订阅。
1. 在“使用新的 AD 林创建 Azure VM”页中提供以下信息：

    |属性 | 值 |
    |---|---|
    |订阅|选择要在其中部署资源的订阅。|
    |资源组|选择“新建”，并输入名称 `OnPremADVRG`|
    |位置|选择一个位置。|
    |管理员用户名|`HDIFabrikamAdmin`|
    |管理员密码|输入密码。|
    |域名|`HDIFabrikam.com`|
    |DNS 前缀|`hdifabrikam`|

    保留其余默认值。

    ![用于通过新的 Azure AD 林创建 Azure VM 的模板](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. 查看“条款和条件”，然后选择“我同意上述条款和条件”。 
1. 选择“购买”，监视部署并等待其完成。 部署需要大约 30 分钟才能完成。

## <a name="configure-users-and-groups-for-cluster-access"></a>配置用于群集访问的用户和组

在此部分中，你将创建在本指南结束时有权访问 HDInsight 群集的用户。

1. 使用远程桌面连接到域控制器。
    1. 在 Azure 门户中，导航到“资源组” > “OnPremADVRG” > “adVM” > “连接”。
    1. 从“IP 地址”下拉列表中，选择公共 IP 地址。
    1. 选择“下载 RDP 文件”，然后打开该文件。
    1. 使用 `HDIFabrikam\HDIFabrikamAdmin` 作为用户名。
    1. 输入为管理员帐户选择的密码。
    1. 选择“确定” 。

1. 从域控制器的“服务器管理器”仪表板导航到“工具” > “Active Directory 用户和计算机”。  

    ![在“服务器管理器”仪表板上打开“Active Directory 管理”](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 创建两个新用户：**HDIAdmin** 和 **HDIUser**。 这两个用户将登录到 HDInsight 群集。

    1. 在“Active Directory 用户和计算机”页中右键单击“`HDIFabrikam.com`”，然后导航到“新建” > “用户”。  

        ![创建新的 Active Directory 用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 在“新建对象 - 用户”页上，输入 `HDIUser` 作为**名字**和**用户登录名**。 其他字段会自动填充。 然后，选择“下一步”。

        ![创建第一个管理员用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在出现的弹出窗口中，输入新帐户的密码。 选择“密码永不过期”，然后在弹出消息中选择“确定”。
    1. 选择“下一步”，然后选择“完成”以创建新帐户。
    1. 重复上述步骤以创建用户 `HDIAdmin`。

        ![创建另外一个管理员用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 创建全局安全组。

    1. 在“Active Directory 用户和计算机”中右键单击“`HDIFabrikam.com`”，然后导航到“新建” > “组”。  

    1. 在“组名”文本框中输入 `HDIUserGroup`。

    1. 选择“确定” 。

    ![创建新的 Active Directory 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![创建新对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 向 **HDIUserGroup** 中添加成员。

    1. 右键单击“HDIUser”并选择“添加到组...”。 
    1. 在“输入要选择的对象名称”文本框中输入“`HDIUserGroup`”。 接着选择“确定”，然后再在弹出窗口中选择“确定”。 
    1. 针对 **HDIAdmin** 帐户重复前面的步骤。

        ![将成员 HDIUser 添加到组 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

你现在已创建了 Active Directory 环境。 你已添加了可以访问 HDInsight 群集的两个用户和一个用户组。

用户将与 Azure AD 同步。

### <a name="create-an-azure-ad-directory"></a>创建 Azure AD 目录

1. 登录到 Azure 门户。
1. 选择“创建资源”，然后键入 `directory`。 选择“Azure Active Directory” > “创建”
1. 在“组织名称”下，输入 `HDIFabrikam`。
1. 在“初始域名”下，输入 `HDIFabrikamoutlook`。
1. 选择“创建” 。

    ![创建 Azure AD 目录](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>创建自定义域

1. 在新 **Azure Active Directory** 中的“管理”下，选择“自定义域名” 。
1. 选择“+ 添加自定义域”。
1. 在“自定义域名”下输入 `HDIFabrikam.com`，然后选择“添加域”。
1. 然后完成[将 DNS 信息添加到域注册机构](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)的操作。

![创建自定义域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>创建组

1. 在新的 **Azure Active Directory** 中，在“管理”下选择“组” 。
1. 选择“+ 新建组”。
1. 在“组名”文本框中输入 `AAD DC Administrators`。
1. 选择“创建” 。

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租户

现在需要配置你的 Azure AD 租户，以便将本地 Active Directory 实例中的用户和组同步到云。

创建 Active Directory 租户管理员。

1. 登录到 Azure 门户并选择 Azure AD 租户 **HDIFabrikam**。

1. 导航到“管理” > “用户” > “新建用户”。

1. 为新用户输入以下详细信息：

    **标识**

    |属性 |说明 |
    |---|---|
    |用户名|在文本框中输入 `fabrikamazureadmin`。 从域名下拉列表中，选择 `hdifabrikam.com`|
    |名称| 输入 `fabrikamazureadmin`。|

    **密码**
    1. 选择“让我创建密码”。
    1. 输入你选择的安全密码。

    **组和角色**
    1. 选择“已选择 0 个组”。
    1. 选择“AAD DC 管理员”，然后选择“选择”。 

    ![“Azure AD 组”对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. 选择“用户”。
    1. 选择“全局管理员”，然后选择“选择”。

    ![“Azure AD 角色”对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. 选择“创建” 。

1. 然后让新用户登录到 Azure 门户，系统会提示用户更改密码。 在配置 Microsoft Azure Active Directory Connect 之前，需要执行此操作。

## <a name="sync-on-premises-users-to-azure-ad"></a>将本地用户同步到 Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>配置 Microsoft Azure Active Directory Connect

1. 从域控制器下载 [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 打开已下载的可执行文件，同意许可条款。 选择“继续”。

1. 选择“使用快速设置”。

1. 在“连接到 Azure AD”页上，输入 Azure AD 的全局管理员的用户名和密码。 使用在配置 Active Directory 租户时创建的用户名 `fabrikamazureadmin@hdifabrikam.com`。 然后，选择“下一步”。

    !["连接到 Azure A D" 页。](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 在“连接到 Active Directory 域服务”页上，输入企业管理员帐户的用户名和密码。 使用之前创建的用户名 `HDIFabrikam\HDIFabrikamAdmin` 及其密码。 然后，选择“下一步”。

   !["连接到 D D S" 页。](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在“Azure AD 登录配置”页上，选择“下一步”。 
   ![“Azure AD 登录配置”页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. 在“已准备好进行配置”页上，选择“安装” 。

   ![“已准备好进行配置”页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. 在“配置完成”页面上，选择“退出” 。
   ![“配置完成”页面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 完成同步后，确认在 IaaS 目录上创建的用户是否已同步到 Azure AD。
   1. 登录到 Azure 门户。
   1. 选择“Azure Active Directory” > “HDIFabrikam” > “用户”。

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识，你可以使用它来配置 Azure AD 域服务 (Azure AD DS)。 有关详细信息，请参阅[使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登录到 Azure 门户。
1. 选择“创建资源”，然后键入 `managed identity`。 选择“用户分配的托管标识” > “创建”。 
1. 对于“资源名称”，请输入 `HDIFabrikamManagedIdentity`。
1. 选择订阅。
1. 在“资源组”下，选择“新建”，然后输入 `HDIFabrikam-CentralUS`。
1. 在“位置”下，选择“美国中部”。 
1. 选择“创建” 。

![创建新的、用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

按以下步骤启用 Azure AD DS。 有关详细信息，请参阅[通过 Azure 门户启用 Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

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
1. 选择“创建资源”，输入“`Domain services`”，然后选择“Azure AD 域服务” > “创建”。  
1. 在“基本信息”页上执行以下操作：
    1. 在“目录名称”下，选择你已创建的 Azure AD 目录：**HDIFabrikam**。
    1. 对于“DNS 域名”，请输入 *HDIFabrikam.com*。
    1. 选择订阅。
    1. 指定资源组 **HDIFabrikam-CentralUS**。 对于“位置”，请选择“美国中部”。 

        ![Azure AD DS 基本详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. 在“网络”页上，选择通过 PowerShell 脚本创建的网络 (**HDIFabrikam-VNET**) 和子网 (**AADDS-subnet**)。 或者选择“新建”，立即创建虚拟网络。

    ![“创建虚拟网络”步骤](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在“管理员组”页上，应该会显示一条通知，指出已创建名为“AAD DC 管理员”的组来管理此组。 可以根据需要修改此组的成员身份，但在此示例中，你不需要更改它。 选择“确定” 。

    ![查看 Azure AD 管理员组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在“同步”页上，通过选择“所有” > “确定”来启用完全同步。

    ![启用 Azure AD DS 同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. 在“摘要”页上验证 Azure AD DS 的详细信息，然后选择“确定” 。

    ![“启用 Azure AD 域服务”的摘要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

启用 Azure AD DS 后，本地 DNS 服务器将在 Azure AD VM 上运行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD DS 虚拟网络

请通过以下步骤将 Azure AD DS 虚拟网络 (**HDIFabrikam-AADDSVNET**) 配置为使用自定义 DNS 服务器。

1. 找到自定义 DNS 服务器的 IP 地址。
    1. 选择 `HDIFabrikam.com` Azure AD DS 资源。
    1. 在“管理”下，选择“属性” 。
    1. 在“虚拟网络上的 IP 地址”找到此 IP 地址。

    ![查找 Azure AD DS 的自定义 DNS IP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 将 **HDIFabrikam-AADDSVNET** 配置为使用自定义 IP 地址 10.0.0.4 和 10.0.0.5。

    1. 在“设置”下，选择“DNS 服务器”。 
    1. 选择“自定义”。
    1. 在文本框中输入第一个 IP 地址 (10.0.0.4)。
    1. 选择“保存” 。
    1. 重复这些步骤以添加另一个 IP 地址 (10.0.0.5)。

在我们的方案中，我们已将 Azure AD DS 配置为使用 IP 地址 10.0.0.4 和 10.0.0.5，并在 Azure AD DS 虚拟网络上设置同一 IP 地址：

![“自定义 DNS 服务器”页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>保护 LDAP 流量

将使用轻型目录访问协议 (LDAP) 从 Azure Active Directory 读取数据或将数据写入到 Azure Active Directory。 可以通过使用安全套接字层 (SSL) 或传输层安全性 (TLS) 技术，确保 LDAP 流量的机密性和安全性。 可以通过安装正确格式的证书来启用基于 SSL 的 LDAP (LDAPS)。

有关安全 LDAP 的详细信息，请参阅[为 Azure AD DS 托管域配置 LDAP](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本部分中，你将创建一个自签名证书，下载该证书，并为 Azure AD DS 托管域 **HDIFabrikam** 配置 LDAPS。

以下脚本为 **HDIFabrikam** 创建证书。 证书保存在 LocalMachine 路径中。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 可以使用可创建有效公钥加密标准 (PKCS) \#10 请求的任何实用工具或应用程序来生成 TLS/SSL 证书请求。

请验证证书是否安装在计算机的“个人”存储中：

1. 启动 Microsoft 管理控制台 (MMC)。
1. 添加用于在本地计算机上管理证书的“证书”管理单元。
1. 展开“证书(本地计算机)” > “个人” > “证书”。   “个人”存储中应存在一个新证书。 此证书将颁发给完全限定的主机名。

    ![验证本地证书创建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右侧窗格中，右键单击所创建的证书。 指向“所有任务”，然后选择“导出”。

1. 在“导出私钥”页上，选择“是，导出私钥”。  要将密钥导入其中的计算机需要具有私钥才能读取加密的消息。

    ![证书导出向导的“导出私钥”页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 在“导出文件格式”页上，保留默认设置，然后选择“下一步”。
1. 在“密码”页上，键入与私钥相对应的密码。 对于“加密”，请选择“TripleDES-SHA1”。 然后，选择“下一步”。
1. 在“要导出的文件”页上，键入导出的证书文件的路径和名称，然后选择“下一步”。  文件名必须带 .pfx 扩展名。 此文件是在 Azure 门户中配置的，用于建立安全连接。
1. 为 Azure AD DS 托管域启用 LDAPS。
    1. 从 Azure 门户中选择 `HDIFabrikam.com` 域。
    1. 在“管理”下选择“安全 LDAP” 。
    1. 在“安全 LDAP”页上，在“安全 LDAP”下，选择“启用”。  
    1. 在计算机上浏览查找你导出的 .pfx 证书文件。
    1. 输入证书密码。

    ![启用安全 LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 启用 LDAPS 后，请通过启用端口 636 来确保可以访问它。
    1. 在 **HDIFabrikam-CentralUS** 资源组中，选择网络安全组 **AADDS-HDIFabrikam.com-NSG**。
    1. 在“设置”下，选择“入站安全规则” > “添加”。  
    1. 在“添加入站安全规则”页上，输入以下属性，然后选择“添加” ：

        | 属性 | 值 |
        |---|---|
        | Source | 任意 |
        | 源端口范围 | * |
        | 目标 | 任意 |
        | 目标端口范围 | 636 |
        | 协议 | Any |
        | 操作 | Allow |
        | 优先级 | \<Desired number> |
        | 名称 | Port_LDAP_636 |

    ![“添加入站安全规则”对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** 是用户分配的托管标识。 为托管标识启用了 HDInsight 域服务参与者角色，该角色允许此标识读取、创建、修改和删除域服务操作。

![创建用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>创建启用了 ESP 的 HDInsight 群集

此步骤需要的先决条件如下：

1. 在“美国西部”位置创建新的资源组“HDIFabrikam-WestUS”。
1. 创建一个虚拟网络，用于承载启用了 ESP 的 HDInsight 群集。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在承载 Azure AD DS (`HDIFabrikam-AADDSVNET`) 的虚拟网络与将承载启用了 ESP 的 HDInsight 群集 (`HDIFabrikam-HDIVNet`) 的虚拟网络之间创建对等关系。 使用以下 PowerShell 代码将这两个虚拟网络对等互连。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 创建新的名为 **Hdigen2store** 的 Azure Data Lake Storage Gen2 帐户。 为帐户配置用户管理的标识 **HDIFabrikamManagedIdentity**。 有关详细信息，请参阅[将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

1. 在 **HDIFabrikam-AADDSVNET** 虚拟网络上设置自定义 DNS。
    1. 转到 Azure 门户 >“资源组” > “OnPremADVRG” > “HDIFabrikam-AADDSVNET” > “DNS 服务器”。
    1. 选择“自定义”，然后输入 10.0.0.4 和 10.0.0.5。
    1. 选择“保存” 。

        ![保存虚拟网络的自定义 DNS 设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 创建新的启用了 ESP 的 HDInsight Spark 群集。
    1. 选择“自定义(大小、设置、应用)”。
    1. 在“基本信息”（第 1 部分）中输入详细信息。 确保“群集类型”为“Spark 2.3 (HDI 3.6)”。  确保“资源组”为“HDIFabrikam-CentralUS”。 

    1. 对于“安全性 + 网络”（第 2 部分），请填写以下详细信息：
        * 在“企业安全性套餐”下选择“启用” 。
        * 选择“群集管理员用户”，然后选择你以本地管理员用户身份创建的 **HDIAdmin** 帐户。 单击“选择”。
        * 选择“群集访问组” > “HDIUserGroup”。 将来添加到此组中的任何用户都将能够访问 HDInsight 群集。

            ![选择群集访问组 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成其他的群集配置步骤，并验证“群集摘要”上的详细信息。 选择“创建” 。

1. 从 `https://CLUSTERNAME.azurehdinsight.net` 登录到新建群集的 Ambari UI。 使用管理员用户名 `hdiadmin@hdifabrikam.com` 及其密码。

    ![Apache Ambari UI 登录窗口](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 从群集仪表板中选择“角色”。
1. 在“角色”页上，在“向这些项分配角色”下，在“群集管理员”角色旁边输入 hdiusergroup 组。   

    ![将群集管理员角色分配给 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 打开你的安全外壳 (SSH) 客户端并登录到群集。 使用在本地 Active Directory 实例中创建的 **hdiuser**。

    ![使用 SSH 客户端登录到群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果你可以通过此帐户登录，则表明你已将 ESP 群集正确配置为与本地 Active Directory 实例同步。

## <a name="next-steps"></a>后续步骤

阅读[使用 ESP 实现 Apache Hadoop 安全性简介](hdinsight-security-overview.md)。
