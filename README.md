# MyAptDemo
一个java自定义注解的简单实现

​
注解：是我们在代码中添加的一种标记，一种添加信息的形式化方法，使得我们可以稍后在某个时刻方便的使用这些方法数据以及标记！
 注解是在一定程度上把元数据跟源代码文件结合在一起，java在SE5中引入了注解算是对自身编程语言的一个完善 同时也是对其他编程语言例如C#的一个回应。

起因
为啥要写一篇这样的文章，来念叨一下注解呢？作为一个Android程序员，我们在项目开发中或多或少的会引入一些三方库，比如为了解耦引入的Arouter 比如为了减少初始化绑定控件id引入的butterknife，为了发送消息引入的EventBus，等等，这些三方库的使用都有一个共性就是会在某些java源文件上加一个标记，例如 @Route()   @Bind() .....仿佛加上这些源文件就有了某些特殊的能力，就会做一些符合我们预期的事情。出于好奇我们对这些小标识来做一些浅薄的探索。

初识
我们新建一个Android工程，切换到我们的工程目录下，找到我们的External Libraries 目录，点开我们的android API目录 我这里以29为参考，点开以后，点开我们的android.jar,找到我们的java lang包

（1）内置注解



这是我们在lang中找到的SE中的默认注解，我的JDK版本是1.8的 早前1.5的只内置了三个注解，后续版本又扩充了几个。





这些是java的内置注解，主要是为了实现编译时的检查的，比如我们常见的@Override 这在其它面向对象的语言里也很常见 我们在编写Kotlin代码是这个注解也常会出现，意在告知编译器我要重写父类的方法，这个注解只会存在源文件中，真正的字节码文件中是没有此类标识的，至于其他的内置注解就不再介绍，有编译警告的，有废弃API提示的，这些在编译期间或者编码期间都会有提示！

（2）元Annotation

只有jdk默认注解是远远不够满足我们编码需要的，有时候我们需要定义自己的注解，我们继续看 annotation包

jdk的设计者考虑到了这些，为我们留下了这些工具包，如果我们稍微看过一点上述三方库的源码的话对这些注解应该会有一些印象。



我们对其中的几个常用的注解做一个简短的解释

@Inherited

@Inherited指定被它修饰的Annotation将具有继承性——如果某个类使用了@Xxx注解（定义该Annotation时使用了@Inherited修饰）修饰，则其子类将自动被@Xxx修饰。

@Retention

@Retention：表示该注解类型的注解保留的时长。当注解类型声明中没有@Retention元注解，则默认保留策略为RetentionPolicy.CLASS。关于保留策略(RetentionPolicy)是枚举类型，共定义3种保留策略，

SOURCE	仅存在java源文件，经过编译后便丢弃
CLASS	存在java源文件，以及编译后的Class字节码文件，但在VM中不再保留注释
RUNTIME	存在java源文件  以及编译的Class字节码文件，以及保留在vm中
@Target

@Target：表示该注解类型的所适用的程序元素类型。当注解类型声明中没有@Target元注解，则默认为可适用所有的程序元素。如果存在指定的@Target元注解，则编译器强制实施相应的使用限制。关于程序元素(ElementType)是枚举类型，共定义8种程序元素

ANNOTATION_TYPE	注解类型声明
CONSTRUCTOR	构造方法声明
FIELD	字段声明
LOCAL_VARIABLE	局部变量声明
METHOD	方法声明
PACKAGE	包声明
PARAMETER	参数声明
TYPE	类接口枚举声明
原因
到这里我们对java 里面存在的一些注解有了一个基本的认识，现在我们来思考一个这样的问题，为啥要使用注解呢?我们在项目中或多或少会碰到一些重复代码的编写，只有一些名字上的差异，但内容上大体一致，对于这些重复的工作，对与编程人员来说自然是不可以容忍的，比如疯狂的findviewById这类的操作，比如一些if els的判断，实现同样的接口之类的操作，重复的总会让人有一瞬间丧失编码的乐趣，所以注解也应运而生！

使用
我们还是拿自定义注解说起，前面我们已经新建了一个Android工程，我们在之前的基础上再新建两个java libary ，为啥不是新建 android的libary，这里说下我们知道Android是基于 OpenJDK来的，在类库包上有一定的取舍，基于OpenJDK的包是没有涵盖这个AbstractProcessor这个类的，所以我们还是新建两个java libary吧！



为啥要新建两个呢，这是因为一个里面放我们的自定义注解，一个放我们的实现，我们也可以打开那些三方库来看下他们的目录结构

这是butterknife的



这是Arouter的



好了我们开始新建我们自己的注解

目录结构如下：



我这里也新建了两个库，下面开始我们的自定义注解  简单的定义了一个Test的注解，至于上面参数的含义 可以看下上面的描述



在另一个库中添加对Test注解的实现处理



我们看到这里继承了

AbstractProcessor 该类上面还加了AutoService的注解这是 源自于JavaPoet库的加持，对自动生成代码给与了很好的封装。
关于AbstractProcessor这个抽象类里面有几个方法需要特别注意一下



getSupportedSourceVersion 

这个方法非常简单，只有一个返回值，用来指定当前正在使用的Java版本，通常return SourceVersion.latestSupported()即可

getSupportedAnnotationTypes

这个方法的返回值是一个Set集合，集合中指要处理的注解类型的名称

 @Override
    public Set<String> getSupportedAnnotationTypes() {
        return Collections.singleton(Test.class.getCanonicalName());
    }
init

这个方法用于初始化处理器，方法中有一个ProcessingEnvironment类型的参数，ProcessingEnvironment是一个注解处理工具的集合。它包含了众多工具类。例如： Filer可以用来编写新文件； Messager可以用来打印错误信息； Elements是一个可以处理Element的工具类。


process

先看这个方法的返回值，是一个boolean类型，返回值表示注解是否由当前Processor 处理。如果返回 true，则这些注解由此注解来处理，后续其它的 Processor 无需再处理它们；如果返回 false，则这些注解未在此Processor中处理并，那么后续 Processor 可以继续处理它们。 在这个方法的方法体中，我们可以校验被注解的对象是否合法、可以编写处理注解的代码，以及自动生成需要的java文件等。因此说这个方法是AbstractProcessor 中的最重要的一个方法。我们要处理的大部分逻辑都是在这个方法中完成。

 @Override
    public boolean process(Set<? extends TypeElement> set, RoundEnvironment roundEnvironment) {
        MethodSpec main = MethodSpec.methodBuilder("main")
                .addModifiers(Modifier.PUBLIC, Modifier.STATIC)
                .returns(void.class)
                .addParameter(String[].class, "args")
                .addStatement("$T.out.println($S)", System.class, "Hello, JavaPoet!")
                .build();
        TypeSpec helloWorld = TypeSpec.classBuilder("HelloWorld")
                .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
                .addMethod(main)
                .build();
        JavaFile javaFile = JavaFile.builder("com.example.helloworld", helloWorld)
                .build();
        try {
            javaFile.writeTo(processingEnv.getFiler());
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;

    }
此外我们要注意我们java库 中的三方库的引入 例如JavaPoet  AutoService等

贴下build.gradle 文件

plugins {
    id 'java-library'
}


dependencies {

    // 编译时期进行注解处理
    annotationProcessor 'com.google.auto.service:auto-service:1.0-rc4'
    compileOnly 'com.google.auto.service:auto-service:1.0-rc4'
    // 帮助我们通过类调用的方式来生成Java代码[JavaPoet]
    implementation 'com.squareup:javapoet:1.10.0'
    // 依赖于注解
    implementation project(':annotation')
}
java {
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}
到此基本的自定义注解我们就基本完成了，我们看下如何使用呢？

看到我们在Activity上加了一个注解



编译运行在我们的build目录下就生成了代码：



我们来对比下我们在process方法中的逻辑看是否一致

方法 类型 文件



当让我们这个逻辑还可以很复杂，实现更加复杂的功能，类似我们的三方库的实现

其他
java中引入注解是对自身的一个完善，也是对其它面向对象语言的一个回应，此外也为java的单元测试引来的新的注解，对于java中的单元测试我们目之所及的Junit 也大量的采用了注解来对代码进行单元测试，这样就大大减少了单元测试所要编写的那些重复代码，而且注解还可以帮我们 测试一些私有的方法，此外还可以结合设计模式中利用注解生成一些模板代码，比如一些工厂模式也可以用注解来实现。

附上demo链接 https://github.com/haibowen/MyAptDemo.git


