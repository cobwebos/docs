---
title: 创建并在 Azure HDInsight 中配置企业安全性套餐群集
description: 了解如何创建并在 Azure HDInsight 中配置企业安全性套餐群集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 98bd222212d616a5d2c608779c607bb431d184b9
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657332"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>创建并在 Azure HDInsight 中配置企业安全性套餐群集

Azure HDInsight Enterprise Security 包，您可以访问你在 Azure 中的 Apache Hadoop 群集的基于 Active Directory 的身份验证、 多用户支持和基于角色的访问控制。 HDInsight ESP 群集启用遵守严格的企业安全策略来安全地处理敏感数据的组织。

本指南的目标是要正确配置所需的资源，以便在本地用户可以登录到 ESP 启用 HDInsight 群集。 本文将指导完成创建企业安全数据包启用 Azure HDInsight 群集所需的步骤。 这些步骤将包括使用 Active Directory 和域名服务 (DNS) 启用创建的 Windows IaaS VM。 此服务器将充当替换为你**实际**的本地环境，以便可以继续完成安装和配置步骤，以便可以重复这些更高版本中您自己的环境。 本指南还将帮助您创建使用密码哈希同步与 Azure Active Directory 混合标识环境。

本指南旨在补充[在 HDInsight 中使用 Enterprise Security 包](apache-domain-joined-architecture.md)

在您自己的环境中使用此过程之前, 设置 Active Directory 和域名服务 (DNS)。 此外，启用到 Azure Active Directory 的 Azure Active Directory 和同步的本地用户帐户。

![体系结构示意图](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>创建的本地环境

概述：在本部分中，将使用 Azure 快速部署模板以创建新的 Vm，配置域名服务 (DNS) 和新的 AD 林。

1. 转到[创建新的 AD 林的 Azure VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)，若要查看快速部署模板。

1. 单击**部署到 Azure**。
1. 登录到 Azure 订阅。
1. 上**创建新的 AD 林的 Azure VM**屏幕上，完成以下步骤：
    1. 选择要从部署的资源的订阅**订阅**下拉列表。
    1. 选择**新建**旁边**资源组**并输入名称**OnPremADVRG**
    1. 模板字段的其余部分输入以下详细信息：

        * **位置**：美国中部
        * **管理员用户名**：HDIFabrikamAdmin
        * **管理员密码**: < YOUR_PASSWORD >
        * **域**:HDIFabrikam.com
        * **Dns 前缀**: hdifabrikam

        ![模板创建 Azure 虚拟机和 AD 林](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 单击**购买**
    1. 监视部署并等待它完成。
    1. 确认正确的资源组下创建的资源`OnPremADVRG`。

## <a name="configure-users-and-groups-for-cluster-access"></a>配置群集访问权限的用户和组

概述：在本部分中，将创建将有权通过本指南末尾的 HDInsight 群集的用户。

1. 连接到使用远程桌面的域控制器。
    1. 如果使用开头提到了模板，域控制器是一个名为的虚拟机**adVM**中`OnPremADVRG`资源组。
    1. 转到 Azure 门户 >**资源组** > **OnPremADVRG** > **adVM** > **Connect**.
    1. 单击**RDP**选项卡，然后单击**下载 RDP 文件**。
    1. 将文件保存到您的计算机，并将其打开。
    1. 当系统提示输入凭据，使用`HDIFabrikam\HDIFabrikamAdmin`用作用户名并输入管理员帐户所选的密码。

1. 在域控制器 VM 上打开远程桌面会话后, 启动**Active Directory 用户和计算机**从**服务器管理器**仪表板。 单击**工具**右上方，然后**Active Directory 用户和计算机**从下拉列表。

    ![服务器管理器中打开 Active Directory 管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 创建两个新用户**HDIAdmin**， **HDIUser**。 这两位用户将用于登录到 HDInsight 群集。

    1. 在中**Active Directory 用户和计算机**屏幕上，单击**操作** > **新建** > **用户**。

        ![创建新的 Active Directory 用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. 在中**新建对象-用户**屏幕中，输入`HDIUser`作为**用户登录名**然后单击**下一步**。

        ![创建第一个管理员用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. 在显示的弹出窗口，输入新帐户所需的密码。 选中复选框，指出**密码永不过期**。 HDIClick**确定**。
    1. 单击**完成**若要创建新帐户。
    1. 创建另一个用户`HDIAdmin`。

        ![创建第二个管理用户](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. 在中**Active Directory 用户和计算机**屏幕上，单击**操作** > **新建** > **组**。 创建`HDIUserGroup`为一个新组。

    ![创建新的 Active Directory 组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![创建新组 2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. 添加**HDIUser**到在上一步中创建**HDIUserGroup**作为成员。

    1. 右键单击**HDIUserGroup**然后单击**属性**。
    1. 转到**成员**选项卡，单击**添加**。
    1. Enter`HDIUser`在标记为**输入要选择的对象名称**然后单击**确定**。
    1. 为其他帐户重复前面的步骤 `HDIAdmin`

        ![将成员添加到组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

现在已访问 HDInsight 群集创建 Active Directory 环境，以及两个用户和用户组。

这些用户将与 Azure AD 同步。

### <a name="create-a-new-azure-active-directory"></a>创建新的 Azure Active Directory

1. 登录到 Azure 门户。
1. 单击**创建资源**并键入**directory**。 选择**Azure Active Directory** > **创建**。
1. 输入**HDIFabrikam**下**组织名称**。
1. 输入**HDIFabrikamoutlook**下**初始域名**。
1. 单击“创建”。 
1. 在 Azure 门户中左侧，单击**Azure Active Directory**。
1. 如有必要，单击**切换目录**更改为你创建的新目录**HDIFabrikamoutlook**。
1. 下**管理**单击**自定义域名** > **添加自定义域**。
1. 输入**HDIFabrikam.com**下**自定义域名**然后单击**添加域**。

![创建新的 azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![创建新的自定义域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租户

概述：现在，你将配置你的 Azure AD 租户，以便可以同步用户和组从本地 AD 到云。

1. 创建 AD 租户管理员。
    1. 登录到 Azure 门户，选择你的 Azure AD 租户**HDIFabrikam**
    1. 选择**用户**下**管理**，然后**新用户**。
    1. 为新的用户输入以下详细信息：

        * 名称： fabrikamazureadmin
        * 用户名： fabrikamazureadmin@hdifabrikam.com
        * 密码： 所选的一个安全密码

    1. 单击**组**部分中，搜索**AAD DC 管理员**，然后单击**选择**。

        ![组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. 单击**目录角色**部分，并选择**全局管理员**右侧。 单击“确定”  。

        ![目录角色](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. 输入用户密码。 单击“创建”。 

1. 如果你想要更改新创建的用户的密码<fabrikamazureadmin@hdifabrikam.com>。 登录到 Azure 门户中使用的标识，然后将提示你更改密码。

## <a name="sync-on-premises-users-to-azure-ad"></a>本地同步到 Azure AD 用户

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>下载并安装 Microsoft Azure Active Directory 连接

1. [下载 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 安装 Microsoft Azure Active Directory 连接的域控制器上。
    1. 在上一步中打开下载的可执行文件，并同意许可条款。 单击 **“继续”** 。

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. 单击**使用快速设置**并完成安装。

        ![使用快速设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>在本地域控制器与配置同步

1. 上**连接到 Azure AD**屏幕上，输入 Azure AD 的用户名和密码的全局管理员。 单击 **下一步**。 这是用户名`fabrikamazureadmin@hdifabrikam.com`配置你的 AD 租户时创建。
    ![连接到 Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. 上**连接到 Active Directory 域服务**屏幕上，输入企业管理员帐户的用户名和密码。 单击 **下一步**。 这是用户名`HDIFabrikam\HDIFabrikamAdmin`并将其先前创建的匹配密码。

   ![连接到 Active Directory 域服务](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. 上**Azure AD 单一登录配置**页上，单击**下一步**。
    ![Azure AD 单一登录配置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. 在已准备好配置屏幕，单击 **安装**。
    ![安装](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. 当**完成配置**显示屏幕中，单击**退出**。
    ![配置完成](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. 在同步完成后确认是否 IAAS Active Directory 创建的用户同步到 Azure Active Directory。
    1. 登录到 Azure 门户。
    1. 选择**Azure Active Directory** > **HDIFabrikam** > **用户**。

### <a name="create-an-user-assigned-managed-identity"></a>创建用户分配托管的标识

创建将用于配置 Azure Active Directory 域服务 (Azure AD DS) 的用户分配的托管的标识。 创建用户分配的托管的标识的详细信息，请参阅[创建、 列表、 删除或分配到使用 Azure 门户的用户分配的托管标识角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登录到 Azure 门户。
1. 单击**创建资源**并键入**托管标识**。 选择**用户分配的托管的标识** > **创建**。
1. 输入**HDIFabrikamManagedIdentity**作为**资源名称**。
1. 选择订阅。
1. 下**资源组**单击**新建**，然后输入**HDIFabrikam CentralUS**。
1. 选择**美国中部**下**位置**。
1. 单击“创建”。 

![创建新的用户分配托管的标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>启用 Azure Active Directory 域服务

有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

1. 创建虚拟网络来托管 Azure AD DS。 运行以下 powershell 代码。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登录到 Azure 门户。
1. 单击**创建资源**，输入**域服务**，然后选择**Azure AD 域服务**。
1. 上**基础知识**屏幕完成以下步骤：
    1. 下**目录名**选择为本文中，创建的 Azure Active Directory **HDIFabrikam**。
    1. 输入**DNS 域名**的**HDIFabrikam.com**。
    1. 选择订阅。
    1. 指定资源组**HDIFabrikam CentralUS**并**位置**的**美国**。

        ![azure ad ds 基本详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. 上**网络**屏幕上完成，选择的网络 (**HDIFabrikam VNET**) 和子网 (**AADDS 子网**) 与上一个 powershell 脚本创建的。 也可以使用**新建**选项立即创建虚拟网络。

    ![选择网络](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. 上**管理员组**屏幕上，你应看到一条通知，一个组名为**AAD DC 管理员**已创建用于管理此组。 您可以根据需要修改此组的成员身份，但这不是必需的这篇文章的步骤。 单击 **“确定”** 。

    ![查看管理员组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. 上**同步**屏幕上，通过选择启用完成同步**所有**，然后单击**确定**。

    ![启用同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. 上**摘要**屏幕上，验证的 Azure AD DS 的详细信息，请单击**确定**。

    ![验证的详细信息](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. 启用 Azure AD-DS 后，本地域名服务 (DNS) 服务器将在 AD 虚拟机 (VM) 上运行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD DS 虚拟网络

在本部分中的步骤将帮助你配置 Azure AD DS 虚拟网络 (**HDIFabrikam AADDSVNET**) 使用自定义 DNS 服务器。

1. 找到自定义 DNS 服务器的 IP 地址。 单击**HDIFabrikam.com** AD DS 资源中，单击 **属性**下**管理** 看一看下面列出的 IP 地址和 **IP虚拟网络上的地址**。

    ![查找有关 Azure AD DS 的自定义 DNS IP 地址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. 配置**HDIFabrikam AADDSVNET**对自定义 Ip`10.0.0.4`和`10.0.0.5`。

    1. 选择**DNS 服务器** 下 **设置** 类别。 然后单击此单选按钮旁边 **自定义**，在文本框中，输入第一个 IP 地址 (10.0.0.4)，然后单击 **保存**。
    1. 添加其他 IP 地址 (10.0.0.5) 使用相同的步骤。

1. 在我们的方案 Azure AD DS 配置为使用在 AADDS VNet 上的设置相同的 IP 地址 10.0.0.4 和 10.0.0.5，IP 地址，如下图中所示。

    ![查看自定义 dns 服务器](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>安全 LDAP 流量

用于读取和写入到 Active Directory 轻型目录访问协议 (LDAP)。 您可以进行 LDAP 流量机密安全通过使用安全套接字层 (SSL) / 传输层安全性 (TLS) 技术。 您可以通过格式正确的证书安装启用 LDAP over SSL (LDAPS)。

安全 LDAP 的详细信息，请参阅[配置安全 LDAP (LDAPS) 为 Azure AD 域服务托管域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本部分中，创建自签名的证书、 下载的证书并配置安全 LDAP (LDAPS) **hdifabrikam** Azure AD DS 托管域。

以下脚本创建 hdifabrikam 的证书。 将证书保存在路径"LocalMachine"下。

> [!Note] 
> 任何实用程序或应用程序创建有效 PKCS \#10 请求用于形成 SSL 证书请求。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

验证是否在计算机中安装了证书\'s 个人存储区。 完成以下步骤：

1. 启动 Microsoft 管理控制台 (MMC)。
1. 添加证书管理单元，用于管理本地计算机上的证书。
1. 展开 **证书 （本地计算机）** ，展开 **个人**，然后展开 **证书**。 新的证书应存在的个人存储中。 此证书颁发给完全限定的主机名。

    ![验证证书创建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. 在右窗格中，右键单击在上一步中创建的证书，指向 **的所有任务**，然后单击 **导出**。

1. 上 **导出私钥** 页上，单击 **是，导出私钥，** 。 需要加密的消息要读取从计算机将导入密钥的私钥。

    ![导出私钥](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. 上 **导出文件格式** 页上，保留默认设置，然后单击 **下一步**。 
1. 上 **密码** 页上，键入将选择的专用密钥的密码**TripleDES SHA1**有关**加密**单击**下一步**.
1. 上 **导出的文件** 页上，键入的路径和导出的证书文件的名称，然后单击 **下一步**。
1. 必须是.pfx 扩展名的文件的名称，若要建立安全连接的 Azure 门户上配置此文件。
1. 启用安全 LDAP (LDAPS) 的 Azure AD 域服务托管域。
    1. 选择的域**HDIFabrikam.com**从 Azure 门户。
    1. 单击**安全 LDAP**下**管理**。
    1. 上**安全 LDAP**屏幕上，单击**启用**下**安全 LDAP**。
    1. 浏览您的计算机导出的.pfx 证书文件。
    1. 输入证书密码。

    ![启用安全 ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. 现在，启用安全 LDAP，请确保它是可连接通过启用端口 636。
    1. 单击网络安全组**AADDS HDIFabrikam.com NSG**中**HDIFabrikam CentralUS**资源组。
    1. 下**设置**单击**入站安全规则** > **添加**。
    1. 上**添加入站的安全规则**屏幕上，输入以下属性，然后单击**添加**:

        | 属性 | ReplTest1 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | 目标 | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | 优先度 | \<所需的数量\> |
        | 名称 | Port_LDAP_636 |

    ![入站的安全规则](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` 为用户分配托管标识，给托管标识，将启用此标识来读取、 创建、 修改和删除域服务操作启用了 HDInsight 域服务参与者角色。

    ![创建用户分配的托管的标识](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>创建企业安全数据包启用 HDInsight 群集

此步骤需要以下先决条件：

1. 创建新的资源组`HDIFabrikam-WestUS`位置中`West US`。
1. 创建虚拟网络将承载 ESP 启用 HDInsight 群集。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 创建承载 AADDS 虚拟网络之间的对等关系 (`HDIFabrikam-AADDSVNET`)，并且将托管 ESP 的虚拟网络启用 HDInsight 群集 (`HDIFabrikam-HDIVNet`)。 使用以下 powershell 代码来建立这两个虚拟网络对等互连。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 创建新的 Azure 数据湖存储第 2 代帐户， **Hdigen2store**，即配置用户管理标识**HDIFabrikamManagedIdentity**。 有关详细信息创建启用了用户的数据湖存储第 2 代帐户管理的标识，请参阅[使用 Azure 数据湖存储第 2 代与 Azure HDInsight 群集](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

1. 在上安装自定义 DNS **HDIFabrikam AADDSVNET**虚拟网络。
    1. 转到 Azure 门户 >**资源组** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >  **DNS 服务器**。
    1. 选择**自定义**并输入`10.0.0.4`和`10.0.0.5`。
    1. 单击“保存”  。

        ![保存自定义 dns 设置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. 创建新的 ESP 启用 HDInsight Spark 群集。
    1. 单击**自定义 （大小、 设置、 应用）** 。
    2. 第 1 节中输入所需的详细信息**基础知识**。 絋粄**群集类型**是**Spark 2.3 (HDI 3.6)** 并**资源组**是**HDIFabrikam CentralUS**

    1. 在第 2 节**安全性 + 网络**，完成以下步骤：
        1. 单击**启用**下**企业安全性套餐**。
        1. 单击**群集管理员用户**，然后选择**HDIAdmin**以前作为本地管理员用户创建帐户。 单击“选择”  。

        1. 单击**群集访问权限组**，然后选择**HDIUserGroup**。 在将来添加到此组的任何用户将能够访问 HDInsight 群集。

            ![选择群集访问权限组](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. 完成群集配置的其他步骤，并在验证详细信息**群集摘要**。 单击“创建”。 

1. 登录到用于在新创建的群集的 Ambari UI`https://CLUSTERNAME.azurehdinsight.net`通过使用您的管理员用户名称`hdiadmin@hdifabrikam.com`和密码。

    ![登录到 Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. 单击**角色**从群集仪表板。
1. 上**角色**页上，输入组**hdiusergroup**若要将其分配给**群集管理器**下的角色**将角色分配给这些**。

    ![将群集的管理员角色分配给 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. 打开你的 SSH 客户端和群集使用的登录名**hdiuser**您先前创建的本地 Active Directory。

    ![登录到使用 SSH 的群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

如果能够使用此帐户登录，然后已 ESP 群集正确配置与你的本地 active directory 进行同步。

## <a name="next-steps"></a>后续步骤

* [使用企业安全性套餐实现 Apache Hadoop 安全性简介](apache-domain-joined-introduction.md)
