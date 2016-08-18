# **1.TomCat服务器使用常见的问题** #
一闪而过：在环境变量中配置java_home弄好就行了;
如果不行，就进入cmd 然后进入放tomcat的bin目录，然后手动运行
startup.bat 就行了；如果还不行 就输入 catalina run这个命令（意思是即使有错误，它也会运行然后找出错误在哪）

----------

# 2.如何在cmd中不切换到d盘的目录中，使用命令运行TomCat服务器 #

方法是(推荐)：配置一个CATALINA_HOME，变量值D:\Program Files\apache-tomcat-6.0.44，然后在path路径中加入%CATALINA_HOME%\bin
----------
# 3.获取一个类的全类名 #

双击类名，然后点击右键，有一个copy Qualified name 复制全类名，即可。

----------
# 4.TomCat服务器使用的细节 #
在Servers那个按钮那里双击进入一个页面，有个Server Location，点击use custom location自己设置一个目录存放这些东西，因为默认存放的目录隐藏的太深，每次看的时候非常不方便


----------
# 5.解决服务器乱码的问题 #
1、修改tomcat的server.xml配置文件   (get有效)
		get:在connector这行加上URIEncoding=utf-8
		post:在取数据之前   request.setCharacterEncoding("utf-8");

2、响应乱码
		①、response.setContentType("text/html;charset=utf-8");
		②、response.setHeader("Content-Type","text/html;charset=utf-8");
#
# 6.在web中写路径名的时候统一用----- 
**“/”**

----------

# 7.路径问题 # 
相对路径：前面不加/
统一用绝对路径（最前面加/）
服务器里面的路径不加项目名
浏览器显示的文件里面加项目名

----------

# 8.导入别人的正确的包却出现问题解决方法 #
 
基本上都是点击build path，configure bulid path ，add library，添加需要的环境，java的环境是jre runtime，servelet的环境是 server runtime，还有tomcat运行环境版本是否一致的问题，点击进入properties，找到Project Facets，右边有个Runtimes，进去修改版本号

# 9.关于jar包 #

----------

jar包就是别人已经写好的一些类，然后将这些类进行打包，你可以将这些jar包引入你的项目中，然后就可以直接使用这些jar包中的类和属性了，这些jar包一般都会放在lib目录下的.