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
ms.openlocfilehash: d274160cc2ed1102dfc8fd11df358b34e40d9923
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "84870295"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>使用自定义 HTTP 标头将数据添加到审核日志

在 Azure 快速医疗保健互操作性资源 (FHIR) API 中，用户可能希望在日志中包含来自调用方系统的其他信息。

例如，当某个外部系统对 API 的用户进行身份验证时，该系统会将调用转发到 FHIR API。 在 FHIR API 层，由于转发了调用，有关原始用户的信息已丢失。 可能有必要记录并保留这些用户信息，以实现审核或管理目的。 调用方系统可以在 HTTP 标头中提供用户标识、调用方位置或其他所需的信息，转发调用时，将一同传递这些信息。

下图显示了此数据流：

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="自定义标头示意图":::

可以使用自定义标头来捕获多种类型的信息。 例如：

* 标识或授权信息
* 调用方来源
* 发起方组织
* 客户端系统详细信息（电子健康记录、患者门户）

> [!IMPORTANT]
> 请注意，在自定义标头中发送的信息先在 Azure 日志监视中提供，然后在 Microsoft 内部日志记录系统中存储 30 天后。 我们建议在将任何信息添加到自定义标头之前先将其加密。 不要通过客户标头传递任何 PHI 信息。

必须对 HTTP 标头使用以下命名约定：X-MS-AZUREFHIR-AUDIT-AUDIT-\<name>。

这些 HTTP 标头包含在添加到日志中的属性包中。 例如：

* X-MS-AZUREFHIR-AUDIT-USERID:1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ:1234

然后，此信息在添加到日志中的属性列时将序列化为 JSON。 例如：

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
与任何 HTTP 标头一样，可以重复使用相同的标头名称，但要指定不同的值。 例如：

* X-MS-AZUREFHIR-AUDIT-USERLOCATION:HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:紧急情况

添加到日志后，将通过一个逗号分隔列表来合并值。 例如：

{ "X-MS-AZUREFHIR-AUDIT-USERLOCATION" :"HospitalA, Emergency" }
 
最多可以添加 10 个唯一标头（标头名称相同但值不同的重复项算作一个标头）。 任一标头的值的最大总长度为 2048 个字符。

如果使用 Firely C# 客户端 API 库，代码将如下所示：

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
在本文中，你已了解如何使用 Azure API for FHIR 中的自定义标头将数据添加到审核日志。 接下来，请了解可在 Azure API for FHIR 中配置的其他设置。
 
>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)