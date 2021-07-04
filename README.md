# MyAptDemo
一个java自定义注解的简单实现

​
注解：是我们在代码中添加的一种标记，一种添加信息的形式化方法，使得我们可以稍后在某个时刻方便的使用这些方法数据以及标记！
 注解是在一定程度上把元数据跟源代码文件结合在一起，java在SE5中引入了注解算是对自身编程语言的一个完善 同时也是对其他编程语言例如C#的一个回应。

起因
为啥要写一篇这样的文章，来念叨一下注解呢？作为一个Android程序员，我们在项目开发中或多或少的会引入一些三方库，比如为了解耦引入的Arouter 比如为了减少初始化绑定控件id引入的butterknife，为了发送消息引入的EventBus，等等，这些三方库的使用都有一个共性就是会在某些java源文件上加一个标记，例如 @Route()   @Bind() .....仿佛加上这些源文件就有了某些特殊的能力，就会做一些符合我们预期的事情。出于好奇我们对这些小标识来做一些浅薄的探索。

https://blog.csdn.net/haibowen/article/details/118460230?spm=1001.2014.3001.5501
