# Android M 动态权限管理

## 说明
在Android M（6.0+）系统中,以下权限需要在代码中动态申请，而不是像以前一样写在AndroidManifest.xml中
> 身体传感器

> 日历

> 摄像头

> 通讯录

> 地理位置

> 麦克风

> 电话

> 短信

> 存储空间

## 基本使用方法
### SDK版本
首先我们需要将SDK版本设置为23
```gradle
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
        applicationId "com.basti.testpermissions"
        minSdkVersion 14
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```
### 检查并申请权限
例如，我们需要完成一个保存功能，需要将数据写入到文件中，在Android M以前的系统时我们是只要直接调用保存数据的方法即可完成,但是在Android M中，需要首先检查权限，如果已经授权，则直接执行save方法，否则去申请授权
```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE)!= PackageManager.PERMISSION_GRANTED) {
    //还未被取得权限
    //申请WRITE_EXTERNAL_STORAGE权限，app显示弹窗
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},
            WRITE_EXTERNAL_STORAGE_REQUEST_CODE);
}else{
    //已经取得权限
    //保存数据的方法
    save();
}
```

### 申请后的回调
重写onRequestPermissionsResult()方法
```java
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if (requestCode == WRITE_EXTERNAL_STORAGE_REQUEST_CODE) {
        if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            // Permission Granted
            //授权成功，做一些操作
            Toast.makeText(getApplicationContext(),"授权成功",Toast.LENGTH_SHORT).show();
            save();
            //...
        } else {
            // Permission Denied
            //用户未授权，应用没有取得该权限
            Toast.makeText(getApplicationContext(),"授权拒绝",Toast.LENGTH_SHORT).show();
        }
    }
}
```

### 需要注意的一些问题
动态申请权限只有在SDK>=23时才有作用，用23之前版本的SDK编译出的App在6.0系统上运行也无法完成动态获取权限，系统会自动默认用户允许该权限的使用。但是如果用户在Settings-App-AppName-permissions中手动关闭该权限，应用
则可能出现崩溃的问题。
