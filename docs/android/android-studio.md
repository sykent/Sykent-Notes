# Android Studio 使用

## Plugins 插件
* GsonFormat：gson 格式化。
* ADB Idea：adb 的一些使用。
* Gradle View：查看依赖版本。

## android studio 文件变乱解决方法
删除 .AndroidStudio3.2\system\caches 目录，重启 android studio

## firebase

打 abb 包链接失败，两种方法
1. gradle.properties 配置
    ```
    systemProp.https.proxyHost=xxx.xxx.xxx.xxx
    systemProp.https.proxyPort=xxxx
    ```
2. 项目 app 模块的 build.gradle
    ```
    gradle.taskGraph.whenReady {
        tasks.each { task ->
            if (task.name.contains("uploadCrashlyticsMappingFile")) {
                task.enabled = false
            }
        }
    }

    ```
