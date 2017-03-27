---
title: "使用 Azure PowerShell 配置已加入域的 HDInsight 群集 | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 安装并配置已加入域的 HDInsight 群集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f8d2956e9f0240392ba839b076d632ccc45d728
ms.openlocfilehash: 3217399ee868707309e96234d4954548b063534a


---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>使用 Azure PowerShell 配置已加入域的 HDInsight 群集（预览版）
了解如何使用 Azure PowerShell 通过 Azure Active Directory (Azure AD) 和 [Apache Ranger](http://hortonworks.com/apache/ranger/) 设置 Azure HDInsight 群集。 提供 Azure PowerShell 脚本以加快配置速度并降低出错可能性。 仅可在基于 Linux 的群集上配置已加入域的 HDInsight。 有关详细信息，请参阅[引入已加入域的 HDInsight 群集](hdinsight-domain-joined-introduction.md)。

典型的已加入域的 HDInsight 群集配置涉及以下步骤：

1. 为 Azure AD 创建 Azure 经典 VNet。  
2. 创建并配置 Azure AD 和 Azure AD DS。
3. 将 VM 添加到经典 VNet 以创建组织单位。 
4. 为 Azure AD DS 创建组织单位。
5. 在 Azure 资源管理模式下创建 HDInsight VNet。
6. 为 Azure AD DS 设置反向 DNS 区域。
7. 对等互连两个 VNet。
8. 创建 HDInsight 群集。

提供的 PowerShell 脚本执行步骤 3 至 7。 必须手动处理步骤 1 和 2。  如果不希望使用 Azure PowerShell，请参阅[配置已加入域的 HDInsight 群集](hdinsight-domain-joined-configure.md)。 

最终拓扑的示例如下所示：

![已加入域的 HDInsight 拓扑](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

由于 Azure AD 当前仅支持经典虚拟网络 (Vnet)，并且基于 Linux 的 HDInsight 群集仅支持基于 Azure Resource Manager 的 Vnet，因此 HDInsight Azure AD 集成需要两个 Vnet 以及两者间的对等互连。 有关两个部署模型之间的比较信息，请参阅 [Azure Resource Manager 与经典部署：了解资源的部署模型和状态](../azure-resource-manager/resource-manager-deployment-model.md)。 两个 VNet 必须与 Azure AD DS 位于同一区域。

> [!NOTE]
> 本教程假设你没有 Azure AD。 如果已有 Azure AD，可跳过步骤 2 中的部分。
> 
> 

## <a name="prerequisites"></a>先决条件
若要学习本教程，必须做好以下准备：

* 熟悉 [Azure AD 域服务](https://azure.microsoft.com/services/active-directory-ds/)及其[定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)结构。
* 请确保订阅已列入此公开预览的白名单。 可使用订阅 ID 向 hdipreview@microsoft.com 发送电子邮件来完成此操作。
* SSL 证书由域的签名颁发机构进行签名。 该证书用于配置安全 LDAP。 不能使用自签名证书。
* Azure PowerShell。  请参阅[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>为 Azure AD 创建 Azure 经典 VNet。
有关说明，请参阅[此处](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)。

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>创建并配置 Azure AD 和 Azure AD DS。
有关说明，请参阅[此处](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)。

## <a name="run-the-powershell-script"></a>运行 PowerShell 脚本
可从 [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight) 下载 PowerShell 脚本。 解压缩 zip 文件并将文件保存到本地。

**编辑 PowerShell 脚本**

1. 使用 Windows PowerShell ISE 或任何文本编辑器打开 run.ps1。
2. 填充以下变量的值：
   
   * **$SubscriptionName** - 要在其中创建 HDInsight 群集的 Azure 订阅的名称。 你已在此订阅中创建经典虚拟网络，并将在订阅下创建 HDInsight 群集的 Azure Resource Manager 虚拟网络。
   * **$ClassicVNetName** - 包含 Azure AD DS 的经典虚拟网络。 此虚拟网络必须位于上面提供的同一订阅中。 必须使用 Azure 门户而非经典门户创建此虚拟网络。 如果按照[配置已加入域的 HDInsight 群集（预览版）](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)中的说明，则默认名称为 contosoaadvnet。
   * **$ClassicResourceGroupName** - 上述经典虚拟网络的资源管理器组名称。 例如 contosoaadrg。 
   * **$ArmResourceGroupName** - 要在其中创建 HDInsight 群集的资源组名称。 可使用与 $ArmResourceGroupName 相同的资源组。  如果资源组不存在，则该脚本将创建资源组。
   * **$ArmVNetName** - 要在其中创建 HDInsight 群集的资源管理器虚拟网络名称。 此虚拟网络将被放入 $ArmResourceGroupName。  如果 VNet 不存在，则 PowerShell 脚本将创建该 VNet。 如果 VNet 存在，则它应该是上面提供的资源组的一部分。
   * **$AddressVnetAddressSpace** - 资源管理器虚拟网络的网络地址空间。 请确保此地址空间可用。 此地址空间不能与经典虚拟网络的地址空间重叠。 例如：“10.1.0.0/16”
   * **$ArmVnetSubnetName** - 要在其中放置 HDInsight 群集 VM 的资源管理器虚拟网络子网名称。 如果子网不存在，则 PowerShell 脚本将创建一个。 如果子网存在，则它应该是上面提供的虚拟网络的一部分。
   * **$AddressSubnetAddressSpace** - 资源管理器虚拟网络子网的网络地址范围。 HDInsight 群集 VM IP 地址将来自此子网地址范围。 例如：“10.1.0.0/24”。
   * **$ActiveDirectoryDomainName** - 要在其中加入 HDInsight 群集 VM 的 Azure AD 域名。 例如：“contoso.onmicrosoft.com”
   * **$ClusterUsersGroups** - AD 中要同步到 HDInsight 群集的安全组的公用名。 此安全组中的用户将能够使用其 Active Directory 域凭据登录到群集仪表板。 这些安全组必须存在于 Active Directory 中。 例如：“hiveusers”或“clusteroperatorusers”。
   * **$OrganizationalUnitName** - 域中的组织单位，该域中将放置 HDInsight 群集 VM 和群集所用的服务主体。 如果此 OU 不存在，则 PowerShell 脚本将创建一个。 例如：“HDInsightOU”。
3. 保存更改。

**运行脚本**

1. 以管理员身份运行 **Windows PowerShell**。
2. 浏览到 run.ps1 的文件夹。 
3. 通过键入文件名运行该脚本，并按 **ENTER**。  将弹出 3 个登录对话框：
   
   1. **登录到 Azure 经典门户** - 输入用于登录到 Azure 经典门户的凭据。 必须已使用这些凭据创建 Azure AD 和 Azure AD DS。
   2. **登录到 Azure Resource Manager 门户** - 输入用于登录到 Azure Resource Manager 门户的凭据。
   3. **域用户名** - 输入想要其成为 HDInsight 群集上的管理员的域用户名称的凭据。 如果从头开始创建 Azure AD，则必须已使用此文档创建此用户。 
      
      > [!IMPORTANT]
      > 按以下格式输入用户名： 
      > 
      > 域名\用户名（例如 contoso.onmicrosoft.com\clusteradmin）
      > 
      > 
      
      此用户必须具有 3 项特权：将计算机加入到提供的 Active Directory 域；在提供的组织单位内创建服务主体和计算机对象；添加反向 DNS 代理规则。

在创建反向 DNS 区域时，脚本将提示你输入网络 ID。 此网络 ID 必须为资源管理器虚拟网络的地址前缀。 例如，如果资源管理器虚拟网络子网地址空间为 10.2.0.0/24，则在工具提示提供网络 ID 时输入 10.2.0.0/24。 

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
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
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

* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。




<!--HONumber=Dec16_HO3-->


