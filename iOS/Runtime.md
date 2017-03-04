Runtime4-706[传送](https://opensource.apple.com/tarballs/objc4/)

**:warning:本文主要参考博客[传送](http://zxfcumtcs.github.io/2014/07/08/Objective-c-Object-Model/)**
1. 首先来看NSObject，很好只有一个暴露属性的属性isa(这其实是is a point 的简写)
```
@interface NSObject <NSObject> {
    Class isa  OBJC_ISA_AVAILABILITY;
}
```

2. 让我们继续探究这个Class类型，好气哦，又是一句跳转~
`typedef struct objc_class *Class;`

3. 那么这个objc_class是什么呢，从外部接口来看接口提只有包含一个只想自己指针。。。干啥呢？
```
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```

4. 在源码中objc-runtime-new.h中看到他继承制objc_object，包含一个只想父类的指针等属性
```
struct objc_class : objc_object {
    // Class ISA;
    Class superclass;
    cache_t cache;             // formerly cache pointer and vtable
    class_data_bits_t bits;    // class_rw_t * plus custom rr/alloc flags
    ...
}
```

5. 我的isa呢，终于出现在objc_object中了
```
struct objc_object {
private:
    isa_t isa;

public:
    ...
}
```

6. 我的成员变量和方法,在第4点中的class_data_bits_b中,可以看到方法列表，属性列表，协议列表，其中的一个常量属性class_ro_t中则包含了，实例的起始偏移量，实例的大小，成员变量的列表，所以是不能想其中增加成员变量的
```
struct class_data_bits_t {
    ...
    public:

    class_rw_t* data() {
        return (class_rw_t *)(bits & FAST_DATA_MASK);
    }
    ...
}

struct class_rw_t {
    // Be warned that Symbolication knows the layout of this structure.
    uint32_t flags;
    uint32_t version;

    const class_ro_t *ro;

    method_array_t methods;
    property_array_t properties;
    protocol_array_t protocols;
    ...
}

struct class_ro_t {
    uint32_t flags;
    uint32_t instanceStart;
    uint32_t instanceSize;
#ifdef __LP64__
    uint32_t reserved;
#endif

    const uint8_t * ivarLayout;
    
    const char * name;
    method_list_t * baseMethodList;
    protocol_list_t * baseProtocols;
    const ivar_list_t * ivars;

    const uint8_t * weakIvarLayout;
    property_list_t *baseProperties;

    method_list_t *baseMethods() const {
        return baseMethodList;
    }
};

```

关于关于对象的继承连
```
    bool isMetaClass() {
        assert(this);
        assert(isRealized());
        return data()->ro->flags & RO_META;
    }

    // NOT identical to this->ISA when this is a metaclass
    Class getMeta() {
        if (isMetaClass()) return (Class)this;
        else return this->ISA();
    }

    bool isRootClass() {
        return superclass == nil;
    }
    bool isRootMetaclass() {
        return ISA() == (Class)this;
    }
```

类的创建过程
```
Class objc_allocateClassPair(Class superclass, const char *name, 
                             size_t extraBytes)
```

```                             
static void objc_initializeClassPair_internal(Class superclass, const char *name, Class cls, Class meta)
```

方法调用查找过程
```
IMP lookUpImpOrForward(Class cls, SEL sel, id inst, 
                       bool initialize, bool cache, bool resolver)
```
1. 现在cache中找方法
2. 查看class是否实例化，没有则实例化
3. 查看class是否初始化，没有则初始化
4. 在cache中查找，
5. 在self方法列表中查找
6. 在super方法列表中查找
7. 没有找到在尝试一次
8. 还是没找到，_objc_msgForward_impcache