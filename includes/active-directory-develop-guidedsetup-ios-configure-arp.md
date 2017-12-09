
## <a name="add-the-applications-registration-information-to-your-app"></a>将应用程序的注册信息添加到你的应用程序

在此步骤中，你需要将应用程序 Id 添加到你的项目：

1.  在`ViewController.swift`，替换开头的行`let kClientID`使用：
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Control + 单击<code>Info.plist</code>以打开上下文菜单，然后单击： <code>Open As</code>> <code>Source Code</code>
</li>
<li>
下<code>dict</code>根节点，添加以下：
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
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
