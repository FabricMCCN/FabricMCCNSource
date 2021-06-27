---
title: 如何加速Fabric模组的构建
date: 2021-06-28 01:15:12
tags:
  - tutorials
  - tech
categories:
  - FabricMod开发杂项
---

由于总所周知的问题，在国内构建 Fabric 模组是一件非常缓慢的事情，因此我们需要一些小小的帮助以便更加快速的完成 Fabric 模组的构建。

<!--more-->

## 让 Gradle 使用代理的方案(推荐)

Gradle 不会根据系统的全局代理方案的启动而自动设定自身的代理方案，它需要一点小小的帮助才会使用代理。

Gradle 的代理分为两种方案：项目方案，全局方案。其中项目方案会针对当前项目进行设定，而全局方案会对所有 Gradle 项目进行设定

- 项目代理：在我们当前的 Fabric 模组文件夹之下会有一个 gradle.properties
- 全局代理：在对应操作系统之下存在一个.gradle 文件夹，在其中新建 gradle.properties 文件即可

选择好你的目标之后，写入以下内容到文件内。重启 IDEA 即可。

```conf
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=10809
systemProp.https.nonProxyHosts=10.*|localhost

systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=10809
systemProp.https.nonProxyHosts=10.*|localhost
```

在这其中，代理的地址`127.0.0.1`以及代理端口`10809`需要自行替换为自己的代理配置，以及需要同时配置`https`以及`http`代理，方可让 Gradle 正常工作。

另外，如果有需要的话请在`noProxyHosts`添加不需要通过代理的地址，这样可以避免 Gradle 通过代理拉取国内的内容。

## 切换 `Fabric` 以及 `MavenCenter` 源，加速 Gradle 拉取内容

需要将`settings.gradle`替换为以下内容，加速 fabric-loom 的下载

```groovy
pluginManagement {
    repositories {
        maven {
            name = 'Fabric'
            url = 'https://repository.hanbings.io/proxy'
        }
        gradlePluginPortal()
    }
}
```

同时在`build.gradle`中的`repositories`节点修改为以下内容，用于加速 FabricAPI 的下载

```groovy
repositories {
    maven {
        url 'https://maven.aliyun.com/nexus/content/groups/public'
    }
    maven {
        url 'https://repository.hanbings.io/proxy'
    }
}
```

其中感谢[hanbings](https://github.com/hanbings)提供的 [FabricMC 镜像服务站](https://repository.hanbings.io/proxy)，让国内开发者能有更好的开发体验。

## 特别注意

当前 fabric-loom 会从 mojang 拉取 Minecraft 资源文件，这一方面暂时无法修改，也就是说我们暂时无法对这一环节进行加速。

---

由[SUIBING112233](https://github.com/SUIBING112233)编写

最后的修改时间为`2021-6-28 2:01` by [SUIBING112233](https://github.com/SUIBING112233)