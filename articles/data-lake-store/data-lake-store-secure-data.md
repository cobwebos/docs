---
title: 保护 Azure Data Lake Storage Gen1 中存储的数据 | Microsoft Docs
description: 了解如何使用组和访问控制列表保护 Azure Data Lake Storage Gen1 中的数据
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198122"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>保护 Azure Data Lake Storage Gen1 中存储的数据
保护 Azure Data Lake Storage Gen1 中的数据包含三个步骤。  必须同时设置基于角色的访问控制 (RBAC) 和访问控制列表 (ACL) 才能为用户和安全组完全启用对数据的访问权限。

1. 首先在 Azure Active Directory (AAD) 中创建安全组。 这些安全组用于实现 Azure 门户中基于角色的访问控制 (RBAC)。 有关详细信息，请参阅 [Microsoft Azure 中基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)。
2. 将 AAD 安全组分配到 Data Lake Storage Gen1 帐户。 这会控制从门户访问 Data Lake Storage Gen1 帐户的访问权限和从门户或 API 的管理操作。
3. 将 AAD 安全组分配为 Data Lake Storage Gen1 文件系统上的访问控制列表 (ACL)。
4. 此外，也可设置可访问 Data Lake Storage Gen1 中数据的客户端的 IP 地址范围。

本文提供有关如何使用 Azure 门户执行以上任务的说明。 如需深入了解 Data Lake Storage Gen1 如何实现帐户和数据级别安全，请参阅 [Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)。 如需深入了解关于 Data Lake Storage Gen1 中如何实现 ACL 的信息，请参阅 [Data Lake Storage Gen1 中的访问控制概述](data-lake-store-access-control.md)。

## <a name="prerequisites"></a>必备组件
在开始阅读本教程前，必须具有：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>在 Azure Active Directory 中创建安全组
有关如何创建 AAD 安全组和如何添加用户到组的说明，请参阅[管理 Azure Active Directory 中的安全组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

> [!NOTE] 
> 可以使用 Azure 门户在 Azure AD 中向组添加用户和其他组。 不过，要将服务主体添加到组，请使用 [Azure AD 的 PowerShell 模块](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)。
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>将用户或安全组分配给 Data Lake Storage Gen1 帐户
将用户或安全组分配给 Data Lake Storage Gen1 帐户后，可使用 Azure 门户和 Azure 资源管理器 API 控制帐户上的管理操作访问权限。 

1. 打开 Data Lake Storage Gen1 帐户。 在左窗格中单击“所有资源”，然后在“所有资源”边栏选项卡中单击要将用户或安全组分配到的帐户名。 

2. 在 Data Lake Storage Gen1 帐户边栏选项卡中，单击“访问控制(IAM)”  。 默认情况下，边栏选项卡会将订阅所有者列出为所有者。
   
    ![向 Azure Data Lake Storage Gen1 帐户分配安全组](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Assign security group to Azure Data Lake Storage Gen1 account")

3. 在“访问控制 (IAM)”边栏选项卡中，单击“添加”以打开“添加权限”边栏选项卡。    在“添加权限”  边栏选项卡，为用户/组选择**角色**。 查找之前在 Azure Active Directory 中创建的安全组并选择它。 如果搜索范围中存在大量的用户和组，请使用“选择”文本框来筛选组名称。  
   
    ![为用户添加角色](./media/data-lake-store-secure-data/adl.add.user.1.png "为用户添加角色")
   
    “所有者”  和“参与者”  角色向此 Data Lake 帐户上的多种管理功能提供访问权限。 对于将需要与 Data Lake 中的数据进行交互但仍然需要查看帐户管理信息的用户，你可以将其添加到“读者”角色。  这些角色的作用域限于与该 Data Lake Storage Gen1 帐户相关的管理操作。
   
    对于数据操作，各个文件系统权限定义用户可执行的操作。 因此，具有 Reader 角色的用户仅可查看与该帐户关联的管理设置，但视向其分配的文件系统权限而定，可能可以读取或写入数据。 Data Lake Storage Gen1 文件系统权限说明参见[将安全组作为 ACL 分配到 Azure Data Lake Storage Gen1 文件系统](#filepermissions)。

    > [!IMPORTANT]
    > 只有“所有者”  角色可以自动启用文件系统访问权限。 “参与者”、“读者”和所有其他角色需要通过 ACL 来启用对文件夹和文件的任何级别的访问权限。    “所有者”角色提供无法通过 ACL 替代的超级用户文件和文件夹权限。  有关 RBAC 策略如何映射到数据访问权限的详细信息，请参阅[用于帐户管理的 RBAC](data-lake-store-security-overview.md#rbac-for-account-management)。

4. 如果希望添加“添加权限”边栏选项卡中未列出的组/用户,则可以通过在“选择”文本框中键入其电子邮件地址然后从列表中选择他们来邀请他们。  
   
    ![添加安全组](./media/data-lake-store-secure-data/adl.add.user.2.png "添加安全组")
   
5. 单击“ **保存**”。 已添加的安全组如下所示。
   
    ![已添加的安全组](./media/data-lake-store-secure-data/adl.add.user.3.png "已添加的安全组")

6. 用户/安全组现在具有 Data Lake Storage Gen1 帐户访问权限。 如要向特定用户提供访问权限，可将其添加到安全组。 同样，如要撤消用户的访问权限，可将其从安全组删除。 可向一个帐户分配多个安全组。 

## <a name="filepermissions"></a>将用户或安全组作为 ACL 分配给 Data Lake Storage Gen1 文件系统
通过将用户/安全组分配给 Data Lake Storage Gen1 文件系统，可对 Data Lake Storage Gen1 中存储的数据设置访问控制。

1. 在 Data Lake Storage Gen1 帐户边栏选项卡中，单击“数据资源管理器”  。
   
    ![通过数据资源管理器查看数据](./media/data-lake-store-secure-data/adl.start.data.explorer.png "通过数据资源管理器查看数据")
2. 在“数据资源管理器”  边栏选项卡中，单击要对其配置 ACL 的文件夹，然后单击“访问”  。 若要将 ACL 分配给文件，必须首先单击文件来预览它，然后从“文件预览”边栏选项卡中单击“访问”。  
   
    ![对 Data Lake Storage Gen1 文件系统设置 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "Set ACLs on Data Lake Storage Gen1 file system")
3. “访问”  边栏选项卡会列出所有者和已分配给 root 的已分配权限。 单击“添加”  图标以添加更多访问 ACL。
    > [!IMPORTANT]
    > 设置单个文件的访问权限不一定会向用户/组授予对该文件的访问权限。 该文件的路径必须可供所分配的用户/组访问。 有关详细信息和示例，请参阅[与权限相关的常见方案](data-lake-store-access-control.md#common-scenarios-related-to-permissions)。
   
    ![列出标准及自定义访问权限](./media/data-lake-store-secure-data/adl.acl.2.png "列出标准及自定义访问权限")
   
   * “所有者”和“其他所有人”提供 UNIX 样式的访问，可将读取、写入、执行 (rwx) 指定给三个不同的用户类型：所有者、组和其他。  
   * “分配的权限”对应于 POSIX ACL，可用来为指定的特定用户或组（而不仅是文件的所有者或组）设置权限。  
     
     有关详细信息，请参阅 [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)。 有关 Data Lake Storage Gen1 中如何实现 ACL 的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)。
4. 单击“添加”  图标以打开“分配权限”  边栏选项卡。 在此边栏选项卡中，单击“选择用户或组”  ，然后在“选择用户或组”  边栏选项卡中查找之前在 Azure Active Directory 中创建的安全组。 如果搜索范围中存在大量的组，请使用顶部的文本框筛选组名称。 单击要添加的组，并单击“选择”  。
   
    ![添加组](./media/data-lake-store-secure-data/adl.acl.3.png "添加组")
5. 单击“选择权限”  ，选择权限、是否应当以递归方式应用权限，以及要将权限分配为访问 ACL、默认 ACL 还是同时分配为这两者。 单击“确定”。 
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.acl.4.png "分配权限给组")
   
    有关 Data Lake Storage Gen1 中的权限和默认/访问 ACL 的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)。
6. 在“选择权限”边栏选项卡中单击“确定”后，新添加的组和关联的权限现在将在“访问”边栏选项卡中列出。   
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.acl.5.png "分配权限给组")
   
   > [!IMPORTANT]
   > 在当前版本中，“分配的权限”下最多可以有 28 个条目。  若要添加的用户多于 28 个，应创建安全组、向安全组添加用户并向这些安全组提供 Data Lake Storage Gen1 帐户访问权限。
   > 
   > 
7. 如果需要，添加组后也可修改访问权限。 根据是否要删除权限或分配权限到安全组，可清除或选中每个权限类型（读取、写入、执行）的复选框。 单击“保存”  保存更改，或单击“放弃”  撤消更改。

## <a name="set-ip-address-range-for-data-access"></a>设置数据访问的 IP 地址范围
Data Lake Storage Gen1 允许进一步在网络级别锁定访问权限。 可启用防火墙、指定 IP 地址或定义受信任客户端的 IP 地址范围。 启用后，仅具有定义范围内的 IP 地址的客户端可连接到 Azure Data Lake Store。

![防火墙设置和 IP 访问](./media/data-lake-store-secure-data/firewall-ip-access.png "防火墙设置和 IP 地址")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>删除 Data Lake Storage Gen1 帐户的安全组
从 Data Lake Storage Gen1 帐户删除安全组时，使用 Azure 门户和 Azure 资源管理器 API 仅会更改此帐户上的管理操作访问权限。  

对数据的访问权限保持不变，仍然由访问 ACL 进行管理。  但是，角色为“所有者”的用户/组例外。  从“所有者”角色中删除的用户/组不再是超级用户，并且其访问权限将回退到访问 ACL 设置。 

1. 在 Data Lake Storage Gen1 帐户边栏选项卡中，单击“访问控制(IAM)”  。 
   
    ![向 Data Lake Storage Gen1 帐户分配安全组](./media/data-lake-store-secure-data/adl.select.user.icon.png "Assign security group to Data Lake Storage Gen1 account")
2. 在“访问控制 (IAM)”  边栏选项卡中，单击要删除的安全组。 单击“删除”。 
   
    ![已删除的安全组](./media/data-lake-store-secure-data/adl.remove.group.png "已删除的安全组")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>从 Data Lake Storage Gen1 文件系统删除安全组 ACL
从 Data Lake Storage Gen1 文件系统中删除安全组 ACL 会更改对此 Data Lake Storage Gen1 帐户中数据的访问权限。

1. 在 Data Lake Storage Gen1 帐户边栏选项卡中，单击“数据资源管理器”  。
   
    ![在 Data Lake Storage Gen1 帐户中创建目录](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Create directories in Data Lake Storage Gen1 account")
2. 在“数据资源管理器”  边栏选项卡中，单击要为其删除 ACL 的文件夹，然后单击“访问”  。 若要为文件删除 ACL，必须首先单击该文件来预览它，然后从“文件预览”边栏选项卡中单击“访问”。   
   
    ![对 Data Lake Storage Gen1 文件系统设置 ACL](./media/data-lake-store-secure-data/adl.acl.1.png "Set ACLs on Data Lake Storage Gen1 file system")
3. 在“访问”  边栏选项卡中，单击要删除的安全组。 在“访问详细信息”  边栏选项卡中，单击“删除”  。
   
    ![分配权限给组](./media/data-lake-store-secure-data/adl.remove.acl.png "分配权限给组")

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)
* [将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
* [通过 PowerShell 实现 Data Lake Storage Gen1 入门](data-lake-store-get-started-powershell.md)
* [通过 .NET SDK 实现 Data Lake Storage Gen1 入门](data-lake-store-get-started-net-sdk.md)
* [访问 Data Lake Storage Gen1 的诊断日志](data-lake-store-diagnostic-logs.md)

