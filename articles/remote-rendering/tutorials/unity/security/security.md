---
title: 保护 Azure 远程渲染和模型存储
description: 强化远程渲染应用程序以保护内容
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 07374debf8d660d8f1c32788db3d218da611d539
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650470"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>教程：保护 Azure 远程渲染和模型存储

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 保护包含 Azure 远程渲染模型的 Azure Blob 存储
> * 使用 Azure AD 进行身份验证以访问 Azure 远程渲染实例
> * 使用 Azure 凭据进行 Azure 远程渲染身份验证

## <a name="prerequisites"></a>先决条件

* 本教程以[教程：优化材料、照明和效果](..\materials-lighting-effects\materials-lighting-effects.md)为基础构建。

## <a name="why-additional-security-is-needed"></a>为什么需要更多安全性

应用程序的当前状态及其对 Azure 资源的访问权限如下所示：

![初始安全性](./media/security-one.png)

“AccountID + AccountKey”和“URL + SAS 令牌”都同时存储了用户名和密码。 例如，如果公开了“AccountID + AccountKey”，攻击者在未经你允许的情况下使用你的 ARR 资源是很容易的，且由你承担后果。

## <a name="securing-your-content-in-azure-blob-storage"></a>在 Azure Blob 存储中保护内容

通过正确的配置，Azure 远程渲染可以安全访问 Azure Blob 存储的内容。 请参阅[操作说明：链接存储帐户](../../../how-tos/create-an-account.md#link-storage-accounts)，了解如何使用 blob 存储帐户配置 Azure 远程渲染实例。

使用链接的 blob 存储时，可以使用略微不同的方法来加载模型：

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

上述行使用 `FromSAS` 版本的参数和会话操作。 必须将它们转换为非 SAS 版本：

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

让我们修改 RemoteRenderingCoordinator 以从链接的 blob 存储帐户加载自定义模型。

1. 如果尚未执行此操作，请完成[操作说明：链接存储帐户](../../../how-tos/create-an-account.md#link-storage-accounts)，向 ARR 实例授予访问 Blob 存储实例的权限。
1. 将以下已修改的 LoadModel 方法添加到当前 LoadModel 方法正下方的 RemoteRenderingCoordinator 中  ：

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    大多数情况下，此代码与原始 `LoadModel` 方法相同，但我们已将该方法调用的 SAS 版本替换为非 SAS 版本。

    还向参数添加了其他输入：`storageAccountName` 和 `blobContainerName`。 我们将从另一个方法中调用这个新的 LoadModel 方法，该方法类似于我们在第一个教程中创建的首个 LoadTestModel 方法 。

1. 将以下方法添加到 LoadTestModel 正后方的 RemoteRenderingCoordinator 

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    此代码将另外三个字符串变量添加到 RemoteRenderingCoordinator 组件。
    ![屏幕截图，其中突出显示了“存储帐户名称”、“Blob 容器名称”和 RemoteRenderingCoordinator 组件的“模型路径”。](./media/storage-account-linked-model.png)

1. 将值添加到 RemoteRenderingCoordinator 组件。 按照[模型转换快速入门](../../../quickstarts/convert-model.md)进行操作后，你的值应为：

    * **存储帐户名称**：你的存储帐户名称，即为存储帐户选择的全局唯一名称。 在快速入门中，该名称为 arrtutorialstorage，你的值将有所不同。
    * **Blob 容器名称**：arroutput，Blob 存储容器
    * **模型路径**：Arrconfig.json 文件中定义的“outputFolderPath”和“outputAssetFileName”的组合。 在快速入门中为 "outputFolderPath":"converted/robot"、"outputAssetFileName": "robot.arrAsset"。 其结果是得到模型路径值“converted/robot/robot.arrAsset”，你的值也会有所不同。

    >[!TIP]
    > 如果[运行 Conversion.ps1](../../../quickstarts/convert-model.md#run-the-conversion) 脚本，而不包含“-UseContainerSas”参数，则该脚本将为你输出上述所有值，而不是 SAS 令牌。 ![链接模型](./media/converted-output.png)
1. 现在，请删除或禁用 GameObject TestModel，以便为自定义模型加载提供空间。
1. 播放场景并连接到远程会话。
1. 右键单击“RemoteRenderingCoordinator”并选择“加载链接的自定义模型” 。
    ![加载链接模型](./media/load-linked-model.png)

通过从本地应用程序中删除 SAS 令牌，这些步骤提高了应用程序的安全性。

现在，应用程序的当前状态及其对 Azure 资源的访问权限如下所示：

![更安全](./media/security-two.png)

还需要从本地应用程序中删除另外一个“密码”，即 AccountKey。 这可以使用 Azure Active Directory (AAD) 身份验证来完成。

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD) 身份验证

使用 AAD 身份验证，可以通过更可控的方式确定使用 ARR 的个人或组。 ARR 内置了对接受[访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)的支持，而不是对使用帐户密钥的支持。 可以将访问令牌看作是一个有时间限制的、特定于用户的密钥，它只解锁所请求的特定资源的某些部分。

RemoteRenderingCoordinator 脚本有一个名为 ARRCredentialGetter 的委托，该委托包含一个返回 AzureFrontendAccountInfo 对象的方法，该对象用于配置远程会话管理  。 可以为 ARRCredentialGetter 分配不同的方法，以便能够使用 Azure 登录流，从而生成包含 Azure 访问令牌的 AzureFrontendAccountInfo 对象 。 此访问令牌特定于正在登录的用户。

1. 请按照[如何：配置身份验证 - 已部署的应用程序的身份验证](../../../how-tos/authentication.md#authentication-for-deployed-applications)进行操作，具体来说，需要遵循 Azure 空间定位点文档 [Azure AD 用户身份验证](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)中列出的说明。 这涉及到注册新的 Azure Active Directory 应用程序并配置对 ARR 实例的访问。
1. 配置新的 AAD 应用程序后，请检查你的 AAD 应用程序是否如下图所示：

    AAD 应用程序 -> 身份验证 ![应用身份验证](./media/app-authentication-public.png)

    AAD 应用程序 -> API 权限 ![应用 API](./media/request-api-permissions-step-five.png)

1. 配置远程渲染帐户后，请检查你的配置是否如下图所示：

    AAR -> AccessControl (IAM) ![ARR 角色](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > 所有者角色的权限不足以通过客户端应用程序管理会话。 对于要授予会话管理权限的每个用户，你需要向他们提供远程渲染客户端角色。 对于要管理会话和转换模型的每个用户，必须为其提供远程渲染管理员角色。

在 Azure 端的各方面准备就绪后，现在需要修改代码连接到 AAR 服务的方式。 为此，我们将实现 BaseARRAuthentication 的实例，该实例将返回一个新的 AzureFrontendAccountInfo 对象 。 在这种情况下，将使用 Azure 访问令牌配置帐户信息。

1. 创建一个名为 AADAuthentication 的新脚本，并将其代码替换为以下内容：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> 此代码并不完整，不能用于商业应用程序。 例如，你至少还可能需要添加注销功能。 可以使用客户端应用程序提供的 `Task RemoveAsync(IAccount account)` 方法实现此目的。 此代码仅用于教程，你的实现过程将特定于你的应用程序。

代码首先尝试使用 AquireTokenSilent 以无提示方式获取令牌。 如果用户之前已经对此应用程序进行了身份验证，此操作会成功。 如果不成功，请转到用户涉及度更高的策略。

对于此代码，我们使用[设备代码流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)来获取访问令牌。 通过此流，用户可在计算机或移动设备上登录其 Azure 帐户，并将生成的令牌发送回 HoloLens 应用程序。

从 ARR 的角度来看，此类最重要的部分是这一行：

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

此处，我们将使用帐户域、帐户 ID 和访问令牌创建新的 AzureFrontendAccountInfo 对象。 只要基于先前配置的基于角色的权限向用户授予了所需权限，ARR 服务便可以使用此令牌来查询、创建和加入远程渲染会话。

进行此更改后，应用程序的当前状态及其对 Azure 资源的访问权限如下所示：

![更好的安全性](./media/security-three.png)

由于用户凭据不存储在设备上（本例中甚至没有在设备上输入），因此其暴露风险非常低。 现在，设备正在使用特定于用户的、有时间限制的访问令牌访问 ARR，而 ARR 使用访问控制 (IAM) 访问 Blob 存储。 这两个步骤完全删除了源代码中的“密码”，从而大大提高了安全性。 然而，这并不是最安全的方法，将模型和会话管理转移到 Web 服务可进一步提高安全性。 [商业准备情况](../commercial-ready/commercial-ready.md)一章中讨论了其他安全注意事项。

### <a name="testing-aad-auth"></a>测试 AAD 身份验证

在 Unity 编辑器中，当 AAD 身份验证处于活动状态时，需要在每次启动应用程序时进行身份验证。 在设备上，首次启动时需要执行身份验证步骤，然后将仅在令牌过期或无效时才需要再次执行。

1. 将 AADAuthentication 组件添加到 RemoteRenderingCoordinator GameObject 中 。

    ![AAD 身份验证组件](./media/azure-active-directory-auth-component.png)

1. 填写客户 ID 和租户 ID 的值。 这些值可以在应用程序注册的概述页面中找到：

    * 帐户域与你在 RemoteRenderingCoordinator 的帐户域中使用的域相同 。
    * Active Directory 应用程序客户端 ID 是 AAD 应用注册中的应用程（客户端）ID（见下图）。
    * Active 租户 ID 是在 AAD 应用注册中找到的目录(租户) ID（请参阅下图）。
    * Azure 远程渲染帐户 ID 与用于 RemoteRenderingCoordinator 的帐户 ID 相同  。

    ![屏幕截图，其中突出显示了“应用程序(客户端)ID”和“目录(租户) ID”。](./media/app-overview-data.png)

1. 在 Unity 编辑器中按“播放”并同意运行会话。
    由于 AADAuthentication 组件有一个视图控制器，它将在会话授权模式面板后自动挂钩以显示提示。
1. 请按照 AppMenu 右边面板中的说明操作。
    看到的内容应该如下所示：![显示在 AppMenu 右侧显示的“指令”面板的插图。](./media/device-flow-instructions.png)
    在辅助设备（或同一设备上的浏览器）上输入提供的代码并使用凭据登录后，一个访问令牌会返回到发出请求的应用程序中（在本例中为 Unity 编辑器）。
1. 此后，应用程序中的所有内容应会正常运行。 如果没有按照预期的方式完成各个阶段，请检查 Unity 控制台是否有任何错误。

## <a name="build-to-device"></a>在设备上构建

如果使用 MSAL 在设备上构建应用程序，需要在项目的 Assets 文件夹中包含一个文件。 这有助于编译器使用 Tutorial Assets 中包含的 Microsoft.Identity.Client.dll 正确地构建应用程序。

1. 在 Assets 中添加名为 link.xml 的新文件 
1. 将以下内容添加到文件：

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. 保存更改

遵循[快速入门：将 Unity 示例部署到 HoloLens - 生成示例项目](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)中的步骤，生成到 HoloLens。

## <a name="next-steps"></a>后续步骤

本教程集的其余部分包含概念性主题，用于创建使用 Azure 远程渲染的生产就绪应用程序。

> [!div class="nextstepaction"]
> [下一步：商业准备情况](../commercial-ready/commercial-ready.md)
