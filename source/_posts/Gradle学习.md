---
title: Gradle学习
date: 2019-11-05 20:11:03
tags:
	- gradle

---



# Maven构建流程

1. validate
2. compile
3. test
4. package
5. integration-test
6. verify
7. install
8. Deploy



# Maven 缺陷

1. 默认的生命周期
2. 制定扩展需要学习Mojo



<!--more-->

# Groovy语言(闭包)

## Closure 类

**作用域：**

```java
private Object delegate;
private Object owner;
private Object thisObject;
```

有3个作用域 闭包对象会从 3个作用域中调用方法和次序

**声明闭包：**

```groovy
class Enclosing {
    void run() {
      	// 定义闭包对象
        def whatIsThisObject = { getThisObject()} 
        assert whatIsThisObject() == this               
        def whatIsThis = { this }                           
        assert whatIsThis() == this                         
    }
}


class NestedClosures {
    void run() { 
      	// 定义闭包对象
        def nestedClosures = { // 1
            def cl = { this } // 2                            
            cl() // 最后一句默认为返回结果
        }
        assert nestedClosures() == this                     
    }
}
```

**总结:**

1. 闭包对象一般像 def xxx = {} or def xxx = ""

2. 闭包的用处一般在于方法里面嵌套方法 ，但是groovy不支持，例如

```groovy
class NestedClosures {
    void run() {
        def nestedClosures = {
						// wrong
            def cl(){
                this
            }
          	// yes
            def cl = { this }
            cl()
        }
        assert nestedClosures() == this
    }
}
```

[闭包参考]: http://www.groovy-lang.org/closures.html

# Gradle

##构建流程(简单的HelloWorld)

`gradle init` 命令初始化一个gradle项目

```shell
#bash
$ gradle build 
#执行Tasks
> Task :compileJava UP-TO-DATE
> Task :processResources NO-SOURCE
> Task :classes UP-TO-DATE
> Task :jar UP-TO-DATE
> Task :assemble UP-TO-DATE
> Task :compileTestJava NO-SOURCE
> Task :processTestResources NO-SOURCE
> Task :testClasses UP-TO-DATE
> Task :test NO-SOURCE
> Task :check UP-TO-DATE
> Task :build UP-TO-DATE


```

**完整的gradle.build**

```groovy
apply plugin: 'java' 
version = '0.0.1-SNAPSHOT'
// java插件下的属性
sourceCompatibility = 1.8 

repositories {
    mavenCentral()
}
// java插件下的属性
jar {
    manifest {
        attributes 'Main-Class': 'Main'
    }
}
```

## Gradle 属性的初始化读取
**1. ext**

```groovy
// build.gradle
ext {
    key1 = 'value1'
}

task myTask{
  assert key1 == 'value1'
}
```



```groovy
// build.gradle
ext {
    key1 = 'value1'
}
task myTask{
     ext {
        key1 = 'task1'
    }
    assert key1 == 'task1'
}
```

**2. gradle.properteis**

```properties
#${project}/gradle.properties
myprop=myvalue
```

```groovy
// build.gradle
task myTask{
    assert myprop = myvalue
}
```

## Gradle 文件读取


```properties
#version.properties
major=0
minor=1
release=false
```

```groovy

task getFile(description: '读取文件') {
    def versionfile = file("$rootDir/version.properties")
    def versionProps = new Properties()
    versionfile.withInputStream { stream ->
        versionProps.load(stream)
    }
    versionProps.each { x->
        println x
      //release=false
			//major=0
			//minor=1
    }
}
```



## Project

| Property                                                     | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`allprojects`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:allprojects) | The set containing this project and its subprojects.<br />`void allprojects(Closure configureClosure);` <br /> void方法 很明显不考虑返回参数，目的只是在 闭包对象里面执行一些东西，目的是对所有的Project便利然后配置一些参数 |
| [`ant`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:ant) | The `AntBuilder` for this project. You can use this in your build file to execute ant tasks. See example below.<br />实现了ant构建的api，能做许多事情例如文件读取 |
| [`artifacts`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:artifacts) | Returns a handler for assigning artifacts produced by the project to configurations.<br />对编译好的项目进行发布的自定义脚本？ |
| [`buildDir`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:buildDir) | The build directory of this project. The build directory is the directory which all artifacts are generated into. The default value for the build directory is `*projectDir*/build`<br />默认当前项目下的build |
| [`buildFile`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:buildFile) | The build script for this project.<br />指的就是build.gradle |
| [`buildscript`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:buildscript) | The build script handler for this project. You can use this handler to query details about the build script for this project, and manage the classpath used to compile and execute the project's build script.<br /> 对编译项目前的预先处理 |
| [`childProjects`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:childProjects) | The direct children of this project.<br />对子项目的配置 不包括根Procjet |
| [`configurations`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:configurations) | The configurations of this project.<br />配置configuration 定义如何载入依赖例如compile, testcompile, |
| [`convention`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:convention) | The [`Convention`](https://docs.gradle.org/current/javadoc/org/gradle/api/plugins/Convention.html) for this project. |
| [`defaultTasks`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:defaultTasks) | The names of the default tasks of this project. These are used when no tasks names are provided when starting the build.<br />默认执行的task |
| [`dependencies`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:dependencies) | The dependency handler of this project. The returned dependency handler instance can be used for adding new dependencies. For accessing already declared dependencies, the configurations can be used.<br />定义如何通过configuration 来加载文件 |
| [`description`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:description) | The description of this project, if any.<br />项目描述       |
| [`extensions`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:extensions) | Allows adding DSL extensions to the project. Useful for plugin authors.<br />对属性的扩展 |
| [`gradle`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:gradle) | The [`Gradle`](https://docs.gradle.org/current/dsl/org.gradle.api.invocation.Gradle.html) invocation which this project belongs to.<br />project属于gradle |
| [`group`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:group) | The group of this project. Gradle always uses the `toString()` value of the group. The group defaults to the path with dots as separators. |
| [`logger`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:logger) | The logger for this project. You can use this in your build file to write log messages. |
| [`logging`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:logging) | The [`LoggingManager`](https://docs.gradle.org/current/javadoc/org/gradle/api/logging/LoggingManager.html) which can be used to receive logging and to control the standard output/error capture for this project's build script. By default, System.out is redirected to the Gradle logging system at the QUIET log level, and System.err is redirected at the ERROR log level. |
| [`name`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:name) | The name of this project. The project's name is not necessarily unique within a project hierarchy. You should use the [`Project.getPath()`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:path) method for a unique identifier for the project. |
| [`normalization`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:normalization) | Provides access to configuring input normalization.          |
| [`parent`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:parent) | The parent project of this project, if any.                  |
| [`path`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:path) | The path of this project. The path is the fully qualified name of the project. |
| [`pluginManager`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:pluginManager) | The plugin manager for this plugin aware object.             |
| [`plugins`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:plugins) | The container of plugins that have been applied to this object. |
| [`project`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:project) | Returns this project. This method is useful in build files to explicitly access project properties and methods. For example, using `project.name` can express your intent better than using `name`. This method also allows you to access project properties from a scope where the property may be hidden, such as, for example, from a method or closure. |
| [`projectDir`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:projectDir) | The directory containing the project build file. <br />项目目录 |
| [`properties`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:properties) | The properties of this project. See [here](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project.properties) for details of the properties which are available for a project. |
| [`repositories`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:repositories) | Returns a handler to create repositories which are used for retrieving dependencies and uploading artifacts produced by the project. |
| [`resources`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:resources) | Provides access to resource-specific utility methods, for example factory methods that create various resources. |
| [`rootDir`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:rootDir) | The root directory of this project. The root directory is the project directory of the root project. |
| [`rootProject`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:rootProject) | The root project for the hierarchy that this project belongs to. In the case of a single-project build, this method returns this project. |
| [`state`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:state) | The evaluation state of this project. You can use this to access information about the evaluation of this project, such as whether it has failed. |
| [`status`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:status) | The status of this project. Gradle always uses the `toString()` value of the status. The status defaults to `release`. |
| [`subprojects`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:subprojects) | The set containing the subprojects of this project.          |
| [`tasks`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:tasks) | The tasks of this project.                                   |
| [`version`](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:version) | The version of this project. Gradle always uses the `toString()` value of the version. The version defaults to `unspecified`. |







