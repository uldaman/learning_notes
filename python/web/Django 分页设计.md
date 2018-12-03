Django 自带分页 api, 然而并不推荐使用, 因为要真正在项目中使用它同样也要修改很多东西, 而且如果换了框架, 并不通用, 所以本篇日记, 提供了一个实现分页功能的思路, 具体实现日后自行摸索.

首先, 分页都是通过 get 不同地址来实现的:
![](http://oi62.tinypic.com/xbwg0y.jpg)

分发 url 时, 通过下面这种方法, 使 url 有默认页:

    url(r<span style="color: #800000">'</span><span style="color: #800000">^all/(?P<p>\d*)/$</span><span style="color: #800000">'</span><span style="color: #000000">, All),
    url(r</span><span style="color: #800000">'</span><span style="color: #800000">^all/$</span><span style="color: #800000">'</span>, All, {<span style="color: #800000">'</span><span style="color: #800000">p</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">1</span><span style="color: #800000">'</span>}),





再来看 All 函数 :




    <span style="color: #0000ff">from</span> django.utils.safestring <span style="color: #0000ff">import</span><span style="color: #000000"> mark_safe

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> All(request, p):
        currentPage </span>=<span style="color: #000000"> int(p)
        start </span>= 5 * (currentPage - 1<span style="color: #000000">)
        end </span>= 5 *<span style="color: #000000"> currentPage
        itemList </span>=<span style="color: #000000"> Asset.objects.all()[start : end]

        nMaxCount </span>=<span style="color: #000000"> Asset.objects.all().count()
        nPages </span>= (nMaxCount + 5 -1)// 5<span style="color: #000000">
        pageList </span>=<span style="color: #000000"> []


        </span><span style="color: #0000ff">if</span> currentPage > 1<span style="color: #000000">:
            pre_herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">上一页</a></span><span style="color: #800000">'</span> % (currentPage - 1<span style="color: #000000">)
            pageList.append(mark_safe(pre_herf))

        </span><span style="color: #0000ff">for</span> x <span style="color: #0000ff">in</span> range(1,nPages + 1<span style="color: #000000">):
            herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">%d</a></span><span style="color: #800000">'</span> %<span style="color: #000000"> (x, x)
            pageList.append(mark_safe(herf))

        </span><span style="color: #0000ff">if</span> currentPage <<span style="color: #000000"> nPages:
            next_herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">下一页</a></span><span style="color: #800000">'</span> % (currentPage + 1<span style="color: #000000">)
            pageList.append(mark_safe(next_herf))

        result </span>= render_to_response(<span style="color: #800000">'</span><span style="color: #800000">index.html</span><span style="color: #800000">'</span><span style="color: #000000">,
                                    {</span><span style="color: #800000">'</span><span style="color: #800000">data</span><span style="color: #800000">'</span><span style="color: #000000">:itemList,
                                     </span><span style="color: #800000">'</span><span style="color: #800000">count</span><span style="color: #800000">'</span><span style="color: #000000">:nMaxCount,
                                     </span><span style="color: #800000">'</span><span style="color: #800000">pages</span><span style="color: #800000">'</span><span style="color: #000000">:nPages,
                                     </span><span style="color: #800000">'</span><span style="color: #800000">pageList</span><span style="color: #800000">'</span><span style="color: #000000">:pageList})
        </span><span style="color: #0000ff">return</span> result




    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>你好<span style="color: #0000ff"></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">table </span><span style="color: #ff0000">border</span><span style="color: #0000ff">="1px"</span><span style="color: #0000ff">></span><span style="color: #000000">
                {% for item in data %}
                    </span><span style="color: #0000ff"><</span><span style="color: #800000">tr</span><span style="color: #0000ff">></span>
                        <span style="color: #0000ff"><</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>{{item.id}}<span style="color: #0000ff"></</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>
                        <span style="color: #0000ff"><</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>{{item.hostName}}<span style="color: #0000ff"></</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>
                    <span style="color: #0000ff"></</span><span style="color: #800000">tr</span><span style="color: #0000ff">></span><span style="color: #000000">
                {% endfor %}
            </span><span style="color: #0000ff"></</span><span style="color: #800000">table</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span><span style="color: #000000">
            总条数: {{count}}
        </span><span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span><span style="color: #000000">
            总页: {{pages}}
            </span><span style="color: #0000ff"><</span><span style="color: #800000">br</span><span style="color: #0000ff">/></span><span style="color: #000000">
            {% for item in pageList %}
                </span><span style="color: #0000ff"><</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>{{item}}<span style="color: #0000ff"></</span><span style="color: #800000">span</span><span style="color: #0000ff">></span><span style="color: #000000">
            {% endfor %}
        </span><span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>


All 函数开始, 把页码从 字符串 转化成 数字, 然后计算出每页显示的内容, 即 itemList;
然后在 HTML 中通过 for 循环模板输出 itemList;




然后获取总条目数, 并计算以每页显示 5 条内容为阈值的分页数, 即 nPages;




定义一个 list : pageList 用来存放每页的内容, 并且通过 **mark_safe() **把字符串转换成 HTML 格式(如果不使用这个函数, 那么最终将在浏览器上直接显示未经渲染的 HTML 代码).







* * *


接下来, 要解决分页过多的问题, 这个没有写代码了, 很简单, 就是逻辑的问题, 记下思路就好.




最终效果是显示 (当前页 - 5) + 当前页 + (当前页 + 5) 共 11 个页码.




tag1: 先判断 当前页 是否 < 11, 如果是, 那就直接显示 1 ~ 当前页 就好.




tag2: 如果 当前页 大于 11, 继续判断, 当前页 > 总页数 – 5 时, 则显示 (当前页 - 5) + 末尾页 即可.




tag3: 否则, 则显示 (当前页 - 5) + 当前页 + (当前页 + 5)
rot
