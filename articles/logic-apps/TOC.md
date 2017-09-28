# 概述
## [什么是逻辑应用？](logic-apps-what-are-logic-apps.md)
## [使用 Enterprise Integration Pack 进行 B2B 协作](logic-apps-enterprise-integration-overview.md)
## [连接器](../connectors/apis-list.md)

# 入门
## [创建第一个逻辑应用](logic-apps-create-a-logic-app.md)
## [逻辑应用的模板](logic-apps-use-logic-app-templates.md)
## [从模板创建逻辑应用](logic-apps-arm-provision.md)
## [将 MABS 应用移到逻辑应用](logic-apps-move-from-mabs.md)

# 如何
## 构建
### [使用 Visual Studio 构建和部署逻辑应用](logic-apps-deploy-from-vs.md)
### [添加条件和运行工作流](logic-apps-use-logic-app-features.md)
### [添加 switch 语句](logic-apps-switch-case.md)
### [使用 Azure Functions 添加自定义代码](logic-apps-azure-functions.md)
### [循环、范围和数据解除批处理](logic-apps-loops-and-scopes.md)
### [对消息进行批处理](logic-apps-batch-process-send-receive-messages.md)
### [编写逻辑应用定义](logic-apps-author-definitions.md)
### [调用、触发或嵌套逻辑应用](logic-apps-http-endpoint.md)
### 开发自定义连接器
#### [自定义连接器概述](custom-connector-overview.md)
#### [从 Web API 创建连接器](custom-connector-build-web-api-app-tutorial.md)
#### [使用 Azure AD 保护连接器](custom-connector-azure-active-directory-authentication.md)
#### [使用 Postman 描述 API](custom-connector-api-postman-collection.md)
#### [为自定义连接器扩展 OpenAPI](custom-connector-openapi-extensions.md)
#### [注册自定义连接器](logic-apps-custom-connector-register.md)
#### [认证连接器](custom-connector-submit-certification.md)
#### [自定义连接器常见问题解答](custom-connector-faq.md)
### 开发自定义 API
#### [为逻辑应用创建自定义 API](logic-apps-create-api-app.md)
#### [保护对自定义 API 的调用](logic-apps-custom-api-authentication.md)
#### [部署和调用自定义 API](logic-apps-custom-api-host-deploy-call.md)
### [处理错误和异常](logic-apps-exception-handling.md)
### [处理内容类型](logic-apps-content-type.md)
### [保护逻辑应用](logic-apps-securing-a-logic-app.md)
### [故障排除](logic-apps-diagnosing-failures.md)
### [限制和配置](logic-apps-limits-and-config.md)

## 使用 Enterprise Integration Pack 进行 B2B 协作
### [集成帐户](logic-apps-enterprise-integration-create-integration-account.md)
### [合作伙伴](logic-apps-enterprise-integration-partners.md)
### [协议](logic-apps-enterprise-integration-agreements.md)
### [B2B 处理](logic-apps-enterprise-integration-b2b.md)
### [成批发送 EDI 消息](logic-apps-scenario-edi-send-batch-messages.md)
### [XML 处理](logic-apps-enterprise-integration-xml.md)
### [平面文件处理](logic-apps-enterprise-integration-flatfile.md)
### [验证 XML](logic-apps-enterprise-integration-xml-validation.md)
### [为 XML 验证添加架构](logic-apps-enterprise-integration-schemas.md)
### [转换 XML](logic-apps-enterprise-integration-transform.md)
### [为 XML 转换添加映射](logic-apps-enterprise-integration-maps.md)
### [添加证书来确保 B2B 的安全性](logic-apps-enterprise-integration-certificates.md)
### [存储项目的元数据](logic-apps-enterprise-integration-metadata.md)
### [AS2 企业集成](logic-apps-enterprise-integration-as2.md)
### [AS2 编码](logic-apps-enterprise-integration-as2-encode.md)
### [AS2 解码](logic-apps-enterprise-integration-as2-decode.md)
### [EDIFACT 企业集成](logic-apps-enterprise-integration-edifact.md)
### [EDIFACT 编码](logic-apps-enterprise-integration-edifact-encode.md)
### [EDIFACT 解码](logic-apps-enterprise-integration-edifact-decode.md)
### [如何处理具有 UNH2.5 段的 EDIFACT 文档](logic-apps-enterprise-integration-edifact_inputfile_unh2.5.md)
### [X12 企业集成](logic-apps-enterprise-integration-x12.md)
### [X12 编码](logic-apps-enterprise-integration-x12-encode.md)
### [X12 解码](logic-apps-enterprise-integration-x12-decode.md)
### [灾难恢复](logic-apps-enterprise-integration-b2b-business-continuity.md)
### [错误和解决方案](logic-apps-enterprise-integration-b2b-list-errors-solutions.md)

## 访问本地数据
### [连接到本地数据](logic-apps-gateway-connection.md)
### [安装数据网关](logic-apps-gateway-install.md)

## 自动化和部署
### [创建自动部署模板](logic-apps-create-deploy-template.md)
### [从 Visual Studio 发布](logic-apps-deploy-from-vs.md)

## 管理和监视
### [使用 Visual Studio 管理逻辑应用](logic-apps-manage-from-vs.md)
### [管理集成帐户](logic-apps-enterprise-integration-accounts.md)
### [监视逻辑应用状态](logic-apps-monitor-your-logic-apps.md)
### [使用 Log Analytics 在 OMS 中监视逻辑应用状态](logic-apps-monitor-your-logic-apps-oms.md)
### [监视 B2B 消息](logic-apps-monitor-b2b-message.md)
#### [在 OMS 中跟踪 B2B 消息](logic-apps-track-b2b-messages-omsportal.md)
#### [B2B 消息的队列 OMS](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
#### [AS2 跟踪架构](logic-apps-track-integration-account-as2-tracking-schemas.md)
#### [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
#### [自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## [示例、方案和演练](logic-apps-examples-and-scenarios.md)
### [创建无服务器的社交仪表板](logic-apps-scenario-social-serverless.md)
### [使用 Azure Functions 调用逻辑应用](logic-apps-scenario-function-sb-trigger.md)
### [添加错误和异常处理](logic-apps-scenario-error-and-exception-handling.md)
### [B2B 处理](logic-apps-enterprise-integration-b2b.md) 

## 定价和计费
### [定价](https://azure.microsoft.com/pricing/details/logic-apps/)
### [用量计量](logic-apps-pricing.md)

# 无服务器
## 概述
### [Azure 中的无服务器产品/服务](logic-apps-serverless-overview.md)
## 入门
### [在 Visual Studio 中创建无服务器应用](logic-apps-serverless-get-started-vs.md)
## 方案和演练
### [收集客户反馈](logic-apps-scenario-social-serverless.md)
### [发送自动电子邮件](https://channel9.msdn.com/Blogs/Microsoft-Integration/Build-and-Deploy-Serverless-Part-1)
### [B2B/EDI 订单处理](logic-apps-enterprise-integration-overview.md)

# 引用
## [工作流定义语言](logic-apps-workflow-definition-language.md)
## [工作流操作和触发器](logic-apps-workflow-actions-triggers.md)
## [REST API](/rest/api/logic/)
## [Azure PowerShell](/powershell/module/azurerm.logicapp)
## 连接器：深入的主题
### [AS2](logic-apps-enterprise-integration-as2.md)
### [Azure Blob 存储](../connectors/connectors-create-api-azureblobstorage.md)
### [Azure Functions](logic-apps-azure-functions.md)
### [Azure 逻辑应用](logic-apps-http-endpoint.md)
### [Azure SQL 数据库](../connectors/connectors-create-api-sqlazure.md)
### [自定义 API/Web 应用](logic-apps-create-api-app.md)
### [DB2](../connectors/connectors-create-api-db2.md)
### [Dynamics 365 CRM Online](../connectors/connectors-create-api-crmonline.md)
### [EDIFACT](logic-apps-enterprise-integration-edifact.md)
### [事件中心](../connectors/connectors-create-api-azure-event-hubs.md)
### [文件系统](logic-apps-using-file-connector.md)
### [平面文件](logic-apps-enterprise-integration-flatfile.md)
### [FTP](../connectors/connectors-create-api-ftp.md)
### [HTTP](../connectors/connectors-native-http.md)
### [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
### [HTTP Webhook](../connectors/connectors-native-webhook.md)
### [Informix](../connectors/connectors-create-api-informix.md)
### [集成帐户](logic-apps-enterprise-integration-metadata.md)
### [MQ](../connectors/connectors-create-api-mq.md)
### [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)
### [Oracle Database](../connectors/connectors-create-api-oracledatabase.md)
### [请求/响应](../connectors/connectors-native-reqres.md)
### [Salesforce](../connectors/connectors-create-api-salesforce.md)
### [SAP](logic-apps-using-sap-connector.md)
### [计划](../connectors/connectors-native-recurrence.md)
### [服务总线](../connectors/connectors-create-api-servicebus.md)
### [SharePoint Online](../connectors/connectors-create-api-sharepointonline.md)
### [SharePoint Server](../connectors/connectors-create-api-sharepointonline.md)
### [SQL Server](../connectors/connectors-create-api-sqlazure.md)
### [转换 XML](logic-apps-enterprise-integration-transform.md)
### [Twitter](../connectors/connectors-create-api-twitter.md)
### [X12](logic-apps-enterprise-integration-x12.md)
### [XML 验证](logic-apps-enterprise-integration-xml-validation.md)

## [连接器：完整详细信息](https://docs.microsoft.com/connectors/)
## 架构历史记录
### [GA](logic-apps-schema-2016-04-01.md)
### [预览](logic-apps-schema-2015-08-01.md)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azurelogicapps)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=logic-apps)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-logic-apps)
