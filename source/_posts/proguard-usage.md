---
title: ProGuard 使用指南
categories:
  - 技术
tags:
  - 文章
date: 2019-04-28 17:34:30
---

ProGuard 是一个免费开源的 java包优化和混淆的工具。

<!-- more -->



[官方介绍](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html)

## Introduction

ProGuard是Java类文件缩减器，优化器，混淆器和预验证器。
压缩(**Shrink**)步骤检测并删除未使用的类，字段，方法和属性。
优化(**Optimize**)步骤分析并优化方法的字节码。
混淆(**Obfuscate**)步骤使用简短的无意义名称重命名剩余的类，字段和方法。
预验证(**Preveirfy**)步骤会向类添加预验证信息.
这些第一步使代码库更小，更高效，并且更难以进行逆向工程。
最后的预验证步骤可以改善Java 6的启动时间。
每个步骤都是可选的。例如，ProGuard还可用于在应用程序中列出死代码，或预先验证类文件以便在Java 6中有效使用。

![img](/img/QQ截图20190428183128.png)

## Usage

- 命令版

  java -jar proguard.jar @myconfig.pro -verbose

  jar包下载地址 在下面GUI版中的lib目录中

- GUI版

  [下载地址1](https://pan.baidu.com/s/1SWTvKmDCI69FKUgWHbjB5g) 提取码: 7v3p

  [下载地址2]()

## 参数定义

### input/output

- -injars
  要处理的应用程序。 可以是jar、war、ears、zips或者是个目录。
- -outjars
  指定要输出的jar、war、ears、zips或者是个目录。
- -libraryjars
  项目中使用到的jar类库。
- -skipnonpubliclibraryclasses
  指定在读取库jar时跳过非公共类，以加快处理速度并减少ProGuard的内存使用量。 默认情况下，ProGuard会读取非公共类和公共库类。 但是，非公共类通常不相关，如果它们不影响输入jar中的实际程序代码。 忽略它们然后加速ProGuard，而不影响输出。 遗憾的是，某些库（包括最近的JSE运行时库）包含由公共库类扩展的非公共库类。 然后，您无法使用此选项。 如果由于设置了此选项而无法找到类，ProGuard将打印出警告。
- -dontskipnonpubliclibraryclassmembers
  指定不忽略包可见库类成员（字段和方法），默认情况下，ProGuard会在解析库类时跳过这些类成员，因为程序类通常不会引用它们。 但有时，程序类与库类位于相同的包中，并且它们确实引用了它们的包可见类成员。 在这些情况下，实际读取类成员可能很有用，以确保处理后的代码保持一致。
- -target
  指定在已处理的类文件中设置的版本号
- -forceprocessing
  强制使用最新的输入文件处理。

### Keep Options

- -keep
  规则是 xxx.class 支持 ?、*或**,比如: com.baidu.demo.* {*;} [参考][1]
- -keepclasseswithmembers
  保留一些特殊的类方法。
- -keepclasseswithmembers
  指定要保留的类和类成员，条件是存在所有指定的类成员。 例如，您可能希望保留所有具有main方法的应用程序，而不必显式列出它们。
- -keepnames
  指定要保留其名称的类和类成员（如果在shrinking阶段 未删除它们）。
- -keepclassmembernames
  指定要保留其名称的类成员。
- -keepclasseswithmembernames
  指定要保留其名称的类和类成员，条件是在shrinking阶段之后存在所有指定的类成员。
- -printseeds
  指定详尽列出由各种-keep选项匹配的类和类成员。 列表将打印到标准输出或给定文件。该列表可用于验证是否确实找到了预期的类成员，尤其是在使用通配符时。例如，您可能希望列出您保留的所有应用程序或所有小程序。

### Shrinking Options

- dontshrink
  指定不压缩输入文件。默认情况下压缩，所有类和类成员都被删除，除了各种-keep选项列出的那些，以及它们所依赖的那些，直接或间接。在每个优化步骤之后也会应用缩小步骤，因为某些优化可能会删除更多类和类成员。
- -printusage [filename]
  指定列出输入类文件的死代码。列表将打印到标准输出或给定文件。例如，您可以列出应用程序的未使用代码。仅适用于收缩时。

-whyareyoukeeping class_specification
指定打印有关为什么在缩小步骤中保留给定类和类成员的详细信息。如果您想知道为什么输出中存在某些给定元素，这可能很有用。通常，可能有许多不同的原因。对于每个指定的类和类成员，此选项将最短的方法链打印到指定的种子或入口点。在当前的实施中，打印出的最短链条有时可能包含循环扣除 - 这些不反映实际的收缩过程。如果指定了-verbose选项，则跟踪包括完整字段和方法签名。仅适用于收缩时。

### Optimization Options

- -dontoptimize
  指定不优化输入类文件。默认情况下，启用优化;所有方法都在字节码级别进行优化。
- -optimizations optimization_filter
  指定要在更细粒度级别启用和禁用的优化。仅适用于优化时。这是一个专家选择。
- -optimizationpasses n
  指定要执行的优化传递的数量。默认情况下，执行单次传递。多次通过可能会导致进一步的改进。如果在优化过程后未找到任何改进，则优化结束。仅适用于优化时。
- -assumenosideeffects class_specification
  指定没有任何副作用的方法（除了可能返回值）。在优化步骤中，如果ProGuard可以确定不使用返回值，则会删除对此类方法的调用。请注意，ProGuard将分析您的程序代码以自动查找此类方法。它不会分析库代码，因此该选项对此有用。例如，您可以指定方法System.currentTimeMillis（），以便删除对它的任何空闲调用。请注意，ProGuard将该选项应用于指定方法的整个层次结构。仅适用于优化时。一般来说，做出假设可能是危险的;您可以轻松破坏已处理的代码。如果您知道自己在做什么，请仅使用此选项！

-allowaccessmodification
指定在处理期间可以扩展类和类成员的访问修饰符。这可以改善优化步骤的结果。例如，在内联公共getter时，可能还需要公开访问的字段。尽管Java的二进制兼容性规范在形式上并不要求这样做（参见Java语言规范，第二版，第13.4.6节），否则一些虚拟机会遇到处理过的代码问题。仅在优化时（以及使用-repackageclasses选项进行模糊处理时）。
反指示：在处理要用作库的代码时，您可能不应该使用此选项，因为未设计为在API中公开的类和类成员可能会公开。

-mergeinterfacesaggressively
指定可以合并接口，即使它们的实现类没有实现所有接口方法。这可以通过减少类的总数来减小输出的大小。请注意，Java的二进制兼容性规范允许这样的结构（参见Java语言规范，第二版，第13.5.3节），即使它们不被Java语言允许（参见Java语言规范，第二版，第8.1节）。 4）。仅适用于优化时。
反指示：设置此选项会降低某些JVM上已处理代码的性能，因为高级即时编译往往倾向于使用更少的实现类来实现更多接口。更糟糕的是，某些JVM可能无法处理生成的代码。值得注意的是：当遇到类中超过256种Miranda方法（没有实现的接口方法）时，Sun的JRE 1.3可能会抛出InternalError。

### Obfuscation Options

- -dontobfuscate
  指定不混淆输入类文件。默认情况下，应用模糊处理;类和类成员接收新的短随机名称，但各种-keep选项列出的名称除外。将删除对调试有用的内部属性，例如源文件名，变量名和行号。
- -printmapping [filename]
  指定为已重命名的类和类成员打印从旧名称到新名称的映射。映射将打印到标准输出或给定文件。例如，它是后续增量混淆所必需的，或者如果您想要再次理解混淆的堆栈跟踪。仅在混淆时适用。
- -applymapping filename
  指定重用在先前的ProGuard混淆运行中打印出的给定名称映射。映射文件中列出的类和类成员将接收与其一起指定的名称。未提及的类和类成员会收到新名称。映射可以指输入类以及库类。此选项对于增量混淆非常有用，即处理现有代码段的附加组件或小补丁。在这种情况下，您应该考虑是否还需要选项-useuniqueclassmembernames。只允许一个映射文件。仅在混淆时适用。
- -obfuscationdictionary filename
  指定一个文本文件，所有有效单词都用作混淆的字段和方法名称。默认情况下，“a”，“b”等短名称用作混淆名称。使用混淆字典，您可以指定保留关键字列表或具有外来字符的标识符。忽略＃符号后的空格，标点字符，重复单词和注释。请注意，混淆字典几乎不会改善混淆。体面的编译器可以自动替换它们，并且通过使用更简单的名称再次混淆可以简单地解除效果。最有用的应用程序是指定通常已经存在于类文件中的字符串（例如“代码”），从而减少了类文件的大小。仅在混淆时适用。
- -classobfuscationdictionary filename
  指定一个文本文件，所有有效的单词都用作混淆的类名。混淆字典类似于选项-obfuscationdictionary之一。仅在混淆时适用。
- -packageobfuscationdictionary filename
  指定一个文本文件，从该文本文件中将所有有效单词用作模糊处理的包名称。混淆字典类似于选项-obfuscationdictionary之一。仅在混淆时适用。
- -overloadaggressively
  指定在混淆时应用积极的重载。然后，多个字段和方法可以获得相同的名称，只要它们的参数和返回类型不同（不仅仅是它们的参数）。此选项可以使处理后的代码更小（并且更难以理解）。仅在混淆时适用。
  反指示：生成的类文件属于Java字节码规范（参见Java虚拟机规范，第二版，第4.5节和第4.6节的第一段），即使Java语言中不允许这种重载（ cfr.Java语言规范，第二版，第8.3节和第8.4.7节）。但是，有些工具存在问题。值得注意的是：Sun的JDK 1.2.2 javac编译器在使用这样的库进行编译时会产生异常（cfr.Bug＃4216736）。您可能不应该使用此选项来处理库。
  Sun的JRE 1.4及更高版本无法使用重载的原始字段序列化对象。
  据报道，Sun的JRE 1.5 pack200工具存在重载类成员的问题。
  谷歌的Dalvik VM无法处理超载的静态字段。
- -useuniqueclassmembernames
  指定将相同的模糊名称分配给具有相同名称的类成员，并将不同的模糊名称分配给具有不同名称的类成员（对于每个给定的类成员签名）。如果没有该选项，可以将更多类成员映射到相同的短名称，如“a”，“b”等。因此，该选项会稍微增加结果代码的大小，但它确保保存的混淆名称映射始终可以在随后的增量混淆步骤中受到尊重。
  例如，考虑两个不同的接口，包含具有相同名称和签名的方法。如果没有此选项，这些方法可能会在第一个混淆步骤中获得不同的混淆名称。如果随后添加了包含实现两个接口的类的补丁，则ProGuard必须在增量混淆步骤中为这两个方法强制实施相同的方法名称。原始混淆代码已更改，以保持生成的代码一致。在初始混淆步骤中使用此选项，将永远不需要这种重命名。此选项仅在混淆时适用。事实上，如果您计划执行增量混淆，您可能希望完全避免收缩和优化，因为这些步骤可能会删除或修改代码中对以后添加必不可少的部分。
- -dontusemixedcaseclassnames
  指定在混淆时不生成混合大小写的类名。默认情况下，混淆的类名称可以包含大写字符和小写字符的混合。这创造了完全可接受和可用的罐子。只有在具有不区分大小写的文件系统（例如，Windows）的平台上解压缩jar时，解包工具才可以让类似命名的类文件相互覆盖。解包时自毁的代码！真正想在Windows上解压缩jar的开发人员可以使用此选项来关闭此行为。请注意，混淆的罐子会因此变大。仅在混淆时适用。
- -keeppackagenames [package_filter]
  指定不混淆给定的包名称。可选过滤器是以逗号分隔的包名列表。包名称可以包含？，*和**通配符，它们之前可以包含！否定器。仅在混淆时适用。
- -flattenpackagehierarchy [package_name]
  指定通过将所有重命名的包移动到单个给定的父包中来重新打包它们。如果没有参数或空字符串（''），包将被移动到根包中。此选项是进一步混淆包名称的一个示例。它可以使处理过的代码更小，更难以理解。仅在混淆时适用。
- -repackageclasses [package_name]
  指定通过将所有重命名的类文件移动到单个给定包中来重新打包它们。如果没有参数或空字符串（''），则会完全删除包。此选项选项会覆盖-flattenpackagehierarchy选项。这是进一步混淆包名称的另一个例子。它可以使处理后的代码更小，更难以理解。它不推荐使用的名称是-defaultpackage。仅在混淆时适用。
  反指示：在包目录中查找资源文件的类如果被移动到其他地方将不再正常工作。如有疑问，请不要使用此选项保持包装不受影响。
- -keepattributes [attribute_filter]
  指定要保留的任何可选属性。可以使用一个或多个-keepattributes指令指定属性。可选过滤器是以逗号分隔的属性名称列表。属性名称可以包含？，*和**通配符，它们之前可以包含！否定器。典型的可选属性是Exceptions，Signature，Deprecated，SourceFile，SourceDir，LineNumberTable，LocalVariableTable，LocalVariableTypeTable，Synthetic，EnclosingMethod，RuntimeVisibleAnnotations，RuntimeInvisibleAnnotations，RuntimeVisibleParameterAnnotations，RuntimeInvisibleParameterAnnotations和AnnotationDefault。也可以指定InnerClasses属性名称，引用此属性的源名称部分。例如，在处理库时，至少应保留Exceptions，InnerClasses和Signature属性。您还应该保留SourceFile和LineNumberTable属性，以生成有用的混淆堆栈跟踪。最后，如果您的代码依赖于它们，您可能希望保留注释。仅在混淆时适用。
- -keepparameternames
  指定保留参数名称和保留的方法类型。此选项实际上保留了调试属性LocalVariableTable和LocalVariableTypeTable的修剪版本。它在处理库时很有用。某些IDE可以使用该信息来帮助使用该库的开发人员，例如使用工具提示或自动完成。仅在混淆时适用。
- -renamesourcefileattribute [string]
  指定要放在类文件的SourceFile属性（和SourceDir属性）中的常量字符串。请注意，必须首先存在该属性，因此还必须使用-keepattributes指令显式保留该属性。例如，您可能希望让已处理的库和应用程序生成有用的混淆堆栈跟踪。仅在混淆时适用。
- -adaptclassstrings [class_filter]
  指定对应于类名的字符串常量也应进行模糊处理。如果没有过滤器，则会调整与类名对应的所有字符串常量。使用过滤器，只调整与过滤器匹配的类中的字符串常量。例如，如果您的代码包含大量引用类的硬编码字符串，并且您不想保留其名称，则可能需要使用此选项。在混淆时主要适用，但相应的类也会自动保留在收缩步骤中。

-adaptresourcefilenames [file_filter]
根据相应类文件（如果有）的模糊名称指定要重命名的资源文件。如果没有过滤器，则会重命名与类文件对应的所有资源文件。使用过滤器，仅重命名匹配的文件。例如，请参阅处理资源文件。仅在混淆时适用。

- -adaptresourcefilecontents [file_filter]
  指定要更新其内容的资源文件。根据相应类的模糊名称（如果有），重命名资源文件中提到的任何类名。没有过滤器，所有资源文件的内容都会更新。使用过滤器，仅更新匹配的文件。使用平台的默认字符集解析和写入资源文件。您可以通过设置环境变量LANG或Java系统属性file.encoding来更改此默认字符集。有关示例，请参阅处理资源文件。仅在混淆时适用。

### Preverification Options

- -dontpreverify
  指定不预验证已处理的类文件。默认情况下，如果类文件针对Java Micro Edition或Java 6或更高版本，则会对其进行预验证。对于Java Micro Edition，需要进行预验证，因此如果指定此选项，则需要在已处理的代码上运行外部预验证程序。对于Java 6，（还）不需要预验证，但它提高了Java虚拟机中类加载的效率。
- -microedition
  指定已处理的类文件以Java Micro Edition为目标。然后，预处理器将添加适当的StackMap属性，这些属性与Java Standard Edition的默认StackMapTable属性不同。例如，如果要处理midlet，则需要此选项。

### General Options

- -verbose
  指定在处理期间写出更多信息。如果程序以异常终止，则此选项将打印出整个堆栈跟踪，而不仅仅是异常消息。
- -dontnote [class_filter]
  指定不打印有关配置中可能存在的错误或遗漏的注释，例如类名中的拼写错误，或者缺少可能有用的缺失选项。可选过滤器是正则表达式; ProGuard不会打印有关匹配名称的类的注释。
- -dontwarn [class_filter]
  指定不要警告未解决的引用和其他重要问题。可选过滤器是正则表达式; ProGuard不会打印有关匹配名称的类的警告。忽略警告可能很危险。例如，如果确实需要处理未解析的类或类成员，则处理的代码将无法正常运行。如果您知道自己在做什么，请仅使用此选项！
- -ignorewarnings
  指定打印有关未解析的引用和其他重要问题的任何警告，但在任何情况下都要继续处理。忽略警告可能很危险。例如，如果确实需要处理未解析的类或类成员，则处理的代码将无法正常运行。如果您知道自己在做什么，请仅使用此选项！
- -printconfiguration [filename]
  指定写出已解析的整个配置，包含已包含的文件和替换的变量。结构将打印到标准输出或给定文件。这有时可用于调试配置或将XML配置转换为更易读的格式。
- -dump [filename]
  指定在任何处理之后写出类文件的内部结构。结构将打印到标准输出或给定文件。例如，您可能想要写出给定jar文件的内容，而根本不处理它。

## 注意事项

1. -dontpreverify  不需要添加这个 否则容易出现下面的问题
   [java.lang.VerifyError: Expecting a stackmap frame at branch target 17][2]
2. -dontshrink 混淆jar包 这个一般需要添加，否者会去除掉重要的方法

[1]: https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification
[2]: https://github.com/robolectric/robolectric-gradle-plugin/issues/144

  

## 例子

```
-injars 'D:\baidu-demo-0.0.1.jar'
-outjars 'D:\baidu-demo-0.0.1-p29.jar'

-libraryjars 'D:\java\jre1.8.0\lib\rt.jar'

-dontskipnonpubliclibraryclassmembers
-target 1.8
-dontshrink
-optimizations !code/simplification/cast,!field/*,!class/merging/*
-printmapping 'C:\proguardMapping.txt'
-dontusemixedcaseclassnames
-keepattributes Signature,SourceFile,LineNumberTable,Exceptions,*Annotation*,InnerClasses
-verbose
-dontwarn okio.**,okhttp3.**,com.alibaba.fastjson.**,javax.crypto.**,com.poseidon.**

-keep class okhttp3.** {
    <fields>;
    <methods>;
}

-keep class okio.** {
    <fields>;
    <methods>;
}

-keep class com.alibaba.fastjson.** {
    <fields>;
    <methods>;
}

-keep class javax.crypto.** {
    <fields>;
    <methods>;
}

-keep class com.poseidon.** {
    <fields>;
    <methods>;
}

-keep interface  * extends okhttp3.** {
    <fields>;
    <methods>;
}

-keep class okhttp3.Callback {
    <methods>;
}

-keep,allowshrinking class com.envision.apim.poseidon.config.PConfig {
    <methods>;
}

-keep,allowshrinking class com.envision.apim.poseidon.core.Poseidon {
    public <methods>;
}

-keep,allowshrinking class com.envision.apim.poseidon.core.PoseidonListener {
    <methods>;
}
```





