# spiders_collection
用于存放一些爬虫脚本

## 01 猫眼电影-最受期待榜榜单
### 实现功能：

1. 使用requests库爬取榜单
2. 分析第1步获取的HTML数据，使用正则表达式过滤采集需要的信息字段，使用生成器存储字段数据，加上re.S参数连换行符一起输出
3. 存储到log文件中
4. 多线程爬取

### 遇到的问题：

1. get请求没有加User-Agent的header，爬虫被拒绝访问，添加后解决

### 未解决问题：

1. 期待度的2个数字不显示

**原因**：源代码中展示的并不是纯粹的数字。而是在页面使用了font-face定义了字符集，并通过unicode去映射展示。简单介绍下这种新型的web-fongt反爬虫机制：使用web-font可以从网络加载字体

[解决方案](http://www.freebuf.com/news/140965.html/1)

### 收获：

1. 学会了基本的requests操作，请求和分析一个静态页面
2. 学会了基本的正则表达式操作，通过()获取得到不同的关键字，提取得到需要的信息
3. 基本的写入文本文件操作
4. 遇到最简单的反爬虫，加入header参数里面的user-agent即可
5. 最简单的多线程抓取，map函数的使用
6. 字符串转JSON生成字典，使用json.loads()方法
7. 用生成器存储算法，而不是直接生成字典，避免存储空间浪费，在需要用到数据的时候，使用for循环调用生成器生成数据即可
8. 保存到文件使用中文的时候，open方法中要使用encoding='utf-0'，write方法中要使用ensure_ascii=False)


## 02 今日头条-采集和下载关键词“新垣结衣”的图集图片
### 实现功能：

1. 使用requests库爬取得到首页信息，返回的HTML代码中不包含需要的信息，都是些JS
2. 在XHR中找到JSON请求和数据
3. 使用request.get方法请求JSON数据，使用urlencode()生成完成的URL
4. 在第3步中得到一个字符串，使用json.loads()方法生成字典形式的JSON变量，通过keys()方法获取需要的标题信息，定义成生成器，得到所有的
5. 使用for循环调用第4步中的生成器，返回每个文章的url，传递给请求详情页的函数
6. 如果请求到了详情页，那么解析详情页，需要从返回的HTML代码中获取字段信息，因此采用BeautifulSoup进行解析
7. 用bs的select()方法获取title，并用get_text()取出第一个内容
8. 使用正则表达式从解析后的页面中找到图片地址，返回的是一个字符串
9. 第一次用json.load()返回的依然是1个字符串，需要嵌套多1层json.load()
10. 在第9步返回的JSON变量中，使用get()方法提取所有图片的地址，返回一个list，再使用for循环，遍历返回的list，得到所有图片的下载地址，并下载，把结果保存到MongoDB

### 遇到的问题：

1. 第9步的字符串也是反爬的手段之一，需要使用2次json.loads()

### 未解决问题：

无

### 收获
1. 分析AJAX返回的网页
2. 使用BeautifulSoup解析网页，并获取需要的信息
3. 字符串转JSON常量
4. 保存图片


## 03 淘宝-使用selenium采集关键词为电动车的数据
### 实现功能：
1. 使用geckodriver驱动firefox访问淘宝首页，输入关键词，点击搜索按钮，翻页，点击确定按钮，采集信息，存储到mongodb，导出成CSV文件，统计程序运行时间
2. 使用selenium的WebDriverWait(browser,10).until(expected_conditions.条件((By.条件,'条件')))方法，判断输入框是否加载完成，搜索按钮是否出现
3. 使用send_keys()方法，输入关键字，使用click()方法，点击搜索
4. 获取第1页的产品信息
5. 使用第2步的方法，判断总页数是否出现，用text方法返回string
6. 翻页的方法使用，使用第2步的方法，判断输入框是否出现，确定按钮是否出现，调用clear()方法清空输入框，send_keys()方法输入页码，click()方法点击按钮
7. 使用第2步的方法，判断高亮的页码是否为翻页需要的页码
8. 每获取1页翻页，就获取1页产品信息
9. 产品信息的解析，使用pyquery库，首先使用第2步的方法，判断整个页面加载完成，即产品框的frame都出现了
10. 提取需要的产品信息字段，存储到MONGODB

### 遇到的问题：

1. Chrome版本和ChromeDriver不匹配，改为使用firefox
2. 使用PhantomJS报错，设置窗口大小后解决
3. 从mongodb导出到csv文件，首次打开为乱码，用的是utf-8 without BOM编码，windows下需要使用utf-8，用notepad++打开csv文件设置一下编码保存即可
4. 从firefox复制的CSS选择器格式错误，改用chrome
5. 使用PhantomJS程序的运行时间：  251.23947518380507，而使用firefox的程序的运行时间：  143.89224086472169，所以其实并不是无界面浏览器就快
6. 图片链接没有抓取完全，提取图片信息属性的时候应该直接使用data-src

### 收获
1. 初步学会使用selenium采集JS渲染的页面
2. mongodb导出
3. CSS选择器使用

### 01 - 03 总结不足
1. 网页结果分析，HTTP和AJAX以及JS基础
2. 数据结构存储的学习
3. 程序化的学习
4. 异常处理


## 04 爬取妹子图最热图片
参考链接：https://github.com/wzyonggege/Mzitu-Crawler/blob/master/mzitu.py

### 实验功能
爬取http://www.mzitu.com/hot/的文章图片
1. 先请求http://www.mzitu.com/hot/首页
2. 分析首页HTML代码，用正则表达式获取一共有多少后翻页数，后续的url格式是http://www.mzitu.com/hot/页码
3. 在"http://www.mzitu.com/hot/页码"中查找图片页的地址，使用正则表达式
4. 把图片详情页的url作为参数，传入main函数，首先请求图片详情页，使用正则表达式查找是有多少张图片，使用图片详情页的url和图片的索引，可以组合出图片页的真实url，使用正则表达式获取图片标题和图片真实的url
5. 下载图片，并保存到MongoDB


### 遇到的问题
1. 请求图片详情页出错，返回“盗链”的图片提示，修改header并传入正确的Referer解决
2. 图片标题带？，使用replace方法替换，否则无法创建目录

### 需要改进的地方
1. 多线程，当前是单线程采集，速度很慢

## 05 爬取华为官网VMALL的手机评论

### 实现功能
1. 爬取手机界面的所有手机评论列表
2. 存储到MONGODB

### 步骤
1. 获取首页的手机列表，并获取各个手机标题和详情页的URL
2. 把第1步获取的详情页URL分别打开，并获取产品ID
3. 根据产品ID结合URL，组合出评论页的JSON请求并获取JSON数据
4. 首页的JSON数据中包含最大的评论页数
5. 使用最大评论页数作为循环，获取得到所有页的评论

### 遇到的问题
1. 返回的JSON数据包含一些乱七八糟的开头，不能使用json.loads()方法生成字典
2. 有时候请求评论页JSON会出现乱码
3. 暂时想不到多线程在哪里使用合适

### 改进
1. 使用json.loads()方法
2. 使用多线程

## 06 使用代理爬取搜狗微信文章

### 实现功能
1. 根据登陆后的cookie制作header，请求搜索微信文章
2. url需要使用urlencode拼接
3. 使用代理避免IP被封
4. 使用pyquery解析得到需要的字段信息
5. 爬取文章详情页并存储到MongoDB

## 步骤
1. 制作cookie，拼接URL
2. 获取每一页的html代码
3. 从每一页的html代码解析得到具体文章的url
4. 获取具体文章的url，解析得到需要的信息

## 收获
1. 使用pyquery
2. 使用代理IP
3. 异常处理





