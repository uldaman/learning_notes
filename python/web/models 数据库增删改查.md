models.py 定义只是数据库的表、字段信息, 真实的向数据库中写数据的代码还是 views.py (业务逻辑中处理) 完成的.

objects 是 Django 实现的 MTV 中的 M, Django 中的模型类都有一个 objects 对象, 它是一个 Django 中定义的 QuerySet 类型的对象, 它包含了模型对象的实例.

**增**

    <span style="color: #008000">#</span><span style="color: #008000"> models.py</span>
    <span style="color: #0000ff">class</span><span style="color: #000000"> Asset(models.Model):
        hostName </span>= models.CharField(max_length=50<span style="color: #000000">)
        create_data </span>= models.DateTimeField(auto_now_add=<span style="color: #000000">True)
        update_data </span>= models.DateTimeField(auto_now=True)




    <span style="color: #008000">#</span><span style="color: #008000"> views.py</span><span style="color: #008000">
    #</span><span style="color: #008000"> 添加数据</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> Add(request, name):
        Asset.objects.create(hostName</span>=<span style="color: #000000">name)
        </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">OK</span><span style="color: #800000">'</span>)





**删除
**


    <span style="color: #008000">#</span><span style="color: #008000"> 删除</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> Delete(request, id):
        Asset.objects.get(id</span>=<span style="color: #000000">id).delete()
        </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">OK</span><span style="color: #800000">'</span>)


**改**



    <span style="color: #0000ff">from</span> views <span style="color: #0000ff">import</span><span style="color: #000000"> Page, Add, Delete, Update

    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*/)</span><span style="color: #800000">'</span>, Page, {<span style="color: #800000">"</span><span style="color: #800000">index</span><span style="color: #800000">"</span> : 1<span style="color: #000000">}),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^add/(?P<name>\d*/)</span><span style="color: #800000">'</span><span style="color: #000000">, Add),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^dele/(?P<id>\d*)/</span><span style="color: #800000">'</span><span style="color: #000000">, Delete),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^update/(?P<id>\d*)/(?P<hostName>\d*)/</span><span style="color: #800000">'</span><span style="color: #000000">, Update),
    ]</span>




    <span style="color: #008000">#</span><span style="color: #008000"> 更新数据</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> Update(request, id, hostName):
        </span><span style="color: #800000">'''</span><span style="color: #800000">
        # 单个更新
        obj = Asset.objects.get(id=id)
        obj.hostName = hostName
        obj.save()
        </span><span style="color: #800000">'''</span>
        <span style="color: #008000">#</span><span style="color: #008000"> 批量修改, 把大于 id 的所有项都修改</span>
        objList = Asset.objects.filter(id__gt=id).update(hostName=<span style="color: #000000">hostName)
        </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">OK</span><span style="color: #800000">'</span>)





具体的"操作符", 如 __gt, 自行搜索吧..




**其它常用操作:**



<table cellpadding="2" width="800" border="0" cellspacing="0" class="table" >
<tbody >
<tr >

<td width="798" valign="top" >


排序:
order_by("name")
order_by("-name")
返回第 n-m 条:
第 n 条[0]
前两条[0:2]
指定映射: values
数量: count()
聚合:
from django.db.models import Min,Max,Sum
objects.all().aggregate(Max('guest_id'))
原始SQL:
cursor = connection.cursor()
cursor.execute('''SELECT DISTINCT first_name ROM people_person WHERE last_name = %s""", ['Lennon'])
row = cursor.fetchone()

</td></tr></tbody></table>
??*
