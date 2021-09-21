## IO

### 节点流类型

| 类型          | 字符流                               | 字节流                                          |
| ------------- | ------------------------------------ | ----------------------------------------------- |
| File(文件)    | FileReader<br />FileWriter           | FileInputStream<br />FileOutputStream           |
| Memory Array  | CharArrayReader<br />CharArrayWriter | ByteArrayInputStream<br />ByteArrayOutputStream |
| Memory String | StringReader<br />StringWriter       | -                                               |
| Pipe(管道)    | PipedReader<br />PipedWriter         | PipedInputStream<br />PipedOutputStream         |



#### InputStream例子

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
public class TestFileInputStream {
	public static void main(String[] args) {
		int b = 0;
		FileReader fr = null;
		try {
			fr = new FileReader("xxxxx"); // 绝对路径
		} catch ( FileNotFountException e ) {
			System.out.print("找不到文件")
			System.exit(-1);
		}
		
		try {
			long num = 0;
			while( (b = fr.read()) != -1 ){
            	System.out.print((char)b);
            	num++;
			}
			fr.close();
			System.out.println();
			System.out.println("共读取了"+num+"个字节");
		} catch( IOException e ) {
			System.out.println("文件读取错误");
			System.exit(-1)
		}
	}
}
```



#### OutputStream例子

```java
package test;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class TestFileOutputStream {

	public static void main(String[] args) {
		int b = 0;
		FileInputStream iStream = null;
		FileOutputStream oStream = null;
		
		try {
			iStream = new FileInputStream("D:\\work\\test\\src\\test\\TestArgsWords.java");
			oStream = new FileOutputStream("D:\\work\\test\\src\\test\\newFile.txt");
			
			while ((b = iStream.read()) != -1) {
				oStream.write(b);
			}
			iStream.close();
			oStream.close();
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			System.exit(-1);
		} catch (IOException e) {
			System.out.print("文件复制错误");
			System.exit(-1);
		}
		System.out.print("文件已复制");
	}

}
```





### 缓冲流

缓冲流要“套接”在节点流之上，对读写的数据提供了缓冲的功能，提高了读写的效率，并提供了一些新的方法。

J2SDK提供了四种缓冲流，常用的构造方法为:

```
BufferedReader(Reader in)
BufferedReader(Reader in, int sz) // sz为自定义缓冲区大小
BufferedWriter(Writer out)
BufferedWriter(Writer out, int sz)
BufferedInputStream(InputStream in)
BufferedInputStream(InputStream in, int sz)
BufferedOutputStream(OutputStream in)
BufferedOutputStream(OutputStream in, int sz)
```

```java
import java.io.*;
public class bufferWriter {

	public static void main(String[] args) {
		try {
			BufferedReader bf = new BufferedReader( new FileReader( "D:\\leisureWork\\test\\src\\TestFileInputStream.java"));
			BufferedWriter bw = new BufferedWriter(new FileWriter( "D:\\leisureWork\\test\\src\\new.java"));
			
			String s = null;
			for( int i =0; i <= 100; i++  ) {
				s = String.valueOf(Math.random());
				bw.write(s);
				bw.newLine();
			}
			bw.flush();
			while ( (s = bf.readLine()) != null  ) {
//				bw.write(s);
				System.out.println(s);
			}
			bw.close();
			bf.close();
			
			
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			System.exit(-1);
			e.printStackTrace();
			
		} catch (IOException e) {
			System.out.println("文件读取错误");
			e.printStackTrace();
			System.exit(-1);
		}
	}
}

```



### 转换流

`InputStreamReader `和` OutputStreamWriter`用与字节数据到字符数据之间的转换

`InputStreamReader` 需要和` InputStream`  "套接"

`OutputStreamWriter` 需要和` OutputStream`(FileOutputStream, ) "套接"

转换流在构造是可以指定其编码集合例如：

```
InputStream isr = new InputStreamReader( System.in, "ISO8859_1" );
```



```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;

public class TestTransform {

	public static void main(String[] args) {
		try {
			OutputStreamWriter ow = new OutputStreamWriter( 
					new  FileOutputStream("D:\\leisureWork\\test\\src\\newFile.txt") );
			
			ow.write("fdsfsdfsdf4132f12ds");
			ow.close();
			ow = new OutputStreamWriter( 
					new FileOutputStream("D:\\leisureWork\\test\\src\\newFile.txt", true), "ISO8859_1");
			
			ow.write("45646161231");
			System.out.println(ow.getEncoding());
			ow.close();			
			
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			e.printStackTrace();
		} catch (IOException e) {
			System.out.print("文件复制错误");
			e.printStackTrace();
		}
	}
}

```



### Object流

`ObjectOutputStream` 和 `ObjectInputStream` 分别与`FileOutputStream` 和 `FileInputStream`  一起使用时，可以为应用程序提供对对象图形的持久存储。ObjectInputStream  用于恢复那些以前序列化的对象。其他用途包括使用套接字流在主机之间传递对象，或者用于编组和解组远程通信系统中的实参和形参。 

- **序列化：将对象写入到IO流中**
- **反序列化：从IO流中恢复对象**
- **意义：序列化机制允许将实现序列化的Java对象转换位字节序列，这些字节序列可以保存在磁盘上，或通过网络传输，以达到以后恢复成原来的对象。序列化机制使得对象可以脱离程序的运行而独立存在。**

**使用场景：所有可在网络上传输的对象都必须是可序列化的，**比如RMI（remote method invoke,即远程方法调用），传入的参数或返回的对象都是可序列化的，否则会出错；**所有需要保存到磁盘的java对象都必须是可序列化的。通常建议：程序创建的每个JavaBean类都实现Serializeable接口。**

**如果想要把某个类的对象序列化，必须得实现 `Serializable` 接口**

```
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class TestObjectIO {

	public static void main(String[] args) throws IOException {
		T t =  new T();
		t.k = 881;
		try {
			FileOutputStream fos = new FileOutputStream("D:\\leisureWork\\test\\src\\newFile2.dat");
			ObjectOutputStream oos = new ObjectOutputStream(fos);
			oos.writeObject( t );
			oos.flush();
			oos.close();
			
			FileInputStream fls = new FileInputStream("D:\\leisureWork\\test\\src\\newFile2.dat");
			ObjectInputStream ois = new ObjectInputStream(fls);
			T readT = (T)ois.readObject();
			System.out.println("readT: "+ readT.k); // 881
			System.out.println("readT: "+ readT.L); // 0 
			
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			System.out.println("找不到序列化对象的类");
			e.printStackTrace();
		}

	}
	
}
class T implements Serializable {
	int i = 10;
	int j = 9;
	double d = 2.3;
	int k = 5;
	transient int L = 23;
}
```

#### transient 关键字 

意思为透明，可以用来修饰修饰成员变量. 被transient修饰的变量在序列化的时候`不予考虑`，赋值为默认的值

#### Serializable接口

用来标记该对象可以序列化，是ObjectStream`序列化`时必须要声明的

#### Externalizable接口

用于自定义序列化过程

+ readExternal
+ writeExternal



## 线程

线程是一个程序里面不同的执行路径

### 声明方式

#### 第一种

 + 定义线程类实现Runnable接口

 + Thread myThread = new Thread(tarage) // tarage为Runnable接口类型

 + Runnable中只有一个方法

   	+  public void run() 用以定义线程运行体

 + 使用Runnable接口可以为多个线程提供共享数据

 + 在实现Runnable接口的类的run方法定义中可以使用Thread的静态方法

    +  public static Thread currenThead() 获取当前线程的引用

   

#### 第二种

+ 可以定义一个Thread的子类并重写其run方法

  ```
  class MyThread extends Thead {
  	public void run() {}
  }
  ```

  

+ 然后生成该类的对象

  ```
  MyThread mt = new MyThread()
  ```

```

public class TestThread {

	public static void main(String[] args) {
		Runner1 runner1= new Runner1();
		runner1.run();  // 阻塞式  非线程

		// 新建线程
		Thread t = new Thread(runner1); 
		t.start(); // 线程启动
		
		for (int i = 0; i <= 100; i++) {
			System.out.println("Main run " + i);
		}
	}
}

class Runner1 implements Runnable {
	public void run() {
		for( int i = 0; i <= 100; i++ ) {
			System.out.println("Runner"+ i );
		}
	}
}
```



#### 线程的合理终止方法

```java
// 通过使用 变量让线程return结束
package testStudy;

public class ThreadTest1 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MyThread mt1 = new MyThread();
		Thread thread = new  Thread(mt1);
		thread.setName("t");
		thread.start();
		
		
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		mt1.run = false;
	}

}

class MyThread implements Runnable {
	
	public Boolean run = true;
	@Override
	public void run( ) {
		
		for (int i = 0; i < 10; i++) {
			if (run) {
				System.out.println(Thread.currentThread().getName()+ "--->" + i);
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			} else {
				return;
			}
		}
		
	}
	
}
```





### 线程优先级

#### Thread.setPriority()

`Thread.setPriority()` 设置线程优先级，优先级较高的，只是抢到的cpu时间片相对多一些



### 线程合并

#### Thread.join()

线程中join方法的意思是把指定的线程加入到当前线程，即将两个交替执行的线程合并为顺序执行的线程。

比如在主线程B中，子线程A调用了join()方法，意思是说直到线程A执行完毕后，线程B才会继续执行。

同理，如果子线程A调用了join(10)方法，意思是说等线程A执行10毫秒后，线程B才会继续执行。



### 线程的同步

#### 锁线程

关键字： **synchronized**

```java
public class TestSync implements Runnable{
	Timer timer = new Timer();
	public static void main(String[] args) {		
		TestSync testSync = new TestSync();
		
		Thread t1 = new Thread(testSync);
		Thread t2 = new Thread(testSync);
		t1.setName("test1");
		t2.setName("test2");
		t1.start();
		t2.start();
	}
	
	public void run() {
		timer.add( Thread.currentThread().getName() );
	}
}

class Timer {
	private static int num = 0;
	// 第一种
	public void add(String name) {
		synchronized (this) {
			num++;
			try {
				Thread.sleep(1);
			} catch (InterruptedException e) {}
			System.out.println(name + "这是第" + num + "个线程");
		}
	}
 
	// 第二种
    /*public synchronized void add(String name) {
            num++;
            try {
            Thread.sleep(1);
            } catch (InterruptedException e) {}
            System.out.println(name + "这是第" + num + "个线程");
        }
    }*/


```



#### 死锁

每个对象都对应一个可称为“互斥锁”的标记，这个标记保证在任一时刻，只能有一个线程访问该对象

```java

public class TestDeadLock implements Runnable{

	public int flag = 0;
	static Object o1 = new Object(),   o2 = new Object();
	@Override
	public void run() {
		System.out.println("flag:" + flag);
		if (flag == 1) {
			synchronized (o1) {
				try {
					Thread.sleep(500);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				
				synchronized (o2) {
					System.out.println("1");
				}
			}
		}
		
		if (flag == 0) {
			synchronized (o2) {
				try {
					Thread.sleep(500);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				
				synchronized (o1) {
					System.out.println("2");
				}
			}
		}
		
	}
	
	
	public static void main(String[] args) {
		TestDeadLock t1 = new TestDeadLock();
		TestDeadLock t2 = new TestDeadLock();
		t1.flag = 0;
		t1.flag = 1;				
		Thread thread1 = new Thread(t1);
		Thread thread2 = new Thread(t2);
		thread1.start();
		thread2.start();
	}

}

```

https://raw.githubusercontent.com/chuerFeng/pictureBed/master

![](https://github.com/img/20200925183955.png)

发



### 线程安全

1. 尽量使用局部变量代替“实例变量和静态变量”。
2. 如果必须是实例变量，那么可以考虑创建多个对象。一个线程对应一个对象，100个线程对应100个对象，对象不共享，就没数据安全问题了。
3. 如果不能使用局部变量，对象也不能创建多个。那就只能使用 `synchronized`了。线程同步机制。



### 守护线程

### 定时器

### 实现线程的第三种方式

### wait和notify





## 反射机制

### 作用

通过java语言的反射机制，可以操作字节码文件

让程序更灵活

```java
package testStudy;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FilterReader;
import java.io.IOException;
import java.util.Properties;

/*
 * 验证反射机制的灵活性
 * 	java代码写一遍，再不改java源代码的基础上，可以做到不同对象的实例化
 * 	非常灵活 (符合OCP开闭原则，对扩展开放，对修改关闭)
 *  高级框架的原理都使用了反射机制，所以反射机制还是很重要的
 * */
public class ThreadTest3 {

	public static void main(String[] args) {

		
		// 这种方法写死了，只能创建一个User类型的对象
		User user = new User();
		
		
		// 以下代码灵活，代码不需要改动，可以修改配置文件，可以创建出不同的实例对象
		try {
			// 通过IO流读取classInfo.properties文件
			FileReader reader = new FileReader("classInfo.properties");
			  pro = new Properties();
			
			pro.load(reader);
			reader.close();
			
			
			String className = pro.getProperty("className");
			
			Class c = Class.forName(className);			
            // newInstance()底层调用的是该类型的无参数构造方法
			Object obj = c.newInstance();
			
			System.out.println(obj);
			
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (InstantiationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

```



### 在java.lang,reflect.*包下

#### 相关类

+ va.lang.Class 代表字节码文件，代表一个类型

+ va.land.reflect.Method 代表字节码中的方法字节码

+ va.land.reflect.Field 代表字节码中的属性字节码

+ va.land.reflect.Constructor 代表字节码中的构造方法字节码

```java
java.land.Class;
public class User {	
	int no;   // Field
   
    public User(){}  // Constructor
    
    public User(int no) {
        this.no = no
    }

    public void setNo(int no) {
        // Method
		this.no = no;
    }
    
    public getNo() {
    	return no;
    }
}
```

### 获取class的三种方式

#### Class.forName()

+ forName() 只让一个类的静态代码块执行

```java
public claass ReflectTest01 {
	public static void main(String [] args) {
		/*
        Class.forName()
			1. 静态方法
			2. 方法的参数是一个字符串
			3. 字符串需要的是一个完整的类名
			4. 完整类名必须带有包名。java.lang包也不能省略
		*/
        
        try {
            Class c1 = Class.forName("java.lang.String"); // c1代表String.class类型(class文件)
            Class c2 = Class.forName("java.util.Date"); // c2代表Date类型 
        } catch( ClassNotFoundException e) {
            e.printStackTace();
        }
	}
}
```



**重点：**

+ 如果只希望一个类的静态代码块执行，其他代码一律不执行，可以使用Class.forName();
+ 这个方法会导致类加载，类加载时，静态代码块执行

```java
public claass ReflectTest04 {
	public static void main(String [] args) {
        try {
           Class c1 =  Class.forName("java.lang.String")
        } catch( ClassNotFoundException e) {
			e.prontStackTrace()
        }
	}
}
class MyClass {
	// 静态代码块在类加载时执行，只加载一次
	static {
		System.out.println("MyClass类的静态代码块执行了")
	}
}
```





#### getClass()

![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/20210115151217.png)

```java
public claass ReflectTest01 {
	public static void main(String [] args) {
		/*
	      "".getClass()
          java任何一个对象都有一个方法: getClass()
		*/
        
        String s = "abc";
        Class x = s.getClass(); // x代表String.class字节码文件，x代表String类型
        System.out..print(c1 == x) // true (==判断对象是否是同一个内存地址)
	}
}
```



#### .class属性

```java
Class z = String.class;
Class k = Date.class;
Class x = int.class;
Class z = Double.class;

```





### 利用反射机制反编译Field

```java
package testStudy;

import java.lang.reflect.Field;
import java.lang.reflect.Modifier;
import java.util.ResourceBundle;

public class ReflectTest03 {

	public static void main(String[] args) throws Exception {
		/**
		* getModifiers() 获取修饰符
		* getType() 获取类型
		*/
		StringBuilder stringBuilder = new StringBuilder();		
		Class userClass = Class.forName("testStudy.User");
		
		stringBuilder.append( Modifier.toString(userClass.getModifiers()) + " class " + userClass.getSimpleName() + "{ \n" );
		
		Field[] fieldClass = userClass.getDeclaredFields();
		
		for(Field f: fieldClass) {
			stringBuilder.append("\t");
			stringBuilder.append(Modifier.toString(f.getModifiers()));
			stringBuilder.append(" ");
			stringBuilder.append(f.getType().getSimpleName()+ " ");
			stringBuilder.append(f.getName() + " = ");
			stringBuilder.append("; \n");
		}
		
		stringBuilder.append("}");
		
		
		System.out.println(stringBuilder.toString());
	}

}

```



### 反射机制访问属性(公私有)

+ field.setAccessible(true)  访问私有

```java
package testStudy;

import java.lang.reflect.Field;

public class ReflectTest04 {

	public static void main(String[] args) throws Exception {
		
		Class c =  Class.forName("testStudy.User");
	
		Object obj = c.newInstance();
		
		Field noField = c.getDeclaredField("no");
		// 给obj对象(User对象)的no属性赋值
		/*  虽然使用了反射机制，但是三要素缺一不可：
		 *  要素1： obj的对象
		 *  要素2： no属性
		 *  要素3： 2222值
		 * */
		noField.set(obj, 2222);
		
		System.out.println(noField.get(obj));
		
		/*访问私有属性*/
		Field nameField = c.getDeclaredField("name");
		nameField.setAccessible(true); // 打破封装，使私有暴露，这样设置后在外部可以访问private
		nameField.set(obj, "name");
		System.out.println( nameField.get(obj) );
		
	}

}

```



### 反射机制调用方法(重点)

要素分析

+ 对象UserService
+ login方法
+ 实参列表
+ 返回值

```java
public class MethodsTest {

	public static void main(String[] args) throws Exception {
		Class c = Class.forName("testStudy.User");
		Object obj = c.newInstance();
		
		Method loginMethod = c.getDeclaredMethod("login", String.class, String.class);
		Object reVal = loginMethod.invoke(obj, "admin", "1234");
		
		
		System.out.println(reVal);
	}
}
```

```java
// User.class
package testStudy;

import javax.security.auth.login.LoginContext;

public class User {
/*	public User() {
		System.out.println("无参数构造");
	}
	*/
	
	private String name = "name";
	int no = 1;
	public static Boolean man = true;
	
	
	public Boolean login(String name, String pass) {
		if (name == "admin" && pass == "123") {
			System.out.println("登陆成功");
			return true;
		}
		
		System.out.println("登陆失败");
		return false;
	}
	
	public void logOut() {
		System.out.println("退出");
	}
}

```



### 利用反射机制调用构造方法实例化java对象(非重点)

```java
public class ReflectTest05 {

	public static void main(String[] args) throws Exception {
		Class userM = Class.forName("testStudy.User");
//		Object o = userM.newInstance();
	
		Constructor con = userM.getDeclaredConstructor(int.class);
		
		Object newObj = con.newInstance(1);
		System.out.println(newObj);
		
	}

}

```



## 注解 

+ 注解，或者叫做注释  

+ 注解Annotation是一种引用数据类型，编译之后也是生成xxx.class文件



### @Annotation

```java
// AnnonationTest01.java
/*  自定义注解
	[修饰符列表] @interface 注解类型名 {	
	}
	注解可以出现在类上，属性上，方法上...还可以出现在注解类型上
**/

@MyAnnotation
public class ReflectTest05 {
	public static void main(String[] args) throws Exception {
		public int no;
        
        public ReflectTest05( @MyAnnotation int no) {}
        
        @MyAnnotation
        public static void m1() {}
        
        public void m2() {
            @MyAnnotation
        }
	}
}

```

```java
// MyAnnotation.java
public @interface MyAnnotation{};
```



### @Override注解

+ @Override只能注解方法

+ @Override是给编译器参考，和运行阶段没有关系

凡是java中的方法带有这个注解的，编译器都会进行编译检查，如果这个方法不是重写父类的方法，编译器报错。



### 元注解

#### @Target

标注`被标注的注解`可以出现在哪些位置

```java
@Target(ElementType.METHOD) // 表示被标注的注解只能出现在方法上
```



#### @Retention

标注`被标注的注解`最终保存在哪里

```java
@Retention(RetentionPolicy.SOURC) // 表示该注解只能出现在java源文件
@Retention(RetentionPolicy.CLASS) // 表示该注解保存在class文件中
@Retention(RetentionPolicy.RUNTIME) // 表示该注解保存在class文件中,并且可以被反射机制所读取
```



```java
// MyAnnotaion.java
/*只允许MyAnnotation注解可以标注类，方法*/
@Target({ElementType.TYPE, ElementType.METHOD})
/*希望MyAnnotation注解可以被反射*/
@Retention(RetentionPolicy.RUNTIME)
public @interator MyAnnotation {
}
```



### @Deprecated

用@Deprecated注释的程序元素会被提示已过时

```java
@deprecated
public static void m1() {}
```



## 扩展

### 获取路径

**注意：**

前提： 这个文件必须在类路径下

什么是类路径？ 文件在src下的都是类路径

src是类的根路径

![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/20210115174357.png)

```java
String path = Thread.currentThread().getContextClassLoader().getResource("com/bjxxxxx/java/bean/db.progerties").getPath()
```





### 资源绑定器 ResourceBundle

java,util包下提供了一个资源绑定器，便于获取属性配置文件中的内容。

使用以下这种方式的时候，属性配置文件xxx.properties文件，并且这个文件必须在类路径下。

文件扩展名也必须是properties

```java
public class ThreadTest4 {
	
	public static void main( String [] args ) {
		// 资源绑定器 只能绑定xxx.properties文件，并且这个文件必须在类路径下，文件扩展名也必须是 properties
        // 在写路径时，后面的扩展名不能写
		ResourceBundle bundle = ResourceBundle.getBundle("calssInfo");
		
		String name = bundle.getString("className");
		System.out.println(name);
	}
}

```



### 类加载器

#### 什么是类加载器？

专门负责加载类的命令 / 工具

classLoader

#### JDK自带了3个类加载器

+ 启动类加载器: rt.jar
+ 扩展类加载器: ext/*.jar
+ 应用类加载器: classPath



假设有这样一段代码

```
String  s = "abc";
```

1. 代码在开始执行之前，会将所需要的类全部加载到JVM中。

2. 通过类加载器的加载，看到以上代码类加载器会找String.class文件，找到就加载，那么是怎么加载的呢？

   1. **首先通过“启动类加载器”加载**

      启动类加载器专门加载 java\jdk1.8.0\jre\lib\rt

      rt.jar中都是JDK最核心的类库

   2. 如果“启动类加载器”找不到

      **会通过“扩展类加载器”**

      扩展类加载器专门加载 java\jdk1.8.0\jre\lib\ext\

   3. 如果“扩展类加载器”找不到

      **会通过“应用类加载器”加载**

      应用类加载器专门加载： classpath(window属性环境变量)中的jar包(class文件)

#### 双亲委派机制

java为了保证安全，使用了双亲委派机制

优先从启动类加载器中加载，这个称为“父”

“父”无法加载到，再从扩展类加载器中加载，这个称为“母”。

“双亲委派”，如果都加载不到，才会考虑从应用类加载器中加载，直到加载为止。



### 可变长参数 ...

```java
public class ArgsTest {

	public static void main(String[] args) {
		
		m("111", 1);
		m("111",1, 2);
		m("111",1, 2, 3);
	}
	/* 可变长参数只能有一个
	*  可变长参数只能放在最后一个
	**/
	public static void m(string s, int... args ) {
		System.out.println("m方法执行了···");
	}

}
```
