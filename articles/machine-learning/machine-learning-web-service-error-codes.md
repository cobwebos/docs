---
title: "Azure 机器学习 REST API 错误代码 | Microsoft 文档"
description: "在 Azure 机器学习 Web 服务中执行操作时，可能会返回这些错误代码。"
keywords: 
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: e8a6db7963203d747b1f506d0cfae8b3e98d58d3
ms.openlocfilehash: 4b5c9e4c62fbcf548aad1dbe242e2df0f2f5d41f
ms.lasthandoff: 11/18/2016


---
 
# <a name="machine-learning-rest-api-error-codes"></a>机器学习 REST API 错误代码
 
在 Azure 机器学习 Web 服务中执行操作时，可能会返回以下错误代码。
 
## <a name="badargument-http-status-code-400"></a>BadArgument（HTTP 状态代码 400）
 
提供的参数无效。
 
出现此类错误表示某处提供的参数无效。 这可能是某个对象传递给 Web 服务的凭据或 Azure 存储位置。 请查看“details”节中的错误“code”字段，诊断是哪个具体的参数无效。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| BadParameterValue | 提供的参数值不满足参数中的参数规则 |
| BadSubscriptionId | 用于评分的订阅 ID 不是资源中的 ID。 |
| BadVersionCall | API 调用期间传递了无效的版本参数: {0}。 请查看 API 帮助页传递正确的版本，然后重试。 |
| BatchJobInputsNotSpecified | 未在请求中指定以下必需输入: {0}。 请确保指定所有输入数据，然后重试。 |
| BatchJobInputsTooManySpecified | 请求指定的输入数目超过了服务中定义的数目。 接受的输入列表: {0}。 请确保正确指定所有输入数据，然后重试。 |
| BlobNameTooLong | 为诊断输出提供的 Azure Blob 存储路径太长: {0}。 请缩短路径，然后重试。 |
| BlobNotFound | 无法访问提供的 Azure Blob - {0}。  Azure 错误消息: {1}。 |
| ContainerIsEmpty | 未提供 Azure 存储容器名称。 请提供有效的容器名称，然后重试。 |
| ContainerSegmentInvalid | 容器名称无效。 请提供有效的容器名称，然后重试。 |
| ContainerValidationFailed | Blob 容器验证失败并出现以下错误: {0}。 |
| DataTypeNotSupported | 提供了不支持的数据类型。 请提供有效的数据类型，然后重试。 |
| DuplicateInputInBatchCall | 批处理请求无效。 不能同时指定单输入和多输入。 请从请求中删除这些项中的一个，然后重试。 |
| ExpiryTimeInThePast | 提供的过期时间已过去: {0}。 请提供将来的过期时间(UTC)，然后重试。 若要设置为永不过期，请将过期时间设置为 NULL。 |
| IncompleteSettings | 诊断设置不完整。 |
| InputBlobRelativeLocationInvalid | 未提供 Azure 存储 Blob 名称。 请提供有效的 Blob 名称，然后重试。 |
| InvalidBlob | Blob 的 Blob 规范无效: {0}。 请检查连接字符串/相对路径或 SAS 令牌规范是否正确，然后重试。 |
| InvalidBlobConnectionString | 为某个输入/输出 Blob 指定的连接字符串无效: {0}。 请更正此问题，然后重试。 |
| InvalidBlobExtension | Blob 引用: {0} 的某个文件扩展名无效或缺失。 此输出类型支持的文件扩展名为: "{1}"。 |
| InvalidInputNames | 在请求中指定了无效的服务输入名称: {0}。 请将输入数据映射到正确的服务输入，然后重试。 |
| InvalidOutputOverrideName | 输出重写名称无效: {0}。 服务中没有任何输出节点使用此名称。 请向重写传入正确的输出节点名称(区分大小写)。 |
| InvalidQueryParameter | 查询参数“{0}”无效。 {1} |
| MissingInputBlobInformation | 缺少 Azure 存储 Blob 信息。 请提供有效的连接字符串和相对路径或 URI，然后重试。 |
| MissingJobId | 未提供作业 ID。 首次提交作业时会返回作业 ID。 请检查作业 ID 是否正确，然后重试。 |
| MissingKeys | 未提供任何密钥，或者未提供某个主密钥或辅助密钥。 |
| MissingModelPackage | 未提供模型包 ID 或模型包。 请提供有效的模型包 ID 或模型包，然后重试。 |
| MissingOutputOverrideSpecification | 请求中缺少输出重写 {0} 的 Blob 规范。 请在请求中指定有效的 Blob 位置；如果不需要位置重写，请删除输出规范。 |
| MissingRequestInput | Web 服务要求提供输入，但未提供任何输入。 请确保根据模型中发布的输入端口提供有效输入，然后重试。 |
| MissingRequiredGlobalParameters | 未提供所有必需的 Web 服务参数。 请检查模块所需的参数是否正确，然后重试。 |
| MissingRequiredOutputOverrides | 调用加密的服务终结点时，必须为服务的所有输出传入输出重写。 以下输出目前缺少重写: {0} |
| MissingWebServiceGroupId | 未提供 Web 服务组 ID。 请提供有效的 Web 服务组 ID，然后重试。 |
| MissingWebServiceId | 未提供 Web 服务 ID。 请提供有效的 Web 服务 ID，然后重试。 |
| MissingWebServicePackage | 未提供 Web 服务包。 请提供有效的 Web 服务包，然后重试。 |
| MissingWorkspaceId | 未提供工作区 ID。 请提供有效的工作区 ID，然后重试。 |
| ModelConfigurationInvalid | 模型包中的模型配置无效。 请确保模型配置包含输出终结点定义、std 错误终结点和 std out 终结点，然后重试。 |
| ModelPackageIdInvalid | 模型包 ID 无效。 请检查模型包 ID 是否正确，然后重试。 |
| RequestBodyInvalid | 未提供请求正文，或者反序列化请求正文时出错。 |
| RequestIsEmpty | 未提供请求。 请提供有效请求，然后重试。 |
| UnexpectedParameter | 提供了意外的参数。 请检查所有参数名称是否拼写正确，只传递所需的参数，然后重试。 |
| UnknownError | 未知错误。 |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | 无法更改 {0} Web 服务的并发请求要求。 |
| WebServiceIdInvalid | 提供的 Web 服务 ID 无效。 Web 服务 ID 应是有效的 GUID。 |
| WebServiceTooManyConcurrentRequestRequirement | 无法将并发请求要求设置为超过 {0}。 |
| WebServiceTypeInvalid | 提供的 Web 服务类型无效。 请检查 Web 服务类型是否正确，然后重试。 有效的 Web 服务类型: {0}。 |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument（HTTP 状态代码 400）
 
提供的用户参数无效。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| InputMismatchError | 输入数据与输入端口架构不匹配。 |
| InputParseError | 分析输入向量失败。  请检查输入向量是否包含正确的列数和数据类型。  其他详细信息: {0}。 |
| MissingRequiredGlobalParameters | 缺少 Web 服务所需的参数。 请检查 Web 服务的所有必需参数是否正确，然后重试。 |
| UnexpectedParameter | 请检查是否已传递 Web 服务的所有必需参数，然后重试。 |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation（HTTP 状态代码 400）
 
请求在当前上下文中无效。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| CannotStartJob | 作业处于 {0} 状态，因此无法启动。 |
| IncompatibleModel | 模型与请求版本不兼容。 该请求版本仅支持单一数据库输出模型。 |
| MultipleInputsNotAllowed | 该模型不允许多个输入。 |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError（HTTP 状态代码 400）
 
执行模块时遇到内部库错误。
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError（HTTP 状态代码 400）
 
执行模块时遇到错误。
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError（HTTP 状态代码 400）
 
Web 服务包无效。 请检查提供的 Web 服务包是否正确，然后重试。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| FormatError | Web 服务包格式不正确。 详细信息: {0} |
| RuntimesError | Web 服务包图形无效。 详细信息: {0} |
| ValidationError | Web 服务包图形无效。 详细信息: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Unauthorized（HTTP 状态代码 401）
 
请求未获授权，无法访问资源。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| AdminRequestUnauthorized | 未授权 |
| ManagementRequestUnauthorized | 未授权 |
| ScoreRequestUnauthorized | 提供的凭据无效。 |
 
## <a name="notfound-http-status-code-404"></a>NotFound（HTTP 状态代码 404）
 
找不到资源。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| ModelPackageNotFound | 找不到模型包。 请检查模型包 ID 是否正确，然后重试。 |
| WebServiceIdNotFoundInWorkspace | 在此工作区下找不到 Web 服务。 webServiceId 与 workspaceId 之间不匹配。 请检查提供的 Web 服务是否属于该工作区，然后重试。 |
| WebServiceNotFound | 找不到 Web 服务。 请检查 Web 服务 ID 是否正确，然后重试。 |
| WorkspaceNotFound | 找不到工作区。 请检查工作区 ID 是否正确，然后重试。 |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout（HTTP 状态代码 408）
 
无法在允许的时间内完成该操作。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| RequestCanceled | 客户端已取消请求。 |
| ScoreRequestTimeout | 执行请求超时。 |
 
## <a name="conflict-http-status-code-409"></a>Conflict（HTTP 状态代码 409）
 
资源已存在。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| ModelOutputMetadataMismatch | 输出参数名称无效。 请尝试使用元数据编辑器模块将列重命名，然后重试。 |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation（HTTP 状态代码 413）
 
模型超出了分配的内存配额。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| OutOfMemoryLimit | 模型消耗的内存超过了分配量。 允许模型消耗的最大内存量为 {0} MB。 请检查模型是否有问题。 |
 
## <a name="internalerror-http-status-code-500"></a>InternalError（HTTP 状态代码 500）
 
执行时遇到内部错误。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | 容器进程崩溃并出现系统错误 |
| ContainerProcessTerminatedWithUnknownError | 容器进程崩溃并出现未知错误 |
| ContainerValidationFailed | Blob 容器验证失败并出现以下错误: {0}。 |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration，ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | 未提供参数。 请检查是否传递了有效参数，然后重试。 |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | ID 为 {0} 的端口使用了不支持的数据类型: {1}。 |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger 生成失败，详细信息: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | 未知的作业状态代码 {0}。 |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage，详细信息: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory（HTTP 状态代码 500）
 
执行时遇到内部错误。 系统内存不足。 请重试。
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError（HTTP 状态代码 500）
 
模型包无效。 请检查提供的模型包是否正确，然后重试。
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError（HTTP 状态代码 500）
 
Web 服务包无效。 请检查提供的 Web 包是否正确，然后重试。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| ModuleError | Web 服务包图形无效。 详细信息: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers（HTTP 状态代码 503）
 
正在初始化容器，请求无法执行。
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable（HTTP 状态代码 503）
 
服务暂时不可用。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| NoMoreResources | 请求没有可用的资源。 |
| RequestThrottled | 为 {0} 终结点限制了请求。 该终结点的最大并发性为 {1}。 |
| TooManyConcurrentRequests | 发送的并发请求过多。 |
| TooManyHostsBeingInitialized | 同时初始化的主机数过多。 请考虑限制/重试。 |
| TooManyHostsBeingInitializedPerModel | 同时初始化的主机数过多。 请考虑限制/重试。 |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout（HTTP 状态代码 504）
 
无法在允许的时间内完成该操作。
 
| 错误代码 | 用户消息 |
| ---------- |--------------|
| BackendInitializationTimeout | 无法在允许的时间内完成 Web 服务初始化。 |
| BackendScoreTimeout | 无法在允许的时间内完成 Web 服务请求执行。 |
 

