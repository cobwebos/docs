---
title: "配置已加入域的 HDInsight 群集 | Microsoft Docs"
description: "了解如何安装并配置已加入域的 HDInsight 群集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 424ee513afce6ab689c8804594754b1b49234754
ms.lasthandoff: 03/25/2017


---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>配置已加入域的 HDInsight 群集（预览版）
了解如何使用 Azure Active Directory (Azure AD) 和 [Apache Ranger](http://hortonworks.com/apache/ranger/) 安装 Azure HDInsight 群集，以便利用强身份验证和丰富的基于角色的访问控制 (RBAC) 策略。  仅可在基于 Linux 的群集上配置已加入域的 HDInsight。 有关详细信息，请参阅[引入已加入域的 HDInsight 群集](hdinsight-domain-joined-introduction.md)。

本文是本系列的第一个教程：

* 启用 Apache Ranger，创建通过 Active Directory 域服务功能连接到 Azure AD 的 HDInsight 群集。
* 通过 Apache Ranger 创建并应用 Hive 策略，并允许数据科学家等用户使用基于 ODBC 的工具（如 Excel、Tableau）连接到 Hive。Microsoft 致力于将其他工作负荷（如 HBase、Spark 和 Storm）尽快添加到已加入域的 HDInsight。

最终拓扑的示例如下所示：

![已加入域的 HDInsight 拓扑](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

由于 Azure AD 当前仅支持经典虚拟网络 (Vnet)，并且基于 Linux 的 HDInsight 群集仅支持基于 Azure Resource Manager 的 Vnet，因此 HDInsight Azure AD 集成需要两个 Vnet 以及两者间的对等互连。 有关两个部署模型之间的比较信息，请参阅 [Azure Resource Manager 与经典部署：了解资源的部署模型和状态](../azure-resource-manager/resource-manager-deployment-model.md)。 两个 VNet 必须与 Azure AD DS 位于同一区域。

Azure 服务名称必须全局唯一。 本教程涉及以下名称。 Contoso 是虚构的名称。 完成本教程的过程中，必须使用其他名称替代 *contoso*。 

**名称：**

| 属性 | 值 |
| --- | --- |
| Azure AD VNet |contosoaadvnet |
| Azure AD Vnet 资源组 |contosoaadrg |
| Azure AD 虚拟机 (VM) |contosoaadadmin. 此 VM 用于配置组织单位和反向 DNS 区域。 |
| Azure AD 目录 |contosoaaddirectory |
| Azure AD 域名 |contoso (contoso.onmicrosoft.com) |
| HDInsight VNet |contosohdivnet |
| HDInsight VNet 资源组 |contosohdirg |
| HDInsight 群集 |contosohdicluster |

本教程介绍了配置已加入域的 HDInsight 群集的步骤。 每个部分都有其他文章的链接，可获取更多背景信息。

## <a name="prerequisite"></a>先决条件：
* 熟悉 [Azure AD 域服务](https://azure.microsoft.com/services/active-directory-ds/)及其[定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)结构。
* 请确保订阅已列入此公开预览的白名单。 可使用订阅 ID 向 hdipreview@microsoft.com 发送电子邮件来完成此操作。
* SSL 证书由域的签名颁发机构进行签名。 该证书用于配置安全 LDAP。 不能使用自签名证书。

## <a name="procedures"></a>过程
1. 为 Azure AD 创建 Azure 经典 VNet。  
2. 创建并配置 Azure AD 和 Azure AD DS。
3. 将 VM 添加到经典 VNet 以创建组织单位。 
4. 为 Azure AD DS 创建组织单位。
5. 在 Azure 资源管理模式下创建 HDInsight VNet。
6. 为 Azure AD DS 设置反向 DNS 区域。
7. 对等互连两个 VNet。
8. 创建 HDInsight 群集。

> [!NOTE]
> 本教程假设你没有 Azure AD。 如果已有 Azure AD，可跳过步骤 2 中的部分。
> 
> 

通过 PowerShell 脚本，可通过步骤 7 自动执行步骤 3。  有关详细信息，请参阅[使用 Azure PowerShell 配置已加入域的 HDInsight 群集](hdinsight-domain-joined-configure-use-powershell.md)。

## <a name="create-an-azure-classic-vnet"></a>创建 Azure 经典 VNet
在本部分中，可使用 Azure 门户创建经典 VNet。 在下一部分中，可在经典 VNet 中为 Azure AD 启用 Azure AD DS。 若要深入了解以下过程并了解如何使用其他 VNet 创建方法，请参阅[使用 Azure 门户创建虚拟网络（经典）](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。

**创建经典 VNet**

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 单击“新建” > “网络” > “虚拟网络”。
3. 在“选择部署模型”中，选择“经典”，然后单击“创建”。
4. 输入或选择下列值：
   
   * **名称**：contosoaadvnet
   * **地址空间**：10.1.0.0/16
   * **子网名称**：Subnet1
   * **子网地址范围**：10.1.0.0/24
   * **订阅**：（选择用于创建此 VNet 的订阅。）
   * **ResourceGroup**：contosoaadrg
   * **位置**：（为 HDInsight 群集选择区域。）
     
     > [!IMPORTANT]
     > 必须选择支持 Azure AD DS 的位置。 有关详细信息，请参阅 [可用产品（按区域）](https://azure.microsoft.com/en-us/regions/services/)。 
     > 
     > 经典 VNet 和资源组 VNet 都必须位于与 Azure AD DS 相同的区域中。
     > 
     > 
5. 单击“创建”以创建该 VNet  。

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>为 Azure AD 创建并配置 Azure AD DS
可在本部分中：

1. 创建 Azure AD。
2. 创建 Azure AD 用户。 这些用户是域用户。 使用第一个用户，通过 Azure AD 配置 HDInsight 群集。  在本教程中，其他两个用户是可选项。 配置 Apache Ranger 策略时，他们可用于[为已加入域的 HDInsight 群集配置 Hive 策略](hdinsight-domain-joined-run-hive.md)。
3. 创建 AAD DC 管理员组并在组中添加 Azure AD 用户。 此用户用于创建组织单位。
4. 为 Azure AD 启用 Azure AD 域服务 (Azure AD DS)。
5. 为 Azure AD 配置 LDAPS。 轻型目录访问协议 (LDAP) 用于从 Azure AD 中读取或写入到 Azure AD。

如果要使用现有的 Azure AD，可跳过步骤 1 和 2。

**创建 Azure AD**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“新建” > 应用服务” > Active Directory” > 目录” > “自定义创建”。 
2. 输入或选择下列值：
   
   * **名称**：contosoaaddirectory
   * **域名**：contoso。  该名称必须全局唯一。
   * **国家/地区**：选择你所在国家或地区。
3. 单击“完成”。

**创建 Azure AD 用户**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“Active Directory” -> “contosoaaddirectory”。 
2. 在顶部菜单中，单击“用户”。
3. 单击“添加用户”。
4. 输入“用户名”，然后单击“下一步”。 
5. 配置用户配置文件；在“角色”中选择“全局管理员”；然后单击“下一步”。  创建组织单位时需要全局管理员角色。
6. 单击“创建”，获取临时密码。
7. 复制密码，然后单击“完成”。 在本教程中，稍后将使用此全局管理员用户登录到管理员 VM，以便创建组织单位并配置反向 DNS。

请按照相同的过程再创建两个用户，“用户”角色为 hiveuser1 和 hiveuser2。 以下用户可用于[为已加入域的 HDInsight 群集配置 Hive 策略](hdinsight-domain-joined-run-hive.md)。

**创建 AAD DC 管理员组并添加 Azure AD 用户**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“Active Directory” > “contosoaaddirectory”。 
2. 在顶部菜单中，单击“组”。
3. 单击“添加一个组”或“添加组”。
4. 输入或选择下列值：
   
   * **名称**：AAD DC 管理员。  不要更改组名称。
   * **组类型**：安全。
5. 单击“完成”。
6. 单击“AAD DC 管理员”打开组。
7. 单击“添加成员”。
8. 选择上一步骤中创建的第一个用户，然后单击“完成”。
9. 重复相同步骤，再创建一个名为 **HiveUsers** 的组，然后将两个 Hive 用户添加到组。

有关详细信息，请参阅 [Azure AD 域服务（预览版）- 创建 AAD DC 管理员组](../active-directory-domain-services/active-directory-ds-getting-started.md)。

**为 Azure AD 启用 Azure AD DS**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“Active Directory” > “contosoaaddirectory”。 
2. 在顶部菜单栏上，单击“配置”。
3. 向下滚动到“域服务”，设置以下值：
   
   * **为该目录启用域服务**：是。
   * **域服务的 DNS 域名**：这将显示 Azure 目录的默认 DNS 名称。 例如 contoso.onmicrosoft.com。
   * **将域服务连接到此虚拟网络**：选择之前创建的经典虚拟网络，例如 **contosoaadvnet**。
4. 单击页面底部的“保存”。 “对该目录启用域服务”旁边将显示“挂起...”。  
5. 请等待“挂起...”消失和“IP 地址”填好。 两个 IP 地址都要填写。 这些 IP 地址属于域服务预配的域控制器。 相应的域控制器进行了预配并准备就绪之后，将显示每个 IP 地址。 记下这两个 IP 地址。 稍后你将需要它们。

有关详细信息，请参阅 [Azure AD 域服务（预览版）- 启用 Azure AD 域服务](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md)。

**同步密码**

如果使用自己的域，需要同步密码。 请参阅[对仅限云的 Azure AD 目录启用 Azure AD 域服务的密码同步](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。

**为 Azure AD 配置 LDAPS**

1. 获取由域的签名颁发机构进行签名的 SSL 证书。 不能使用自签名证书。 如果无法获取 SSL 证书，请访问 hdipreview@microsoft.com 以处理异常。
2. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“Active Directory” > “contosoaaddirectory”。 
3. 在顶部菜单栏上，单击“配置”。
4. 滚动到“域服务”。
5. 单击“配置证书”。
6. 按照说明指定证书文件和密码。 “对该目录启用域服务”旁边将显示“挂起...”。  
7. 请等待“挂起...”消失和“安全 LDAP 证书”填好。  此操作至少需要 10 分钟。

> [!NOTE]
> 如果一些后台任务正在 Azure AD DS 上运行，则上传证书时可能会显示错误：<i>正在为此租户执行操作。请稍后重试</i>。  如果遇到此错误，请稍后再试。 预配第二个域控制器 IP 最多可能需要 3 小时。
> 
> 

有关详细信息，请参阅[为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>配置组织单位和反向 DNS
在本部分中，可将虚拟机添加到 Azure AD VNet 并在此 VM 上安装管理工具，以便配置组织单位和反向 DNS。 Kerberos 身份验证需要反向 DNS 查找。

**在虚拟网络中创建虚拟机**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“新建” > “计算” > “虚拟机” > “从库中”。
2. 选择一个映像，然后单击“下一步”。  如果不知道要使用哪一个，请选择默认值 **Windows Server 2012 R2 Datacenter**。
3. 输入或选择下列值：
   
   * 虚拟机名称：**contosoaadadmin**
   * 层：**基本**
   * 新用户名：（输入用户名）
   * 密码：（输入密码）
     
     请注意，请使用本地管理员的用户名和密码。
4. 单击“下一步”
5. 在“区域/虚拟网络”中，选择上一步中创建的新虚拟网络 (contosoaadvnet)，然后单击“下一步”。
6. 单击“完成”。

**通过 RDP 访问 VM**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，单击“虚拟机” > “contosoaadadmin”。
2. 在顶部菜单中单击“仪表板”。
3. 单击页面底部的“连接”。
4. 按照说明操作，使用本地管理员的用户名和密码进行连接。

**将 VM 加入 Azure AD 域**

1. 在 RDP 会话中，单击“启动”，然后单击“服务器管理器”。
2. 在左窗格中，单击“本地服务器”。
3. 在“工作组”中，单击“工作组”。
4. 单击“更改”。
5. 单击“域”，输入“contoso.onmicrosoft.com”，然后单击“确定”。
6. 输入域用户凭据，然后单击“确定”。
7. 单击 **“确定”**。
8. 单击“确定”，同意重启计算机。
9. 单击“**关闭**”。
10. 请单击“立即重启”。

有关详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md)。

**安装 Active Directory 管理工具和 DNS 工具**

1. 使用 Azure AD 用户帐户，通过 RDP 访问 **contosoaadadmin**。
2. 单击“启动”，然后单击“服务器管理器”。
3. 在左侧菜单中，单击“仪表板”。
4. 单击“管理”，然后单击“添加角色和功能”。
5. 单击“下一步”。
6. 选择“**基于角色或基于功能的安装**”，然后单击“**下一步**”。
7. 在服务器池中，选择当前的虚拟机，然后单击“下一步”。
8. 单击“下一步”跳过角色。
9. 展开“远程服务器管理工具”和“角色管理工具”，选择“AD DS 和 AD LDS 工具”和“DNS 服务器工具”，然后单击“下一步”。 
10. 单击“下一步”
11. 单击“安装” 。

有关详细信息，请参阅[在虚拟机上安装 Active Directory 管理工具](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine)。

**配置反向 DNS**

1. 使用 Azure AD 用户帐户，通过 RDP 访问 contosoaadadmin。
2. 依次单击“启动”、“管理工具”和“DNS”。 
3. 单击“否”，跳过添加 ContosoAADAdmin。
4. 选择“下列计算机”，输入之前配置的第一个 DNS 服务器的 IP 地址，然后单击“确定”。  左窗格应显示添加有 DC/DNS。
5. 展开 DC/DNS 服务器，右键单击“反向查找区域”，然后单击“新建区域”。 将打开“新建区域向导”。
6. 单击“下一步”。
7. 选择“主要区域”，然后单击“下一步”。
8. 选择“至此域中域控制器上运行的所有 DNS 服务器”，然后单击“下一步”。
9. 选择“IPv4 反向查找区域”，然后单击“下一步”。
10. 在“网络 ID”中，为 HDInsight VNET 网络范围输入前缀，然后单击“下一步”。 HDInsight VNet 将在下一部分中创建。
11. 单击“下一步”。
12. 单击“下一步”。
13. 单击“完成” 。

接下来创建的组织单位将在创建 HDInsight 群集时用到。 Hadoop 系统用户和计算机帐户将位于此 OU 中。

**在 Azure AD 域服务托管域中创建组织单位 (OU)**

1. 使用“AAD DC 管理员”组中的域帐户，通过 RDP 访问 **contosoaadadmin**。
2. 依次单击“启动”、“管理工具”和“Active Directory 管理中心”。
3. 单击左窗格中的域名。 例如 contoso。
4. 在“任务”窗格的域名下，依次单击“新建”“组织单位”。
5. 输入一个名称（例如“HDInsightOU”），然后单击“确定”。 

有关详细信息，请参阅[在 Azure AD 域服务托管域中创建组织单位 (OU)](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>创建适用于 HDInsight 群集的 Resource Manager VNet
在本部分中，将创建用于 HDInsight 群集的 Azure Resource Manager VNet。 若要深入了解如何使用其他方法创建 Azure VNET，请参阅[创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

创建 VNet 后，将配置 Resource Manager VNet 以使用与 Azure AD VNet 相同的 DNS 服务器。 如果遵循本教程中的步骤创建经典 VNet 和 Azure AD，则 DNS 服务器为 10.1.0.4 和 10.1.0.5。

**创建 Resource Manager VNet**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“新建”、“网络”和“虚拟网络”。 
3. 在“选择部署模型”中，选择“Resource Manager”，然后单击“创建”。
4. 键入或选择以下值：
   
   * **名称**：contosohdivnet
   * **地址空间**：10.2.0.0/16。 请确保地址范围与经典 VNet 的 IP 地址范围不重叠。
   * **子网名称**：Subnet1
   * **子网地址范围**：10.2.0.0/24
   * **订阅**：（选择你的 Azure 订阅。）
   * **资源组**：contosohdirg
   * **位置**：（选择与 Azure AD VNet 相同的位置，例如 contosoaadvnet。）
5. 单击“创建” 。

**为 Resource Manager VNet 配置 DNS**

1. 在 [Azure 门户](https://portal.azure.com)中，单击“更多服务” -> “虚拟网络”。 请确保不要单击“虚拟网络(经典)”。
2. 单击“contosohdivnet”。
3. 在新边栏选项卡的左侧，单击“DNS 服务器”。
4. 单击“自定义”，然后输入以下值：
   
   * 10.1.0.4
   * 10.1.0.5
     
     这些 DNS 服务器 IP 地址必须与 Azure AD VNet（经典 VNet）中的 DNS 服务器匹配。
5. 单击“保存” 。

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>对等互连 Azure AD VNet 和 HDInsight VNet
**对等互连两个 VNet**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，单击“更多服务”。
3. 单击“虚拟网络”。 不要单击“虚拟网络(经典)”。
4. 单击“contosohdivnet”。  这是 HDInsight VNet。
5. 在边栏选项卡的左侧菜单上，单击“对等互连”。
6. 在顶部菜单中，单击“添加”。 这将打开“添加对等互连”边栏选项卡。
7. 在“添加对等互连”边栏选项卡上，设置或选择以下值：
   
   * **名称**：ContosoAADHDIVNetPeering
   * **虚拟网络部署模型**：经典
   * **订阅**：选择用于经典 (Azure AD) VNet 的订阅名称。
   * **虚拟网络**：contosoaadvnet。
   * **是否允许虚拟网络访问**：（检查）
   * **是否允许转发的流量**：（检查）。 将其他两个复选框保留为未选中状态。
8. 单击 **“确定”**。

## <a name="create-hdinsight-cluster"></a>创建 HDInsight 群集
在本部分中，将在 HDInsight 中使用 Azure 门户或 [Azure Resource Manager 模板](../azure-resource-manager/resource-group-template-deploy.md)创建基于 Linux 的 Hadoop 群集。 对于其他群集创建方法以及了解设置，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。 若要深入了解如何在 HDInsight 中使用 Resource Manager 模板创建 Hadoop 群集，请参阅[在 HDInsight 中使用 Resource Manager 模板创建 Hadoop 群集](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**使用 Azure 门户创建已加入域的 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“新建”、“智能+分析”和“HDInsight”。
3. 在“新 HDInsight 群集”边栏选项卡上，输入或选择以下值：
   
   * **群集名称**：为已加入域的 HDInsight 群集输入新的群集名称。
   * **订阅**：选择用于创建此群集的 Azure 订阅。
   * **群集配置**：
     
     * **群集类型**：Hadoop。 已加入域的 HDInsight 当前仅在 Hadoop 群集上受支持。
     * **操作系统**：Linux。  已加入域的 HDInsight 仅在基于 Linux 的 HDInsight 群集上受支持。
     * **版本**：Hadoop 2.7.3 (HDI 3.5)。 已加入域的 HDInsight 仅在 HDInsight 群集 3.5 版上受支持。
     * **群集类型**：PREMIUM
       
       单击“选择”保存更改。
   * **凭据**：为群集用户和 SSH 用户配置凭据。
   * **数据源**：创建新的存储帐户或使用现有的存储帐户，作为 HDInsight 群集的默认存储帐户。 位置必须与两个 VNet 的位置相同。  这个位置也是 HDInsight 群集的位置。
   * **定价**：选择群集的辅助角色节点数量。
   * **高级配置**： 
     
     * **域加入和 VNet/子网**： 
       
       * **域设置**： 
         
         * **域名**：contoso.onmicrosoft.com
         * **域用户名**：输入域用户名。 此域必须具有以下特权：将计算机加入到域并放置在此前配置的组织单位中；在此前配置的组织单位内创建服务主体；创建 DNS 项。 此域用户将成为此已加入域的 HDInsight 群集的管理员。
         * **域密码**：输入域用户密码。
         * **组织单位**：输入此前配置的 OU 的可分辨名称。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **LDAPS URL**：ldaps://contoso.onmicrosoft.com:636
         * **访问用户组**：指定其用户要同步到群集的安全组。 例如，HiveUsers。
           
           单击“选择”保存更改。
           
           ![已加入域的 HDInsight 门户配置域设置](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **虚拟网络**：contosohdivnet
       * **子网**：Subnet1
         
         单击“选择”保存更改。        
         单击“选择”保存更改。
   * **资源组**：选择用于 HDInsight VNet (contosohdirg) 的资源组。
4. 单击“创建” 。  

还可使用 Azure Resource Management 模板创建已加入域的 HDInsight 群集。 以下过程演示如何：

**使用 Resource Management 模板创建已加入域的 HDInsight 群集**

1. 单击以下映像可在 Azure 门户中打开 Resource Manager 模板。 Resource Manager 模板位于公共 Blob 容器中。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 在“参数”边栏选项卡中，输入以下值：
   
   * **订阅**：（选择你的 Azure 订阅）。
   * **资源组**：单击“使用现有的”，并指定正在使用的同一资源组。  例如，contosohdirg。 
   * **位置**：指定资源组位置。
   * **群集名称**：为将创建的 Hadoop 群集输入名称。 例如，contosohdicluster。
   * **群集类型**：选择群集类型。  默认值为 **hadoop**。
   * **位置**：为群集选择位置。  默认存储帐户将使用同一位置。
   * **群集辅助角色节点数**：选择辅助角色节点的数量。
   * **群集登录名和密码**：默认登录名是 **admin**。
   * **SSH 用户名和密码**：默认用户名是 **sshuser**。  可以重命名它。 
   * **虚拟网络 ID**：/subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **虚拟网络子网**：/subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **域名**：contoso.onmicrosoft.com
   * **组织单位 DN**：OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **群集用户组 D Ns**："\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrls**：["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**：（输入域管理员用户名）
   * **DomainAdminPassword**：（输入域管理员用户密码）
   * **我同意上述条款和条件**：（检查）
   * **固定到仪表板**：（检查）
3. 单击“购买”。 此时会出现标题为“正在部署模板”的新磁贴。 创建群集大约需要 20 分钟时间。 创建群集之后，便可以在门户中单击群集边栏选项卡以打开它。

完成教程之后，你可能要删除群集。 有了 HDInsight，你就可以将数据存储在 Azure 存储空间中，因此可以在群集不用时安全地删除群集。 此外，你还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 有关删除群集的说明，请参阅[使用 Azure 门户在 HDInsight 中管理 Hadoop 群集](hdinsight-administer-use-management-portal.md#delete-clusters)。

## <a name="next-steps"></a>后续步骤
* 若要使用 Azure PowerShell 配置已加入域的 HDInsight 群集，请参阅[使用 Azure PowerShell 配置已加入域的 HDInsight 群集](hdinsight-domain-joined-configure-use-powershell.md)。
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。


