---
title: 通过对 FHIR 的 Azure API 调用 $export 命令执行导出
description: 本文介绍如何设置和使用取消识别的导出
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482311"
---
# <a name="how-to-export-fhir-data"></a>如何导出 FHIR 数据

若要配置导出设置和创建 Azure 存储帐户，请参阅 [此处](configure-export-data.md)。

## <a name="exporting-fhir-resources-using-export-command"></a>使用 $export 命令导出 FHIR 资源

配置用于 FHIR 的 Azure API 进行导出后，现在可以使用 $export 命令将该服务中的数据导出到配置导出时指定的存储帐户。 若要了解如何在 FHIR server 中调用 $export 命令，请参阅中有关 $export 规范的文档 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 。 

Azure API for FHIR 中的 $export 命令采用可选的_ \_ conatiner_参数，该参数可用于指定应将数据导出到的已配置存储帐户中的容器。

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> 请注意，当前的 Azure API for FHIR 仅支持在 $export 规范中定义的系统级导出 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 。 而且 _，只有当前 \_ 支持_query 参数。

## <a name="exporting-de-identified-data-preview"></a>导出已取消识别的数据 (预览) 

"$Export" 命令还可用于从 FHIR 服务器导出已取消识别的数据。 它使用 [匿名的 FHIR 工具](https://github.com/microsoft/FHIR-Tools-for-Anonymization)中的匿名引擎，并在查询参数中使用匿名的配置详细信息。 你可以创建自己的匿名配置文件，或者使用适用于 HIPAA Safe 安全港方法的 [示例配置文件](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 作为起点。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|查询参数            | 示例 |可选性| 说明|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js|对于取消识别的导出是必需的 |配置文件的名称。 请在 [此处](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)查看配置文件格式。 此文件应保留在配置为导出位置的同一 Azure 存储帐户中名为 **匿名** 的容器内。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|对于取消识别的导出是可选的|这是配置文件的 Etag。 可以使用 blob 属性中的 Azure 存储资源管理器获取 Etag|

> [!IMPORTANT]
> 请注意，在导出配置过程中指定的相同 Azure 存储帐户中，原始导出和取消识别的导出写入。 建议使用与不同的已取消识别的配置对应的不同容器，并在容器级别管理用户访问权限。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 $export 命令（包括取消识别的数据）导出 FHIR 资源。 接下来，可以配置导出数据：
 
>[!div class="nextstepaction"]
>[配置导出数据](configure-export-data.md)
