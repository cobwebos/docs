---
title: 在 Azure 机器学习中创建用于模型管理的 Docker 映像 | Microsoft Docs
description: 本文介绍为 Web 服务创建 Docker 映像的步骤。
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 3efc351861a7d5b3c02c08b96494e83a3ccb99f6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362135"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure 机器学习模型管理帐户 API 参考

有关设置部署环境的信息，请参阅[模型管理帐户设置](deployment-setup-configuration.md)。

Azure 机器学习模型管理帐户 API 实现以下操作：

- 模型的注册
- 清单的创建
- Docker 映像的创建
- Web 服务的创建

可以使用此映像在本地，或者在远程 Azure 容器服务群集或 Docker 支持的另一所选环境中创建 Web 服务。

## <a name="prerequisites"></a>先决条件
请确保已完成[安装和创建快速入门](../service/quickstart-installation.md)文档中所述的每个安装步骤。

在继续下一步之前，需要满足以下条件：
1. 完成模型管理帐户预配
2. 创建用于部署和管理模型的环境
3. 机器学习模型

### <a name="azure-ad-token"></a>Azure AD 令牌
如果使用 Azure CLI，请使用 `az login` 进行登录。 CLI 将从 .azure 文件中使用 Azure Active Directory (Azure AD) 令牌。 如果想要使用 API，可使用以下选项。

##### <a name="acquire-the-azure-ad-token-manually"></a>手动获取 Azure AD 令牌
可以使用 `az login`，从主目录中的 .azure 文件中获取最新令牌。

##### <a name="acquire-the-azure-ad-token-programmatically"></a>以编程的方式获取 Azure AD 令牌
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
创建服务主体后，请保存输出。 现在，可以使用该服务主体从 Azure AD 中获取令牌：

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
令牌放在 API 调用的授权标头中。


## <a name="register-a-model"></a>注册模型

模型注册步骤会将机器学习模型注册到所创建的 Azure 模型管理帐户。 通过此项注册，可以跟踪注册时分配的模型及其版本。 用户提供模型的名称。 以后使用同一名称注册模型会生成新的版本和 ID。

### <a name="request"></a>请求

| 方法 | 请求 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>说明
注册模型。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| model | body | 用于注册模型的有效负载。 | 是 | [Model](#model) |


### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 没问题。 模型注册成功。 | [Model](#model) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>查询帐户中的模型列表
### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>说明
查询帐户中的模型列表。 可按标记和名称筛选结果列表。 如果未传递任何筛选器，查询会列出帐户中的所有模型。 返回的列表会分页，每页中的项数是一个可选参数。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| 名称 | query | 对象名称。 | 否 | 字符串 |
| 标记 | query | 模型标记。 | 否 | 字符串 |
| 计数 | query | 要在页中检索的项数。 | 否 | 字符串 |
| $skipToken | query | 用于检索下一页的继续标记。 | 否 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [PaginatedModelList](#paginatedmodellist) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>获取模型详细信息
### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>说明
按 ID 获取模型。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 对象 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [Model](#model) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>将清单注册到已注册的模型和所有依赖项

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>说明
将清单注册到已注册的模型和所有依赖项。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| manifestRequest | body | 用于注册清单的有效负载。 | 是 | [Manifest](#manifest) |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 清单注册成功。 | [Manifest](#manifest) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>查询帐户中的清单列表

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>说明
查询帐户中的清单列表。 可以按模型 ID 和清单名称筛选结果列表。 如果未传递任何筛选器，查询会列出帐户中的所有清单。 返回的列表会分页，每页中的项数是一个可选参数。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| modelId | query | 模型 ID。 | 否 | 字符串 |
| manifestName | query | 清单名称。 | 否 | 字符串 |
| 计数 | query | 要在页中检索的项数。 | 否 | 字符串 |
| $skipToken | query | 用于检索下一页的继续标记。 | 否 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [PaginatedManifestList](#paginatedmanifestlist) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>获取清单详细信息

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>说明
按 ID 获取清单。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 对象 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [Manifest](#manifest) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>创建映像

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>说明
创建映像，作为 Azure 容器注册表中的 Docker 映像。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| imageRequest | body | 用于创建映像的有效负载。 | 是 | [ImageRequest](#imagerequest) |

### <a name="responses"></a>响应
| 代码 | 说明 | 标头 | 架构 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 异步操作位置 URL。 一个显示映像创建任务状态的 GET 调用。 | Operation-Location |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>查询帐户中的映像列表

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>说明
查询帐户中的映像列表。 可以按清单 ID 和清单名称筛选结果列表。 如果未传递任何筛选器，查询会列出帐户中的所有映像。 返回的列表会分页，每页中的项数是一个可选参数。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| manifestId | query | 清单 ID。 | 否 | 字符串 |
| manifestName | query | 清单名称。 | 否 | 字符串 |
| 计数 | query | 要在页中检索的项数。 | 否 | 字符串 |
| $skipToken | query | 用于检索下一页的继续标记。 | 否 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [PaginatedImageList](#paginatedimagelist) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>获取映像详细信息

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>说明
按 ID 获取映像。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 映像 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [图像](#image) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>创建服务

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>说明
从映像创建服务。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| serviceRequest | body | 用于创建服务的有效负载。 | 是 | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>响应
| 代码 | 说明 | 标头 | 架构 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 异步操作位置 URL。 一个显示服务创建任务状态的 GET 调用。 | Operation-Location |
| 409 | 已存在具有指定名称的服务。 |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>查询帐户中的服务列表

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>说明
查询帐户中的服务列表。 可以按模型名称/ID、清单名称/ID、映像 ID、服务名称或机器学习计算资源 ID 筛选结果列表。 如果未传递任何筛选器，查询会列出帐户中的所有服务。 返回的列表会分页，每页中的项数是一个可选参数。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| serviceName | query | 服务名称。 | 否 | 字符串 |
| modelId | query | 模型名称。 | 否 | 字符串 |
| modelName | query | 模型 ID。 | 否 | 字符串 |
| manifestId | query | 清单 ID。 | 否 | 字符串 |
| manifestName | query | 清单名称。 | 否 | 字符串 |
| imageId | query | 映像 ID。 | 否 | 字符串 |
| computeResourceId | query | 机器学习计算资源 ID。 | 否 | 字符串 |
| 计数 | query | 要在页中检索的项数。 | 否 | 字符串 |
| $skipToken | query | 用于检索下一页的继续标记。 | 否 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [PaginatedServiceList](#paginatedservicelist) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>获取服务详细信息

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>说明
按 ID 获取服务。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 对象 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [ServiceResponse](#serviceresponse) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>更新服务

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>说明
更新现有服务。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 对象 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| serviceUpdateRequest | body | 用于更新现有服务的有效负载。 | 是 |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>响应
| 代码 | 说明 | 标头 | 架构 |
|--------------------|--------------------|--------------------|--------------------|
| 202 | 异步操作位置 URL。 一个显示更新服务任务状态的 GET 调用。 | Operation-Location |
| 404 | 不存在具有指定 ID 的服务。 |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>删除服务

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| 删除 |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>说明
删除服务。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 对象 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 |  |
| 204 | 不存在具有指定 ID 的服务。 |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>获取服务密钥

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>说明
获取服务密钥。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 服务 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [AuthKeys](#authkeys)
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>重新生成服务密钥

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/regenerateKeys | 

### <a name="description"></a>说明
重新生成并返回服务密钥。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 服务 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| regenerateKeyRequest | body | 用于更新现有服务的有效负载。 | 是 | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [AuthKeys](#authkeys)
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>查询帐户中的部署列表

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>说明
查询帐户中的部署列表。 可以按服务 ID 筛选结果列表，以便仅返回针对特定服务创建的部署。 如果未传递任何筛选器，查询会列出帐户中的所有部署。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |
| serviceId | query | 服务 ID。 | 否 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [DeploymentList](#deploymentlist) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>获取部署详细信息

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>说明
按 ID 获取部署。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 部署 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [部署](#deployment) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>获取操作详细信息

### <a name="request"></a>请求
| 方法 | 请求 URI |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>说明
按操作 ID 获取异步操作状态。

### <a name="parameters"></a>parameters
| 名称 | 所在位置 | 说明 | 必选 | 架构
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | 路径 | Azure 订阅 ID。 | 是 | 字符串 |
| resourceGroupName | 路径 | 模型管理帐户所在的资源组的名称。 | 是 | 字符串 |
| accountName | 路径 | 模型管理帐户的名称。 | 是 | 字符串 |
| id | 路径 | 操作 ID。 | 是 | 字符串 |
| api-version | query | 要使用的 Microsoft.Machine.Learning 资源提供程序 API 的版本。 | 是 | 字符串 |
| 授权 | 标头的值开始缓存响应 | 授权令牌。 令牌应类似于“Bearer XXXXXX”。 | 是 | 字符串 |

### <a name="responses"></a>响应
| 代码 | 说明 | 架构 |
|--------------------|--------------------|--------------------|
| 200 | 成功。 | [OperationStatus](#asyncoperationstatus) |
| default | 描述操作失败原因的错误响应。 | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>定义

<a name="asset"></a>
### <a name="asset"></a>资产
创建 Docker 映像期间所需的资产对象。


|名称|说明|架构|
|---|---|---|
|**id**  <br>（可选）|资产 ID。|字符串|
|**mimeType**  <br>（可选）|模型内容的 MIME 类型。 有关 MIME 类型的详细信息，请参阅 [IANA 媒体类型列表](https://www.iana.org/assignments/media-types/media-types.xhtml)。|字符串|
|**unpack**  <br>（可选）|指示在创建 Docker 映像期间需要解压缩内容的位置。|布尔值|
|**url**  <br>（可选）|资产位置 URL。|字符串|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
异步操作状态。

类型：枚举（NotStarted、Running、Cancelled、Succeeded、Failed）


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
操作状态。


|名称|说明|架构|
|---|---|---|
|**createdTime**  <br>（可选）  <br>（只读）|异步操作创建时间 (UTC)。|字符串（日期时间）|
|**endTime**  <br>（可选）  <br>（只读）|异步操作结束时间 (UTC)。|字符串（日期时间）|
|**error**  <br>（可选）||[ErrorResponse](#errorresponse)|
|**id**  <br>（可选）|异步操作 ID。|字符串|
|**operationType**  <br>（可选）|异步操作类型。|枚举（Image、Service）|
|**resourceLocation**  <br>（可选）|异步操作创建/更新的资源。|字符串|
|**state**  <br>（可选）||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
服务的身份验证密钥。


|名称|说明|架构|
|---|---|---|
|**primaryKey**  <br>（可选）|主密钥。|字符串|
|**secondaryKey**  <br>（可选）|辅助密钥。|字符串|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
AutoScaler 设置。


|名称|说明|架构|
|---|---|---|
|**autoscaleEnabled**  <br>（可选）|启用或禁用 AutoScaler。|布尔值|
|**maxReplicas**  <br>（可选）|要纵向扩展到的最大 pod 副本数。  <br>最小值：`1`|integer|
|**minReplicas**  <br>（可选）|要减少到的最小 pod 副本数。  <br>最小值：`0`|integer|
|**refreshPeriodInSeconds**  <br>（可选）|自动缩放触发器的刷新时间。  <br>最小值：`1`|integer|
|**targetUtilization**  <br>（可选）|触发自动缩放的利用率 (%)。  <br>最小值：`0`  <br>最大值：`100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
机器学习计算资源。


|名称|说明|架构|
|---|---|---|
|**id**  <br>（可选）|资源 ID。|字符串|
|**类型**  <br>（可选）|资源的类型。|枚举 (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
有关在群集中预留容器资源的配置。


|名称|说明|架构|
|---|---|---|
|**cpu**  <br>（可选）|指定 CPU 预留。 有关 Kubernetes 的格式，请参阅 [CPU 的含义](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu)。|字符串|
|**memory**  <br>（可选）|指定内存预留。 有关 Kubernetes 的格式，请参阅[内存的含义](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory)。|字符串|


<a name="deployment"></a>
### <a name="deployment"></a>部署
Azure 机器学习部署的实例。


|名称|说明|架构|
|---|---|---|
|**createdAt**  <br>（可选）  <br>（只读）|部署创建时间 (UTC)。|字符串（日期时间）|
|**expiredAt**  <br>（可选）  <br>（只读）|部署过期时间 (UTC)。|字符串（日期时间）|
|**id**  <br>（可选）|部署 ID。|字符串|
|**imageId**  <br>（可选）|与此部署关联的映像 ID。|字符串|
|**serviceName**  <br>（可选）|服务名称。|字符串|
|**status**  <br>（可选）|当前部署状态。|字符串|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
部署对象的数组。

类型：<[Deployment](#deployment)> 数组


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
模型管理服务错误详细信息。


|名称|说明|架构|
|---|---|---|
|**code**  <br>（必需）|错误代码。|字符串|
|**message**  <br>（必需）|错误消息。|字符串|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
模型管理服务错误对象。


|名称|说明|架构|
|---|---|---|
|**code**  <br>（必需）|错误代码。|字符串|
|**details**  <br>（可选）|错误详细信息对象的数组。|<[ErrorDetail](#errordetail)> 数组|
|**message**  <br>（必需）|错误消息。|字符串|
|**statusCode**  <br>（可选）|HTTP 状态代码。|integer|


<a name="image"></a>
### <a name="image"></a>映像
Auzre 机器学习映像。


|名称|说明|架构|
|---|---|---|
|**computeResourceId**  <br>（可选）|机器学习计算资源中创建的环境的 ID。|字符串|
|**createdTime**  <br>（可选）|映像创建时间 (UTC)。|字符串（日期时间）|
|**creationState**  <br>（可选）||[AsyncOperationState](#asyncoperationstate)|
|**说明**  <br>（可选）|映像说明文本。|字符串|
|**error**  <br>（可选）||[ErrorResponse](#errorresponse)|
|**id**  <br>（可选）|映像 ID。|字符串|
|**imageBuildLogUri**  <br>（可选）|从映像版本上传的日志的 URI。|字符串|
|**imageLocation**  <br>（可选）|所创建的映像的 Azure 容器注册表位置字符串。|字符串|
|**imageType**  <br>（可选）||[ImageType](#imagetype)|
|**manifest**  <br>（可选）||[Manifest](#manifest)|
|**name**  <br>（可选）|映像名称。|字符串|
|**version**  <br>（可选）|模型管理服务设置的映像版本。|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
创建 Auzre 机器学习映像的请求。


|名称|说明|架构|
|---|---|---|
|**computeResourceId**  <br>（必需）|机器学习计算资源中创建的环境的 ID。|字符串|
|**说明**  <br>（可选）|映像说明文本。|字符串|
|**imageType**  <br>（必需）||[ImageType](#imagetype)|
|**manifestId**  <br>（必需）|要从中创建映像的清单的 ID。|字符串|
|**name**  <br>（必需）|映像名称。|字符串|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
指定映像的类型。

类型：枚举 (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>清单
Azure 机器学习清单。


|名称|说明|架构|
|---|---|---|
|**assets**  <br>（必需）|资产列表。|<[Asset](#asset)> 数组|
|**createdTime**  <br>（可选）  <br>（只读）|清单创建时间 (UTC)。|字符串（日期时间）|
|**说明**  <br>（可选）|清单说明文本。|字符串|
|**driverProgram**  <br>（必需）|清单的驱动程序。|字符串|
|**id**  <br>（可选）|清单 ID。|字符串|
|**modelIds**  <br>（可选）|已注册模型的模型 ID 列表。 如果包含的任何模型未注册，请求会失败。|<string> 数组|
|**modelType**  <br>（可选）|指定已注册到模型管理服务的模型。|枚举 (Registered)|
|**name**  <br>（必需）|清单名称。|字符串|
|**targetRuntime**  <br>（必需）||[TargetRuntime](#targetruntime)|
|**version**  <br>（可选）  <br>（只读）|模型管理服务分配的清单版本。|integer|
|**webserviceType**  <br>（可选）|指定要从清单创建的所需 Web 服务类型。|枚举 (Realtime)|


<a name="model"></a>
### <a name="model"></a>模型
Azure 机器学习模型的实例。


|名称|说明|架构|
|---|---|---|
|**createdAt**  <br>（可选）  <br>（只读）|模型创建时间 (UTC)。|字符串（日期时间）|
|**说明**  <br>（可选）|模型说明文本。|字符串|
|**id**  <br>（可选）  <br>（只读）|模型 ID。|字符串|
|**mimeType**  <br>（必需）|模型内容的 MIME 类型。 有关 MIME 类型的详细信息，请参阅 [IANA 媒体类型列表](https://www.iana.org/assignments/media-types/media-types.xhtml)。|字符串|
|**name**  <br>（必需）|模型名称。|字符串|
|**标记**  <br>（可选）|模型标记列表。|<string> 数组|
|**unpack**  <br>（可选）|指示在创建 Docker 映像期间是否需要解压缩模型。|布尔值|
|**url**  <br>（必需）|模型的 URL。 我们通常在此处放入共享访问签名 URL。|字符串|
|**version**  <br>（可选）  <br>（只读）|模型管理服务分配的模型版本。|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
模型数据收集信息。


|名称|说明|架构|
|---|---|---|
|**eventHubEnabled**  <br>（可选）|为服务启用事件中心。|布尔值|
|**storageEnabled**  <br>（可选）|为服务启用存储。|布尔值|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
映像的分页列表。


|名称|说明|架构|
|---|---|---|
|**nextLink**  <br>（可选）|列表中下一页结果的继续链接（绝对 URI）。|字符串|
|**值**  <br>（可选）|模型对象的数组。|<[Image](#image)> 数组|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
清单的分页列表。


|名称|说明|架构|
|---|---|---|
|**nextLink**  <br>（可选）|列表中下一页结果的继续链接（绝对 URI）。|字符串|
|**值**  <br>（可选）|清单对象的数组。|<[Manifest](#manifest)> 数组|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
模型的分页列表。


|名称|说明|架构|
|---|---|---|
|**nextLink**  <br>（可选）|列表中下一页结果的继续链接（绝对 URI）。|字符串|
|**值**  <br>（可选）|模型对象的数组。|<[Model](#model)> 数组|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
服务的分页列表。


|名称|说明|架构|
|---|---|---|
|**nextLink**  <br>（可选）|列表中下一页结果的继续链接（绝对 URI）。|字符串|
|**值**  <br>（可选）|服务对象的数组。|<[ServiceResponse](#serviceresponse)> 数组|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
创建服务的请求。


|名称|说明|架构|
|---|---|---|
|**appInsightsEnabled**  <br>（可选）|为服务启用 Application Insights。|布尔值|
|**autoScaler**  <br>（可选）||[AutoScaler](#autoscaler)|
|**computeResource**  <br>（必需）||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>（可选）||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>（可选）||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>（必需）|用于创建服务的映像。|字符串|
|**maxConcurrentRequestsPerContainer**  <br>（可选）|最大并发请求数。  <br>最小值：`1`|integer|
|**name**  <br>（必需）|服务名称。|字符串|
|**numReplicas**  <br>（可选）|在任意时间运行的 pod 副本数。 如果未启用自动缩放器，则无法指定。  <br>最小值：`0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
为服务重新生成密钥的请求。


|名称|说明|架构|
|---|---|---|
|**keyType**  <br>（可选）|指定要重新生成哪个密钥。|枚举（Primary、Secondary）|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
服务的详细状态。


|名称|说明|架构|
|---|---|---|
|**createdAt**  <br>（可选）|服务创建时间 (UTC)。|字符串（日期时间）|
|**ID**  <br>（可选）|服务 ID。|字符串|
|**图片**  <br>（可选）||[图像](#image)|
|**manifest**  <br>（可选）||[Manifest](#manifest)|
|**models**  <br>（可选）|模型列表。|<[Model](#model)> 数组|
|**name**  <br>（可选）|服务名称。|字符串|
|**scoringUri**  <br>（可选）|用于为服务评分的 URI。|字符串|
|**state**  <br>（可选）||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>（可选）|上次更新时间 (UTC)。|字符串（日期时间）|
|**appInsightsEnabled**  <br>（可选）|为服务启用 Application Insights。|布尔值|
|**autoScaler**  <br>（可选）||[AutoScaler](#autoscaler)|
|**computeResource**  <br>（必需）||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>（可选）||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>（可选）||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>（可选）|最大并发请求数。  <br>最小值：`1`|integer|
|**numReplicas**  <br>（可选）|在任意时间运行的 pod 副本数。 如果未启用自动缩放器，则无法指定。  <br>最小值：`0`|integer|
|**error**  <br>（可选）||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
更新服务的请求。


|名称|说明|架构|
|---|---|---|
|**appInsightsEnabled**  <br>（可选）|为服务启用 Application Insights。|布尔值|
|**autoScaler**  <br>（可选）||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>（可选）||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>（可选）||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>（可选）|用于创建服务的映像。|字符串|
|**maxConcurrentRequestsPerContainer**  <br>（可选）|最大并发请求数。  <br>最小值：`1`|integer|
|**numReplicas**  <br>（可选）|在任意时间运行的 pod 副本数。 如果未启用自动缩放器，则无法指定。  <br>最小值：`0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
目标运行时的类型。


|名称|说明|架构|
|---|---|---|
|**properties**  <br>（必需）||<string, string> 映射|
|**runtimeType**  <br>（必需）|指定运行时。|枚举（SparkPython、Python）|

