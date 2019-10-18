---
title: "Markdown语法及常见用法举例"
date: 2019-05-10T21:18:52+08:00
categories: 2019-05

tags: [markdown]

author: sgl
---

## 缩进{#13}

1. 标题（Headers）

    >Markdown 标题支持两种形式：

    1.1 用1~6个#标记

       > 在标题开头加上1~6个#，依次代表一级标题、二级标题....六级标题

        # 一级标题
        ## 二级标题
        ### 三级标题
        ##### 四级标题
        ###### 五级标题
        ###### 六级标题

    # 一级标题
    ## 二级标题
    ### 三级标题
    ##### 四级标题
    ###### 五级标题
    ###### 六级标题
    1.2 用=和-标记

    >在标题底下 加上任意个=代表一级标题，-代表二级标题

        一级标题
        ======
        
        二级标题
        ----------

    一级标题
    ======
    
    二级标题
----------
2. 列表（Lists）

    > Markdown 支持有序列表和无序列表。列表不光可以单独使用，也可以使用其他的 Markdown 语法，包括标题、引用、代码区块等

    2.1 无序列表（Unordered Lists）

    > 无序列表使用-、+和*作为列表标记
    
        -  Red
        - Green
        - Blue
        
        * Red
        * Green
        *  Blue
        
        +  Red
        + Green
        + Blue

    -  Red
    - Green
    - Blue
    
    * Red
    * Green
    *  Blue
    
    +  Red
    + Green
    + Blue

    2.2 有序列表（Ordered Lists）

    >有序列表则使用数字加英文句点.来表示,注意点之后有个空格：

    
        1. Red
        2. Green
        3. Blue

    1. Red
    2. Green
    3. Blue    
    
    2.3 列表里的代码块
    
    + 示例：
            
        ```java
        protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_main);
        } 
        ```
    
3. 引用（Reference）

    >引用以>来表示，引用中支持多级引用、标题、列表、代码块、分割线等常规语法。

    3.1 常见的引用写法：    

        > 这是一段引用    //在`>`后面有 1 个空格
        > 
        >     这是引用的代码块形式    //在`>`后面有 5 个空格
    
    > 这是一段引用    //在`>`后面有 1 个空格
    > 
    >     这是引用的代码块形式    //在`>`后面有 5 个空格

        > 代码例子：
        >   
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
            }
    
    > 代码例子：
    >   
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }            
    
    3.2分级引用

        > 一级引用
        > > 二级引用
        > > > 三级引用
    
        > #### 这是一个四级标题
        > 
        > 1. 这是第一行列表项
        > 2. 这是第二行列表项

    > 一级引用
    
    >> 二级引用
    
    >>> 三级引用

    
    > #### 这是一个四级标题
    > 
    > 1. 这是第一行列表项
    > 2. 这是第二行列表项
    
    >建立多级列表时，每个子级别都要比上个级别前面多打2个以上空格。
     在 「 * 」 前加两个空格键或者一个 tab 键就可以产生一个子列表。
     有序列表： 在文本前加 「字母.」 或 「数字.」 即可生成一个有序列表。
     注意，当你第一个序号使用什么作为标记的，那么同级别的列表就会自动使用其作为标记。
 
4. 文字样式（Text Styling）

    4.1. 基本语法

    >三个*或-代表斜粗，两个*或-代表加粗，一个*或-代表斜体，~~代表删除。
    
        **加粗文本** 或者 __加粗文本__
        
        *斜体文本*  或者　_斜体文本_
        
        ***斜粗体*** 或者 ___斜粗文本___
        
        ~~删除文本~~                
         
        `底纹`
    
    **加粗文本** 或者 __加粗文本__
    
    *斜体文本*  或者　_斜体文本_
    
    ***斜粗体*** 或者 ___斜粗文本___
    
    ~~删除文本~~
     
    `底纹`             
    
    4.2 非基本语法
    
    字体上标（superscript）和下标（subscript ）
    
    4.2.1 简书中，字体上、下标的语法为：
    
        这是<sup>上标文字</sup>
        这是<sub>下标文字</sub>
    
    这是<sup>上标文字</sup>
    
    这是<sub>下标文字</sub>
    
    4.2.2 Typora 中，字体上、下标的语法为：
    
        这是^上标文字^
        这是~下标文字~
    
    这是^上标文字^
    这是~下标文字~    

5. 图片与链接（Images & Links）

    >图片与链接的语法很像，区别在一个 ! 号。
     注：Markdown 不能设置图片大小，如果必须设置则应使用HTML标记 <img>
     
        图片：![]() ![图片描述(可忽略)](链接的地址)
        
        链接：[]() [链接描述](链接的地址)
        
        This is [an example](http://example.com/ "Title") inline link.
        [This link](http://example.net/) has no title attribute.
    
    图片：![]() ![图片描述(可忽略)](http://mouapp.com/Mou_128.png)
    
    链接：[]() [链接描述](链接的地址)
    
    This is [an example](http://example.com/ "Title") inline link.
    [This link](http://example.net/) has no title attribute.
    
    >链接又分为行内式、参考式和 自动链接： 
    >>内联（Inline）：以中括号[]标记显示的链接文本，后面紧跟用小括号()包围的链接。 如果链接 Title 属性，则在链接中使用空格加"Title 属性"，即Title。    
    
        这是行内式链接：[Mou](http://25.io/mou/)。
        
        这是参考式链接：[Mou][url]，其中url为链接标记，可置于文中任意位置。
        
        [url]: 25.io/mou/
    
    这是行内式链接：[Mou](http://25.io/mou/)。
    
    这是参考式链接：[Mou][url]，其中url为链接标记，可置于文中任意位置。
    
[url]: https://shengulong.github.io/blog/
    
    >链接标记格式为：[链接标记文本]: 链接地址 链接title(可忽略)
    
        这是自动链接：直接使用`<>`括起来<http://25.io/mou/>
    
    这是自动链接：直接使用`<>`括起来<http://25.io/mou/>
    
        这是图片：![][Mou icon]
        
        [Mou icon]: http://mouapp.com/Mou_128.png
        
    这是图片：![][Mou icon]
    
[Mou icon]: http://mouapp.com/Mou_128.png         
    
        添加图片的使用方法基本上和链接类似，只是在中括号前加叹号!，即![Image Title](URL "Image Title")。
        

    > 图片格式控制
    
        居中及大小控制
        方法一：效果不是太好
        ![](/img/blog/2019/IO Model-figure_6.1.png#pic_center=100x100)
        <p align="center">图1</p>
        方法二：效果可以
        <div align="center">
            <img src="/img/blog/2019/IO Model-figure_6.1.png" height="100" width="100" >
            <p align="center">图1</p>
        </div>

![](/img/blog/2019/IO Model-figure_6.1.png#pic_center=100x100)

<p align="center">图1</p>

<div align="center">
    <img src="/img/blog/2019/IO Model-figure_6.1.png" height="100" width="100" >
    <p align="center">图1</p>
</div>        
        
    
6. 代码块引用（Fenced Code Blocks）

    >代码分为行内代码和代码块。

    6.1 行内代码
    
    >行内代码使用 代码 标识，可嵌入文字中
     需要引用代码时，如果引用的语句只有一段，不分行，可以用 ` 将语句包起来。
     
        `Hello world`
    
    `Hello world`

    6.2 代码框，缩进 4 个空格或是 1 个制表符
    
    > 两对「 」包裹,如果引用的语句为多行，可以将 置于这段代码的首行和末行。代码前加四个空格键或代码前加一个 tab 键
          
        ```
        Code Line 1
        Code Line 2
        Code Line 3
        ​```
    
    ```
    Code Line 1
    Code Line 2
    Code Line 3
    ```
    
    
    代码语法高亮在后面加上空格和语言名称即可
    
        ```语言      
        这里是代码
        ​```
        
    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    ```
    6.3 代码块里面包含html代码在代码区块里面， & 、 < 和 > 会自动转成 HTML 实体，这样的方式让你非常容易使用 Markdown 插入范例用的 HTML 原始码，只需要复制贴上，剩下的 Markdown 都会帮你处理。
    
7. 表格（Tables）

    >表格对齐格式
    
    >居左：:----
     
    >居中：:----:或-----
    
    >居右：----:
    
    >例子：
     
        |标题|标题|标题|
        |:---|:---:|---:|
        |居左文本|居中文本|居右文本|
    
    |标题|标题|标题|
    |:---|:---:|---:|
    |居左文本|居中文本|居右文本|

8. 分隔线

    在一行中用三个以上的、-、_来建立一个分隔线，行内不能有其他东西。也可以在符号间插入空格。
    在一行中使用三个或三个以上的、-或_可以添加分隔线，中间插入空格，但是不能有其他字符。
    
        ***
        ---
        _____
        
        * * *
        
    ***
    ---
    _____
    
    * * *
    
    >效果均为一条分割线

9. 脚注|注解（Footnotes）

    9.1 使用[^]来定义脚注：
    
        这是一个脚注的例子[^1]
        [^1]: 这里是脚注  ---这行代码必必须顶格对齐
    
    这是一个脚注的例子[^1]
    
    9.2 注释
        
        注释是给自己看的，预览时也不会出现，当然发布出去别人也不会看见。
        
            <!--注释-->
    
[^1]: 这里是脚注
        
10. 常用弥补Markdown的Html标签

    10.1 字体
    
    > 字体颜色参考<http://www.w3school.com.cn/tags/html_ref_colornames.asp>，还可以查看某个颜色背景下其他颜色的表现
    
        <font face="微软雅黑" color="red" size="6">字体及字体颜色和大小</font>
        
        <font color="#0000ff">字体颜色</font>
    
    <font face="微软雅黑" color="red" size="6">字体及字体颜色和大小</font>
    
    <font color="#0000ff">字体颜色</font>
    
    10.2 换行
    
        使用html标签`<br/>`<br/>换行
        
    使用html标签`<br/>`<br/>换行
    
    10.3 文本对齐方式
    
        <p align="left">居左文本</p>
        <p align="center">居中文本</p>
        <p align="right">居右文本</p>
    
    <p align="left">居左文本</p>
    <p align="center">居中文本</p>
    <p align="right">居右文本</p>

    10.4 下划线
    
        <u>下划线文本</u>
    
    <u>下划线文本</u>

11. 任务列表
    
        - [ ] [links](), **formatting**, and ~~tags~~ supported
        - [x] list syntax required (any unordered or ordered list supported)
        - [ ] this is a complete item
        - [x] this is an incomplete item
        
    - [ ] [links](), **formatting**, and ~~tags~~ supported
    - [x] list syntax required (any unordered or ordered list supported)
    - [ ] this is a complete item
    - [x] this is an incomplete item

12. 转义字符（Backslash Escapes）

    >Markdown 可以利用反斜杠\来插入一些在语法中有其它意义的符号，例如：如果想要用星号加在文字旁边的方式来做出强调效果，可以在星号的前面加上反斜杠：
    
        \*literal asterisks\*
    
    \*literal asterisks\*
            
13. 缩进

            信仰          正常没有缩进的
            
            &nbsp;信仰    缩进1/4中文
            
            &#160;信仰    缩进1/4中文
            
            &ensp;信仰   缩进半个中文，1个字符
            
            &#8194;信仰  缩进半个中文，1个字符
            
            &emsp;信仰   缩进1中文，2个字符
            
            &#8195;信仰  缩进1中文，2个字符
    
     信仰
    
     &nbsp;信仰
    
     &#160;信仰
    
     &ensp;信仰
    
     &#8194;信仰
    
     &emsp;信仰
    
     &#8195;信仰


14. [特殊字符表](https://unicode-table.com/cn/#geometric-shapes),举例：
    
            &#10084; 
            &#10003;
            &#9728;
            &#9733;
            &#9730;
            &#9775;
            &#9762;
            &#9742;
            &#9734;
            &#10052;
            &#9835;
    
    &#10084;
    
    &#10003;
    
    &#9728;
    
    &#9733;
    
    &#9730;
    
    &#9775;
    
    &#9762;
    
    &#9742;
    
    &#9734;
    
    &#10052;
    
    &#9835;


15. 锚点

        ## 缩进{#13} 
        跳转到[缩进](#13)

    跳转到[缩进](#13)
    
    <table><tr><td bgcolor=orange>背景色是：orange</td></tr></table>
    
    
    
16. 时序图Sequence diagrams
    
        <div class="mermaid">
            sequenceDiagram
                Alice->>John: Hello John, how are you?
                John-->>Alice: Great!
        </div>
    
    
    <div class="mermaid">
    sequenceDiagram
        Alice->>John: Hello John, how are you?
        John-->>Alice: Great!
    </div>    

17. 插入公式***Latex***

    参考这几篇文章，后续用的时候再添加
    1. [使用Pandoc和KaTeX为HUGO添加LaTeX支持](https://lowentropy.me/flight-rules/20181130-%E4%BD%BF%E7%94%A8pandoc%E5%92%8Ckatex%E4%B8%BAhugo%E6%B7%BB%E5%8A%A0latex%E6%94%AF%E6%8C%81/)
    2. [MathJax with Hugo](https://gohugo.io/content-management/formats/) 
   
    

18. 插入甘特图Gant diagrams

        <div class="mermaid">
                gantt
                    title A Gantt Diagram
                    dateFormat  YYYY-MM-DD
                    section Section
                    A task           :a1, 2014-01-01, 30d
                    Another task     :after a1  , 20d
                    section Another
                    Task in sec      :2014-01-12  , 12d
                    another task      : 24d
        </div>

    <div class="mermaid">
        gantt
            title A Gantt Diagram
            dateFormat  YYYY-MM-DD
            section Section
            A task           :a1, 2014-01-01, 30d
            Another task     :after a1  , 20d
            section Another
            Task in sec      :2014-01-12  , 12d
            another task      : 24d
    </div>

19. 插入流程图Flowcharts  

    >hugo加入[mermaid](https://mermaidjs.github.io/)作图,[github](https://github.com/knsv/mermaid)
    
        vi ./themes/hugo-ivy/layouts/partials/footer.html 
        添加下面代码
        <!-- mermaid JS -->
        <script src="https://mermaidjs.github.io/scripts/mermaid.min.js"></script>
        <script>
            mermaid.initialize({ startOnLoad: true });
        </script>
        
        源码
        <div class="mermaid">
        graph LR
            A --- B
            B-->C[fa:fa-ban forbidden]
            B-->D(fa:fa-spinner);
        </div>
    
    效果

    <div class="mermaid">
    graph LR
        A --- B
        B-->C[fa:fa-ban forbidden]
        B-->D(fa:fa-spinner);
    </div>     

20. 插入emoji

21. 思维导图
    
    [Hugo 中使用思维导图](https://wocai.de/post/dev/make-hugo-blog-add-mindmap/)


