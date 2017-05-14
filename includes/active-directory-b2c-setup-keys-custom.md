
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>将签名和加密密钥添加到 B2C 租户，供自定义策略使用

1. 导航到 Azure AD B2C 租户设置中的“标识体验框架”边栏选项卡。
1. 选择`Policy Keys`，以便查看租户中的可用密钥。 如果 `B2C_1A_TokenSigningKeyContainer` 存在，则跳过此密钥。
1. 创建 `TokenSigningKeyContainer`  
 * 单击`+Add`
 * “选项”>`Generate`
 * “名称”>`TokenSigningKeyContainer` 前缀 B2C_1A_ 可以自动添加。
 * 密钥类型 > `RSA`
 * 日期 - 使用默认值
 * “密钥用法”>`Signature`
1. 单击 `Create`
1. 如果名为 `B2C_1A_TokenEncryptionKeyContainer` 的密钥存在，则跳过此密钥。
1. 创建 `TokenEncryptionKeyContainer`。
 * “选项”>`Generate`
 * “名称”>`TokenSigningKeyContainer` 前缀 B2C_1A_ 可以自动添加。
 * 密钥类型 > `RSA`
 * 日期 - 使用默认值
 * “密钥用法”>`Encryption`
1. 单击 `Create`


[!TIP]
若要向最终用户提供社交标识提供者或联合身份验证标识提供者，则后续步骤为可选。  若要了解如何通过自定义策略将外部标识提供者与 Azure AD B2C 配合使用，则不妨试试 Facebook。

1. 创建 `FacebookSecret`。  虽然此步骤为可选，但建议执行此步骤，轻松测试你能否进行外部联合身份验证。  这样可以为你将来使用其他标识提供者制定策略打下坚实的基础
 * 单击`+Add`
 * “选项”>`Manual`
 * “名称”>`FacebookSecret` 前缀 B2C_1A_ 可以自动添加。
 * “机密”> 输入 developers.facebook.com 提供的 FacebookSecret。  *这不是你的 Facebook 应用 ID*
 * “密钥用法”>“签名”
1. 单击`Create`确认创建，记下名称

[!NOTE]
若要使用 Azure AD B2C 内置策略，则通常情况下，将对内置策略和自定义策略使用同一机密。 
