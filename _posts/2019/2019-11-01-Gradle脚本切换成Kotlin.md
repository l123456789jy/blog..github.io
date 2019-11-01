---
layout:     post
title:      "Gradle脚本切换成Kotlin"
subtitle:   ""
date:       2019-10-27 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:
tags:
    -
---

- 最近项目已经开始使用kotlin了,之前gradle脚本一直觉得不舒服，现在能使用kotlin写了，就打算先搞个demo


- 确认你的AS版本和Gradle版本是最新的


- 把自己工程项目下的所有"build.gradle"改成"build.gradle.kts" 和"settings.gradle"改成"settings.gradle.kts"


- 首先把"settings.gradle.kts"替换成下面、

```groovy
rootProject.buildFileName = "build.gradle.kts"
include("app")
```


- 然后在修改根目录下的"build.gradle.kts"文件

```groovy
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {

    repositories {
        google()
        jcenter()

    }
    dependencies {
        classpath ("com.android.tools.build:gradle:3.5.1")
        classpath ("org.jetbrains.kotlin:kotlin-gradle-plugin:1.3.50")
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        google()
        jcenter()
    }
}

task("clean", Delete::class) {
    delete = setOf(rootProject.buildDir)
}
```

这里只是吧之前的单引号改成了双引号，并且加上大括号



- 在修改项目下的"build.gradle.kts"

```groovy
import com.android.build.gradle.internal.api.ApkVariantOutputImpl
plugins  {
    id("com.android.application")
    id("kotlin-android")
    id("kotlin-android-extensions")
}
android {
    compileSdkVersion(28)
    defaultConfig {
        applicationId = "com.kotlin.dsl.gradle.sample"
        minSdkVersion(16)
        versionCode = 1
        versionName = "1.0"
        testInstrumentationRunner = "android.support.test.runner.AndroidJUnitRunner"
    }

    signingConfigs {

    }

    buildTypes {
        getByName("release") {
            proguardFiles(getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro")

        }
        getByName("debug") {
            proguardFiles(getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro")

        }
    }

    packagingOptions {
        exclude("META-INF/LICENSE.txt")
        exclude("META-INF/NOTICE.txt")
    }

    android.applicationVariants.all {
        outputs.all {
            if (this is ApkVariantOutputImpl) {
                this.outputFileName = "$versionCode@app_$versionName.apk"
            }
        }
    }

}

task("greeting") {
    var age=10
    doLast { println(age) }
}


dependencies {
    implementation(fileTree(mapOf("dir" to "libs", "include" to listOf("*.jar"))))

    val supportVersion = "28.0.0"
    implementation("com.android.support:appcompat-v7:$supportVersion")
    implementation("com.android.support:support-v4:$supportVersion")
    implementation("com.android.support:gridlayout-v7:$supportVersion")
    implementation("com.android.support:recyclerview-v7:$supportVersion")
    implementation("com.android.support:design:$supportVersion")

    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.2.61")

    testImplementation("junit:junit:4.12")
    androidTestImplementation("com.android.support.test:runner:1.0.2")
}
```

这里是可以看到我们把 plugins  换成括号和之前的版本号都换成了大括号，这里我们写了一个测试的 greeting  task,可以看到这个语法使用kotlin写的

![微信截图_20191101153654.png](http://ww1.sinaimg.cn/large/9f723435ly1g8ikwfhizij210p0jqgnr.jpg)

可以到运行正常，到此kotlin替换成功，其他就是看文档了，参考以下文章
demo地址：https://github.com/l123456789jy/KotlinGradle

https://juejin.im/post/5b875c9c6fb9a01a0d74c34b
https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/#before_you_start_migrating



