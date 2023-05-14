#python #selenium #web自动化
Selenium Python 提供了一个简单的API 便于我们使用 Selenium WebDriver编写 功能/验收测试。
 通过Selenium Python的API，你可以直观地使用所有的 Selenium WebDriver 功能

运行selenium需要对应浏览器的驱动,Chrome浏览器驱动下载
:https://chromedriver.storage.googleapis.com/index.html
注:浏览器大版本号对应即可

### 一.webdriver对象属性方法
1. 导入`from selenium import webdriver`
2. Chrome(chrome_options=cp , executable_path='local') 启动浏览器,可以设定启动选项 , 返回一个浏览器对象
	- chrome_options=cp , 一个ChromeOptions对象 ,关键字参数
	- executable_path='local'  浏览器驱动路径 , 添加到path里可以不用加这个选项
  

### 二.ChromeOptions对象属性和方法
1. 导入`from selenium.webdriver.chrome.options import Options`
2. add_argument("str") 给CO对象添加元素,设置运行选项
	- '--user-data-dir=用户数据目录' 注:Chrome浏览器在chrome://version/查看
	- '--headless' 无界面启动
	- '--disable-gpu'  不加这个选项，无界面启动有时定位会出现问题
3. 禁用正在受自动软件控制警告
	- `add_experimental_option('excludeSwitches',['enable-automation'])`

### 三.浏览器对象(browserObj)属性和方法
1. get("Url")	 访问指定url

2. page_source	返回一个浏览器页面html数据的str对象

3. find_element(By.id,"kw")	查找元素,返回一个element对象,"kw" 为查找元素的标签
   返回一个(如果找到多个返回第一个)WebElement对象
   参数:
	- By.id 一个`selenium.webdriver.common.by`中的[[#五.By selenium.webdriver.common.by中的By实例 | By实例]],指定查找方式
	- "kw" 为查找元素的标签

4. find_elements((By.CLASS_NAME, 'animal')	返回一个包含所有已找到元素的list

5. implicitly_wait(10)	设定隐式等待(当发现元素没有找到的时候， 并不立即返回找不到
    元素的错误。而是周期性（每隔半秒钟）重新寻找该元素，直到该元素找到，)时间

6. quit()       退出浏览器

7. switch_to.frame('str' or WebEOnj )       在html界面中,如果要选择的元素是被iframe标签加载的,需要用该方法切换到iframe标签中,在进行后续操作
           参数:
	        -  iframe 元素的属性 name 或者 ID 
	        - iframe 所对应的 WebElement 对象

8. switch_to.default_content()                如果我们使用了switch_to.frame方法那么后续的
	操作都在frame中进行,这时如果我们要在原html中进行操作就需要运行此方法

9.  title 获取当前窗口的标题栏 返回一个str对象

10. switch_to.window(handle)               在网页中如果操作了什么,使浏览器跳转其他窗口,
      此时我们browser对象操作的还是此窗口,需要用此方法切换其他窗口操作
	      参数:
		      - handle browser对象有一个属性window_handles,这时一个列表对象,**所有的窗口句柄**。可以用迭代的方式获取所有窗口句柄并用title属性判断是不是我们需要操作的
11. wd.current_window_handle           浏览器当前窗口句柄



### 四.WebElement对象
1. send_keys("str")	在对应的元素中输入内容

2. click()	点击该元素
		
3. text		获取元素内的文本,文本没有完全展示在页面上不能用此方法

4. find_elements， find_element类似于浏览器对象的 find_elements, find_element
	不过查找范围是元素内部

5. clear()       清除输入框已有的字符串

6. get_attribute("str")      获取元素的属性值    
	参数:
	- 'class'   元素的class的值
	- 'outerHTML'   获取整个元素对应的HTML文本内容
	- 'innerHTML'    获取元素内部的html文本内容
	- 'value'    获取输入框中的文本内容
	- 'innerText'    获取元素里的文本内容,适用于当文本没有完全展示在页面上


### 五.By   selenium.webdriver.common.by中的By实例
		(用于设置查找方式)		
1. ID	通过id查找元素

2. CLASS_NAME 通过class名字查找元素
	注:在HTML中如果一个标签有两个class,写法是:class="student  chinese"
	所以我们查找该元素要写成find_element(By.CLASS_NAME,"student")
	或find_element(By.CLASS_NAME,"chinese")

3.  TAG_NAME	通过HTML标签名查找元素

5. CSS_SELECTOR    通过css选择器查找元素
		注:选择器用于“查找”（或选取）要设置样式的 HTML 元素,常用css选择器如下
	  - 元素选择器: p {css...}    p 这是p表示选择网页上所有的</p>标签
	  - id选择器:#para1{css..}  \#para1 这里\#para1表示选择所有id为para1的标签
		  查找元素实例:find_elements(CSS_SELETOR,"\#para1")
	  - 类选择器:  .center {css...} .center表示所有class为center的标签
		  查找元素实例:find_elements(CSS_SELETOR,".center")
	  -  选择 子元素 和 后代元素 
		  查找元素实例:find_elements(CSS_SELETOR,"#container > div  span")
		  表示查找id为container标签为div的子元素中标签为span的后代元素
	  -  根据属性选择:假如有个标签 为
	     \<a href="http://www.miitbeian.gov.cn">苏ICP备88885574号\</a>可用
	     `find_element(By.CSS_SELECTOR, '[href="http://www.miitbeian.gov.cn"]')
	     来找到该标签
			[href*="miitbeian"]href属性包含miitbian字符串
			[href^="http"] href属性中字符串已http开头
			[href$="gov.cn"]href属性中字符串已http结尾
	  -  如果一个元素有多个属性如
		        \<div class="misc" ctype="gun">沙漠之鹰\</div>
		 CSS 选择器 可以指定 选择的元素要 同时具有多个属性的限制，像这样 `div[class=misc][ctype=gun]`
	       如果同时选择所有class 为 plant `和` class 为 animal 的元素(组选择)
	       `.plant , .animal`
	  - 选择父元素的第n个子节点可以这样写  父元素 `:nth-child(n)`
		  例:`input  :nth-child(2)`  表示查找input要求该节点是其父节点的第二个节点
		  反过来我们可以找父元素的倒数第几个节点:`:nth-last-child(n)`
		  父元素的偶数节点: `:nth-of-type(even)`
		  父元素的奇数节点: `:nth-of-type(odd)`
	   - 后续节点选择:如果要选择是 选择 h3 `后面所有的兄弟节点` span，可以这样写
			  `h3 ~ span`



### 六. Select单选框使用
	注:from selenium.webdriver.support.ui import Select
1. Select(WebElementObj)  创建一个select对象

2.  选择元素,在一个select标签中包含很多元素,select提供了很多选择元素的方法
	- s.select_by_value('str')     通过value属性选择元素
	- select_by_index(int)        通过次序选择元素
	- select_by_visible_text("str")        通过可见文本选择元素
	- deselect_by_value()                      根据选项的value属性值， 去除选中元素
	- deselect_by_index()                      根据选项的次序，去除选中元素
	- deselect_all()                                 去除选中所有元素

















headless启动
Headless Chrome 是 Chrome 浏览器的无界面形态，可以在不打开浏览器的前提下，使用所有 
Chrome 支持的特性运行你的程序。相比于现代浏览器，Headless Chrome 更加方便测试 web 
应用，获得网站的截图，做爬虫抓取信息等
```python
from selenium import webdriver
	chrome_options = webdriver.ChromeOptions()
	# 使用headless无界面浏览器模式
	chrome_options.add_argument('--headless') #增加无界面选项
	#如果不加这个选项，有时定位会出现问题
	chrome_options.add_argument('--disable-gpu') 
	# 启动浏览器，获取网页源代码
	browser = webdriver.Chrome(options=chrome_options)
	mainUrl = "https://www.baidu.com/"
	browser.get(mainUrl)
	print(f"browser text = {browser.page_source}")
	browser.quit()
```

















