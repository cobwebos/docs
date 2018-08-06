---
title: 如何将 Azure Blob 存储装载为 Linux 上的文件系统 | Microsoft Docs
description: 使用 FUSE 在 Linux 上装载 Azure Blob 存储容器
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 05/10/2018
ms.author: seguler
ms.openlocfilehash: 9964aa4d263e0b75eb59b4e1434a9b3f0aac6ea1
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400180"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>如何使用 Blobfuse 将 Blob 存储装载为文件系统

## <a name="overview"></a>概述
[Blobfuse](https://github.com/Azure/azure-storage-fuse) 是适用于 Azure Blob 存储的虚拟文件系统驱动程序，用于通过 Linux 文件系统访问存储帐户中的现有块 Blob 数据。 Azure Blob 存储是一项对象存储服务，因此没有分层命名空间。 Blobfuse 使用虚拟目录方案提供这种命名空间，并使用正斜杠“/”作为分隔符。  

本指南介绍如何使用 Blobfuse，以及如何在 Linux 上装载 Blob 存储容器并访问数据。 若要详细了解 Blobfuse，请阅读 [Blobfuse 存储库](https://github.com/Azure/azure-storage-fuse)中的详细信息。

> [!WARNING]
> Blobfuse 不保证 100% 的 POSIX 符合性，因为它只是将请求转换成 [Blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)。 例如，重命名操作在 POSIX 中是原子操作，但在 Blobfuse 中不是。
> 有关本机文件系统和 Blobfuse 之间差异的完整列表，请访问 [Blobfuse 源代码存储库](https://github.com/azure/azure-storage-fuse)。
> 

## <a name="install-blobfuse-on-linux"></a>在 Linux 上安装 Blobfuse
Blobfuse 二进制文件在[适用于 Linux 的 Microsoft 软件存储库](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)中提供。 若要安装 Blobfuse，请配置一个此类存储库。

### <a name="configure-the-microsoft-package-repository"></a>配置 Microsoft 包存储库
配置 [Microsoft 产品的 Linux 包存储库](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)。

例如，在 Enterprise Linux 6 发行版中：
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

类似地，将 URL 更改为 `.../rhel/7/...`，使之指向 Enterprise Linux 7 发行版。

Ubuntu 14.04 上的另一个示例：
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

类似地，将 URL 更改为 `.../ubuntu/16.04/...`，使之指向 Ubuntu 16.04 发行版。

### <a name="install-blobfuse"></a>安装 Blobfuse

在 Ubuntu/Debian 发行版中：
```bash
sudo apt-get install blobfuse
```

在 Enterprise Linux 发行版中：
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>准备装载
Blobfuse 要求文件系统中存在一个临时路径，用于缓冲和缓存任何打开的文件，以便提供类似本机的性能。 对于此临时路径，请选择性能最高的磁盘，或者使用 ramdisk 来获得最佳性能。 

> [!NOTE]
> Blobfuse 将所有打开的文件内容存储在临时路径中。 请确保有足够的空间来容纳所有打开的文件。 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>（可选）将 ramdisk 用于临时路径
以下示例创建 16 GB 的 ramdisk，并创建适用于 Blobfuse 的目录。 请根据需要选择大小。 此 ramdisk 允许 Blobfuse 打开多个文件，只要其大小总计不超过 16 GB 即可。 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>将 SSD 用于临时路径
在 Azure 中，可以使用 VM 上提供的临时磁盘 (SSD)，为 Blobfuse 提供低延迟缓冲区。 此临时磁盘在 Ubuntu 发行版中装载在“/mnt”上，而在 Red Hat 和 CentOS 发行版中则装载在“/mnt/resource/”上。

请确保用户可以访问临时路径：
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>配置存储帐户凭据
Blobfuse 要求将凭据采用以下格式存储在文本文件中： 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

创建此文件以后，请确保限制对它的访问权限，防止其他用户读取它。
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>创建装载用的空目录
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>装载

> [!NOTE]
> 有关装载选项的完整列表，请查看 [Blobfuse 存储库](https://github.com/Azure/azure-storage-fuse#mount-options)。  
> 

若要装载 Blobfuse，请以用户身份运行以下命令。 此命令将“/path/to/fuse_connection.cfg”中指定的容器装载到“/mycontainer”位置。

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

现在应该可以通过常规文件系统 API 访问块 Blob。 请注意，装载的目录只能由装载它的用户访问，这样是为了确保访问安全。 如果希望所有用户都能够访问，可以通过选项 ```-o allow_other``` 进行装载。 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>后续步骤

* [Blobfuse 主页](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [报告 Blobfuse 问题](https://github.com/Azure/azure-storage-fuse/issues) 

