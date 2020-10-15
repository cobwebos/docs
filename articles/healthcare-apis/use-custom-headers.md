---
title: 使用自定义标头将数据添加到审核日志 - Azure API for FHIR
description: 本文介绍如何使用 Azure API for FHIR 中的自定义 HTTP 标头将数据添加到审核日志。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081837"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>使用自定义 HTTP 头将数据添加到审核日志

在 Azure 快速医疗保健互操作性资源 (FHIR) API 中，用户可能希望在日志中包含来自调用系统的其他信息。

例如，当通过外部系统对 API 的用户进行身份验证时，该系统会将调用转发到 FHIR API。 在 FHIR API 层上，由于已转发调用，因此有关原始用户的信息已丢失。 出于审核或管理目的，可能需要记录和保留此用户信息。 调用系统可以在 HTTP 标头中提供用户标识、调用方位置或其他所需的信息，转发调用时会携带这些信息。

可以在下图中看到此数据流：

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="自定义标头关系图&quot;:::

可以使用自定义标头来捕获多种类型的信息。 例如：

* 标识或授权信息
* 调用方的源点
* 发起组织
* 客户端系统详细信息（电子病历、患者门户）

> [!IMPORTANT]
> 请注意，自定义标头中发送的信息在 Azure 日志监控中可用之后，会在 Microsoft 内部日志记录系统中存储 30 天。 我们建议在将任何信息添加到自定义标头之前对其进行加密。 不应通过自定义标头传递任何 PHI 信息。

必须为 HTTP 标头使用以下命名约定：X-MS-AZUREFHIR-AUDIT-\<name>。

这些 HTTP 标头包含在添加到日志的属性包中。 例如：

* X-MS-AZUREFHIR-AUDIT-USERID：1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION：XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ：1234

将此信息添加到日志中的属性列后，此信息会序列化为 JSON。 例如：

```json
{ &quot;X-MS-AZUREFHIR-AUDIT-USERID&quot; : &quot;1234&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; : &quot;XXXX&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-XYZ&quot; : &quot;1234&quot; }
```
 
与任何 HTTP 标头一样，相同的标头名称也可以用不同的值重复。 例如：

* X-MS-AZUREFHIR-AUDIT-USERLOCATION：HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION：紧急情况

将这些值添加到日志时，这些值会与逗号分隔的列表合并。 例如：

{ &quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; :&quot;HospitalA, Emergency" }
 
最多可以添加 10 个唯一标头（具有不同值的相同标头的重复仅计为一）。 任何一个标头值的总最大长度为 2048 个字符。

如果使用的是 Firefly C# 客户端 API 库，代码将如下所示：

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>后续步骤
在本文中，你了解了如何使用 Azure API for FHIR 中的自定义标头将数据添加到审核日志。 接下来，了解可在 Azure API for FHIR 中配置的其他设置。
 
>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
