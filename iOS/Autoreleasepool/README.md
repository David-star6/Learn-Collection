## 内存管理
- 内存的五大区域
栈区,堆区,BBS段,数据段和代码段；除栈区外内存管理由系统自行回收，OC对象存储在堆区，所以OC的内存管理主要是对”堆区中的OC对象”进行管理

- 内存泄露
指对象没有在该回收的时候被回收,而是一直驻留在内存中,直到程序结束的时候才被释放

- OC内存管理的方案
引用计数器：retain：+1；release：-1；
autorelease：自动释放池

## 内存管理
### MRC
- 自己生成的对象，自己持有
```
// 使用了alloc分配了内存，obj指向了对象，该对象本身引用计数为1,不需要retain 
    id obj = [[NSObject alloc] init]; 
    // 使用了new分配了内存,objc指向了对象，该对象本身引用计数为1，不需要retain 
    id obj = [NSObject new];
```

- 非自己生成的对象，自己也能持有
```
   // NSMutableArray通过类方法array产生了对象(并没有使用alloc、new、copy、mutableCopt来产生对象),因此该对象不属于obj自身产生的    // 因此，需要使用retain方法让对象计数器+1,从而obj可以持有该对象(尽管该对象不是他             产生的)
    id obj = [NSMutableArray array];
    [obj retain];
```

- 无法释放非自己持有的对象
```
 // 释放一个不属于自己的对象
    id obj = [NSMutableArray array]; 
     
 // obj没有进行retain操作而进行release操作，然后autoreleasePool也会对其进行一次release操作，导致奔溃。
  [obj release];
```
针对[NSMutableArray array]方法取得对象方法，自己并不会持有对象，在底层是这样实现的

```
+ (id)object {    //自己持有对象
    id obj = [[NSObject alloc]init];
     
    [obj autorelease];     
    //取得的对象存在，但自己不持有对象
    return obj;
}
```
使用了autorelease方法，将obj对象注册到autoreleasePool中，不会立即释放，当pool结束时，再自动调用release。


### ARC
```
/* newPerson方法中，方法调用者持有返回对象。因此在方法内部，除了allo使引用计数＋1外，没有其余的保留与释放操作，最后在调用方法后，对象需要增加一次释放操作。*/
+ (Person *)newPerson{
    Person *person = [[person alloc] init];
    return person;
}
/*createPerson方法中，方法调用者不持有返回对象，因此在return操作时，ARC会增加 [person autorelease] 方法。最后计数器平衡。*/
+ (Person *)createPerson{
    Person *person = [[person alloc] init];
    return person;
}
```

# @autoreleasepool





