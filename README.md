## buildSrc 的缺陷

Android 开发中统一不同 module 的依赖版本十分重要，传统的方式是使用 ext 的方式

![ext](https://gitee.com/flywith24/Album/raw/master/img/20200527111409.png)



之前我发过关于使用 buildSrc 简化项目中 gradle 代码的译文：[什么？项目里gradle代码超过200行了！你可能需要 Kotlin+buildSrc Plugin](https://juejin.im/post/5e22c2ce6fb9a02ff67d41c3)

该种方式可以很好的管理 gradle 的公共配置，这其中当然包括依赖版本

![配置依赖](https://gitee.com/flywith24/Album/raw/master/img/20200527111722.gif)



如图，在使用依赖时有代码提示，而且可以点击进入查看



但是由于 buildSrc 是对全局的所有 module 的配置，因此在构建速度上会慢一些。那么有没有一个更纯净的方式来配置依赖版本呢？



今天我们来介绍一种新的方式



## 自定义 plugin + includeBuild

使用 [Gradle Composite builds](https://docs.gradle.org/current/userguide/composite_builds.html) 可以很容易解决这一问题

我们新建一个 module，命名为 version ，并将原来的 buildSrc 的代码转移过来

```kotlin
class DependencyVersionPlugin : Plugin<Project> {
    override fun apply(project: Project) {

    }
}
```

在 version 的 build.gradle 文件加入

```groovy
gradlePlugin {
    plugins {
        version {
            id = 'com.flywith24.version'
            implementationClass = 'com.flywith24.version.DependencyVersionPlugin'
        }
    }
}
```

在 settings.gradle 加入 `includeBuild("version")` （**重点**）

```groovy
includeBuild("version")

rootProject.name='VersionControlDemo'
include ':app'
include ':lib'
```



接下来在需要引用的 module 中引入该插件

```groovy
plugins {
    id "com.flywith24.version"
}
```



之后我们就可以使用了

## Demo

![demo代码截图](https://gitee.com/flywith24/Album/raw/master/img/20200527113952.png)

![demo代码截图](https://gitee.com/flywith24/Album/raw/master/img/20200527114023.png)



[demo 在这](https://github.com/Flywith24/VersionControlDemo)



## 往期文章



该系列主要介绍一些「骚操作」，它未必适合生产环境使用，但是是一些比较新颖的思路



- [【奇技淫巧】AndroidStudio Nexus3.x搭建Maven私服遇到问题及解决方案](https://juejin.im/post/5e481a28f265da570b3f235c)


- [【奇技淫巧】什么？项目里gradle代码超过200行了！你可能需要 Kotlin+buildSrc Plugin](https://juejin.im/post/5e22c2ce6fb9a02ff67d41c3)


- [【奇技淫巧】gradle依赖查找太麻烦？这个插件可能帮到你](https://juejin.im/post/5e481a28f265da570b3f235c)


- [【奇技淫巧】Android组件化不使用 Router 如何实现组件间 activity 跳转](https://juejin.im/post/5e967f35f265da47d77cd4c3)


- [【奇技淫巧】新的图片加载库？基于Kotlin协程的图片加载库——Coil](https://juejin.im/post/5ebdfb0b6fb9a0436153db22)


- [【奇技淫巧】使用 Navigation + Dynamic Feature Module 实现模块化](https://juejin.im/post/5ec50ae46fb9a047a862124f)



我的其他系列文章 [在这里](https://github.com/Flywith24/BlogList)



## 关于我

我是 [Fly_with24](https://flywith24.gitee.io/)
- [掘金](https://juejin.im/user/57c7f6870a2b58006b1cfd6c)

- [简书](https://www.jianshu.com/u/3d5ad6043d66)

- [Github](https://github.com/Flywith24)



![](https://gitee.com/flywith24/Album/raw/master/img/20200508153754.jpg)

