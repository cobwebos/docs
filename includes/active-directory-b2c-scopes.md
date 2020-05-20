---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183361"
---
#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 选择“应用程序”。 
1. 选择“webapi1”  应用程序以打开其属性页。 
1. 选择“已发布的范围”  。 可以使用已发布的范围向客户端应用程序授予访问 Web API 的某些权限。
1. 在“范围”中输入 `demo.read`，在“说明”中输入 `Read access to the web API`。
1. 在“范围”中输入 `demo.write`，在“说明”中输入 `Write access to the web API`。
1. 选择“保存”。 

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 选择“应用注册(预览版)”  。
1. 选择“webapi1”  应用程序以打开其“概览”页。 
1. 在“管理”下，选择“公开 API”   。
1. 选择“应用程序 ID URI”旁边的“设置”链接。  
1. 将默认值（一个 GUID）替换为 `api`，然后选择“保存”。  完整的 URI 已显示，应采用 `https://your-tenant-name.onmicrosoft.com/api` 格式。 Web 应用程序在请求 API 的访问令牌时，应将此 URI 添加为你为 API 定义的每个范围的前缀。
1. 在“此 API 定义的范围”下选择“添加范围”。  
1. 输入以下值来创建一个定义对 API 的读取访问权限的范围，然后选择“添加范围”： 
    1. **范围名称**：`demo.read`
    1. **管理员许可显示名称**：`Read access to demo API`
    1. **管理员许可说明**：`Allows read access to the demo API`
1. 选择“添加范围”，输入以下值来添加一个定义对 API 的写入访问权限的范围，然后选择“添加范围”：  
    1. **范围名称**：`demo.write`
    1. **管理员许可显示名称**：`Write access to demo API`
    1. **管理员许可说明**：`Allows write access to the demo API`