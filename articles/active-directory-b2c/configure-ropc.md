---
title: 在 Azure AD B2C 中配置资源所有者密码凭据流 | Microsoft Docs
description: 了解如何在 Azure AD B2C 中配置资源所有者密码凭据流。
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: c1b4d641f6830751e2cb9e66d5052eb20a48d371
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158248"
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>在 Azure AD B2C 中配置资源所有者密码凭据流 (ROPC)

资源所有者密码凭据 (ROPC) 流是 OAUTH 标准身份验证流，其中应用程序（也称为信赖方）使用有效凭据（例如用户 ID 和密码）交换 ID 令牌、访问令牌和刷新令牌。 

> [!NOTE]
> 此功能为预览版。

在 Azure AD B2C 中，以下选项受到支持：

- **本机客户端**：身份验证期间的用户交互使用在用户端设备上运行的代码进行，其可能会是在本机操作系统（例如 Android）中运行的移动应用程序，也可能会是在浏览器（例如 JavaScript）中运行的应用程序。
- **公共客户端流**：只有通过应用程序收集的用户凭据才会在 API 调用中发送。 不会发送应用程序的凭据。
- **添加新声明**：可更改 ID 令牌内容以添加新的声明。 

以下流不受支持：

- **服务器到服务器**：身份保护系统 (IDPS) 在交互过程中需要收集自调用方（本地客户端）的可靠 IP 地址。  在服务器端 API 调用中，仅会使用服务器的 IP 地址，并且如果超过失败身份验证次数的动态阈值，IDPS 可能会将重复的 IP 地址标识为攻击者。
- **机密客户端流**：应用程序客户端 ID 已验证，但应用程序密钥未验证。

##  <a name="create-a-resource-owner-policy"></a>创建资源所有者策略

1. 以 Azure AD B2C 租户的全局管理员的身份登录 Azure 门户。
2. 若要切换到 Azure AD B2C 租户，请在门户右上角选择 B2C 目录。
3. 在“策略”下，选择“资源所有者策略”。
4. 提供策略名称（例如 *ROPC_Auth*），然后单击“应用程序声明”。
5. 选择应用程序所需的应用程序声明，例如 *Display Name*、*Email Address* 和 *Identity Provider*。
6. 单击“确定”，然后单击“创建”。

你随后会看到一个终结点，如以下示例所示：

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>注册应用程序

1. 在 B2C 设置中，单击“应用程序”，然后单击“+ 添加”。
2. 提供应用程序名称，例如 *ROPC_Auth_app*。
3. 对“Web 应用/Web API”单击“否”，然后对“本机客户端”单击“是”。
4. 保留所有其他值不变，然后单击“创建”。
5. 选择新的应用程序并记下应用程序 ID。

## <a name="test-the-policy"></a>测试策略

使用最喜欢的 API 开发应用程序来生成 API 调用，然后查看响应以调试策略。 使用下表中的信息构建如下所示的调用作为 POST 请求的正文：
- 将 *yourtenant.onmicrosoft.com* 替换为 B2C 租户的名称
- 将 *B2C_1A_ROPC_Auth* 替换为 ROPC 策略的全称
- 将 *bef2222d56-552f-4a5b-b90a-1988a7d634c3* 替换为注册时提供的应用程序 ID。

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| 密钥 | 值 |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| 作用域 | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| response_type | token id_token |

*Client_id* 是之前记录为应用程序 ID 的值。 如果想要接收刷新令牌，则 *Offline_access* 是可选的。 

实际的 POST 请求如下所示：

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


具有脱机访问权限的成功响应类似以下示例：

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>兑换刷新令牌

使用下表中的信息构建如下所示的 POST 调用作为请求的正文：

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| 密钥 | 值 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| resource | bef2222d56-552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* 和 *resource* 是之前记录为应用程序 ID 的值。 *Refresh_token* 是在前述身份验证调用中收到的令牌。

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>使用首选本机 SDK 实现或使用 AppAuth


Azure AD B2C 实现符合 OAuth 2.0 标准或公共客户端 ROPC，并且应该与大多数客户端 SDK 兼容。  我们已在生产环境中针对适用于 iOS 的 AppAuth 和适用于 Android 的 AppAuth 广泛测试这一流程。  有关最新信息，请参阅 https://appauth.io/。

你可在 GitHub 中从 https://aka.ms/aadb2cappauthropc (Android) 和 https://aka.ms/aadb2ciosappauthropc 下载工作示例，这些示例已配置为与 Azure AD B2C 配合使用。




