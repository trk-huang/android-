# Gradle统一配置依赖版本

简介：

> 目前移动开发为了减少编译时间，开发效率，大多采用模块化、组件化开发模式。采用这种方式不可避免的将会引用多个library

那么当我们协同开发的时候，我们怎么样才能做到引入的library包版本的一致呢。



对于文件的build.gradle目录：

```java
repositories {
    mavenLocal()
    jcenter()
}

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "***.***.***"
        minSdkVersion 15
        targetSdkVersion 25
        versionCode 2100400800
        versionName version
        vectorDrawables.useSupportLibrary = true
        ndk {
            abiFilters "armeabi"
        }
    }

    dexOptions {
        jumboMode true
    }
    productFlavors {
        ceshi {
            resValue "string", "channelId", "****"
        }
    }
    buildTypes {
        debug{
            signingConfig signingConfigs.release
        }
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

这段重复的配置，难道每新增一个libs，我们都需要去手动配置成一样的？有没有更好的方式呢？答案是有的。

下面我们来看看如何将所有的配置版本信息统一处理

1. 我们要建立主目录的config.gradle
2. 我们将需要的配置信息写入config.gradle内

如下图：

![](/assets/QQ20170812-0.png)

![](/assets/QQ20170812-1.png)

##### 总结

至此，我们所有的lib都可以采用这种方式来配置，一次配置，一劳永逸了。

