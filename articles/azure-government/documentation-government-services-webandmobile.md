---
title: "Azure Government Web、移动和 API |Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Government Web + 移动
## <a name="app-services"></a>应用程序服务
### <a name="variations"></a>变体
Azure App Services 已在 Azure Government 中正式发布。

在 Azure Government 中创建的 Azure App Service 应用的地址与在公有云中创建的不同：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| 应用服务 |*.azurewebsites.net |*.azurewebsites.us|

在公有云中可用的某些应用服务功能在 Azure Government 中尚不可用：

- 应用服务环境
- 应用部署
    - 持续交付（预览）
- 设置
    - vNet 集成和混合连接
    - 安全扫描
- 开发工具
    - 性能测试
    - 资源浏览器
    - PHP 调试
- 监视
    - Application Insights
    - 每个实例的指标
    - 实时 HTTP 流量
    - 应用程序事件
    - FREB 日志
- 技术支持和故障排除
    - 应用服务顾问
    - 失败历史记录
    - 诊断即服务
    - 缓解


### <a name="considerations"></a>注意事项
以下信息标识针对应用服务的 Azure Government 边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| 在 Azure App Service 内输入、存储及处理的数据可包含导出控制数据。 在 Azure App Service 中运行的二进制文件。 静态身份验证器，例如用于访问 Azure 平台组件的密码和智能卡 PIN。 用于管理 Azure 平台组件的证书私钥。 SQL 连接字符串。 其他安全信息/机密信息，例如证书、加密密钥、主密钥和 Azure 服务中存储的存储密钥。 |元数据不允许包含导出控制数据。 此元数据包括创建和维护 Azure App Service 时输入的所有配置数据。 不要将管理/控制数据输入到以下字段：资源组、资源名称、资源标记|

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Microsoft Azure Government 博客](https://blogs.msdn.microsoft.com/azuregov/)。




<!--HONumber=Dec16_HO2-->


