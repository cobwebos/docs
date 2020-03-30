---
title: Azure 数据目录开发人员示例
description: 本文概述了数据目录 REST API 可用的开发人员示例。
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7d0e27802745dda62f87e412053650907e9b812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950158"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure 数据目录开发人员示例

开始使用数据目录 REST API 开发 Azure 数据目录应用。 数据目录 REST API 是基于 REST 的 API，提供对数据目录资源的编程访问，以编程方式注册、批注和搜索数据资产。

## <a name="samples-available-on-githubcom"></a>GitHub.com上的样品

* [Azure 数据目录入门](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   入门示例演示如何使用 Azure AD 进行身份验证以使用数据目录 REST API 注册、搜索和删除数据资产。
   
* [使用服务主体开始使用 Azure 数据目录](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   此示例演示如何使用数据目录 REST API 注册、搜索和删除数据资产。 此示例使用服务主体身份验证。

* [Azure 数据目录的导入/导出工具](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   此示例演示如何使用数据目录 REST API 从 Azure 数据目录提取资产并将其序列化到文件中。 还会演示如何采用序列化为 JSON 的资产并将它们推送到目录。 它支持使用搜索查询导出目录的子集。

* [Azure 数据目录中的批量注册和批号](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   此示例演示如何使用数据目录 REST API 和打开 XML 从 Excel 工作簿批量注册数据资产。
  
* [批量将术语表术语导入到 Azure 数据目录中](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   此示例演示如何从 CSV 文件将术语表术语导入到 ADC 术语表。

* [批量导入到 Azure 数据目录的关系](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   此示例演示如何以编程方式将关系信息从 CSV 文件导入到数据目录中。

* [将关系发布到 Azure 数据目录](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   此示例演示如何以编程方式将关系信息发布到数据目录。
   
## <a name="next-steps"></a>后续步骤
[Azure 数据目录 REST API 引用](/rest/api/datacatalog/)
