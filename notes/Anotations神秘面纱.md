# Annotation神秘面纱

### 如何成为T型人才，垂直在一个行业中，必须要有一整套[知识体系](https://github.com/WeiSmart/Android-Advanced-CloudMap)，在这里，就一个字坚持~

## 前言

Annotation意思代表注解，其实留意平时代码，你会发现我们一直跟注解打交道。Android主流框架框架大量用到注解，要想看懂框架源码，弄懂实现原理，在注解、反射、泛型必须下点功夫，这些知识点我会单独讲解。注解的功能就要强大很多，不但能够生成描述符文件，而且有助于减轻编写“样板”代码的负担，使代码干净易读并且有利于减低耦合度。接下来分点介绍：

1、Annotation概念介绍和实用注解

2、了解注解处理器

2、什么是编译时注解，如何自定义，如何通过APT进行解析

3、什么是运行时注解，如何自定义，如何通过反射进行解析

## Contonts

### 一、Annotation概念介绍和实用注解

#### 1.1、什么是注解(**Annotation**)

Java提供的一种元程序中的元素关联任何信息或者任何元数据（metadata）的途径和方法。基本规则：Annotation(注解)不能影响程序代码的执行，无论增加，删除注解，代码都始终如一的执行。

#### 1.2、什么是元数据（**Metadata**）

- 以标签的形式存在于Java代码中
- 描述的信息是类型安全的
- 需要编译器之外的工具额外的处理用来生成其他的程序部件
- 可以存在于Java源代码级别，也可以存在于Java代码编译之后的class文件内部

#### 1.3、什么是元注解

用来定义其他注解的注解。元注解分别四种：@Retention、 @Target、@Inherited、@Documented。每种元注解，有多种值可以选择，具体功能如下。

1. **@Retention**：表示什么级别保存注解信息，参数值通过RetentionPolicy枚举获取。

   | **参数** |                           **说明**                           |
   | :------: | :----------------------------------------------------------: |
   |  SOURCE  |             注解将被编译器丢弃，只能存于源代码中             |
   |  CLASS   | 注解在class文件中可用，能够存于编译之后的字节码之中，但会被VM丢弃 |
   | RUNTIME  | VM在运行期也会保留注解，因此运行期注解可以通过反射获取注解的相关信息 |

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

   ```
   @Documented
   @Inherited
   @Retention(RetentionPolicy.CLASS)  //编译时时注解
   @Target({ElementType.TYPE, ElementType.METHOD, ElementType.FIELD}) //应用于类、接口（包括注解）、enum、方法、成员变量上
   public @interface Point {
       int id() default -1;   //埋点Id
   
       String message() default "";  //埋点描述
   }
   ```

   解析说明
   
   - 通过 @interface 定义，注解名即为自定义注解名，这里注解名为Point
   - 注解中将 id 和 message称为：int元素id , String 元素 message。而且注解元素的类型是有限制的，并不是任何类型都可以，主要包括：基本数据类型（理论上是没有基本类型的包装类型的，但是由于自动封装箱，所以也不会报错）、String 类型、enum 类型、Class 类型、Annotation 类型、以及以上类型的数组，否则编译器便会提示错误。

#### 1.4、常用Java注解

1. **@Override**：表示当前的方法定义将覆盖超类中的方法，如果方法拼写错误或者方法签名不匹配，编译器便会提出错误提示。

   ```
   @Target(ElementType.METHOD)  // 限制方法上使用
   @Retention(RetentionPolicy.SOURCE)  //源码上
   public @interface Override {
   }
   ```

2. **@Deprecated**： 表示当前方法已经被弃用，如果开发者使用了注解为它的元素，编译器便会发出警告信息。

   ```
   @Documented
   @Retention(RetentionPolicy.RUNTIME)  //运行时
   @Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})
   public @interface Deprecated {
   }
   ```

3. **@SuppressWarnings**：用来抑制编译时的警告信息。

   ```
   @Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
   @Retention(RetentionPolicy.SOURCE) //源码上
   public @interface SuppressWarnings {
       String[] value();  定义一个value类型为String参数
   }
   ```

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

### 二、运行时注解

介绍之前，先引用 Jeremy Meyer 的一段话：如果没有用来读取注解的工具，那么注解也不会比注释更有用。使用注解的过程中，很重要的一个部分就是创建与使用注解处理器。Java SE5 扩展了反射机制的API，以帮助程序员构造这类工具。同时，它还提供了一个外部工具 apt帮助程序员解析带有注解的 java 源代码。

根据上面描述我们可以知道，注解处理器并不是一个特定格式，并不是只有继承了 AbstractProcessor 这个抽象类才叫注解处理器，凡是根据相关API 来读取注解的类或者方法都可以称为注解处理器。

#### 2.1、反射

在解析注解时，我可以通过反射机制获取对象，再通过调用类Class对象获取特定注解，便可以获取注解之上的元素值，主要方法如下。

- **forName(className)**：获取全限定类名，获取Class对象

  ```
   Class c = Class.forName(className);
  ```

- **getDeclaredMethods()**：获取该对象内部所有方法

  ```
    Method[] methods = clasz.getDeclaredMethods();
  ```

- **getDeclaredConstructors()**：获取该对象内部所有构造方法

  ```
  Constructor[] constructors clasz.getDeclaredConstructors();
  ```
- **getAnnotation(AnnotationName.class)** ：表示得到该 Target 某个 Annotation 的信息，因为一个 Target 可以被多个 Annotation 修饰

  ```
  /*
  * 根据注解类型返回方法的指定类型注解
  */
  Point annotation = (Point) constructor
                       .getAnnotation(Point.class);
  ```

- **getAnnotations()** ：则表示得到该 Target 所有 Annotation

  ```
  /*
  * 获取该类所以注解数组
  */
  Annotation[] annotations = clazz.getAnnotations();
  for (Annotation annotation : annotations) {
      Point point = (Point) annotation
  }
  ```

- **isAnnotationPresent(AnnotationName.class)** 表示该 Target 是否被某个 Annotation 修饰

  ```
  /*
  * 判断构造方法中是否有指定注解类型的注解
  */
  boolean hasAnnotation = constructor
  .isAnnotationPresent(Point.class);
  if (hasAnnotation) {
      /*
      * 根据注解类型返回方法的指定类型注解
      */
      Point annotation = (Point) constructor
      .getAnnotation(Point.class);
  }
  ```

#### 2.2、自定义运行时注解（RUNTIME）

通过@Retention(RetentionPolicy.RUNTIME)定义注解，当前的注解在运行时是可见，采用反射方式进行解析，并获取注解信息。

1. 如何创建运行时注解

   ```
   @Retention(RetentionPolicy.RUNTIME)
   @Target({ElementType.TYPE, ElementType.METHOD,ElementType.CONSTRUCTOR})
   public @interface Point {
       String key() default "";  //埋点key
   
       int message() default -1; //埋点描述
   }
   ```
   
2. 如何使用自定义运行时注解

   ```
   public class RuntimePoint {
   
       @Point(key = "00", message = R.string.send_message_point)
       private void sendMessage() {
           System.out.println("发送数据..");
       }
   
       @Point(key = "11", message = R.string.receive_message_point)
       private void receiveMessage() {
           System.out.println("接受数据..");
       }
   
   }
   ```

3. 如何使用反射机制解析运行时注解

   ```
   public class RuntimeParser {
       /**
        * 使用到类注解 该方法只打印了 Type 类型的注解
        *
        * @param className
        * @throws ClassNotFoundException
        */
       public static void parseTypeAnnotation(String className) throws ClassNotFoundException {
           if (TextUtils.isEmpty(className)) return;
           Class clazz = Class.forName(className);
           //读取该类所有注解信息
           Annotation[] annotations = clazz.getAnnotations();
           for (Annotation annotation : annotations) {
               if (annotation instanceof Point) {
                   Point point = (Point) annotation;
                   System.out.println("key=" + point.key() +
                           ";message=" + point.message());
               }
           }
       }
   
       /**
        * 使用到构造注解，该方法只打印了 Method类型的注解
        */
       public static void  parseConstructAnnotation(){
           //获取改类所有构造对象
           Constructor[] constructors = RuntimePoint.class.getDeclaredConstructors();
           for(Constructor constructor:constructors){
               //判断该构造对象是否存在@Point注解
               boolean hasAnnotation = constructor.isAnnotationPresent(Point.class);
               if (hasAnnotation) {
                   Point point = (Point) constructor.getAnnotation(Point.class);
                   System.out.println("key=" + point.key() +
                           ";message=" + point.message());
               }
           }
       }
   
       /**
        * 使用到方法注解 该方法只打印了 Method类型的注解
        */
       public static void parseMethodAnnotation() {
           //获取该类中所有方法
           Method[] methods = RuntimePoint.class.getDeclaredMethods();
           for (Method method : methods) {
               //判断该方法是否存在@Point注解
               boolean hasAnnotation = method.isAnnotationPresent(Point.class);
               if (hasAnnotation) {
                   Point point = method.getAnnotation(Point.class);
                   System.out.println("key=" + point.key() +
                           ";message=" + point.message());
               }
           }
   
       }
   }
   ```
### 三、编译时注解

#### 3.1、AnnotationProcessor

由于反射对性能会有一定的损耗，现在使用最多的还是 AbstractProcessor 自定义注解处理器，因为后者并不需要通过反射实现，效率和直接调用普通方法没有区别，这也是为什么编译期注解比运行时注解更受欢迎，但是并不是说为了性能运行期注解就不能用了，只能说不能滥用，要在性能方面给予考虑。目前主要的用到运行期注解的框架差不多都有缓存机制，只有在第一次使用时通过反射机制，当再次使用时直接从缓存中取出。

##### 3.1.1、创建注解处理器

```
/**
 * Created by zmj on 2017/6/19.
 */
@AutoService(javax.annotation.processing.Processor.class)
public class BuriedPointProcessor extends AbstractProcessor {

    /**
     * 做一些初始化工作，注释处理工具框架调用了这个方法，
     * 当我们使用这个方法的时候会给我们传递一个 ProcessingEnvironment
     * 类型的实参。
     * 如果在同一个对象多次调用此方法，则抛出IllegalStateException异常
     * @param processingEnvironment 这个参数里面包含了很多工具方法
     */
    @Override
    public synchronized void init(ProcessingEnvironment processingEnvironment) {

        /**
         * 返回用来在元素上进行操作的某些工具方法的实现
         */
        Elements es = processingEnvironment.getElementUtils();
        /**
         * 返回用来创建新源、类或辅助文件的Filer
         */
        Filer filer = processingEnvironment.getFiler();
        /**
         * 返回用来在类型上进行操作的某些实用工具方法的实现
         */
        Types types = processingEnvironment.getTypeUtils();
        /**
         * 这是提供给开发者日志工具，我们可以用来报告错误和警告以及提示信息
         * 注意 message 使用后并不会结束过程
         * Kind 参数表示日志级别
         */
        Messager messager = processingEnvironment.getMessager();
        messager.printMessage(Diagnostic.Kind.ERROR,"例如当默认值为空则提示一个错误");
        /**
         * 返回任何生成的源和类文件应该符合的源版本
         */
        SourceVersion version = processingEnvironment.getSourceVersion();

        super.init(processingEnvironment);
    }

    /**
     * 注解的处理逻辑
     * @param set 
     * @param roundEnvironment
     * @return 如果返回true 不要求后续Processor处理它们，反之，则继续执行处理
     */
    @Override
    public boolean process(Set<? extends TypeElement> set, RoundEnvironment roundEnvironment) {

        /**
         * TypeElement 这表示一个类或者接口元素集合
         * 常用方法不多，TypeMirror getSuperclass()返回直接超类
         * 
         * 详细介绍下 RoundEnvironment 这个类
         * 常用方法：
         * boolean errorRaised() 如果在以前的处理round中发生错误，则返回true
         * Set<? extends Element> getElementsAnnotatedWith(Class<? extends Annotation> a)
         * 这里的 a 即你自定义的注解class类，返回使用给定注解类型注解的元素的集合
         * Set<? extends Element> getElementsAnnotatedWith(TypeElement a)
         * 
         * Element 的用法：
         * TypeMirror asType() 返回此元素定义的类型 如int
         * ElementKind getKind() 返回元素的类型 如 e.getkind() = ElementKind.FIELD 字段
         * boolean equals(Object obj) 如果参数表示与此元素相同的元素，则返回true
         * Name getSimpleName() 返回此元素的简单名称
         * List<? extends Elements> getEncloseElements 返回元素直接封装的元素
         * Element getEnclosingElements 返回此元素的最里层元素，如果这个元素是个字段等，则返回为类
         * 
         * Element子类：
         * ExecutableElement：表示某个类或接口的方法、构造方法或初始化程序（静态或实例），包括注释类型元		   * 素。对应@Target(ElementType.METHOD) @Target(ElementType.CONSTRUCTOR)
         *
         * PackageElement：表示一个包程序元素。提供对有关包极其成员的信息访问。
         * 对应@Target(ElementType.PACKAGE)
         *
         * TypeElement：表示一个类或接口程序元素。提供对有关类型极其成员的信息访问。对应                        * @Target(ElementType.TYPE)注意：枚举类型是一种类，而注解类型是一种接口。
         *  
         * TypeParameterElement:表示一般类、接口、方法或构造方法元素的类型参数。对应
         * @Target(ElementType.PARAMETER)    
         *
         * VariableElement:表示一个字段、enum常量、方法或构造方法参数、局部变量或异常参数。
		 * 对应 @Target(ElementType.LOCAL_VARIABLE)
         */

        return false;
    }

    /**
     * 指出注解处理器 处理哪种注解
     * 在 jdk1.7 中，我们可以使用注解 @SupportedAnnotationTypes()代替
     */
    @Override
    public Set<String> getSupportedAnnotationTypes() {
        return super.getSupportedAnnotationTypes();
    }

    /**
     * 指定当前注解器使用的Jdk版本
     * 在 jdk1.7 中，我们可以使用注解 @SupportedSourceVersion()代替
     */
    @Override
    public SourceVersion getSupportedSourceVersion() {
        return super.getSupportedSourceVersion();
    }
}
```



- **init(ProcessingEnvironment processingEnvironment)**: 做一些初始化工作，注释处理工具框架调用了这个方法，当我们使用这个方法的时候会给我们传递一个 ProcessingEnvironment  类型的实参。

- **getSupportedAnnotationTypes()**:告知Processor哪些注解需要处理。在 jdk1.7 中，我们可以使用注解 @SupportedAnnotationTypes()代替，需要考虑jdk版本问题。

  ```
  @Override
      public Set<String> getSupportedAnnotationTypes() {
          //返回一个Set集合，集合内容为 自定义注解的包名+类名。
          LinkedHashSet<String> types = new LinkedHashSet<>();
          types.add(Point.class.getCanonicalName());
          return types;
      }
  ```

  ```
  @SupportedAnnotationTypes({"com.linwei.buriedpointlibrary.Point"})
  ```

  注：@SupportedAnnotationTypes修饰AbstractProcessor类

- **process(Set<? extends TypeElement >annotations，RoundEnvironment roundEnv)**：这个方法是AbstractProcessor 中最核心，主要进行注解解析操作,该方法放回true,不要求后续Processor处理它们，反之，则继续执行处理。

- **getSupportedSourceVersion**():指定当前注解器使用的Jdk版本, 在 jdk1.7 中，我们可以使用注解 @SupportedSourceVersion()代替，需要考虑jdk版本问题。

##### 3.1.2、使用android-apt插件
我们的主工程项目（app）中引用了 processor 库，但注解处理器只在编译处理期间需要用到，编译处理完后就没有实际作用了，而主工程项目添加了这个库会引入很多不必要的文件。为了处理这个问题我们需 要引入插件android-apt。它主要有两个作用： 

- 仅仅在编译时期去依赖注解处理器所在的函数库并进行工作，但不会打包到APK中。 

- 为注解处理器生成的代码设置好路径，以便Android Studio能够找到它。 接下来介绍如何使用它。首先需要在整个工程（Progect）的 build.gradle 中添加如下语句：

  ```
  apply plugin: 'com.neenbedankt.android-apt'
  dependencies {
          classpath 'com.android.tools.build:gradle:3.6.1'
          //gradle3.0.0出现不兼容问题
          classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
      }
  ```

 接下来在主工程项目（app）的 build.gradle 中以apt的方式引入注解处理器processor，如下所示

```
dependencies {
 	//引入注解
    implementation project(':annotation')
    //引入注解处理器
    apt  project(':annotation-processor')
    //annotationProcessor  project(':annotation-processor')
}
```

[^apt]: 代表的是android-apt插件，Gradle3.X.X 后，不兼容android-apt插件生成编译时注解的方式，推荐使用`annotationProcessor project（）`代替`apt project（）`

##### 3.1.3、注册注解处理器 
为了能使用注解处理器，需要用一个服务文件来注册它。现在我们就来创建这个服务文件。首先在 anotation-processor 库的 main 目录下新建 resources 资源文件夹，接下来在 resources 中再建立META-INF/services目录 文件夹。最后在META-INF/services中创建 javax.annotation.processing.Processor文件，这个文件中的内容是 注解处理器的名称。这里我们的javax.annotation.processing.Processor文件的内容为 com.linwei.buriedpointlibrary.BuriedPointProcessor，完整目录如下：

```
-annotation-processor(注解处理器java moule)
 -main
 	-BuriedPointProcessor.java
 -resources
 	-META-INF
 		-services
 			-javax.annotation.processing.Processor
```

如果你觉得前面创建服务文件的步骤比较麻烦，也可以使用 Google 开源的 AutoService，它用来帮助开 发者生成META-INF/services/javax.annotation.processing.Processor文件。首先我们添加该开源库，可以在 File→Project Structure 搜索“auto-service”查找该库并添加,也可以在anotation-processor的build.gradle中 直接添加如下代码：

```
dependencies {
    implementation 'com.google.auto.service:auto-service:1.0-rc3'
}
```

最后在注解处理器BuriedPointProcessor中添加@AutoService（Processor.class）就可以了：

```
@AutoService(javax.annotation.processing.Processor.class)
public class BuriedPointProcessor extends AbstractProcessor {
}
```

#### 3.2、自定义编译时注解（CLASS）

通过@Retention(RetentionPolicy.CLASS)定义注解，当前的注解在编译时是可见，一般通过自定义AbstractProcessor 处理器。在使用AbstractProcessor 创建自定义注解处理器时，只需要继承AbstractProcssor类。

在自定义编译时注解时，注解处理器必须单独创建java module,因为Android环境是不支持。而且，注解定义（创建）单独抽取到一个java module中，其实这样处理是有好处，在主工程项目（app）的build.gradle中以apt方式引入注解处理器时，因为只有在编译处理期间需要用到，编译处理完成就没有实际作用，避免主项目引入多余文件。

1. 项目目录结构，module项目引入，build.gradle配置，代码如下：

   ```
   -BuriedPointFactory（Android Project）
    -app
    	-build.gradle==>dependencies {
    	//引入注解
       implementation project(':annotation')
       //引入注解处理器
       annotationProcessor project(':annotation-processor')
   }
    -annotation(定义注解java module)==>dependencies {
       implementation fileTree(dir: 'libs', include: ['*.jar'])
   
   }
    -annotation-processor(注解处理器java moule)==>dependencies {
       implementation fileTree(dir: 'libs', include: ['*.jar'])
       //注册注解处理器
       implementation 'com.google.auto.service:auto-service:1.0-rc3'
       //生成java文件
       implementation 'com.squareup:javapoet:1.11.1'
       //引入注解
       implementation project(':annotation')
   
   }
    -build.gradle==>dependencies {
           classpath 'com.android.tools.build:gradle:3.6.1'
           //gradle3.0.0出现不兼容问题
           //classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
       }
    
   ```
   

2.创建埋点注解，在annotation(java module)项目进行创建，代码如下：

```
@Retention(RetentionPolicy.CLASS)  //编译时注解
@Target({ElementType.TYPE, ElementType.METHOD,ElementType.CONSTRUCTOR})
public @interface Point {
    String key() default ""; //定义埋点标识key

    int message() default -1;  //定义埋点描述
}
```

3.编写我们的解析器,继承 AbstractProcessor ，并重写 process 方法，处理相关逻辑。

```
//@SupportedAnnotationTypes({"com.linwei.buriedpointlibrary.Point"})
//@SupportedSourceVersion(SourceVersion.RELEASE_7)
@AutoService(javax.annotation.processing.Processor.class)
public class BuriedPointProcessor extends AbstractProcessor {
    @Override
    public synchronized void init(ProcessingEnvironment processingEnvironment) {
        super.init(processingEnvironment);
    }

    /**
     * 注解的处理逻辑
     *
     * @param set
     * @param roundEnvironment
     * @return 如果返回true 不要求后续Processor处理它们，反之，则继续执行处理
     */
    @Override
    public boolean process(Set<? extends TypeElement> set, RoundEnvironment roundEnvironment) {
        Messager messager = processingEnv.getMessager();
        //第一步，根据我们自定义的注解拿到elememts set 集合
        Set<? extends Element> elements = roundEnvironment.getElementsAnnotatedWith(Point.class);
        TypeElement typeElement;
        VariableElement variableElement;
        ExecutableElement executableElement;
        // 第二步： 根据 element 的类型做相应的处理
        for (Element element : elements) {
            //第三步：返回此元素定义的类型
            ElementKind kind = element.getKind();
            messager.printMessage(Diagnostic.Kind.NOTE, "kind=" + kind );
            if (kind == ElementKind.CLASS) {
                //判断该元素是否为类
                typeElement = (TypeElement) element;
                String qualifiedName = typeElement.getQualifiedName().toString();
                messager.printMessage(Diagnostic.Kind.NOTE, "Name=" + qualifiedName);
            } else if (kind == ElementKind.FIELD) {
                // 判断该元素是否为成员变量
                variableElement = (VariableElement) element;
                //获取该元素的封装类型
                typeElement = (TypeElement) variableElement.getEnclosingElement();
                String qualifiedName = typeElement.getQualifiedName().toString();
                messager.printMessage(Diagnostic.Kind.NOTE, "Name=" + qualifiedName);
            }else if(kind==ElementKind.METHOD){
                executableElement = (ExecutableElement) element;
                typeElement = (TypeElement) executableElement.getEnclosingElement();
                String qualifiedName = typeElement.getQualifiedName().toString();
                messager.printMessage(Diagnostic.Kind.NOTE, "Name=" + qualifiedName);
            }
        }
        return true;
    }


    /**
     * 指出注解处理器 处理哪种注解
     * 在 jdk1.7 中，我们可以使用注解 @SupportedAnnotationTypes()代替
     */
    @Override
    public Set<String> getSupportedAnnotationTypes() {
        //返回一个Set集合，集合内容为 自定义注解的包名+类名。
        LinkedHashSet<String> types = new LinkedHashSet<>();
        types.add(Point.class.getCanonicalName());
        return types;
    }

    /**
     * 指定当前注解器使用的Jdk版本
     * 在 jdk1.7 中，我们可以使用注解 @SupportedSourceVersion()代替
     */
    @Override
    public SourceVersion getSupportedSourceVersion() {
        return SourceVersion.latestSupported();
    }
}
```

4、注解使用方式，代码如下

```
    @Point(key = "00", message = R.string.send_message_point)
    public void apply() {
        //增加编译时埋点
        System.out.println("point");
    }
```

增加注解后，点击Rebuild Project按钮进行项目编译，编译成功后，在Build Output控制台输出注解信息，信息如下：

```
注: kind=METHOD
注: Name=com.linwei.buriedpointfactory.MainActivity
```

### 四、总结

该篇幅比较长，能坚持看完，恭喜您离成功又近了一步。该篇主要讲解注解一些名词概念，一些api使用，如何创建运行时注解，编译时注解，并进行简单使用。弄懂注解知识点，对于框架原理理解有很大帮助，在下片文章我会带领大家进行简单手写ButterKnife框架。文章有哪些知识点有出入，希望大家"举报"作者，谢谢!

## 参考资料 

1. [Android 自定义注解详解](https://blog.csdn.net/qq_36707431/article/details/94384696)
2. [Android注解快速入门和实用解析](https://www.jianshu.com/p/9ca78aa4ab4d)
3. Android进阶之光

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
