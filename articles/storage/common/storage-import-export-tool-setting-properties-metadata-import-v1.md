---
title: "使用 Azure 导入/导出设置属性和元数据 - v1 | Microsoft Docs"
description: "了解如何在运行 Azure 导入/导出工具准备驱动器时，指定要对目标 blob 设置的属性和元数据。 本文所述的导入/导出工具为 v1 版本。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>在导入过程中设置属性和元数据
在运行 Microsoft Azure 导入/导出工具准备驱动器时，可以指定要对目标 blob 设置的属性和元数据。 执行以下步骤:  
  
1.  若要设置 Blob 属性，请在本地计算机上创建一个指定属性名称和值的文本文件。  
  
2.  若要设置 Blob 元数据，请在本地计算机上创建一个指定元数据名称和值的文本文件。  
  
3.  在 `PrepImport` 操作过程中，将其中一个或两个文件的完整路径传递给 Azure 导入/导出工具。  
  
> [!NOTE]
>  将某个属性或元数据文件指定为复制会话的一部分时，将为作为该复制会话一部分导入的每个 Blob 设置这些属性或元数据。 如果想要为导入的某些 Blob 指定一组不同的属性或元数据，需要创建包含不同属性或元数据文件的单独复制会话。  
  
## <a name="specify-blob-properties-in-a-text-file"></a>在文本文件中指定 Blob 属性  
要指定 Blob 属性，请创建一个本地文本文件，同时包含将属性名称指定为元素、将属性值指定为值的 XML。 以下示例演示如何指定一些属性值：  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
将该文件保存到本地位置，如 `C:\WAImportExport\ImportProperties.txt`。  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>在文本文件中指定 Blob 元数据  
同样，要指定 Blob 元数据，请创建一个本地文本文件，用于将元数据名称指定为元素、将元数据值指定为值。 以下示例演示如何指定一些元数据值：  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
将该文件保存到本地位置，如 `C:\WAImportExport\ImportMetadata.txt`。  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>创建包含属性或元数据文件的复制会话  
运行 Azure 导入/导出工具准备导入作业时，可以使用 `PropertyFile` 参数在命令行中指定属性文件。 可以使用 `/MetadataFile` 参数在命令行中指定元数据文件。 以下示例演示如何指定这两个文件：  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>后续步骤

* [导入/导出服务元数据和属性文件格式](../storage-import-export-file-format-metadata-and-properties.md)
