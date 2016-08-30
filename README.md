# javaSE-
java基础知识

##配置java环境

    安装jdk，然后进入高级系统设置，环境变量，

     1.变量名JAVA_HOME

     变量值为C:\Java\jdk1.7.0_15

     2.修改Path，将JDK的bin目录添加到PATH目录下。

     %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;  

打开cmd命令窗口，执行Javac，弹出一系列命令，这就成功了

# 1.有关局部变量 #
局部变量中不能用任何的访问修饰符修饰，也不能用static修饰，只能用final修饰

----------

# 2.单例设计的思路 #
保证系统的一个类有且仅有一个实例对象，私有化构造


----------
# 3.== 和 equals 的区别 #
== :对于基本类型来说，判断的就是字面值（内容）是否相等。对于引用类型，判断的是地址（引用的是否同一个对象）

equals:对于基本类型来说，根本没有这个方法。对于引用类型，判断的是地址（引用的是否为同一个对象），很多类重写了equals方法，像String、File、Date类等都重写了此方法，所以判断是内容。
***
2016/4/7 11:56:31 
# 4.关于自定义异常自己的认识 #
自定义异常：因为系统提供的异常名字已经满足不了自己，所以得自己创建一个异常。
       try{
       }catch(){
      system.out.println("不能为负数");
       }
      public static int ecm(int a, int b) {
      if (a<0 ||b<0) {
      throw new MyException("不能为负数");
      }
       int div = a/b;
       return div;
    	}
    } 
          class MyException extends RuntimeException {//类
       	public MyException() { //无参构造
    
       }
       	public MyException(String message) {//有参构造
       		super(message);
      	}
       } 
 2016/4/9 8:34:02      
    

----------
# 5.关于构造方法的看法 #
构造方法的目的是赋值的时候比较方便，
1.如果想要得到已有的数据，可以写个无参构造，在无参构造里面直接赋值,然后还得创建一个那个构造的对象，初始化构造，才能得到值；
2.如果想要赋新的值，就要在调用构造的时候在括号里面赋值


----------
# 6.关于return的用法 #
Java中的return语句总是和方法有密切关系，return语句总是用在方法中，有两个作用，一个是返回方法指定类型的值（这个值总是确定的），一个是**结束方法**的执行（仅仅一个return语句）。

# 1.关于匿名内部类的收获 #

    File[] listFiles1=file.listFiles(new FilenameFilter(){

			@Override
			public boolean accept(File dir, String name) {
				if(name.endsWith(".exe")){
					
					return true;
				}
				return false;
			}
			
		});
这个就属于一个匿名内部类（一般会有方法的重写，匿名内部类一般创建一个抽象类或接口的子类，不能定义静态的成员。）
因为FilenameFilter是一个接口，它并不能实例化，所以如果不想再写一个类来调用FilenameFilter的方法就可以使用匿名内部类（意思就是只使用一次这个对象，因为它实现了自动向上转型，所以可以这样），然后使用里面的方法就行了。

----------

# 2.有关object的类型收获 #
object是所有类的父类，它在java，lang包里，
它有三个常用的方法，
1.equals 一般用来判断地址是否相等（是否为同一个对象），但重写此方法的话，一般是判断内容是否相等；
2.hashcode 通过哈希算法，获得hash值，一般用于集合的hashset判断集合中是否有指定元素，先去判断hashCode，然后再去判断equals；
3.tostring 作用：返回对象的字符串形式：  全类名+@+哈希值（地址号）

一般来讲，重写toString方法，返回对象的基本信息，例如：String类、包装类（Integer）等


----------

# 3.全局变量加上static修饰对象 #
为了保证每次使用的时候都是同一个对象

# //1.创建流对象(字节流) #
		
		BufferedInputStream bis  =new BufferedInputStream(new FileInputStream("src/a.txt"));
		
		
		//2.读取
		
		byte[] b=new byte[10];
		int len;
		while((len=bis.read(b))!=-1){
			
			System.out.print(new String(b,0,len));
		}
		
		//关闭
		
		
		bis.close();

----------
# //创建流对象（字符流，读取中文） #
		
		BufferedReader br = new BufferedReader(new FileReader("src/b.txt"));
		
		
		//2.读取 readLine
		/*
		 * 读取一行，返回String类型
		 * 读到文件末尾，返回null
		 * 
		 */
	
		String line=null;
		while((line=br.readLine())!=null){
			System.out.println(line);
		}
		
		
		//关闭:只关闭增强流
		
		br.close();# 

----------

# //3.获取person类的字节码对象 # #
		Class  c = Class.forName("day23.exercise.Person");(或者可以 Class c=Person.class) 
		//2.通过反射获取Person类对象
		Person p =(Person) c.newInstance();
		//注意 newInstance方法要求，Person类必须有无参构造!!!!
		//获取公共的属性
		Field field = c.getField("name");
		field.set(p, "范冰冰");
		//获取非公共的属性(static)
		Field field2 = c.getDeclaredField("age");		
		//暴力破解
		field2.setAccessible(true);
		field2.set(p,35);
		//获得方法
		Method method = c.getMethod("show");//无参无返回
		method.invoke(p);//调用方法
		Method method2 = c.getMethod("getDetails", String.class);
		Object invoke = method2.invoke(p, "范冰冰");
		System.out.println(invoke);

----------

# 2》对象流 ☆ #
引入：

【永久保存数据时，有可能出现写入的和读取的对象不一致的问题！！！！


为了避免这个问题，要将写入对象设置一个记号，就是序列化

Java中的具体做法：将对象类型实现序列化接口Serializable！ 】
概念：

序列化：将数据永久存储到文件或网络  ，使用ObjectOutputStream
反序列化：将数据从文件读到内存中,使用ObjectInputStream
		
1》写入和读取的顺序要一致
2》写入的对象必须实现序列化接口
3》序列化对象中所有属性也都要求全部实现序列化
4》如果有的属性不想序列化，则加上修饰符  static或transient

----------
# 3》转换流 ★ #

InputStreamReader：inputStream——Reader  ★
OutputStreamWriter:OutputStream——Writer

案例：从键盘输入的通过转换流读取出来即可！！

----------

# 2016/4/21 15:33:22  

# 4.关于mysql 数据库的认识和外键的使用 #
mysql其实是一个管理数据库的软件，它并不是数据库，它起着管理的作用，管理着各个数据库。
外键的作用其实就是：当一个员工表中，想增加一个这个员工在哪个部门，就新建一个表，设置一个主键（比如是id），然后员工表用一个emp_id （这个列刚开始没有建）通过语句跟 这个（id）绑定在一起就行了。

# 1.产生随机数 
  `//Math.random()默认 产生的是：0-1之间的浮点数,不包含1`
   `//产生  a-b范围的整数。公式  ：(int)(Math.random()*(b-a+1)+a)`
# 2.开根号 #
sqrt
# 3.匿名对象 #
创建的对象没有名字，只使用一次
# 4.方法可以嵌套调用 #
# 5.对象数组，自己常常记不住的 #
1、声明并开辟长度
类型[]  名=new 类型[长度];

2、为每个元素创建对象并赋值★
名[下标]=new 类型(值，值);(通过构造方法赋值)

3、使用（打印）

不能直接使用：名[下标]，因为这是一个引用（地址号），而是应该
继续访问元素中的属性和方法.<br>
**例如：syso(名[下标].getName());**
# 6.关于栈和堆自己的看法 #
栈：1.局部变量，2.对象的引用

堆：1.全局变量，2.对象 <br>
只要是涉及到（引用）这两个字，都是修改的地址，再次输出不是原来的值。

# 7.关于二维数组需要注意的 #
 int[][] num=new int[10][];//每行并不知道有多少列

二维数组的列长度不固定，数组名[i] 里面放的是null

二维数组的行列长度都固定，数组名[i]里面放的是具体地址号


		
//赋值
	
    for (int i = 0; i < num.length; i++) {
     //num[i] 因为还没有具体对象（没有实际开空间），所以现在是null
    //NullPointException  ：当调用了一个空对象的 属性或方法时候
    			
    num[i] = new int[i+1];//  ★
# 8.特别注意 #
			
当使用 Boolean s=c1.exit()时，是先调用exit这个方法（运行这个方法），然后接收返回的值。
 # 9.运算符优先级 #

() > 算术>关系>逻辑>短路>三元>赋值

# 1.在for循环时，if else分支不能乱用！ #
        for (int i = 0; i < customer.length; i++) {
    			if(bianhao==customer[i].getNo()){
    				return bianhao;//找到后返回编号
    			}else{
                    return -1；//这一个else不能要
                        }
    		}
    		return -1;
因为，如果第一次运行时，条件不成立，走了else这条分支，直接跳出了循环，相当于for循环只运行了一次！
# 2.定义一个不知道大小的数组时，如果你定义为： #

    Customer[] customer = new Customer[100];//但是
你只输入了五个数，然后你在for循环中使用为：

    for (int i = 0; i < customer.length; i++) {
           }//这样会出现空指针异常，因为从第6个数开始，后面都是null
应该改为：
        for (int i = 0; i < count; i++) {
      }
# 3.	空指针异常 #
当数组引用指向null（值），但却想继续使用，会报空指针异常，因为数组引用通常指向的是地址，

# 1.在对象继承时，关于子类的属性如何赋值？ #
##第一种方法：如果想要以有参构造赋值##
首先，在那个子类中直接写一个无参构造，第一句写上super（），调用父类无参的构造；super（参数），调用父类的有参构造；
然后，在测试类中使用对象赋值，Car car = new Car(9,8);
##第二种方法：如果想要以set，get方法赋值##
首先，在那个子类中(直接写属性的get，set方法)可以不写get，set方法
然后，在测试类中赋值，例如：
Train train=new Train();
              train.setWeight(12);
              
              train.setDistance(20);
         double s= train.spendtrain(train.getWeight(),train.getDistance());
# 第三种方法：直接赋值 #
直接在测试类中，

    Train train=new Train();
     `train.属性=值 ;`  即可
# 2.关于子类转化为父类，父类强制转化为子类的作用 #
子类转化为父类：编译父类，运行结果为子类的方法；
父类强制转化为子类:这样的话父类可以调用子类的方法。
# 3.关于==和equals #
== :对于基本类型来说，判断的就是字面值（内容）是否相等。对于引用类型，判断的是地址（引用的是否同一个对象）

equals:对于基本类型来说，根本没有这个方法。对于引用类型，判断的是地址（引用的是否为同一个对象），很多类重写了equals方法，像String、File、Date类等都重写了此方法，所以判断是内容。
# 4.经典的多态简单题 #（子类中没有找父类）
class A {
         public String show(D obj)...{
                return ("A and D");
         } 
         public String show(A obj)...{  找到他
                return ("A and A");
         } 
} 
class B extends A{
         public String show(B obj)...{
                return ("B and B");
         }
         public String show(A obj)...{ //重写方法
                return ("B and A");
         } 
}
class C extends B...{} 
class D extends B...{} 


（二）问题：以下输出结果是什么？
 A a1 = new A();
    B b  = new B();
  A a2 = new B();
          
              C c  = new C();
              D d  = new D(); 
        System.out.println(a1.show(c));   ② A and A



        System.out.println(a1.show(d));   ③ A and D
        System.out.println(a2.show(b));   ④ B And A
        System.out.println(a2.show(c));   ⑤ B And A
        System.out.println(a2.show(d));   ⑥ A And D

        System.out.println(b.show(b));     ⑦ 正常调用   B And B
        System.out.println(b.show(c));     ⑧  B And B
        System.out.println(b.show(d));     ⑨    B And B


               
            
        System.out.println(a1.show(b));   ①    A and A

5.java对象序列化（bean类 implements Serializable）的原因：

    当两个进程在进行远程通信时，彼此可以发送各种类型的数据。
    无论是何种类型的数据，都会以二进制序列的形式在网络上传送。
    发送方需要把这个Java对象转换为字节序列，才能在网络上传送；
    接收方则需要把字节序列再恢复为Java对象。

    序列化：把Java对象转换为字节序列的过程。
　　反序列化：把字节序列恢复为Java对象的过程。

　  对象的序列化主要有两种用途：
　　1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中；
　　2） 在网络上传送对象的字节序列。
