# Annotation神秘面纱

### 如何成为T型人才，垂直在一个行业中，必须要有一整套[知识体系](https://github.com/WeiSmart/Android-Advanced-CloudMap)，在这里，就一个字坚持~

## 前言

Annotation意思代表注解，其实留意平时代码，你会发现我们一直跟注解打交道。Android主流框架框架大量用到注解，要想看懂框架源码，弄懂实现原理，在注解、反射、泛型必须下点功夫，这些知识点我会单独讲解。而且，注解可以解决一些规律性逻辑，并且有利于减低耦合度。接下来分点介绍：

1、Annotation概念介绍和实用注解

2、什么是编译时注解，如何自定义，如何通过APT进行解析

3、什么是运行时注解，如何自定义，如何通过反射进行解析

## Contonts

### 一、Annotation概念介绍和实用注解

#### 1.1、什么是注解(**Annotation**)

​            Annotation：Java提供的一种元程序中的元素关联任何信息或者任何元数据（metadata）的途径和方法。基本规则：Annotation(注解)不能影响程序代码的执行，无论增加，删除注解，代码都始终如一的执行。

#### 1.2、什么是元数据（**Metadata**）

- 以标签的形式存在于Java代码中
- 描述的信息是类型安全的
- 需要编译器之外的工具额外的处理用来生成其他的程序部件
- 可以存在于Java源代码级别，也可以存在于Java代码编译之后的class文件内部

#### 1.3、什么是元注解

​			用来定义其他注解的注解。元注解分别四种：@Retention、 @Target、@Inherited、@Documented。每种元注解，有多种值可以选择，具体功能如下。

1. **@Retention**：表示什么级别保存注解信息，参数值通过RetentionPolicy枚举获取。

   | **参数** |                      **说明**                      |
   | :------: | :------------------------------------------------: |
   |  SOURCE  |                 注解将被编译器丢弃                 |
   |  CLASS   |        注解在class文件中谁用，但会被JVM抛弃        |
   | RUNTIME  | 在运行期也保留注解信息，可通过发射机制读取注解信息 |

2. **@Target**：表示注解使用范围，参数值通过ElementType枚举获取。

   |      参数       |               说明               |
   | :-------------: | :------------------------------: |
   |      TYPE       | 类，接口(包括注释类型)或enum声明 |
   |      FIELD      |       域声明(包括enum实例)       |
   |     METHOD      |             方法声明             |
   |    PARAMETER    |             参数声明             |
   |   CONSTRUCTOR   |           构造器的声明           |
   | LOCAL_VARIABLE  |           局部变量声明           |
   | ANNOTATION_TYPE |           注解类型声明           |
   |     PACKAGE     |              包声明              |
   | TYPE_PARAMETER  |           输入参数声明           |
   |    TYPE_USE     |           使用类型声明           |

   

3. **@Inherited**：表明我们标记的注解是被继承的。如果一个父类使用了@Inherited修饰的注解，则允许子类继承该父类的注解。同时该声明注解只对类有效，对与方法、属性无效。

4. **@Documented**：标记的注解可以被javadoc此类的工具文档化。

#### 1.4、常用Java注解

1. **@Override**：子类对父类方法的重写所带的注解。

2. **@Deprecated**：表示已经过时，不建议使用，但是依然可以使用。

3. **@SuppressWarnings**：用来抑制编译时的警告信息。

   | **参数**    | **说明**                                         |
   | ----------- | ------------------------------------------------ |
   | deprecation | 过滤使用了过时的类或方法的警告                   |
   | unchecked   | 过滤执行了未检查的转换警告                       |
   | fallthrough | 过滤switch语句发生case穿透的警告                 |
   | path        | 过滤类路径，源文件路径等路径不存在的警告         |
   | serial      | 过滤可序列化类上缺少serialVersionUID定义时的警告 |
   | finally     | 过滤任何finally子句不能完成时的警告              |
   | all         | 过滤所有情况的警告                               |

   #### 1.5、常用Android注解

   1. **@NonNull**：声明方法的参数不能为空

   2. **@Nullable**：表示一个方法的参数或者返回值可以是Null的

   3. **@CallSuper**：重写的方法必须要调用super方法

   4. **@RequiresPermission**：辅助发现需要申请的权限

   5. **@CheckResult**：用来注解方法，如果一个方法得到了结果，却没有使用这个结果，就会有错误出现

   6. **@Keep**：不混淆

   7. **@ColorInt**：限定颜色资源id

   8. **@UiThread**：通常可以等同于主线程,标注方法需要在UIThread执行

   9. **@MainThread**：主线程,经常启动后创建的第一个线程

   10. **@WorkerThread**：工作者线程

   11. **@BinderThread**：注解方法必须要在BinderThread线程中执行,使用较少



## 赞赏

如果Open Coder对您有很大帮助，您愿意扫描下面的二维码，只需要支付0.01，表达您对我认可和鼓励。
> 非常感谢您的捐赠。谢谢!

<div align="center">
<img src="https://github.com/WeiSmart/tablayout/blob/master/screenshots/weixin_pay.jpg" width=20%>
<img src="https://github.com/WeiSmart/tablayout/blob/master/screenshots/zifubao_pay.jpg" width=20%>
</div>
---
## About me
- #### Email:linwei9605@gmail.com   
- #### Blog: [https://offer.github.io/](https://offer.github.io/)
- #### 掘金: [https://juejin.im/user/59091b030ce46300618529e0](https://juejin.im/user/59091b030ce46300618529e0)

## License
```
   Copyright 2020 offer

      Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.```

```