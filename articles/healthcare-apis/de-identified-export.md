---
title: '导出已取消识别的数据 (预览用于 FHIR 的 Azure API) '
description: 本文介绍如何设置和使用取消识别的导出
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: bdbab0e032764d07119402686051d391376cb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843759"
---
# <a name="exporting-de-identified-data-preview"></a>导出已取消识别的数据 (预览) 

> [!Note] 
> 使用取消识别的导出时的结果将取决于数据输入和客户选择的功能等因素。 Microsoft 无法评估已取消识别的导出输出或确定 acceptability 的客户用例和合规性需求。 不保证取消识别的导出符合任何特定的法律、法规或合规性要求。

"$Export" 命令还可用于从 FHIR 服务器导出已取消识别的数据。 它使用 [匿名的 FHIR 工具](https://github.com/microsoft/FHIR-Tools-for-Anonymization)中的匿名引擎，并在查询参数中使用匿名的配置详细信息。 你可以创建自己的匿名配置文件，或者使用适用于 HIPAA Safe 安全港方法的 [示例配置文件](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 作为起点。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|查询参数            | 示例 |可选性| 说明|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js|对于取消识别的导出是必需的 |配置文件的名称。 请在 [此处](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)查看配置文件格式。 此文件应保留在配置为导出位置的同一 Azure 存储帐户中名为 **匿名** 的容器内。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|对于取消识别的导出是可选的|这是配置文件的 Etag。 可以使用 Azure 存储资源管理器从 blob 属性获取 Etag|

> [!IMPORTANT]
> 在导出配置过程中指定的相同 Azure 存储帐户中，原始导出和取消识别的导出写入。 建议使用与不同的已取消识别的配置对应的不同容器，并在容器级别管理用户访问权限。