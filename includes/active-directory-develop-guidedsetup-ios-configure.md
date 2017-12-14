
## <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)注册应用程序
2.  输入应用程序的名称和电子邮件
3.  确保选中“指导式设置”选项
4.  按照说明获取应用程序 ID，并将其粘贴到代码中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）

1.  转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)
2.  输入应用程序的名称和电子邮件
3.  确保取消选中“指导式设置”选项
4.  单击 `Add Platform`，选择 `Native Application` 并单击 `Save`
5.  返回 Xcode。 在 `ViewController.swift` 中，将以“ `let kClientID`”开头的行替换为刚注册的应用程序 ID：

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
按住 Control 键并单击 <code>Info.plist</code> 打开上下文菜单，然后单击：<code>Open As</code>> <code>Source Code</code>
</li>
<li>
在 <code>dict</code> 根节点下添加以下代码：
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
将 <i><code>[Your_Application_Id_Here]</code></i> 替换为刚注册的应用程序 ID
</li>
</ol>
