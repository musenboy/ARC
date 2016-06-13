# ARC基本原理

[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](https://raw.githubusercontent.com/musenboy/ARC/master/LICENSE)&nbsp;

阅读苹果官方[文档](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)对ARC的介绍后总结。

## ARC概述：  

> ARC(automatic reference counting)，自动引用计数。代码中自动加入retain/release，
以前需要手动添加管理内存，现在自动引用计数器可在编译时自动添加完成。

## ARC新规则

- 不能调用dealloc，不能复写或者调用retain,release,retainCount,autorelease
- 禁止使用@selector(retain), @selector(release)。
- 可重写dealloc方法
- 不能调用[super dealloc]，编译器会报错
- 可以使用CFRetain, CFRelease.
- 不可以使用NSAllocateObject,NSDeallocateObject
- C结构中不能使用对象指针
- 不可以使用NSAutoreleasePool
- 弱(weak)引用不是扩展指向对象的生命周期，而是当对象没有强引用时自动转为nil。
- 没有强指针指向对象，立即释放对象。
- 指针超出作用域，回收内存。
- 指针指向别的对象，原来的内存被释放。
- 指针被置为nil，内存释放。

## property属性

- strong和retain同义，weak和assign类似，如果weak对象实例被释放，属性值被设置为nil而不是一个空指针（the property value is set to nil instead of remaining as a dangling pointer）。
- ARC下，strong是对象类型默认的。

## variable限定符

- 使用__strong，__weak，__unsafe_unretained，__autoreleasing
- __strong是默认的。
- 不需要保持引用对象活动着用__weak指定引用（__weak specifies a reference that does not keep the referenced object alive）。
- 不需要保持引用对象活动着和不设置为nil用__unsafe_unretained指定引用（__unsafe_unretained specifies a reference that does not keep the referenced object alive and is not set to nil when there are no strong references to the object）。
- __autoreleasing 是用来表示参数通过引用传递(id *)并返回自动释放（__autoreleasing is used to denote arguments that are passed by reference (id *) and are autoreleased on return）。
- 正确使用限定符生命变量格式：
`ClassName * qualifier variableName;`


## ARC使用新的声明管理自动释放池

- 在ARC下，不能直接使用NSAutoreleasePool类管理自动释放池。而是使用@autoreleasepool块。

## 使用编译器标志启用和禁用ARC

- 使用-fobjc-arc编译器标志启用ARC，可以对单个文件使用ARC。
- 使用-fno-objc-arc编译器标志禁用ARC，对单个文件也可以设置。
- ARC只支持Xcode4.2、OS X v10.6和IOS 4之后的版本。

## 注意事项

- 不能在C结构中使用ids强引用。

> 比如：`struct X { id x; float y; };`
因为x默认为强引用保留，编译不能安全的合成所需的所有的代码。
解决方法:
(1)使用Objective-C对象代替结构体。
(2)如果使用objective - c的对象不是最佳的，那就考虑使用void*。
(3)标志对象的引用为__unsafe_unretained。
比如：`struct x { NSString * __unsafe_unretained S; int X; }`

- 不能直接在id和void*之间cast
