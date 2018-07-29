---
title: Java for Android 人脸 API 教程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本教程创建一个简单的 Android 应用，以便使用认知服务人脸服务来检测和定格图像中的人脸。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: ad7b85b378db9e9687b5f8081bc9832e91e9ee5e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125630"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>教程：创建一个用于检测和定格图像中人脸的 Android 应用

本教程创建一个简单的 Android 应用程序，以便使用人脸服务 Java 类库来检测图像中的人脸。 此应用程序显示一个选定的图像，其中的每个检测到的人脸都用矩形来定格。 完整的示例代码在 GitHub 上的[在 Android 上检测和定格图像中的人脸](https://github.com/Azure-Samples/cognitive-services-face-android-sample)中提供。

![一张照片的 Android 屏幕截图，其中的人脸已使用红色矩形定格](../Images/android_getstarted2.1.PNG)

本教程演示如何：

> [!div class="checklist"]
> - 创建 Android 应用程序
> - 安装人脸服务客户端库
> - 使用客户端库检测图像中的人脸
> - 围绕每个检测到的人脸绘制一个框架

## <a name="prerequisites"></a>先决条件

- 需要一个订阅密钥来运行此示例。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。
- [Android Studio](https://developer.android.com/studio/)，至少装有 SDK 22（这是人脸客户端库需要的）。
- Maven 提供的 [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) 人脸客户端库。 不需下载程序包。 下面提供了安装说明。

## <a name="create-the-project"></a>创建项目

请按以下步骤创建 Android 应用程序项目：

1. 打开 Android Studio。 本教程使用 Android Studio 3.1。
1. 选择“启动新的 Android Studio 项目”。
1. 在“创建 Android 项目”屏幕上根据需要修改默认字段，然后单击“下一步”。
1. 在“目标 Android 设备”屏幕上使用下拉列表选择器选择“API 22”或更高版本，然后单击“下一步”。
1. 选择“空活动”，然后单击“下一步”。
1. 取消选中“后向兼容性”，然后单击“完成”。

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>创建用于选择和显示图像的 UI

打开 *activity_main.xml*，此时会看到布局编辑器。 选择“文本”选项卡，然后将其中的内容替换为以下代码。

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

打开 *MainActivity.java*，然后将除第一个 `package` 语句之外的所有内容替换为以下代码。

代码会在 `Button` 上设置一个事件处理程序，以便启动一项让用户选择图片的新活动。 选中后的图片显示在 `ImageView` 中。

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

现在，应用可以通过浏览方式查找照片并将其显示在窗口中，如下图所示。

![一张包含人脸的照片的 Android 屏幕截图](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>配置人脸客户端库

人脸 API 是可以使用 HTTPS 请求调用的云 API。 本教程使用人脸客户端库，该库通过封装这些 Web 请求来简化你的工作。

在“项目”窗格中，使用下拉列表选择器选择“Android”。 展开“Gradle 脚本”，然后打开“build.gradle (模块: 应用)”。

为人脸客户端库 `com.microsoft.projectoxford:face:1.4.3` 添加一个依赖项，如以下屏幕截图所示，然后单击“立即同步”。

![应用 build.gradle 文件的 Android Studio 屏幕截图](../Images/face-tut-java-gradle.png)

打开 **MainActivity.java**，追加以下 import 指令：

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>添加人脸客户端库代码

在 `MainActivity` 类中 `onCreate` 方法上面插入以下代码：

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

将 `<API endpoint>` 替换为分配到密钥的 API 终结点。 免费试用订阅密钥在 **westcentralus** 区域中生成。 因此，如果使用免费试用订阅密钥，则相关语句为：

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

将 `<Subscription Key>` 替换为订阅密钥。 例如：

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

在“项目”窗格中展开“应用”，接着展开“清单”，然后打开 *AndroidManifest.xml*。

插入以下元素作为 `manifest` 元素的直接子级：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

生成用于检查错误的项目。 现在可以调用人脸服务了。

## <a name="upload-an-image-to-detect-faces"></a>上传用于检测人脸的图像

检测人脸最直接的方式是调用 `FaceServiceClient.detect` 方法。 此方法包装了[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 方法，返回一个 `Face` 数组。

返回的每张`Face`均包含一个指示其位置的矩形，以及一系列可选的人脸特性。 在本示例中，只有人脸位置是必需的。

如果出错，`AlertDialog` 会显示其背后的原因。

将以下方法插入到 `MainActivity` 类中。

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>定格图像中的人脸

将以下帮助程序方法插入到 `MainActivity` 类中。 此方法围绕每个检测到的人脸绘制一个矩形。

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

在 `detectAndFrame` 方法中完成 `TODO` 注释指示的 `AsyncTask` 方法。 成功后，会在 `ImageView` 中显示包含已定格人脸的所选图像。

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

最后，在 `onActivityResult` 方法中取消注释对 `detectAndFrame` 方法的调用，如下所示。

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>运行应用

运行此应用程序，以浏览方式查找包含人脸的图像。 等待几秒钟，以便人脸服务响应。 然后会收到下图所示结果：

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>摘要

本教程介绍了人脸服务的基本使用过程，并创建了一个应用程序来显示图像中的已定格人脸。

## <a name="next-steps"></a>后续步骤

了解如何检测并使用人脸特征点。

> [!div class="nextstepaction"]
> [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

了解用于检测人脸及其特性（例如姿势、性别、年龄、头部姿势、面部毛发和眼镜）的人脸 API。

> [!div class="nextstepaction"]
> [人脸 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。