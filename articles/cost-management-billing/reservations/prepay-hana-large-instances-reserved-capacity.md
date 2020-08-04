---
title: 使用 Azure 预留节省 SAP HANA 大型实例
description: 了解购买 HANA 大型实例预留之前需注意的事项以及如何进行购买。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: cefb6b2cb2908b9c7311df745417686b52241bab
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286557"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>使用 Azure 预留节省 SAP HANA 大型实例

在预购买一年或三年的 Azure 预留时，可以节省 SAP HANA 大型实例 (HLI) 成本。 预留折扣应用于与购买的预留实例匹配的预配 HLI SKU。 本文可帮助你了解购买预留之前需注意的事项以及如何进行购买。

购买预留后，你将承诺在一年或三年内使用 HLI。 购买的 HLI 预留容量包括与 SKU 绑定的计算和 NFS 存储。 预留不包括软件许可成本，如操作系统、SAP 或额外的存储成本。 预留折扣将自动应用到预配的 SAP HLI。 当预留期限结束时，即用即付费率将应用于预配资源。

## <a name="purchase-considerations"></a>购买注意事项

必须先预配一个 HLI SKU，然后才能完成预留容量购买。 通过提前付款或按月付款的方式为预留付款。 下面的限制适用于 HLI 预留容量：

- 预留折扣仅适用于企业协议和 Microsoft 客户协议订阅。 不支持其他订阅。
- HLI 预留容量不支持实例大小灵活性。 预留仅适用于为其购买的 SKU 和区域。
- 不支持自助取消和交换。
- 预留容量范围是单一范围，因此它适用于单个订阅和资源组。 无法更新购买的容量以供其他订阅使用。
- 不能为 HANA 预留容量提供共享预留范围。 无法拆分、合并或更新预留范围。
- 可以使用预留容量 API 调用一次购买一个 HLI。 若要购买更多数量，请进行额外的 API 调用。

可以在 Azure 门户或使用 [REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase) 购买预留容量。

## <a name="buy-a-hana-large-instance-reservation"></a>购买 HANA 大型实例预留

使用以下信息，通过[预留订单 REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase) 购买 HLI 预留。

### <a name="get-the-reservation-order-and-price"></a>获取预留订单和价格

首先，使用[计算价格](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/calculate) API，获取预配 HANA 大型实例 SKU 的预留订单和价格。

下面的示例使用 [armclient](https://github.com/projectkudu/ARMClient) 通过 PowerShell 进行 REST API 调用。 预留订单和计算价格 API 请求以及请求正文应如下所示：

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

有关数据字段及其说明的详细信息，请参阅 [HLI 预留字段](#hli-reservation-fields)。

下面的示例响应类似于返回的内容。 请注意为 `quoteId` 返回的值。

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>进行购买

使用返回的 `quoteId` 以及前面的[获取预留订单和价格](#get-the-reservation-order-and-price)部分中获得的 `reservationOrderId` 进行购买。

下面是一个示例请求：

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

下面是一个示例响应。 如果成功下达订单，则 `provisioningState` 应为 `creating`。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>验证购买状态是否成功

运行预留订单 GET 请求，查看采购订单的状态。 `provisioningState` 应为 `Succeeded`。

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

响应应如以下示例所示。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI 预留字段

以下信息说明了各种预留字段的含义。

  SKU：HLI SKU 名称。 应类似于 `SAP_HANA_On_Azure_<SKUname>`。

  位置：可用的 HLI 区域。 有关可用区域，请参阅 [Azure 上的 SAP HANA（大型实例）的 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)。 若要获取位置字符串格式，请使用[获取位置 API 调用](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations#locationlistresult)。

  **保留的资源类型** `SapHana`

  订阅：用于支付预留费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P or MS-AZR-0148P）或 Microsoft 客户协议。 从货币承诺余额中扣除费用（如果可用）或作为超额收取费用。

  范围：预留范围应为单一范围。

  期限：一年或三年。 类似于 `P1Y` 或 `P3Y`。

  数量：为预留购买的实例数。 要购买的数量为一次一个 HLI。 对于其他预留，请对相应的字段重复执行 API 调用。

## <a name="troubleshoot-errors"></a>排查错误

在购买预留时，可能会收到类似于以下示例的错误。 可能的原因是未预配 HLI 进行购买。 如果是这样，请联系你的 Microsoft 帐户团队，以在尝试进行预留购买之前获取预配的 HLI。

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Postman 和 cURL 调用 Azure REST API](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)。
- 请参阅 [Azure 上的 SAP HANA（大型实例）的 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus) 获取可用的 SKU 列表和区域。