# Gradle
## 查看依赖
### 插件模式

Gradle View 可以比较直观的看出组件间的依赖关系。

### 命令模式

查看所有：
```
gradlew :app:dependencies>xxx.txt
```
部分查看：
```
gradlew :app:dependencies --configuration xxx
```
其中，xxx 为 implementation 或 api 等等依赖方式。

### 比命令行好看的模式

```
gradlew build --scan
```

### 输出到 html

```
apply plugin: 'project-report'
```
命令
```
gradlew htmlDependencyReport
```

## 依赖解读

`(*)` 表示这个库被覆盖过，最后的为使用版本,同时表示这个依赖被忽略掉。
`omitted for duplicate` 标志重复依赖

依赖查找：首先找到 A.aar 的版本 v1，再找其上级依赖的版本 v2，查看 v2.pom 的依赖版本是否是 v1，然后一级一级往上推。

## 依赖缓存

### .gradle 目录剖析

**根目录**
|目录|功能|
|:--:|:--:|
|caches|gradle缓存目录|
|native|gradle平台相关目录|
|wrapper|gradle-wrapper下载目录|

**caches/modules-2 目录**

|目录|功能|
|:--:|:--:|
|files-2.1|gradle下载的 jar/aar 目录|
|metadata-xxx|gradle-yyyy 的描述文件|

**备注：** 如果本地下载 maven 仓库的某个 A.aar ，然后修改 maven 仓库的依赖，本地 A.aar 依赖并不会刷新，需要手动去删除 A 对于的 metadata 描述文件，重新拉取，才会保持本地和线上的一致。

## 命令



[参考][1]

[1]: https://www.shuzhiduo.com/A/x9J2PXwVd6/
