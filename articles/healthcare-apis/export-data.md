---
title: 通过对 FHIR 的 Azure API 调用 $export 命令执行导出
description: 本文介绍如何使用 $export 导出 FHIR 数据
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 74fe09895f49cc9f7c3cdf6b6c97c1624c3e9c0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839820"
---
# <a name="how-to-export-fhir-data"></a>如何导出 FHIR 数据


大容量导出功能允许按照 [FHIR 规范](https://hl7.org/fhir/uv/bulkdata/export/index.html)从 FHIR 服务器导出数据。 

使用 $export 之前，需确保将用于 FHIR 的 Azure API 配置为使用该 API。 若要配置导出设置和创建 Azure 存储帐户，请参阅 [配置导出数据页](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

配置用于 FHIR 的 Azure API 以进行导出后，可以使用 $export 命令将数据导出到服务外。 数据将存储在配置导出时指定的存储帐户中。 若要了解如何在 FHIR server 中调用 $export 命令，请阅读有关 [$export 规范](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的文档。 

Azure API for FHIR 中的 $export 命令采用一个可选的_ \_ 容器_参数，该参数指定在已配置的存储帐户中要将数据导出到的容器。 如果指定容器，则会将数据导出到名为的新文件夹中的容器。 如果未指定容器，则会将其导出到具有随机生成的名称的新容器。 

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>支持的方案

适用于 FHIR 的 Azure API 支持系统、患者和组级别的 $export。 对于组导出，将导出所有相关资源，但不导出组的特征。

> [!Note] 
> 如果资源位于多个资源的隔离舱中或位于多个组中，$export 将导出重复的资源。

此外，支持在队列中通过 location 标头返回的 URL 检查导出状态，同时支持取消实际的导出作业。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 $export 命令导出 FHIR 资源。 接下来，可以查看支持的功能：
 
>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)
