## 前言
稍微学习了点有关py爬虫相关的知识点，在此先记录下，有人说起点网比较难爬，正好拿它先试下手吧。看了下网站，感觉搜索功能蛮不错的，正好可以通过关键字来获取到任意的小说内容，那就先整个搜索的接口，开整！<br>

## 环境配置&工具
编译器：Pycharm<br>
Python版本：Python 3.7.0<br>
Playwright版本：1.35.0<br>

## PlayWright
在这之前先了解一下这个PlayWright这个工具，功能和selenium差不多，也是一个自动化测试的工具，个人感觉比selenium好用一丢丢，可以支持当前所有主流的浏览器，支持的功能也比较强大，有很多完善的自动化控制的API，而且也已经开源了。<br>
支持两种编写模式：一种是异步的，一种是和selenium一样的同步模式，就是根据实际的需要选择不同的模式

## 详情页
最近的《庆余年》剧好像蛮火的，这里那先拿它搜索看下。唉，我们看下搜索的结果，跳转到一个html页面了。我们看到首页是这样的
![ ](https://github.com/huaisheng512/Retpile/blob/main/Pic/shouye.png)

先直接搜下庆余年，看下当前网页跳转的情况
![ ](https://github.com/huaisheng512/Retpile/blob/main/Pic/search-result.png)

可以看到当前是一个html的页面，我们直接进入到详情页，测试过几个小说，这里如果我们是通过搜索的话，就是直接<link>https://www.qidian.com/so/关键词.html</link>，这样的话是可以达到搜索结果的，这里我们可以先记录一下

我们先看一下这一页的数据好不好获取，嗯~好像完全没问题，这下我们可以直接通过url来进行跳转了，直接进入书籍详情，现在就是所有章节的内容获取了，我们可以看到也是比较清晰的

先了解一下PlayWright一些常见API大概用法：
playwright.selector(element): 生成一个指向元素的选择器。
playwright.inspect(selector): 检查“元素”面板中的选择器

现在直接看下章节页的详情内容，这里我们直接跑下代码，先获取一下标题看是否有啥问题
很好，现在标题获取到了，那内容的话应该也就没啥问题了
我们写下代码随便获取章节中一段的内容

额好像也能获取到，看似是没啥问题的，凭经验判断是通过上面的p[8]后面的的来进行段落的获取，那我们再来测试一下

怎么会每个都一样？这里我们得看下我们的代码，是通过page.evaluate里面然后通过document.querySelector获得到选择器，然后通过这个选择器找到属性，那我们回头看一下我们找的属性，换一种写法，直接通过query_selector来

![ ](https://github.com/huaisheng512/Retpile/blob/main/Pic/s6.png)
哦豁，好像还是有点报错，没关系我们先看一下报错信息，提示'NoneType' object has no attribute 'text_content'，很显然找不到属性，那我们就得先看一下我们找的选择器id是否正确了

再来一次，嗯~现在没啥问题了，现在就直接获取整章的内容了

OK，和网页上最后的一段对比一下，都没啥问题，现在我们应该算是写完这部分了，接下来就是整合，上面我们都看了每个URL大概是怎么生成的了，再大概梳理代码写一下<br>

```
item = 1
    while True:
        try:
            context_text = page.query_selector(
                "//*[@id='c-20236869']/p[" + str(item) + "]/span").text_content()
            item += 1
            print(context_text)
        except Exception as e:
            break
```

