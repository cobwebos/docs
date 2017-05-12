---
title: "保护 Azure Data Lake Store 中存储的数据 | Microsoft Docs"
description: "了解如何使用组和访问控制列表保护 Azure Data Lake Store 中的数据"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 376b61037de8b1af657095b8b32ee16568af8894
ms.openlocfilehash: 0df8932668a954cc60a1db9b745019decb98d1e9
ms.contentlocale: zh-cn
ms.lasthandoff: 02/07/2017


---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>保护 Azure Data Lake Store 中存储的数据
保护 Azure Data Lake Store 中的数据包含三个步骤。

1. 首先在 Azure Active Directory (AAD) 中创建安全组。 这些安全组用于实现 Azure 门户中基于角色的访问控制 (RBAC)。 有关详细信息，请参阅 [Microsoft Azure 中基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。
2. 将 AAD 安全组分配到 Azure Data Lake Store 帐户。 这会控制从门户访问 Data Lake Store 帐户的访问权限和从门户或 API 的管理操作。
3. 将 AAD 安全组分配为 Data Lake Store 文件系统上的访问控制列表 (ACL)
4. 此外，也可设置可访问 Data Lake Store 中数据的客户端的 IP 地址范围。

本文提供有关如何使用 Azure 门户执行以上任务的说明。 有关 Data Lake Store 如何实现帐户和数据级别安全的详细信息，请参阅 [Azure Data Lake Store 安全性](data-lake-store-security-overview.md)。 有关 Azure Data Lake Store 中如何实现 ACL 的详细信息，请参阅 [Data Lake Store 中访问控制概述](data-lake-store-access-control.md)。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>在 Azure Active Directory 中创建安全组
有关如何创建 AAD 安全组和如何添加用户到组的说明，请参阅[管理 Azure Active Directory 中的安全组](../active-directory/active-directory-accessmanagement-manage-groups.md)。

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>将用户或安全组分配给 Azure Data Lake Store 帐户
将用户或安全组分配到 Azure Data Lake Store 后，可使用 Azure 门户和 Azure Resource Manager API 控制帐户上的管理操作访问权限。 

1. 打开 Azure Data Lake Store 帐户。 在左窗格中，单击“浏览”，单击“Data Lake Store”，然后在“Data Lake Store”边栏选项卡，单击要向其分配用户或安全组的帐户的名称。

2. 在 Data Lake Store 帐户设置边栏选项卡中，单击“访问控制(IAM)”。 该边栏选项卡默认会将“订阅管理员”组列为所有者。
   
    ![将安全组分配到 Azure Data Lake Store 帐户](./media/data-lake-store-secure-data/adl.select.user.icon.png "将安全组分配到 Azure Data Lake Store 帐户")

    有两种方式可添加组和分配相关角色。
   
    * 先添加用户/组到帐户，然后分配角色，或者
    * 先添加角色，然后向角色分配用户/组。
     
    本部分介绍第一种方式，即先添加组，然后分配角色。 可执行相似步骤来先选择角色，然后向所选角色分配组。
4. 在“用户”边栏选项卡中，单击“添加”打开“添加访问”边栏选项卡。 在“添加访问”边栏选项卡中，单击“选择角色”，然后为该用户/组选择一个角色。
   
    ![为用户添加角色](./media/data-lake-store-secure-data/adl.add.user.1.png "为用户添加角色")
   
    “所有者”和“参与者”角色向此 Data Lake 帐户上的多种管理功能提供访问权限。 对于要和此 Data Lake 中的数据进行交互的用户，可将其添加到“读者”角色。 这些角色的作用域限于与该 Azure Data Lake Store 帐户相关的管理操作。
   
    对于数据操作，个人文件系统权限定义用户可执行的操作。 因此，具有 Reader 角色的用户仅可查看与该帐户关联的管理设置，但视向其分配的文件系统权限而定，可能可以读取或写入数据。 Data Lake Store 文件系统权限说明参见[将安全组作为 ACL 分配到 Azure Data Lake Store 文件系统](#filepermissions)。
5. 在“添加访问”边栏选项卡中，单击“添加用户”打开“添加用户”边栏选项卡。 在此边栏选项卡中，查找之前在 Azure Active Directory 中创建的安全组。 如果搜索范围中存在大量的组，请使用顶部的文本框筛选组名称。 单击“选择”。
   
    ![添加安全组](./media/data-lake-store-secure-data/adl.add.user.2.png "添加安全组")
   
    如果要添加未列出的组/用户，可通过使用“邀请”图标并指定该用户/组的电子邮件地址将其邀入。
6. 单击 **“确定”**。 已添加的安全组如下所示。
   
    ![已添加的安全组](./media/data-lake-store-secure-data/adl.add.user.3.png "已添加的安全组")

7. 你的用户/安全组现在具有 Azure Data Lake Store 帐户访问权限。 如要向特定用户提供访问权限，可将其添加到安全组。 同样，如要撤消用户的访问权限，可将其从安全组删除。 可向一个帐户分配多个安全组。 

## <a name="filepermissions"></a>将用户或安全组作为 ACL 分配给 Azure Data Lake Store 文件系统
通过将用户/安全组分配给 Azure Data Lake 文件系统，可对 Azure Data Lake Store 中存储的数据设置访问控制。

1. 在 Data Lake Store 帐户边栏选项卡中，单击“数据资源管理器” 。
   
    ![在 Data Lake Store 帐户中创建目录](./media/data-lake-store-secure-data/adl.start.data.explorer.png "在 Data Lake 帐户中创建目")
2. 在“数据资源管理器”边栏选项卡中，单击要对其配置 ACL 的文件或文件夹，然后单击“访问”。 若要向文件分配 ACL，必须在“文件预览”边栏选项卡中单击“访问”。
   
    ![对 Data Lake 文件系统设置 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "对 Data Lake 文件系统设置 ACL")
3. “访问”边栏选项卡会列出已分配给根的标准访问和自定义访问。 单击“添加”图标添加自定义级别的 ACL。
   
    ![列出标准及自定义访问权限](./media/data-lake-store-secure-data/adl.acl.2.png "列出标准及自定义访问权限")
   
   * “标准访问”即 UNIX 样式的访问，可将读取、写入、执行 (rwx) 指定给三个不同的用户类型：所有者、组和其他。
   * “自定义访问”对应 POSIX ACL，允许为特定命名用户或组（而不仅是文件的所有者或组）设置权限。 
     
     有关详细信息，请参阅 [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)。 有关 Data Lake Store 中如何实现 ACL 的详细信息，请参阅 [Data Lake Store 中的访问控制](data-lake-store-access-control.md)。
4. 单击“添加”图标打开“添加自定义访问”边栏选项卡。 在此边栏选项卡中，单击“选择用户或组”，然后在“选择用户或组”边栏选项卡中，查找之前在 Azure Active Directory 中创建的安全组。 如果搜索范围中存在大量的组，请使用顶部的文本框筛选组名称。 单击要添加的组，然后单击“选择”。
   
    ![添加组](./media/data-lake-store-secure-data/adl.acl.3.png "添加组")
5. 单击“选择权限”，选择权限以及是将这些权限分配为默认 ACL、访问 ACL 还是同时分配为这两类。 单击 **“确定”**。
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.acl.4.png "分配权限给组")
   
    有关 Data Lake Store 中的权限和默认/访问 ACL 的详细信息，请参阅 [Data Lake Store 中的访问控制](data-lake-store-access-control.md)。
6. 在“添加自定义访问”边栏选项卡中，单击“确定”。 “访问”边栏选项卡中会列出新添加的组以及相关的权限。
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.acl.5.png "分配权限给组")
   
   > [!IMPORTANT]
   > 当前版本中，“自定义访问”中仅允许 9 个条目。 若要添加的用户多于 9 个，应创建安全组、添加用户到安全组以及向这些安全组提供 Data Lake Store 帐户访问权限。
   > 
   > 
7. 如果需要，添加组后也可修改访问权限。 根据是否要删除权限或分配权限到安全组，可清除或选中每个权限类型（读取、写入、执行）的复选框。 单击“保存”保存更改，或单击“放弃”撤消更改。

## <a name="set-ip-address-range-for-data-access"></a>设置数据访问的 IP 地址范围
Azure Data Lake Store 允许进一步在网络级别锁定访问权限。 可启用防火墙、指定 IP 地址或定义受信任客户端的 IP 地址范围。 启用后，仅具有定义范围内的 IP 地址的客户端可连接到 Azure Data Lake Store。

![防火墙设置和 IP 访问](./media/data-lake-store-secure-data/firewall-ip-access.png "防火墙设置和 IP 地址")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>删除 Azure Data Lake Store 帐户的安全组
从 Azure Data Lake Store 帐户删除安全组时，使用 Azure 门户 和 Azure Resource Manager API 仅会更改此帐户上的管理操作访问权限。

1. 在“Data Lake Store 帐户”边栏选项卡中，单击“设置”。 在“设置”边栏选项卡中，单击“用户”。
   
    ![将安全组分配到 Azure Data Lake 帐户](./media/data-lake-store-secure-data/adl.select.user.icon.png "将安全组分配到 Azure Data Lake 帐户")
2. 在“用户”边栏选项卡中，单击要删除的安全组。
   
    ![要删除的安全组](./media/data-lake-store-secure-data/adl.add.user.3.png "要删除的安全组")
3. 在此安全组边栏选项卡中，单击“删除”。
   
    ![已删除的安全组](./media/data-lake-store-secure-data/adl.remove.group.png "已删除的安全组")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>从 Azure Data Lake Store 文件系统删除安全组 ACL
从 Azure Data Lake Store 文件系统删除安全组 ACL 会更改此 Data Lake Store 中数据的访问权限。

1. 在 Data Lake Store 帐户边栏选项卡中，单击“数据资源管理器” 。
   
    ![在 Data Lake 帐户中创建目录](./media/data-lake-store-secure-data/adl.start.data.explorer.png "在 Data Lake 帐户中创建目录")
2. 在“数据资源管理器”边栏选项卡中，单击要对其删除 ACL 的文件或文件夹，然后在“帐户”边栏选项卡中单击“访问”图标。 若要删除文件的 ACL，必须在“文件预览”边栏选项卡中单击“访问”。
   
    ![对 Data Lake 文件系统设置 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "对 Data Lake 文件系统设置 ACL")
3. 在“访问”边栏选项卡中的“自定义访问”部分中，单击要删除的安全组。 在“自定义访问”边栏选项卡中，单击“删除”，然后单击“确定”。
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.remove.acl.png "分配权限给组")

## <a name="see-also"></a>另请参阅
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)
* [从 Azure 存储 Blob 复制数据到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [ PowerShell 实现 Data Lake Store 入门](data-lake-store-get-started-powershell.md)
* [通过 .NET SDK 实现 Data Lake Store 入门](data-lake-store-get-started-net-sdk.md)
* [获取 Data Lake Store 的诊断日志](data-lake-store-diagnostic-logs.md)


