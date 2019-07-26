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
ms.openlocfilehash: 8da50757182609402ecb035b6f3e92959758ef46
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442292"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中创建和配置企业安全性套餐群集

Azure HDInsight 的企业安全性套餐提供对 Azure 中 Apache Hadoop 群集的基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制的访问权限。 HDInsight ESP 群集可让组织遵守严格的公司安全策略, 从而安全地处理敏感数据。

本指南的目的是正确配置所需的资源, 使本地用户能够登录到已启用 ESP 的 HDInsight 群集。 本文逐步讲解创建已启用企业安全性套餐的 Azure HDInsight 群集所需的步骤。 这些步骤将介绍如何创建 Active Directory 已启用 & 域名服务 (DNS) 的 Windows IaaS VM。 此服务器将作为你**实际**的本地环境的替代, 并使你能够继续完成安装和配置步骤, 以便稍后在你的环境中重复使用。 本指南还将帮助你使用密码哈希同步与 Azure Active Directory 创建混合标识环境。

本指南旨在补充[HDInsight 中的使用企业安全性套餐](apache-domain-joined-architecture.md)

在你自己的环境中使用此过程之前, 请安装 Active Directory 和域名服务 (DNS)。 同时, 启用 Azure Active Directory 和同步本地用户帐户以 Azure Active Directory。

![体系结构示意图](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>创建本地环境

概述：在本部分中, 你将使用 Azure 快速部署模板来创建新的 Vm、配置域名服务 (DNS) 和新的 AD 林。

1. 若要查看快速部署模板, 请参阅[使用新的 AD 林创建 AZURE VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)。

1. 单击 "**部署到 Azure**"。
1. 登录到 Azure 订阅。
1. 在 "**使用新的 AD 林创建 AZURE VM** " 屏幕上, 完成以下步骤:
    1. 从 "**订阅**" 下拉列表中选择要部署的资源所在的订阅。
    1. 选择 "**资源组**" 旁边的 "**新建**", 并输入名称**OnPremADVRG**
    1. 为其余模板字段输入以下详细信息:

        * **位置**：美国中部
        * **管理员用户名**：HDIFabrikamAdmin
        * **管理员密码**: < YOUR_PASSWORD >
        * **域**:HDIFabrikam.com
        * **Dns 前缀**: hdifabrikam

        ![模板创建 Azure VM 和 AD 林](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 单击 "**购买**"
    1. 监视部署并等待其完成。
    1. 确认资源是在正确的资源组`OnPremADVRG`下创建的。

## <a name="configure-users-and-groups-for-cluster-access"></a>配置群集访问的用户和组

概述：在本部分中, 将创建在本指南结束时有权访问 HDInsight 群集的用户。

1. 使用远程桌面连接到域控制器。
    1. 如果你使用了在开头提到的模板, 则域控制器是`OnPremADVRG`资源组中名为**adVM**的 VM。
    1. 请参阅 Azure 门户 > > **资源组** > "**adVM** > "**连接**"。
    1. 单击 " **rdp** " 选项卡, 然后单击 "**下载 RDP 文件**"。
    1. 将该文件保存到您的计算机并将其打开。
    1. 当系统提示输入凭据时`HDIFabrikam\HDIFabrikamAdmin` , 请使用作为用户名, 然后输入为管理员帐户选择的密码。

1. 在域控制器 VM 上打开远程桌面会话后, 从**服务器管理器**仪表板启动**Active Directory 用户和计算机**。 单击右上角的 "**工具**", 然后从下拉列表中**Active Directory "用户和计算机**"。

    ![服务器管理器打开 Active Directory 管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 创建两个新用户: **HDIAdmin**、 **HDIUser**。 这两个用户将用于登录到 HDInsight 群集。

    1. 在 " **Active Directory 用户和计算机**" 屏幕中, 单击 "**操作** > " "**新建** > **用户**"。

        ![新建 Active Directory 用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. 在 "**新建对象-用户**" 屏幕中`HDIUser` , 输入作为 "**用户登录名**", 然后单击 "**下一步**"。

        ![创建第一个管理员用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. 在出现的弹出窗口中, 输入新帐户的所需密码。 选中 "**密码永不过期**" 复选框。 HDIClick**正常**。
    1. 单击 "**完成**" 创建新帐户。
    1. 创建其他用户`HDIAdmin`。

        ![创建第二个管理用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. 在 " **Active Directory 用户和计算机**" 屏幕中, 单击 "**操作** > " "**新建** > **组**"。 创建`HDIUserGroup`为新组。

    ![新建 Active Directory 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![创建新的 group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. 将在上一步中创建的**HDIUser**添加到**HDIUserGroup**作为成员。

    1. 右键单击**HDIUserGroup** , 然后单击 "**属性**"。
    1. 中转到 "**成员**" 选项卡, 然后单击 "**添加**"。
    1. 在`HDIUser`标有 "**输入要选择的对象名称**" 框中输入, 然后单击 **"确定"** 。
    1. 为另一个帐户重复前面的步骤`HDIAdmin`

        ![向组中添加成员](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

你现在已创建了 Active Directory 环境, 以及两个用户和一个用于访问 HDInsight 群集的用户组。

这些用户将与 Azure AD 同步。

### <a name="create-a-new-azure-active-directory"></a>创建新 Azure Active Directory

1. 登录到 Azure 门户。
1. 单击 "**创建资源**和类型**目录**"。 选择**Azure Active Directory** > **创建**。
1. 在 "**组织名称**" 下输入**HDIFabrikam** 。
1. 在 "**初始域名**" 下输入**HDIFabrikamoutlook** 。
1. 单击“创建”。
1. 在 Azure 门户的左侧, 单击 " **Azure Active Directory**"。
1. 如有必要, 请单击 "**切换目录**" 以更改为你创建**HDIFabrikamoutlook**的新目录。
1. 在 "**管理**"**下, 单击**"**自** > 定义域"。
1. 在 "**自定义域名**" 下输入**HDIFabrikam.com** , 并单击 "**添加域**"。

![创建新的 azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![创建新的自定义域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租户

概述：现在, 你将配置 Azure AD 租户, 以便可以将本地 AD 中的用户和组同步到云。

1. 创建 AD 租户管理员。
    1. 登录到 Azure 门户并选择 Azure AD 租户**HDIFabrikam**
    1. 选择 **"** **管理**", 然后选择 "**新建用户**"。
    1. 为新用户输入下列详细信息:

        * 名称: fabrikamazureadmin
        * 用户名:fabrikamazureadmin@hdifabrikam.com
        * 密码: 所选的安全密码

    1. 单击 "**组**" 部分, 搜索 " **AAD DC 管理员**", 然后单击 "**选择**"。

        ![个组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. 单击 "**目录角色**" 部分, 然后选择右侧的 "**全局管理员**"。 单击“确定” 。

        ![目录角色](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. 输入用户的密码。 单击“创建”。

1. 如果要更改新创建的用户<fabrikamazureadmin@hdifabrikam.com>的密码, 则为。 使用标识登录到 Azure 门户, 然后系统会提示更改密码。

## <a name="sync-on-premises-users-to-azure-ad"></a>将本地用户同步到 Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>下载并安装 Microsoft Azure Active Directory 连接

1. [下载 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 在域控制器上安装 Microsoft Azure Active Directory 连接。
    1. 打开你在上一步中下载的可执行文件, 并同意许可条款。 单击 **“继续”** 。

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. 单击 "**使用快速设置**" 并完成安装。

        ![使用快速设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>配置与本地域控制器的同步

1. 在 "**连接到 Azure AD** " 屏幕上, 输入 Azure AD 的全局管理员的用户名和密码。 单击 " **下一步**"。 这是你在`fabrikamazureadmin@hdifabrikam.com`配置 AD 租户时创建的用户名。
    ![连接到 Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. 在 "**连接到 Active Directory 域服务**" 屏幕上, 输入企业管理员帐户的用户名和密码。 单击 " **下一步**"。 这是之前创建`HDIFabrikam\HDIFabrikamAdmin`的用户名和匹配的密码。

   ![连接到 Active Directory 域服务](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. 在 " **Azure AD 登录配置**" 页上, 单击 "**下一步**"。
    ![Azure AD 登录配置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. 在 "已准备好配置" 屏幕上, 单击 " **安装**"。
    ![安装](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. 显示 "**配置完成**" 屏幕时, 单击 "**退出**"。
    ![配置完成](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. 完成同步后, 确认在 IAAS Active Directory 上创建的用户是否同步到 Azure Active Directory。
    1. 登录到 Azure 门户。
    1. 选择**Azure Active Directory** > HDIFabrikam用户 > "。

### <a name="create-an-user-assigned-managed-identity"></a>创建用户分配的托管标识

创建用户分配的托管标识, 该标识将用于配置 Azure Active Directory 域服务 (Azure AD DS)。 有关创建用户分配的托管标识的详细信息, 请参阅[使用 Azure 门户创建、列出、删除或分配用户分配的托管标识的角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登录到 Azure 门户。
1. 单击 "**创建资源**", 然后键入 "**托管标识**"。 选择 "**用户分配的托管标识** > **创建**"。
1. 输入**HDIFabrikamManagedIdentity**作为**资源名称**。
1. 选择订阅。
1. 在 "**资源组**" 下, 单击 "**新建**", 然后输入**HDIFabrikam-CentralUS**。
1. 选择 "**位置**" 下的 "**美国中部**"。
1. 单击“创建”。

![创建新的用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>启用 Azure Active Directory 域服务

有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

1. 创建虚拟网络以承载 Azure AD DS。 运行以下 powershell 代码。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登录到 Azure 门户。
1. 单击 "**创建资源**", 输入**域服务**, 然后选择 " **Azure AD 域服务**"。
1. 在 "**基本**信息" 屏幕上, 完成以下步骤:
    1. 在 "**目录名称**" 下, 选择为**HDIFabrikam**创建的 Azure Active Directory。
    1. 输入**DNS 域名** **HDIFabrikam.com**。
    1. 选择订阅。
    1. 指定资源组**HDIFabrikam-CentralUS**和**美国中部**的**位置**。

        ![azure ad ds 基本详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. 在 "**网络**" 屏幕上, 选择你用之前的 powershell 脚本创建的网络 (**HDIFabrikam**) 和子网 (**AADDS**)。 或者, 你可以使用 "**新建**" 选项立即创建虚拟网络。

    ![选择网络](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. 在 "**管理员" 组**屏幕上, 你应该会看到一条通知, 指出已创建了一个名为 " **AAD DC 管理员**" 的组来管理此组。 您可以选择修改此组的成员身份, 但本文的步骤并不需要这样做。 单击 **“确定”** 。

    ![查看管理员组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. 在 "**同步**" 屏幕上, 选择 "**全部**", 然后单击 **"确定"** 以启用完成同步。

    ![启用同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. 在 "**摘要**" 屏幕上, 验证 AZURE AD-DS 的详细信息, 然后单击 **"确定"** 。

    ![验证详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. 启用 Azure AD-DS 后，本地域名服务 (DNS) 服务器将在 AD 虚拟机 (VM) 上运行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD-DS 虚拟网络

本部分中的步骤将帮助你将 Azure AD-DS 虚拟网络 (**HDIFabrikam-AADDSVNET**) 配置为使用自定义 DNS 服务器。

1. 找到自定义 DNS 服务器的 IP 地址。 单击 " **HDIFabrikam.com** " AD-DS 资源, 单击 "**管理**  " 下的 " **属性**", 并查看 " **虚拟网络上的 ip 地址**" 下列出的 IP 地址。

    ![为 Azure AD-DS 定位自定义 DNS IP 地址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. 将**HDIFabrikam-AADDSVNET**配置为自`10.0.0.4`定义`10.0.0.5`ip 和。

    1. 选择 "  **设置** "类别下的"DNS服务器 "。 然后单击 " **自定义**" 旁边的单选按钮, 在下面的文本框中输入第一个 IP 地址 (10.0.0.4), 然后单击 " **保存**"。
    1. 使用相同的步骤添加其他 IP 地址 (10.0.0.5)。

1. 在方案 Azure AD-DS 配置为使用 IP 地址10.0.0.4 和 10.0.0.5, 在 AADDS VNet 上设置与下图中显示的 IP 地址相同的 IP 地址。

    ![查看自定义 dns 服务器](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>保护 LDAP 流量

轻型目录访问协议 (LDAP) 用于读取和写入 Active Directory。 可以通过使用安全套接字层 (SSL)/传输层安全性 (TLS) 技术, 使 LDAP 流量成为机密和安全。 可以通过安装格式正确的证书来启用 LDAP over SSL (LDAPS)。

有关安全 LDAP 的详细信息, 请参阅为[Azure AD 域服务托管域配置安全 LDAP (LDAPS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本部分中, 将创建一个自签名证书, 下载证书并为**hdifabrikam** Azure AD DS 托管域配置安全 LDAP (LDAPS)。

以下脚本将为 hdifabrikam 创建证书。 证书保存在 "LocalMachine" 路径下。

> [!Note] 
> 任何创建有效 PKCS \#10 请求的实用程序或应用程序都可用于形成 SSL 证书请求。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

验证证书是否已安装在计算机\'的 "个人" 存储区中。 完成以下步骤：

1. 启动 Microsoft 管理控制台 (MMC)。
1. 添加管理本地计算机上的证书的证书管理单元。
1. 展开 " **证书 (本地计算机)** ", 展开 " **个人**", 然后展开 " **证书**"。 个人存储区中应存在新证书。 此证书将颁发给完全限定的主机名。

    ![验证证书创建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. 在右侧窗格中, 右键单击在上一步中创建的证书, 指向 " **所有任务**", 然后单击 " **导出**"。

1. 在 " **导出私钥** " 页上, 单击 **"是, 导出私钥**"。 私钥是从将导入密钥的计算机读取加密消息所必需的。

    ![导出私钥](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. 在 " **导出文件格式** " 页上, 保留默认设置, 然后单击 " **下一步**"。 
1. 在 " **密码** " 页上, 键入私钥的密码, 选择 " **TripleDES** " 作为 "**加密**", 然后单击 "**下一步**"。
1. 在 " **要导出** 的文件" 页上, 键入导出的证书文件的路径和名称, 然后单击 " **下一步**"。
1. 文件名必须是 .pfx 扩展名, 此文件在 Azure 门户上配置为建立安全连接。
1. 为 Azure AD 域服务托管域启用安全 LDAP (LDAPS)。
    1. 从 Azure 门户中选择域**HDIFabrikam.com** 。
    1. 单击 "**管理**" 下的**安全 LDAP** 。
    1. 在**安全 LDAP**屏幕上, 单击**安全 LDAP**下的 "**启用**"。
    1. 浏览在您的计算机上导出的 .pfx 证书文件。
    1. 输入证书密码。

    ![启用安全 ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. 现在, 你已启用安全 LDAP, 请通过启用端口636确保可以访问它。
    1. 单击**HDIFabrikam-CentralUS**资源组中的网络安全组**AADDS-HDIFabrikam.com-NSG** 。
    1. 在 "**设置**" 下单击 "**入站安全规则** >  **"。**
    1. 在 "**添加入站安全规则**" 屏幕上, 输入以下属性, 然后单击 "**添加**":

        | 属性 | ReplTest1 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | 目标 | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<所需数量\> |
        | 姓名 | Port_LDAP_636 |

    ![入站安全规则](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`是用户分配的托管标识, HDInsight 域服务参与者角色已启用到托管标识, 这将使此标识能够读取、创建、修改和删除域服务操作。

    ![创建用户分配的托管标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>正在创建启用企业安全性套餐的 HDInsight 群集

此步骤需要以下先决条件:

1. 在位置`HDIFabrikam-WestUS` `West US`创建新的资源组。
1. 创建一个将托管已启用 ESP 的 HDInsight 群集的虚拟网络。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在托管 AADDS 的虚拟网络 (`HDIFabrikam-AADDSVNET`) 和将承载已启用 ESP 的 HDInsight 群集的虚拟网络之间创建对等关系 (`HDIFabrikam-HDIVNet`)。 使用以下 powershell 代码对这两个虚拟网络对等互连。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 创建新的 Azure Data Lake Storage Gen2 帐户**Hdigen2store**, 该帐户配置有用户托管的标识**HDIFabrikamManagedIdentity**。 有关创建使用用户管理标识启用 Data Lake Storage Gen2 帐户的详细信息, 请参阅[将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

1. 在**HDIFabrikam-AADDSVNET**虚拟网络上设置自定义 DNS。
    1. 请参阅 Azure 门户 >**资源组** > "**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS 服务器**"。
    1. 选择 "**自定义**" `10.0.0.5`, 然后输入`10.0.0.4`和。
    1. 单击“保存” 。

        ![保存自定义 dns 设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. 创建新的启用 ESP 的 HDInsight Spark 群集。
    1. 单击 "**自定义 (大小、设置、应用)** "。
    2. 输入所需的详细信息 (**第1部分**)。 确保该**群集类型**为**SPARK 2.3 (HDI 3.6)** , 并且**资源组**为**HDIFabrikam-CentralUS**

    1. 在第2部分的 "安全" 和 "**网络**" 下, 完成以下步骤:
        1. 单击 "**企业安全性套餐**" 下的 "**启用**"。
        1. 单击 "**群集管理用户**", 然后选择之前创建的**HDIAdmin**帐户作为本地管理员用户。 单击“选择”。

        1. 单击 "**群集访问组**", 然后选择 " **HDIUserGroup**"。 将来添加到此组中的任何用户都能够访问 HDInsight 群集。

            ![选择群集访问组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. 完成群集配置的其他步骤, 并验证**群集摘要**上的详细信息。 单击“创建”。

1. 使用管理员`https://CLUSTERNAME.azurehdinsight.net` `hdiadmin@hdifabrikam.com`用户名和密码登录到新创建的群集的 Ambari UI。

    ![登录到 Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. 单击群集仪表板中的 "**角色**"。
1. 在 "**角色**" 页上, 在 "**将角色分配给这些**组" 下, 输入**Hdiusergroup**将其分配给**群集管理员**角色。

    ![将群集管理员角色分配给 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. 打开 SSH 客户端, 并使用之前在本地 Active Directory 中创建的**hdiuser**登录到群集。

    ![通过 SSH 登录到群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

如果你可以使用此帐户登录, 则已将 ESP 群集正确地配置为与本地 active directory 同步。

## <a name="next-steps"></a>后续步骤

* [使用企业安全性套餐实现 Apache Hadoop 安全性简介](hdinsight-security-overview.md)
