---
title: Azure Data Lake Storage Gen2 中的访问控制概述 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 中访问控制的工作原理
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 906b1dde3d145268df4fb1ff5c243c7daa8396ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992441"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的访问控制

Azure Data Lake Storage Gen2 实现了一个访问控制模型，该模型支持 Azure 基于角色的访问控制 (RBAC) 和像 POSIX 一样的访问控制列表 (ACL)。 本文汇总了 Data Lake Storage Gen2 访问控制模型的基本知识。 

## <a name="azure-role-based-access-control-rbac"></a>Azure 基于角色的访问控制 (RBAC)

Azure 基于角色的访问控制 (RBAC) 使用角色分配对用户、组和 Azure 资源的服务主体有效地应用权限集。 通常情况下，这些 Azure 资源限制为顶级资源（例如 Azure 存储帐户）。 就 Azure 存储以及 Azure Data Lake Storage Gen2 而言，此机制已扩展到文件系统资源。

虽然使用 RBAC 角色分配是一种强大的用户权限控制机制，但对 ACL 而言，这种机制并不精细。 RBAC 的最小粒度为文件系统级别，我们将它的优先级评为高于 ACL。 因此，如果你对文件系统分配 RBAC 权限，用户或服务主体将有权访问该文件系统的所有目录和文件，而不考虑 ACL 分配情况。

Azure 存储为 Blob 存储提供三个内置的 RBAC 角色： 

- [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)
- [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)
- [存储 Blob 数据读者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)

通过上述三个内置角色之一或某个自定义角色授予用户或服务主体 RBAC 数据权限后，在授权请求时首先评估这些权限。 如果请求的操作由调用方的 RBAC 分配授权，则立即解析授权，不执行额外的 ACL 检查。 或者，如果调用方没有 RBAC 分配或请求的操作与分配的权限不匹配，则通过执行 ACL 检查来确定调用方是否有权执行请求的操作。

特别需要注意存储 Blob 数据所有者内置角色。 如果调用方具有此 RBAC 分配，则将用户视为“超级用户”并向其授予对所有转变操作（包括设置目录或文件的所有者以及设置他们不是所有者的目录和文件的 ACL）的完全访问权限。 超级用户访问是唯一获准的更改资源所有者的方式。

## <a name="shared-key-and-shared-access-signature-authentication"></a>共享密钥和共享访问签名身份验证

Azure Data Lake Storage Gen2 支持共享密钥和共享访问签名两种身份验证方法。 这两种身份验证方法的特点是没有与调用方关联的标识，因此不能执行基于用户权限的身份验证。

就共享密钥这一种方法而言，调用方有效地获得了“超级用户”访问权限，这意味着对所有资源上所有操作（包括设置所有者和更高 ACL）的完全访问权限。

SAS 令牌本身就包含允许的权限。 它包含的权限有效地应用到所有授权决策，但不执行额外的 ACL 检查。

## <a name="access-control-lists-on-files-and-directories"></a>文件和目录上的访问控制列表

访问控制列表 (ACL) 有两种类型：访问 ACL 和默认 ACL。

* **访问 ACL**：访问 ACL 控制对某个对象的访问权限。 文件和目录都具有访问 ACL。

* **默认 ACL**：与目录关联的 ACL 模板，用于确定在该目录下创建的任何子项的访问 ACL。 文件没有默认 ACL。

访问 ACL 和默认 ACL 具有相同的结构。

> [!NOTE]
> 更改父级的默认 ACL 不影响现有子项的访问 ACL 或默认 ACL。

## <a name="permissions"></a>权限

文件系统对象权限为“读取”、“写入”和“执行”，可对下表中所示的文件和目录使用这些权限：

|            |    文件     |   Directory |
|------------|-------------|----------|
| **读取 (R)** | 可以读取文件内容 | 需有“读取”和“执行”权限才能列出目录内容 |
| **写入 (W)** | 可以在文件中写入或追加内容 | 需有“写入”和“执行”权限才能在目录中创建子项 |
| **执行 (X)** | 不表示 Data Lake Storage Gen2 上下文中的任何内容 | 需要遍历目录的子项 |

### <a name="short-forms-for-permissions"></a>权限的简短形式

**RWX** 用于表示“读取 + 写入 + 执行”。 还有更精简的数字形式，“读取=4”，“写入=2”，“执行=1”，其总和表示各种不同的权限。 下面是一些示例。

| 数字形式 | 简短形式 |      含义     |
|--------------|------------|------------------------|
| 7            | `RWX`        | RWX |
| 5            | `R-X`        | 读取 + 执行         |
| 4            | `R--`        | 读取                   |
| 0            | `---`        | 无权限         |

### <a name="permissions-inheritance"></a>权限继承

在 Data Lake Storage Gen2 使用的 POSIX 样式的模型中，项的权限存储在项本身中。 换而言之，如果是在已创建子项后设置的权限，则不能从父项继承项的权限。 只有于创建子项前在父项上设置了默认权限时，才能继承权限。

## <a name="common-scenarios-related-to-permissions"></a>与权限相关的常见方案

下表列出了一些常见方案，可帮助你了解对 Data Lake Storage Gen2 帐户执行特定操作所需的权限。

|    Operation             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> 只要以上两个条件成立，删除文件时就不需要文件的写入权限。
>
>

## <a name="users-and-identities"></a>用户和标识

每个文件和目录都有这些标识的不同权限：

- 拥有用户
- 拥有组
- 命名用户
- 命名组
- 命名服务主体
- 所有其他用户

用户和组的标识是 Azure Active Directory (Azure AD) 标识。 因此，除非另有规定，否则“用户”在 Data Lake Storage Gen2 的上下文中可以表示 Azure AD 用户、服务主体或安全组。

### <a name="the-owning-user"></a>拥有用户

创建项的用户自动成为该项的拥有用户。 拥有用户可以：

* 更改所拥有文件的权限。
* 更改所拥有文件的拥有组，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 拥有用户无法更改某个文件或目录的拥有用户。 只有超级用户可以更改文件或目录的拥有用户。

### <a name="the-owning-group"></a>拥有组

在 POSIX ACL 中，每个用户都与“主组”关联。 例如，用户“alice”可能属于“finance”组。 Alice 还可能属于多个组，但始终有一个组指定为她的主组。 在 POSIX 中，当 Alice 创建文件时，该文件的拥有组设置为她的主组，在本例中为“finance”。 否则，所有者组的行为类似于为其他用户/组分配的权限。

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>为新的文件或目录分配拥有组

* **情况 1**：根目录“/”。 此目录是在创建 Data Lake Storage Gen2 文件系统时创建的。 在本例中，如果使用的是 OAuth，则拥有组设置为创建文件系统的用户。 如果文件系统是使用共享密钥、帐户 SAS 或服务 SAS 创建的，则所有者和拥有组设置为“$superuser”。
* **情况 2**（所有其他情况）：创建新项时，从父目录复制拥有组。

#### <a name="changing-the-owning-group"></a>更改拥有组

拥有组可由以下用户更改：
* 任何超级用户。
* 拥有用户，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 拥有组无法更改某个文件或目录的 ACL。  虽然拥有组设置为在根目录那一种情况（即上面的情况 1）中创建了帐户的用户，但单个用户帐户不能有效地用于通过拥有组提供权限。 可以将此权限分配给有效的用户组（如果适用）。

## <a name="access-check-algorithm"></a>访问检查算法

以下伪代码显示了 Data Lake Storage Gen2 帐户的访问检查算法。

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask IS NOT used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>掩码

如访问检查算法中所示，掩码会限制对命名用户、拥有组和命名组的访问权限。  

> [!NOTE]
> 对于新的 Data Lake Storage Gen2 文件系统，根目录（“/”）的访问 ACL 的掩码默认为：目录 750，文件 640。 文件不接收 X 位，因为它与只存储系统中的文件无关。
>
> 可能会在每次调用时指定掩码。 这就使不同的使用系统（例如群集）能够为文件操作使用不同的有效掩码。 如果根据特定请求指定了掩码，则该掩码完全替代默认掩码。

### <a name="the-sticky-bit"></a>粘滞位

粘滞位是 POSIX 文件系统更高级的一项功能。 在 Data Lake Storage Gen2 的上下文中，不太可能需要粘滞位。 总之，如果目录上已启用粘滞位，子项只能由子项的拥有用户删除或重命名。

粘滞位不会显示在 Azure 门户中。

## <a name="default-permissions-on-new-files-and-directories"></a>新文件和目录的默认权限

在现有目录下创建新文件或目录时，父目录的默认 ACL 会确定：

- 子目录的默认 ACL 和访问 ACL。
- 子文件的访问 ACL（文件没有默认 ACL）。

### <a name="umask"></a>umask

创建文件或目录时，umask 用于修改默认 ACL 在子项上的设置方式。 umask 是父目录上一个 9 位的值，它包含“拥有用户”、“拥有组”和“其他”的 RWX 值。

Azure Data Lake Storage Gen2 的 umask 是设置为 007 的常量值。 此值将转换为：

| umask 组件     | 数字形式 | 简短形式 | 含义 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 对于拥有用户，将父项的默认 ACL 复制到子项的访问 ACL | 
| umask.owning_group  |    0         |   `---`      | 对于拥有组，将父项的默认 ACL 复制到子项的访问 ACL | 
| umask.other         |    7         |   `RWX`      | 对于其他，删除子项的访问 ACL 的所有权限 |

Azure Data Lake Storage Gen2 的 umask 值实际上表示无论默认 ACL 作何指示，默认情况下永远不会在新子项上传输“其他”的值。 

以下伪代码显示了在为子项创建 ACL 时如何应用 umask。

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>有关 Data Lake Storage Gen2 中 ACL 的常见问题

### <a name="do-i-have-to-enable-support-for-acls"></a>是否必须启用 ACL 的支持？

不是。 只要开启了分层命名空间 (HNS) 功能，Data Lake Storage Gen2 帐户就能通过 ACL 进行访问控制。

即使关闭了 HNS 功能，Azure RBAC 授权规则仍适用。

### <a name="what-is-the-best-way-to-apply-acls"></a>应用 ACL 的最佳方式是什么？

始终将 Azure AD 安全组用作 ACL 中分配的主体。 拒绝直接分配各个用户或服务主体。 使用此结构，你可以添加和删除用户或服务主体，不需要向整个目录结构重新应用 ACL。 而只需要从相应的 Azure AD 安全组添加或删除它们。 请记住，ACL 不是继承的，重新应用 ACL 需要更新每个文件和子目录上的 ACL。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>以递归方式删除目录及其内容需要哪些权限？

- 调用方需要拥有“超级用户”权限，

或

- 父目录必须拥有“写入 + 执行”权限。
- 要删除的目录及其中的每个目录都需要“读取 + 写入 + 执行”权限。

> [!NOTE]
> 删除目录中的文件不需要具有“写入”权限。 另外，永远无法删除根目录“/”。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>谁是文件或目录的所有者？

文件或目录的创建者就是所有者。 就根目录而言，这就是创建文件系统的用户的标识。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>创建文件或目录时将哪个组设置为其拥有组？

拥有组是从创建新文件或目录的父目录的拥有组复制而来的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>文件的拥有用户没有所需的 RWX 权限， 我该怎么办？

拥有用户只需更改文件的权限，即可自动获得所需的任何 RWX 权限。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>为什么我时候在 ACL 中看到了 GUID？

如果条目表示一个用户且该用户不再位于 Azure AD 中，则显示 GUID。 当用户离职，或者其帐户已在 Azure AD 中删除时，往往会发生这种情况。 此外，服务主体和安全组没有用于标识它们的用户主体名称 (UPN)，因此用它们的 OID 属性（即一个 GUID）来表示它们。 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 是否支持 ACL 继承？

Azure RBAC 分配支持继承。 从订阅、资源组和存储帐户资源往下分配到文件系统资源。

ACL 不支持继承。 但是，可以使用默认 ACL 来设置父目录下创建的子目录和文件的 ACL。 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>在哪里可以了解 POSIX 访问控制模型的详细信息？

* [Linux 上的 POSIX 访问控制列表](https://www.linux.com/news/posix-acls-linux)
* [HDFS 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [在 Linux 上使用访问控制列表 (ACL)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>另请参阅

* [Azure Data Lake Storage Gen2 概述](../blobs/data-lake-storage-introduction.md)
