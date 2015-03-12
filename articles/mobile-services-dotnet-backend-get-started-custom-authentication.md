<properties urlDisplayName="Get started with custom authentication" pageTitle="自定义身份验证入门 | 移动开发人员中心" metaKeywords="" description="了解如何使用用户名和密码对用户进行身份验证。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="mahender" />

# 自定义身份验证入门

本主题说明如何通过颁发自己的移动服务身份验证令牌，对 Azure 移动服务 .NET 后端中的用户进行身份验证。在本教程中，你将使用应用程序的自定义用户名和密码向快速入门项目添加身份验证。

>[AZURE.NOTE] 本教程演示了使用自定义凭据对移动服务进行身份验证的高级方法。许多应用程序最好是改用内置的社交标识提供程序，以便让用户通过 Facebook、Twitter、Google、Microsoft 帐户和 Azure Active Directory 登录。如果你这是第一次体验移动服务中的身份验证，请参阅[用户入门]教程。

本教程将指导你完成在应用程序中启用身份验证的以下基本步骤：

1. [设置帐户表]
2. [创建注册终结点]
3. [创建 LoginProvider]
4. [创建登录终结点]
5. [将移动服务配置为要求身份验证]
6. [使用测试客户端测试登录流]

本教程基于移动服务快速入门。因此，你还必须先完成[移动服务入门]教程。 

>[AZURE.NOTE] 本教程旨在说明如何颁发移动服务的身份验证令牌。请不要将其视为安全指导原则。在开发应用程序时，你必须留意密码存储的安全问题，并且需要制定暴力破解攻击的管理策略。


## <a name="table-setup"></a>设置帐户表

由于你使用自定义身份验证，且未依赖其他标识提供程序，因此需要存储用户的登录信息。在本部分中，你将为帐户创建一个表，并设置基本的安全性机制。帐户表包含用户名和加盐哈希密码，你也可以视需要加入其他用户信息。

1. 在后端项目的 `DataObjects` 数据夹中，创建名为 `Account` 的新实体：

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    这样，我们的新表中将会显示一行，其中包含用户名、该用户的盐以及安全存储的密码。

2. 在 `Models` 文件夹下，你将会看到与移动服务同名的 `DbContext` 类。本教程的余下部分将以 `todoContext` 为示例，而你需要相应地更新代码段。请打开你的内容并添加以下代码，将帐户表添加到数据模型：

        public DbSet<Account> Accounts { get; set; }

3. 接下来，请设置安全功能以处理此数据。你需要通过适当机制来生成新的长格式盐，并需要具有哈希处理加盐密码的能力，以及比较两个哈希代码的安全方式。创建名为 `CustomLoginProviderUtils` 的类，然后在其中添加以下方法：


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }


## <a name="register-endpoint"></a>创建注册终结点

此时，创建用户帐户的各项准备都已完成。在本部分中，你将设置注册终结点以处理新的注册请求。你将在此处强制实施新的用户名和密码策略，并确保用户名不会盗用。然后，你要将用户信息安全存储在数据库中。

1. 创建一个对象用于表示传入的注册尝试：

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    如果你要在注册时收集其他信息，可以将其包含在此对象中。

1. 在移动服务后端项目中，添加名为 CustomRegistrationController 的新自定义控制器，并粘贴到以下代码中：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomRegistrationController : ApiController
        {
            public ApiServices Services { get; set; }

            // POST api/CustomRegistration
            public HttpResponseMessage Post(RegistrationRequest registrationRequest)
            {
                if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
                }
                else if (registrationRequest.password.Length < 8)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
                }

                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
                if (account != null)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
                }
                else
                {
                    byte[] salt = CustomLoginProviderUtils.generateSalt();
                    Account newAccount = new Account
                    {
                        Id = Guid.NewGuid().ToString(),
                        Username = registrationRequest.username,
                        Salt = salt,
                        SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
                    };
                    context.Accounts.Add(newAccount);
                    context.SaveChanges();
                    return this.Request.CreateResponse(HttpStatusCode.Created);
                }
            }
        }   

    请用以下代码修饰控制器，以确保允许所有定向到此终结点的流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>创建 LoginProvider

 `LoginProvider` 是移动服务身份验证管道中的基础构造之一。在本部分中，你将创建自己的 `CustomLoginProvider`。它并不会像内置提供程序一样插入管道中，但会为你提供方便的功能。

1. 创建新类 `CustomLoginProvider`，此类派生自 `LoginProvider`：

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       `LoginProvider`具有你稍后要实现的其他三个抽象方法。

2. 创建名为 `CustomLoginProviderCredentials` 的新类。此类表示有关用户的信息，将通过 `ServiceUser.getIdentitiesAsync()` 在后端上供你使用。如果你要添加自定义声明，请确保可在此对象中捕获这些声明。

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. 将抽象方法 `ConfigureMiddleware` 的以下实现添加到 `CustomLoginProvider`。这是一个无操作方法，因为 `CustomLoginProvider` 不会与身份验证管道集成。

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. 将抽象方法 `ParseCredentials` 的以下实现添加到 `CustomLoginProvider`。此方法将使后端能够从传入的身份验证令牌反序列化用户信息。

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. 将抽象方法 `CreateCredentials` 的以下实现添加到 `CustomLoginProvider`。此方法将 `ClaimsIdentity` 转换成在身份验证令牌颁发阶段使用的 `ProviderCredentials` 对象。此时，你可以再次捕获任何其他声明。

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

## <a name="login-endpoint"></a>创建登录终结点

接下来，需要创建一个终结点，使你的用户能够登录。系统会根据数据库检查你所收到的用户名和密码，方法是应用用户的盐，哈希处理密码，然后确保传入值与数据库的值匹配。如果匹配，则你可以创建 `ClaimsIdentity` 并将其传递给 `CustomLoginProvider`。然后，客户端应用程序将接收用户 ID 和身份验证令牌，以进一步访问你的移动服务。

1. 在移动服务后端项目中创建一个对象，用于表示传入的登录尝试：

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. 添加名为 `CustomLoginController` 的新自定义控制器，并粘贴以下代码：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    请用以下代码修饰控制器，以确保允许所有定向到此终结点的流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.NOTE] 用于生产环境的 `CustomLoginController` 也应包含暴力破解攻击检测策略。否则，你的登录解决方案可能容易受到攻击。

## <a name="require-authentication"></a>将移动服务配置为要求身份验证

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>使用测试客户端测试登录流

在客户端应用程序中，需要开发一个自定义登录屏幕，以捕获用户名和密码，并将其以 JSON 负载形式发送到你的注册和登录终结点。要完成本教程，你只需使用移动服务 .NET 后端的内置测试客户端。

>[AZURE.NOTE] 移动服务 SDK 将通过 HTTPS 与服务通信。如果你要通过直接 REST 调用访问此终结点，则必须确保使用 HTTPS 调用你的移动服务，因为密码会以明文形式发送。

1. 在 Visual Studio 中，右键单击项目并选择"调试"->"启动新实例"以启动移动服务后端的新调试实例

    ![][0]

2. 单击"试用"

    ![][1]

3. 选择注册终结点。你可以看到有关 API 的一些基本文档。单击"试用此项"。

    ![][2]

4. 在正文中，将示例字符串替换为符合前面所指定条件的用户名和密码。然后单击"发送"。响应应为"201/已创建"。

    ![][3]

5. 针对登录终结点重复此过程。发送前面注册的相同用户名和密码之后，你应该会收到用户 ID 和身份验证令牌。

    ![][4]


<!-- Anchors. -->
[设置帐户表]: #table-setup
[创建注册终结点]: #register-endpoint
[创建 LoginProvider]: #login-provider
[创建登录终结点]: #login-endpoint
[将移动服务配置为要求身份验证]: #require-authentication
[使用测试客户端测试登录流]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[用户入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
