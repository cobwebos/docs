---
title: include 文件
description: include 文件
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665945"
---
提供以下角色用于协作：

|角色|功能|API 访问|API 权限|
|--|--|--|--|
|所有者|全部|身份验证密钥|全部|
|参与者|向角色添加新成员的所有功能除外|身份验证密钥|向角色添加新成员的所有功能除外|
|QnA Maker 读取<br>读取|导出/下载<br>测试|持有者令牌|1. 下载 KB API<br>2. 列出用户 API 的 Kb<br>3. 获取知识库详细信息<br>4. 下载变更<br>生成答案 |
|QnA Maker 编辑器<br>（读/写）|导出/下载<br>测试<br>更新 KB<br>导出 KB<br>导入 KB<br>替换 KB<br>创建知识库|持有者令牌|1. 创建 KB API<br>2. 更新知识库 API<br>3. 替换 KB API<br>4. 替换变更<br>5. "训练 API" [在新的服务模型 v5] 中|
|认知服务用户<br>（读取/写入/发布）|全部|持有者令牌|全部|