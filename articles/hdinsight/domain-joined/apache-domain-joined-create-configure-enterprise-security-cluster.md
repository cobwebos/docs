---
title: 在 Azure HDInsight 中创建和配置企业安全性套餐群集
description: 了解如何在 Azure HDInsight 中创建和配置企业安全性套餐群集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: aed656c65fc70946f7d91cb4354e1c081954e68c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030393"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中创建和配置企业安全性套餐群集

Azure HDInsight 企业安全性套餐（ESP）允许访问 Azure 中的 Apache Hadoop 群集的基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制。 HDInsight ESP 群集使符合严格企业安全策略的组织可以安全地处理敏感数据。

本指南演示如何创建已启用 ESP 的 Azure HDInsight 群集。 它还演示了如何创建在其上启用 Active Directory 和域名系统（DNS）的 Windows IaaS VM。 使用本指南来配置所需的资源，以允许本地用户登录到已启用 ESP 的 HDInsight 群集。

创建的服务器将充当*实际*本地环境的替代项。 将使用它进行设置和配置步骤。 稍后你将在你自己的环境中重复上述步骤。 

本指南还将帮助你通过将密码哈希同步与 Azure Active Directory （Azure AD）一起使用来创建混合标识环境。 本指南补充了[HDInsight 中的使用 ESP](apache-domain-joined-architecture.md)。

在你自己的环境中使用此过程之前：
* 设置 Active Directory 和 DNS。
* 启用 Azure AD。
* 将本地用户帐户同步到 Azure AD。

![Azure AD 体系结构关系图](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>创建本地环境

在本部分中，你将使用 Azure 快速入门部署模板来创建新的 Vm、配置 DNS 并添加新的 Active Directory 林。

1. 请参阅快速入门部署模板，以[使用新的 Active Directory 林创建 AZURE VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)。

1. 选择 "**部署到 Azure**"。
1. 登录到 Azure 订阅。
1. 在 "**创建具有新 AD 林的 AZURE VM** " 页上：
    1. 从 "**订阅**" 下拉列表中，选择要在其中部署资源的订阅。
    1. 在 "**资源组**" 旁边，选择 "**新建**"，然后输入名称*OnPremADVRG*。
    1. 对于其余的模板字段，请输入以下详细信息：

        * **位置**：美国中部
        * **管理员用户名**：HDIFabrikamAdmin
        * **管理员密码**：\<YOUR_PASSWORD >
        * **域名**：HDIFabrikam.com
        * **Dns 前缀**： hdifabrikam

        ![用于创建具有新 Azure AD 林的 Azure VM 的模板](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 选择“购买”。
    1. 监视部署并等待其完成。
    1. 请确保在正确的资源组**OnPremADVRG**下创建资源。

## <a name="configure-users-and-groups-for-cluster-access"></a>配置群集访问的用户和组

在本部分中，你将创建在本指南结束时有权访问 HDInsight 群集的用户。

1. 使用远程桌面连接到域控制器。
    1. 如果你使用了在开头提到的模板，则域控制器是**OnPremADVRG**资源组中名为**adVM**的 VM。
    1. 在 Azure 门户中，选择 "**资源组**"  > **OnPremADVRG**@no__t **"@no__t-** 5**连接**"。
    1. 选择 " **rdp** " 选项卡 >**下载 rdp 文件**。
    1. 将该文件保存到您的计算机并将其打开。
    1. 当系统提示输入凭据时，请使用*HDIFabrikam\HDIFabrikamAdmin*作为用户名。 然后输入为管理员帐户选择的密码。

1. 当远程桌面会话在域控制器 VM 上打开时，在 "**服务器管理器**" 仪表板上，打开 " **Active Directory 用户和计算机**"。 在右上角，选择 "**工具**"  >  "**Active Directory 用户和计算机**"。

    ![在服务器管理器仪表板上，打开 "Active Directory 管理"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 创建两个新用户：**HDIAdmin**和**HDIUser**。 这两个用户将登录到 HDInsight 群集。

    1. 在 " **Active Directory 用户和计算机**" 页上，选择 "**操作**"  > **New** > **用户**。

        ![新建 Active Directory 用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 在 "**新建对象-用户**" 页上，为 "**用户登录名**" 输入*HDIUser*。 然后，选择“下一步”。

        ![创建第一个管理员用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在出现的弹出窗口中，输入新帐户的密码。 选择 "**密码永不过期** > **正常"** 。
    1. 选择 "**完成**" 以创建新帐户。
    1. 创建另一个名为*HDIAdmin*的用户。

        ![创建另一个管理用户对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 在 " **Active Directory 用户和计算机**" 页上，选择 "**操作**"  > **新**的  > **组**。 添加名为*HDIUserGroup*的组。

    ![创建新的 Active Directory 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![创建新的对象](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 将**HDIUser**添加到**HDIUserGroup**作为组成员。

    1. 右键单击**HDIUserGroup** ，然后选择 "**属性**"。
    1. 在“成员”选项卡上，选择“添加”。
    1. 在 "**输入要选择的对象名称**" 框中，输入*HDIUser*。 然后选择“确定”。
    1. 对于**HDIAdmin**帐户，请重复上述步骤。

        ![将成员 HDIUser 添加到组 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

你现在已创建 Active Directory 环境。 你已添加了两个用户和一个可以访问 HDInsight 群集的用户组。

用户将与 Azure AD 同步。

### <a name="create-an-azure-ad-directory"></a>创建 Azure AD 目录

1. 登录到 Azure 门户。
1. 选择 "**创建资源**和类型*目录*"。 选择**Azure Active Directory**@no__t**创建**"。
1. 在 "**组织名称**" 下输入*HDIFabrikam*。
1. 在 "**初始域名**" 下输入*HDIFabrikamoutlook*。
1. 选择“创建”。

    ![创建 Azure AD 目录](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. 在 Azure 门户的左侧，选择 " **Azure Active Directory**"。
1. 如有必要，选择 "**切换目录**" 以更改为新的**HDIFabrikamoutlook**目录。
1. 在 "**管理**" 下，选择 "**自定义域名** > **添加自**定义域"。
1. 在 "**自定义域名**" 下，输入*HDIFabrikam.com* ，然后选择 "**添加域**"。

![创建自定义域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租户

现在，你将配置 Azure AD 租户，以便可以将本地 Active Directory 实例中的用户和组同步到云。

1. 创建 Active Directory 租户管理员。
    1. 登录到 Azure 门户并选择 Azure AD 租户**HDIFabrikam**。
    1. 在 "**管理**" 下，选择 "**用户** >  个**新用户**。
    1. 为新用户输入下列详细信息：
        * **名称**： fabrikamazureadmin
        * **用户名**： fabrikamazureadmin@hdifabrikam.com
        * **密码**：你选择的安全密码

    1. 在 "**组**" 部分，搜索 " **AAD DC 管理员**"，然后单击 "**选择**"。

        !["Azure AD 组" 对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. 打开 "**目录角色**" 部分，然后在右侧选择 "**全局管理员** > **正常"** 。

        !["Azure AD 角色" 对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. 输入用户的密码。 然后选择“创建”。

1. 如果要更改新创建的用户的密码 \< @ no__t >，请使用标识登录到 Azure 门户。 然后，系统将提示你更改密码。

## <a name="sync-on-premises-users-to-azure-ad"></a>将本地用户同步到 Azure AD

### <a name="download-and-install-azure-ad-connect"></a>下载并安装 Azure AD Connect

1. [下载 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 在域控制器上安装 Azure AD Connect。

    1. 打开已下载的可执行文件，并同意许可条款。 选择**继续**。

        !["欢迎使用 Azure AD Connect" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. 选择 "**使用快速设置**" 并完成安装。

        ![Azure AD Connect 快速设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>配置与本地域控制器的同步

1. 在 "**连接到 Azure AD** " 页上，输入 Azure AD 的全局管理员的用户名和密码。 使用在配置 Active Directory 租户时创建的用户名 `fabrikamazureadmin@hdifabrikam.com`。 然后，选择“下一步”。 

    !["连接到 Azure AD" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 在 "**连接到 Active Directory 域服务**" 页上，输入企业管理员帐户的用户名和密码。 使用之前创建的用户名 `HDIFabrikam\HDIFabrikamAdmin` 及其密码。 然后，选择“下一步”。 

   !["连接到 Azure AD" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在 " **Azure AD 登录配置**" 页上，选择 "**下一步**"。
   @no__t 0The "Azure AD 登录配置" 页面 @ no__t-1

1. 在 "已**准备好配置**" 页上，选择 "**安装**"。

   !["已准备好配置" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. 在“配置完成”页面上，选择“退出”。
   @no__t 0The "配置完成" 页 @ no__t-1

1. 完成同步后，确认在 IaaS 目录上创建的用户是否已同步到 Azure AD。
   1. 登录到 Azure 门户。
   1. 选择**Azure Active Directory @no__t**@no__t **-** 3 个**用户**。

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识，你可以使用它来配置 Azure AD 域服务（Azure AD DS）。 有关详细信息，请参阅[使用 Azure 门户创建、列出、删除或向用户分配的托管标识分配角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登录到 Azure 门户。
1. 选择 "**创建资源**" 并键入 "*托管标识*"。 选择 "**用户分配的托管身份** > **创建**"。
1. 对于 "**资源名称**"，请输入*HDIFabrikamManagedIdentity*。
1. 选择订阅。
1. 在 "**资源组**" 下，选择 "**新建**" 并输入*HDIFabrikam-CentralUS*。
1. 在 "**位置**" 下，选择 "**美国中部**"。
1. 选择“创建”。

![创建新的用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

请按照以下步骤启用 Azure AD DS。 有关详细信息，请参阅[使用 Azure 门户启用 AZURE AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

1. 创建虚拟网络以托管 Azure AD DS。 运行以下 PowerShell 代码。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登录到 Azure 门户。
1. 选择 "**创建资源**"，输入*域服务*，然后选择 " **Azure AD 域服务**"。
1. 在 "**基本**信息" 页上：
    1. 在 "**目录名称**" 下，选择创建的 Azure AD 目录：**HDIFabrikam**。
    1. 对于 " **DNS 域名称**"，输入*HDIFabrikam.com*。
    1. 选择订阅。
    1. 指定资源组**HDIFabrikam-CentralUS**。 对于 "**位置**"，请选择 "**美国中部**"。

        ![Azure AD DS 基本详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. 在 "**网络**" 页上，选择使用 PowerShell 脚本创建的网络（**HDIFabrikam**）和子网（**AADDS**）。 或者选择 "**新建**" 以立即创建虚拟网络。

    !["创建虚拟网络" 步骤](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在 "**管理员组**" 页上，你应该会看到一条通知，指出已创建了一个名为 " **AAD DC 管理员**" 的组来管理此组。 如果需要，可以修改此组的成员身份，但在此情况下，不需要更改它。 选择“确定”。

    ![查看 Azure AD 管理员组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在 "**同步**" 页上，通过选择 "**所有** > **正常"** 来启用完成同步。

    ![启用 Azure AD DS 同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. 在 "**摘要**" 页上，验证 Azure AD DS 的详细信息，然后选择 **"确定"** 。

    !["启用 Azure AD 域服务" 的摘要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

启用 Azure AD DS 后，本地 DNS 服务器将在 Azure AD Vm 上运行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD DS 虚拟网络

使用以下步骤将 Azure AD DS 虚拟网络（**HDIFabrikam-AADDSVNET**）配置为使用自定义 DNS 服务器。

1. 找到自定义 DNS 服务器的 IP 地址。 
    1. 选择**HDIFabrikam.com** Azure AD DS 资源。 
    1. 在 "**管理**" 下选择 "**属性**"。 
    1. 在 "**虚拟网络上的 ip 地址**" 下找到 ip 地址。

    ![为 Azure AD DS 查找自定义 DNS IP 地址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 将**HDIFabrikam-AADDSVNET**配置为使用自定义 IP 地址10.0.0.4 和10.0.0.5。

    1. 在 "**设置**" 下，选择 " **DNS 服务器**"。 
    1. 选择“自定义”。
    1. 在文本框中，输入第一个 IP 地址（*10.0.0.4*）。
    1. 选择“保存”。
    1. 重复上述步骤以添加其他 IP 地址（*10.0.0.5*）。

在我们的方案中，我们将 Azure AD DS 配置为使用 IP 地址10.0.0.4 和10.0.0.5，并在 Azure AD DS 虚拟网络上设置相同的 IP 地址：

!["自定义 DNS 服务器" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>保护 LDAP 流量

轻型目录访问协议（LDAP）用于读取和写入 Azure Active Directory。 可以通过使用安全套接字层（SSL）或传输层安全性（TLS）技术，使 LDAP 流量成为机密和安全。 可以通过安装格式正确的证书来启用 LDAP over SSL （LDAPS）。

有关安全 LDAP 的详细信息，请参阅为[AZURE AD DS 托管域配置 LDAPS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本部分中，将创建一个自签名证书，下载证书，并为**HDIFabrikam** Azure AD DS 托管域配置 LDAPS。

以下脚本将为**HDIFabrikam**创建证书。 证书保存在*LocalMachine*路径中。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> 任何创建有效公钥加密标准（PKCS） @no__t 010 请求的实用程序或应用程序都可用于形成 SSL 证书请求。

验证该证书是否已安装在计算机的**个人**存储中：

1. 启动 Microsoft 管理控制台（MMC）。
1. 添加管理本地计算机上的证书的**证书**管理单元。
1. 展开 "**证书（本地计算机）** "  > **个人**@no__t**证书**。 **个人**存储区中应存在新证书。 此证书将颁发给完全限定的主机名。

    ![验证本地证书创建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右侧窗格中，右键单击所创建的证书。 指向 "**所有任务**"，然后选择 "**导出**"。

1. 在 "**导出私钥**" 页上，选择 **"是，导出私钥"** 。 将导入密钥的计算机需要私钥才能读取加密消息。

    ![证书导出向导的 "导出私钥" 页](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 在 "**导出文件格式**" 页上，保留默认设置，然后选择 "**下一步**"。 
1. 在 "**密码**" 页上，键入私钥的密码。 对于 "**加密**"，请选择**TripleDES**。 然后，选择“下一步”。
1. 在 "**要导出的文件**" 页上，键入导出的证书文件的路径和名称，然后选择 "**下一步**"。 文件名必须具有 .pfx 扩展名。 此文件在 Azure 门户中配置为建立安全连接。
1. 为 Azure AD DS 托管域启用 LDAPS。
    1. 在 Azure 门户中，选择域**HDIFabrikam.com**。
    1. 在 "**管理**" 下，选择**安全 LDAP**。
    1. 在**安全 LDAP** "页上的"**安全 LDAP**下，选择 "**启用**"。
    1. 浏览在您的计算机上导出的 .pfx 证书文件。
    1. 输入证书密码。

    ![启用安全 LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 启用 LDAPS 后，请确保可以通过启用端口636来访问它。
    1. 在 " **HDIFabrikam-CentralUS** " 资源组中，选择 "网络安全组**AADDS-HDIFabrikam.com-NSG**"。
    1. 在 "**设置**" 下，选择 "**入站安全规则** > **添加**"。
    1. 在 "**添加入站安全规则**" 页上，输入以下属性，然后选择 "**添加**"：

        | 属性 | ReplTest1 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired number > |
        | 名称 | Port_LDAP_636 |

    !["添加入站安全规则" 对话框](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity**是用户分配的托管标识。 为允许此标识读取、创建、修改和删除域服务操作的托管标识启用 HDInsight 域服务参与者角色。

![创建用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>创建启用了 ESP 的 HDInsight 群集

此步骤要求满足以下先决条件：

1. 在 "**美国西部**" 位置创建新的资源组 " *HDIFabrikam-WestUS* "。
1. 创建一个虚拟网络，该虚拟网络将托管启用了 ESP 的 HDInsight 群集。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在承载 Azure AD DS （`HDIFabrikam-AADDSVNET`）的虚拟网络与将托管启用了 ESP 的 HDInsight 群集的虚拟网络（`HDIFabrikam-HDIVNet`）之间创建一个对等关系。 使用以下 PowerShell 代码对这两个虚拟网络对等互连。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 创建名为**Hdigen2store**的新 Azure Data Lake Storage Gen2 帐户。 用用户管理的标识**HDIFabrikamManagedIdentity**配置帐户。 有关详细信息，请参阅[将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

1. 在**HDIFabrikam-AADDSVNET**虚拟网络上设置自定义 DNS。
    1. 请参阅 Azure 门户 >**资源组**， > **OnPremADVRG**@no__t **-3 @no__t** **DNS 服务器**。
    1. 选择 "**自定义**"，然后输入*10.0.0.4*和*10.0.0.5*。
    1. 选择“保存”。

        ![保存虚拟网络的自定义 DNS 设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 创建新的启用 ESP 的 HDInsight Spark 群集。
    1. 选择 **"自定义（大小、设置、应用）** "。
    2. 输入详细信息 **（第**1 部分）。 确保该**群集类型**为**SPARK 2.3 （HDI 3.6）** 。 确保**资源组**为**HDIFabrikam-CentralUS**。

    1. 对于 "**安全性 + 网络**（第2部分）"，请填写以下详细信息：
        * 在 "**企业安全性套餐**" 下，选择 "**已启用**"。
        * 选择 "**群集管理用户**"，然后选择你作为本地管理员用户创建的**HDIAdmin**帐户。 单击“选择”。
        * 选择 "**群集访问组** > **HDIUserGroup**"。 将来添加到此组中的任何用户都能够访问 HDInsight 群集。

            ![选择群集访问组 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成群集配置的其他步骤，并验证**群集摘要**上的详细信息。 选择“创建”。

1. 在 `https://CLUSTERNAME.azurehdinsight.net` 登录到新创建的群集的 Ambari UI。 使用管理员用户名 `hdiadmin@hdifabrikam.com` 及其密码。

    ![Apache Ambari UI 登录窗口](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 在群集仪表板中，选择 "**角色**"。
1. 在 "**角色**" 页上的 "**将角色分配给这些角色**" 下的 "**群集管理员**" 角色旁边，输入组*hdiusergroup*。 

    ![将群集管理员角色分配给 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 打开安全外壳（SSH）客户端并登录到群集。 使用在本地 Active Directory 实例中创建的**hdiuser** 。

    ![使用 SSH 客户端登录到群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果你可以通过此帐户登录，则已将你的 ESP 群集正确配置为与本地 Active Directory 实例同步。

## <a name="next-steps"></a>后续步骤

阅读[使用 ESP Apache Hadoop 安全性简介](hdinsight-security-overview.md)。
