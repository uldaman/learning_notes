使用 CEGUI 的第一步是要创建一个 Renderer 对象(渲染对象) 参考: [cegui渲染入门](http://blog.csdn.net/bluekane/article/details/3738376)

最新的 CEGUI 支持的渲染组件有: Direct3D9、Direct3D10、Direct3D11、OpenGl、Ogre3d 等.

一般的游戏都使用的是 Direct3D 系列, 而要创建一个 D3D Renderer 对象就需要提供一个参数, 即 Direct3DDevice (D3D 设备句柄), 这个参数由 D3D API CreateDevice() 得到;
但是, 如果要想在游戏中使用 CEGUI 自绘界面, 就必须使用游戏本身创建的 Direct3DDevice, 而不能直接自己调用 CreateDevice();
那么要想获得游戏的 Direct3DDevice, 就需要 HOOK D3D, 在游戏调用 CreateDevice() 时取得游戏创建的 Direct3DDevice.

HOOK D3D 的源码网上流传的有很多, 这里也收集了两份:

- 注入法: [http://yunpan.cn/cwmYIwqDQYGzX](http://yunpan.cn/cwmYIwqDQYGzX)  访问密码 9694
- 劫持法: [http://yunpan.cn/cw3V3zaqMkBdF](http://yunpan.cn/cw3V3zaqMkBdF)  访问密码 c178

这两种方法在最终 HOOK 上采取的方式都一致的, 都是利用了 C++ 继承的特性, 区别只在于 dll 的注入方式.

这里以 d3d9 为例简单说明一下:
首先, 已经知道要 HOOK IDirect3D9::CreateDevice() 这个函数才能取得游戏创建的 Direct3DDevice, 而 CreateDevice() 是 IDirect3D9 接口的虚函数, 这一点我们阅读 D3D 的头文件 d3d9.h 就可以得知(见下面代码摘要), 实际上, d3d 系列的所有函数都是虚函数.

```c++
#define DECLARE_INTERFACE_(iface, baseiface) interface DECLSPEC_NOVTABLE iface : public baseiface

#define STDMETHOD(method) virtual COM_DECLSPEC_NOTHROW HRESULT STDMETHODCALLTYPE method
#define STDMETHOD_(type,method) virtual COM_DECLSPEC_NOTHROW type STDMETHODCALLTYPE method

DECLARE_INTERFACE_(IDirect3D9, IUnknown) {
    /*** IUnknown methods ***/
    STDMETHOD(QueryInterface)(THIS_ REFIID riid, void** ppvObj) PURE;
   ......
    /*** IDirect3D9 methods ***/
    STDMETHOD(RegisterSoftwareDevice)(THIS_ void* pInitializeFunction) PURE;
   ......
};
```

游戏初始化时, 会通过 Direct3DCreate9() 创建一个 IDirect3D9 指针, 然后用这个 IDirect3D9 指针调用它的 CreateDevice() 函数;

此时, 如果我们创建一个子类继承 IDirect3D9 类, 然后在游戏初始化前 HOOK Direct3DCreate9() 函数, 在 Direct3DCreate9()  内部用我们刚创建的子类创建一个指针, 并把这个子类的指针返回给游戏;
根据 C++ 继承特性, 这个指针是子类的, 所以当游戏用这个指针调用 d3d 系列的函数时, 如果我们重写了父类的对应虚函数, 那么实际上它调用的就会是我们子类重写后的函数了.

因此, 我们想要 HOOK IDirect3D9::CreateDevice() 取 Direct3DDevice, 只需要在子类中重写 IDirect3D9 的 CreateDevice() 就可以了, 具体代码见上面分享, 个人推荐用劫持的方式, 相对安全一点.

* * *

除了利用 C++ 继承特性外, 还有一种方法是利用 C++ 虚函数的特性直接 HOOK IDirect3D9 的虚表, 所以有必要先了解一下虚函数的机制: 当类中定义有虚函数时, 编译器会将该类中所有虚函数的地址保存在一张表中, 这张表被称为虚函数地址表(虚表), 同时编译器会在类中添加一个隐藏的数据成员, 即虚表指针, 它指向该类的虚表,  这个隐藏的数据成员在类的开始处, 即类的头 4 字节.

知道了上面这些, 那 HOOK IDirect3D9 的虚表也就不在话下了, 第一步仍然是 HOOK Direct3DCreate9() 取游戏创建的 IDirect3D9 指针, 有了这个指针, 就能从它的头 4 字节处取出虚表, 再找到 CreateDevice() 在虚表中的位置, 替换成我们自己的 CreateDevice(), 然后 OOXX . . .

参考代码:

```c++
class Test {
public:
    virtual void Hello() {
        AfxMessageBox(nullptr);
    }
};

void __stdcall New_Hello() {
    AfxMessageBox(TEXT("Hello"));
}

void BnClickedButton() {
    Test* pTest = new Test;
    PVOID p = ((PVOID*)pTest)[0]; // 取虚表
    DWORD dwOldPro;
    VirtualProtect(p, 4, PAGE_EXECUTE_READWRITE, &dwOldPro); // 修改内存保护属性
    ((PVOID*)p)[0] = New_Hello; // 改函数表函数
    VirtualProtect(p, 4, dwOldPro, &dwOldPro); // 恢复内存保护属性
    pTest->Hello();
    delete pTest;
}
```

最后共享一份 d3d 虚函数的索引: [http://yunpan.cn/cw3kuPtUp2xHu](http://yunpan.cn/cw3kuPtUp2xHu)  访问密码 bc7a
