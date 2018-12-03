原理就是找到 d3d9.dll 的入口, 搜索特征码, 找到 Vtalbe(虚函数表), 查表找到需要 hook 的函数的地址, 然后hook, 這樣就不需要在遊戲啟動時劫持 d3d9.dll 了;

這裡上傳一份 HOOK Present 的例子, 需要 Hook 其它函数的可以查一下其在虛表中的位置就可以了.

Hook_Present.zip: [http://yunpan.cn/cQcFqJeFapJue](http://yunpan.cn/cQcFqJeFapJue)  访问密码 989e

D3D9虚函数表: [http://yunpan.cn/cw3kuPtUp2xHu](http://yunpan.cn/cw3kuPtUp2xHu)  访问密码 bc7a
