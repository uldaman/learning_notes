环境搭建好, Device 也取得后就开始初始化 CEGUI 了.

取 Device 时要注意下, 有的游戏不止创建了一个 Device, 有可能是多个, 所以 HOOK 的时候要先试试用哪个 Device 初始化 CEGUI 可以成功, 例如下面的代码:

```c++
HRESULT APIENTRY hkIDirect3D9::CreateDevice(UINT Adapter, D3DDEVTYPE DeviceType, HWND hFocusWindow
    , DWORD BehaviorFlags, D3DPRESENT_PARAMETERS *pPresentationParameters
    , IDirect3DDevice9 **ppReturnedDeviceInterface) {
    HRESULT hRet = m_pD3Dint->CreateDevice(Adapter, DeviceType, hFocusWindow, BehaviorFlags
                        , pPresentationParameters, ppReturnedDeviceInterface);
    static int nCount = 0;
    if (SUCCEEDED(hRet)) {
        theApp.m_pDevice = new hkIDirect3DDevice9(ppReturnedDeviceInterface, pPresentationParameters, this);
        martin->add_log("Hooked Direct3D9 device: 0x%x -> 0x%x"
            , ((hkIDirect3DDevice9*)theApp.m_pDevice)->m_pD3Ddev, theApp.m_pDevice);
        if (++nCount == 3) {
            theApp.initGui();
            if (theApp.m_OrgWndProc == 0) {
                theApp.m_hGWnd = hFocusWindow;
                theApp.m_OrgWndProc = ::GetWindowLong(theApp.m_hGWnd, GWL_WNDPROC);
                if (theApp.m_OrgWndProc) {
                    ::SetWindowLong(theApp.m_hGWnd, GWL_WNDPROC, (LONG)&FilterWndProc);
                } else {
                    martin->add_log("GetWindowLong() Failed.");
                    ::ExitProcess(-1);
                }
            }
        }
    }

    return hRet;
}
```

可以看到, 这里加了个 nCount 计数, 当游戏第三次 CreateDevice() 时才初始化 CEGUI;
后面的代码等下再说, 先看看如何初始化 CEGUI, 也就是 initGui() 函数.

```c+++
CEGUI::Direct3D9Renderer::bootstrapSystem(theApp.m_pDevice);
//-----------------------------------------------------------
//    此行等同于下面两行, 即初始化 Direct3D9Renderer
//    Direct3D9Renderer *myRenderer = &CEGUI::Direct3D9Renderer::create(pDevice);
//    System::create(*myRenderer);
//-----------------------------------------------------------

/////////////////////////////////////////////////////////////////////////
//    设置默认资源路径
/////////////////////////////////////////////////////////////////////////
CEGUI::DefaultResourceProvider* rp = static_cast<CEGUI::DefaultResourceProvider*>
                    (CEGUI::System::getSingleton().getResourceProvider());
rp->setResourceGroupDirectory("schemes", "E:\\cegui-0.8.4\\datafiles\\schemes");
rp->setResourceGroupDirectory("imagesets", "E:\\cegui-0.8.4\\datafiles\\imagesets");
rp->setResourceGroupDirectory("fonts", "E:\\cegui-0.8.4\\datafiles\\fonts");
rp->setResourceGroupDirectory("layouts", "E:\\cegui-0.8.4\\datafiles\\layouts");
rp->setResourceGroupDirectory("looknfeels", "E:\\cegui-0.8.4\\datafiles\\looknfeel");
rp->setResourceGroupDirectory("lua_scripts", "E:\\cegui-0.8.4\\datafiles\\lua_scripts");
rp->setResourceGroupDirectory("schemas", "E:\\cegui-0.8.4\\datafiles\\xml_schemas");
rp->setResourceGroupDirectory("animations", "E:\\cegui-0.8.4\\datafiles\\animations");
martin->add_log("设置默认资源路径");

///////////////////////////////////////////////////////////////////////////
////    设置使用的缺省资源
///////////////////////////////////////////////////////////////////////////
CEGUI::ImageManager::setImagesetDefaultResourceGroup("imagesets");
CEGUI::Font::setDefaultResourceGroup("fonts");
CEGUI::Scheme::setDefaultResourceGroup("schemes");
CEGUI::WidgetLookManager::setDefaultResourceGroup("looknfeels");
CEGUI::WindowManager::setDefaultResourceGroup("layouts");
CEGUI::ScriptModule::setDefaultResourceGroup("lua_scripts");
CEGUI::AnimationManager::setDefaultResourceGroup("animations");
CEGUI::XMLParser* parser = CEGUI::System::getSingleton().getXMLParser();
if (parser->isPropertyPresent("SchemaDefaultResourceGroup")) {
parser->setProperty("SchemaDefaultResourceGroup", "schemas");
}
martin->add_log("设置使用的缺省资源");

///////////////////////////////////////////////////////////////////////
//    加载方案
///////////////////////////////////////////////////////////////////////
// 加载主题
CEGUI::SchemeManager::getSingleton().createFromFile("TaharezLook.scheme");

// 设置字体
CEGUI::Font& defaultFont = CEGUI::FontManager::getSingleton().createFromFile("DejaVuSans-12.font");
CEGUI::System::getSingleton().getDefaultGUIContext().setDefaultFont(&defaultFont);

// 设置鼠标, hook 游戏 d3d 时不用设置, 直接使用游戏的鼠标模型
//System::getSingleton().getDefaultGUIContext().getMouseCursor().setDefaultImage("TaharezLook/MouseArrow");

// 得到窗口管理器
CEGUI::WindowManager& winMgr = CEGUI::WindowManager::getSingleton();
martin->add_log("加载方案");

/////////////////////////////////////////////////////////////////////////
//    开始绘图
/////////////////////////////////////////////////////////////////////////
// 得到根窗口, 即画布
m_root = static_cast<CEGUI::DefaultWindow*>(winMgr.loadLayoutFromFile("monster.layout"));
CEGUI::System::getSingleton().getDefaultGUIContext().setRootWindow(m_root);

// 得到主窗口
m_mainWnd = static_cast<CEGUI::FrameWindow*>(m_root->getChild("Demo"));
m_mainWnd->subscribeEvent(CEGUI::FrameWindow::EventCloseClicked,
CEGUI::Event::Subscriber(&CGame::onEventHome, this));


martin->add_log("\n---------------------\n全部加载完毕...\n---------------------\n");

m_root->setVisible(false);
martin->ModuleHide(GetModuleHandle("D3D9_CEGUI.dll"));
m_bInit = true;
```

代码也没什么好说的, 标准的初始化 CEGUI 代码, 适用版本 8.0+ .

接下来就是要游戏的画面上绘画我们刚初始化好的界面了, 我们只需要 HOOK IDevice 的 Present 虚函数即可:

```c+++
HRESULT APIENTRY hkIDirect3DDevice9::Present(CONST RECT *pSourceRect, CONST RECT *pDestRect
                , HWND hDestWindowOverride, CONST RGNDATA *pDirtyRegion) {
    if (theApp.m_bInit) {
        CEGUI::System::getSingleton().renderAllGUIContexts();
    }

    return m_pD3Ddev->Present(pSourceRect, pDestRect, hDestWindowOverride, pDirtyRegion);
}
```

此时, CEGUI 已经初始化完毕了, 当然前提是你还需要准备 .layout 文件, 启动游戏并注入我们的 DLL 后, 暂时还是看不到我们的画面的, 因为上面的代码中倒数第三行 m_root->setVisible(false); 将窗体隐藏了, 为此, 我们可以 HOOK 游戏的窗口过程(HOOK 的代码就在上面 CreateDevice() 中), 然后给一个快捷键, 例如 HOME, 按下后, 就显示窗体.

```c++
case WM_KEYDOWN:
switch (wParam) {
case VK_HOME:
    if (m_bInit) {
    if (m_root->isVisible()) {
        m_root->setVisible(false);
    } else {
        m_root->setVisible(true);
    }
    }
    break;
}
break;
}
```

但是我们 HOOK 窗口过程可并不只是因为这个需求;

现在显示出来 CEGUI 窗体后, 你会发现, 窗体是静态的, 点击它, 拖动它, 完全不响应, 这是因为现在并没有把鼠标, 键盘等消息传递给 CEGUI, 也就是说, CEGUI 现在并不知道你点击了什么或者你拖动什么等等消息.
我们 HOOK 游戏的窗口过程就是为了把这些消息传递给 CEGUI, 好让 CEGUI 去响应.

```c++
if (m_bInit) {
    switch (message) {
    case WM_MOUSEMOVE:
        //ShowCursor(true);
        if (CEGUI::System::getSingleton().getDefaultGUIContext().injectMousePosition((float)(LOWORD(lparam))
    , (float)(HIWORD(lparam)))) {
        return 0;
        }
        break;

    case WM_LBUTTONDOWN:
        if (CEGUI::System::getSingleton().getDefaultGUIContext().injectMouseButtonDown(CEGUI::LeftButton)) {
        return 0;
        }
        break;
    }
}
```

上面的代码传递了 鼠标移动 + 左键按下 这两个消息, 现在你的窗体上 拖动窗口, 点击按钮 这两个动作是可以被支持的了.
