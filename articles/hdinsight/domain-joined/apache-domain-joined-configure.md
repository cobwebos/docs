---
title: "配置已加入域的 HDInsight 群集 — Azure | Microsoft Docs"
description: "了解如何安装并配置已加入域的 HDInsight 群集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/15/2018
ms.author: saurinsh
ms.openlocfilehash: b4d71eeb0aab75e67e851f867f194ed7578d0d1c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>配置已加入域的 HDInsight 沙盒环境

了解如何使用独立的Active Directory 和 [Apache Ranger](http://hortonworks.com/apache/ranger/) 安装 Azure HDInsight 群集，以便利用强身份验证和丰富的基于角色的访问控制 (RBAC) 策略。 有关详细信息，请参阅[引入已加入域的 HDInsight 群集](apache-domain-joined-introduction.md)。 

> [!IMPORTANT]
> 默认情况下，此设置只能在使用 Azure 存储帐户时使用。 若要用于 Azure Data Lake Store，请将 Active Directory 同步到新的 Azure Active Directory。

如果没有已加入域的 HDInsight 群集，每个群集只能有一个 Hadoop HTTP 用户帐户和一个 SSH 用户帐户。  可使用以下服务实现多用户身份验证：

-   在 Azure IaaS 上运行的独立 Active Directory。
-   Azure Active Directory。

本文介绍如何使用在 Azure IaaS 上运行的独立 Active Directory。 它是客户可用来获得 HDInsight 多用户支持的最简单的体系结构。 本文介绍实现此配置的两种方法：

- 选项 1：使用一个 Azure 资源管理模板创建独立 Active Directory 和 HDInsight 群集。
- 选项 2：整个过程分为以下步骤：
    - 使用模板创建 Active Directory。
    - 设置 LDAPS。
    - 创建 AD 用户和组
    - 创建 HDInsight 群集

> [!IMPORTANT]
> 
> 在加入域的 HDInsight 上未启用 Oozie。

## <a name="prerequisite"></a>先决条件
* Azure 订阅

## <a name="option-1-one-step-approach"></a>选项 1：单步方法
在本部分中，从 Azure 门户打开 Azure 资源管理模板。 使用该模板创建独立 Active Directory 和 HDInsight 群集。 目前，可以创建已加入域的 Hadoop 群集、Spark 群集和交互式查询群集。

1. 单击下面的图像即可在 Azure 门户中打开该模板。 该模板位于 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)中。
   
    若要创建 Spark 群集，请执行以下操作：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    若要创建交互式查询群集，请执行以下操作：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    若要创建 Hadoop 群集，请执行以下操作：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 输入值，选择“我同意上述条款和条件”，选择“固定到仪表板”，然后单击“购买”。 将鼠标光标悬停在字段旁边的说明标志上可查看说明。 大多数值均已填充。 你可以使用默认值，也可以使用自己的值。

    - **资源组**：输入 Azure 资源组名称。
    - **位置**：选择离你近的位置。
    - **新建存储帐户名称**：输入 Azure 存储帐户名称。 PDC、BDC 和 HDInsight 群集将使用这个新的存储帐户作为默认存储帐户。
    - **管理员用户名**：输入域管理员用户名。
    - **管理员密码**：输入域管理员密码。
    - **域名**：默认名称为 *contoso.com*。如果更改域名，则还必须更新“安全 LDAP 证书”字段和“组织单位 DN”字段。
    - **DNS 前缀**：负载均衡器使用的公共 IP 地址的 DNS 前缀。
    - **群集名称**：输入 HDInsight 群集的名称。
    - **群集类型**：请勿更改此值。 若想更改群集类型，请使用最后一步中的特定模板。
    - **安全 Ldap 证书密码**：除非更改“安全 LDAP 证书”字段，否则使用默认值。
    模板中的某些值是硬编码值，例如，辅助节点实例计数为 2。  若要更改硬编码值，请单击“编辑模板”。

    ![已加入域的 HDInsight 群集编辑模板](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

成功完成模板后，就会在资源组中创建 23 项资源。

## <a name="option-2-multi-step-approach"></a>选项 2：多步骤方法

本部分包含四个步骤：

1. 使用模板创建 Active Directory。
2. 设置 LDAPS。
3. 创建 AD 用户和组
4. 创建 HDInsight 群集

### <a name="create-an-active-directory"></a>创建 Active Directory

使用 Azure 资源管理器模板可以更轻松地创建 Azure 资源。 在本部分中，使用 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/)新建包含两个虚拟机的林和域。 这两个虚拟机分别充当主域控制器和备用域控制器。

**创建具有两个域控制器的域**

1. 单击下面的图像即可在 Azure 门户中打开该模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    该模板如下所示：

    ![已加入域的 HDInsight 创建林域虚拟机](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. 输入以下值：

    - **订阅**：选择一个 Azure 订阅。
    - **资源组名称**：键入资源组名称。  资源组用于管理与项目相关的 Azure 资源。
    - **位置**：选择离你近的 Azure 位置。
    - **管理员用户名**：这是域管理员用户名。 此用户不是 HDInsight 群集的 HTTP 用户帐户。 这是你在本教程中使用的帐户。
    - **管理员密码**：输入域管理员的密码。
    - **域名**：域名必须由两部分组成。 例如：contoso.com、contoso.local 或 hdinsight.test。
    - **DNS 前缀**：键入 DNS 前缀
    - **PDC RDP 端口**：（对于本教程，请使用默认值）
    - **BDC RDP 端口**：（对于本教程，请使用默认值）
    - **项目位置**：（对于本教程，请使用默认值）
    - **项目位置 SAS 令牌**：（对于本教程，请将其留空。）

创建资源大约需要 20 分钟。

### <a name="setup-ldaps"></a>设置 LDAPS

轻型目录访问协议 (LDAP) 用于从 AD 中读取或写入到 AD。

**使用远程桌面连接到 PDC**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开资源组，然后打开主域控制器 (PDC) 虚拟机。 默认 PDC 名称为 adPDC。 
3. 单击“连接”，使用远程桌面连接到 PDC。

    ![已加入域的 HDInsight 连接 PDC 远程桌面](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**添加 Active Directory 证书服务**

4. 打开“服务器管理器”（如果尚未打开）。
5. 单击“管理”，然后单击“添加角色和功能”。

    ![已加入域的 HDInsight 添加角色和功能](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. 从“开始之前”中单击“下一步”。
6. 选择“**基于角色或基于功能的安装**”，并单击“**下一步**”。
7. 选择 PDC，然后单击“下一步”。  默认 PDC 名称为 adPDC。
8. 选择“Active Directory 证书服务”。
9. 从弹出对话框中单击“添加功能”。
10. 按向导操作，对该过程的其余部分使用默认设置。
11. 单击“**关闭**”以关闭向导。

**配置 AD 证书**

1. 从服务器管理器中单击黄色通知图标，然后单击“配置 Active Directory 证书服务”。

    ![已加入域的 HDInsight 配置 AD 证书](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. 单击左侧的“角色服务”，选择“证书颁发机构”，然后单击“下一步”。
3. 按向导操作，对该过程的其余部分使用默认设置（在最后一步单击“配置”）。
4. 单击“**关闭**”以关闭向导。

### <a name="optional-create-ad-users-and-groups"></a>（可选）创建 AD 用户和组

**在 AD 中创建用户和组**
1. 使用远程桌面连接到 PDC
1. 打开“Active Directory 用户和计算机”。
2. 在左窗格中选择域名。
3. 单击顶部菜单中的“在当前容器中创建一个新用户”图标。

    ![已加入域的 HDInsight 创建用户](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. 按照说明创建几个用户。 例如，hiveuser1 和 hiveuser2。
5. 单击顶部菜单中的“在当前容器中创建一个新组”图标。
6. 按照说明创建一个名为 **HDInsightUsers** 的组。  在本教程的后面部分创建 HDInsight 群集时会使用此组。

> [!IMPORTANT]
> 在创建已加入域的 HDInsight 群集之前，必须重新启动 PDC 虚拟机。

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>在 VNet 中创建 HDInsight 群集

在本部分中，使用 Azure 门户将 HDInsight 群集添加到在本教程前面部分使用资源管理器模板创建的虚拟网络中。 本文仅介绍已加入域的群集配置的特定信息。  有关常规信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。  

**创建已加入域的 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开在本教程前面部分使用资源管理器模板创建的资源组。
3. 向资源组中添加一个 HDInsight 群集。
4. 选择“自定义”选项：

    ![已加入域的 HDInsight 自定义创建选项](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    一共有六个部分使用自定义配置选项：基本信息、存储、应用程序、群集大小、高级设置和摘要。
5. 在“基本信息”部分中：

    - 群集类型：选择“Enterprise Security 包”。 目前只能为以下群集类型启用 Enterprise Security 包：Hadoop、交互式查询和 Spark。

        ![已加入域的 HDInsight Enterprise Security 包](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - 群集登录用户名：这是 Hadoop HTTP 用户。 此帐户不同于域管理员帐户。
    - 资源组：选择先前使用资源管理器模板创建的资源组。
    - 位置：该位置必须与使用资源管理器模板创建 vnet 和 DC 时使用的位置相同。

6. 在“高级设置”部分中：

    - 域设置：

        ![已加入域的 HDInsight 高级设置域](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - 域名：输入在“创建 Active Directory”[](#create-an-active-directory)中使用的域名。
        - 域用户名：输入在“创建 Active Directory”[](#create-an-active-directory)中使用的 AD 管理员用户名。
        - 组织单位：相关示例请参阅屏幕截图。
        - LDAPS URL：相关示例请参阅屏幕截图
        - 访问用户组：输入在“创建 AD 用户和组”[](#optionally-createad-users-and-groups)中创建的用户组名称
    - 虚拟网络：选择在“创建 Active Directory”[](#create-an-active-directory)中创建的虚拟网络。 模板中所用的默认名称为 **adVNET**。
    - 子网：模板中所用的默认名称为 **adSubnet**。



完成教程之后，可能要删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 有关删除群集的说明，请参阅[使用 Azure 门户在 HDInsight 中管理 Hadoop 群集](../hdinsight-administer-use-management-portal.md#delete-clusters)。

## <a name="next-steps"></a>后续步骤
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

