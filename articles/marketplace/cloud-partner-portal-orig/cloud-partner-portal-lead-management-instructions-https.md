---
title: HTTPS 终结点 | Microsoft Docs
description: 为 Https 配置潜在顾客管理。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805220"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 终结点配置潜在顾客管理

可以使用 HTTPS 终结点处理可以写入到 CRM 系统中的 Azure 市场和 AppSource 潜在顾客。 本文介绍了如何使用 Microsoft Flow 自动化服务配置潜在顾客管理。


## <a name="create-a-flow-using-microsoft-flow"></a>使用 Microsoft Flow 创建流

1.  打开[流](https://flow.microsoft.com/)网页。 选择“登录”或者选择“免费注册”来创建免费的流帐户。

2.  登录并在菜单栏上选择“我的流”。

    ![我的流](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  选择“从头开始创建”。

    ![从头开始创建](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  选择“请求/响应”连接器，然后搜索请求触发器。 

    ![从头开始创建](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. 选择“请求”触发器。
    ![请求触发器](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  将本文末尾的 **JSON 示例**复制到**请求正文 JSON 架构**中。

7.  添加一个新步骤，并选择你选择的 CRM 系统以及要新建记录的操作。 下一屏幕截图上显示了“Dynamics 365 - 新建记录”作为示例。

    ![新建记录](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  为你的连接器提供连接器输入并选择“潜在顾客”实体。

    ![选择“潜在顾客”](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  流显示一个用于提供潜在顾客信息的窗体。 可以通过选择添加动态内容来映射输入请求中的项。

    ![添加动态内容](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  映射所需的字段，然后选择“保存”以保存你的流。

11. 将在“请求”中创建一个 HTTP POST URL。 复制此 URL 并使用它作为 HTTPS 终结点。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

为你的产品/服务配置潜在顾客管理信息时，选择“HTTPS 终结点”作为潜在顾客目标并粘贴你在之前的步骤中复制的 HTTP POST URL。  

![添加动态内容](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

当开发出潜在顾客时，Microsoft 会将潜在顾客发送到流，然后这些潜在顾客将被路由到你配置的 CRM 系统。


## <a name="json-example"></a>JSON 示例

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```
