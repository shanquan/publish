---
layout: pagelt
title: Android Interview
tags: [byd]
---
## Process
---

### 工作环境与工作内容介绍
制造业IT部门，主要职责是工厂MES（制造执行系统）、WMS（仓库物料管理系统）的功能开发与运作支持。<br>
岗位职责：开发和维护用于工厂内部产线人员执行数据采集与生产操作的android程序，运行在PDA手持设备上。<br>
工作地点：惠州大亚湾

有现有的Android项目需要维护，涉及到的技术比较基础，主要是一些基本的界面开发、网络接口调用和数据解析处理，还有现成的源码资源。

通常的开发工作是负责业务的同事（策划科）提需求，服务端的人写接口，Android这边写界面和调接口，然后发给工厂的人员进行测试。验证OK之后这边PDA程序就会在生产过程中使用。工作中有很多需要与策划和后端开发沟通的情况，主动获取信息、与人沟通能力也很重要。正常情况下不需要加班，工作时间是标准的855。但是生产是24*7排班的，如PDA使用中遇到问题且涉及到影响生产的，PDA这边需要及时排查下，但这种情况极少且一般都是接口的问题。

我们这边还有一些JS混合开发的APP，做一些Web前端的项目，如果有兴趣也可以一起学习。但这个职位的主要技术方向还是Android的相关开发。

### 招聘要求
1. 计算机类专业，大专及以上学历，熟练掌握Java语言，具有两年以上Android项目（可累计合并JAVA的）开发工作经验，熟悉Android UI组件开发、网络异步Okhttp库开发和Android性能优化；熟练使用Android Studio进行Android项目的开发与调试；
2. 熟悉AJAX、JSON、XML、SQL等开发技术；
3. 具备Javascript、Html5、Cordova混合开发项目经验优先；
4. 具备RxJava、Retrofit开发经验优先；
5. 具备良好的沟通能力及团队合作精神。

开发机器配置：
环境变量、代理配置、svn checkout/git clone代码 and pull request

### 程序员职业基本素养与技能（通用型）：
1. 理解熟悉语言的90%以上语法、开发调试工具、运行环境配置；
2. 了解软件项目工程的常用管理方法、工程文档、角色职责分工，熟悉项目管理工具、源码管理工具，熟悉自己岗位常用的开发技术、开发工具与开发框架；
3. 掌握（积累）开发技术、资源查找方法（途径），能读懂同语言框架的项目源码，具备快速定位相关代码段并进行调试与修改确认代码执行结果，问题分析与判断解决能力；
4. 会主动学习尝试新技术新方法，行业内英文技术文档阅读能力强，具备丰富的IT技术基础知识与发展趋势分析能力；

### Interview Questions

#### Java,Android基本问题
1. int与Integer的区别，HashMap与ArrayList区别
   > 1.Integer是int的包装类，int则是Java的一种基本数据类型<br>
   2.Integer变量必须实例化之后才可以使用，而int变量不需要<br>
   3.Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值<br>
   4.Integer的默认值是null，int的默认值是0<br><br>
   HashMap与ArrayList区别：<br>
    1.实现的接口<br>ArrayList实现了List接口（Collection（接口）->List（接口）->ArrayList（类）），底层使用的是数组；而HashMap现了Map接口（Map（接口）->HashMap（类）），底层使用的是Hash算法存储数据 <br>
    2.存储元素 <br>
    ArrayList以数组的方式存储数据，里面的元素是有顺序，可以重复的；而HashMap将数据以键值对的方式存储，键的哈希码（hashCode）不可以相同，相同后面的值会将前面的值覆盖，值可以重复，里面的元素无序 <br>
    3.添加元素的方法 <br>
    ArrayList用add(Object object)方法添加元素，而HashMap用put(Object key, Object value)添加元素
1. String、StringBuffer、StringBuilder区别和使用场景
   > 1.如果要操作少量的数据用 --> String<br>
   2.单线程操作字符串缓冲区 下操作大量数据 --> StringBuilder<br>
   3.多线程操作字符串缓冲区 下操作大量数据 --> StringBuffer<br>
2. 谈谈java泛型、抽象类、内部类、接口概念与用法
   - A: 泛型
   ```java
    class Test<T>
    {
        T obj;
        Test(T obj) {  this.obj = obj;  }
        public T getObject()  { return this.obj; }
    }
    class Main
    {
      public static void main (String[] args)
      {
        Test <Integer> iObj = new Test<Integer>(15);
        System.out.println(iObj.getObject());
        Test <String> sObj = new Test<String>("GeeksForGeeks"); 
        System.out.println(sObj.getObject());
      }
    }
   ```
   - B: 抽象类
   ```java
    abstract class Animal{
      private int age;
      public Animal(int age){
        this.age = age;
        System.out.println("初始化Animal");
      }
      public void move(){
        System.out.println("跑步数："+this.age);
      }
    }
    abstract class Dog extends Animal{
      public Dog(int age){
        super(age);
        System.out.println("初始化Dog");
      }
    }
    public class BigDog extends Dog{
      public BigDog(){
        super(10);
        System.out.println("初始化BigDog");
      }
      public static void main(String[] args){
        BigDog a = new BigDog();
        a.move();
      }
    }
   ```
   - C: 内部类
   ```java
    public class Main{
      private String data="data";
      class test{
        public void go{
          System.out.println(data);
        }
      }
    }
   ```
   - D: 接口
   ```java
    interface in1
    {
      final int a = 10;
      void display();
    }
    class testClass implements in1
    {
      public void display()
      {
      System.out.println("test");
      }
      public static void main (String[] args)
      {
      testClass t = new testClass();
      t.display();
      System.out.println(a);
      }
    }
   ```
3. 熟悉哪些设计模式
   > 工厂模式`Factory`、单例模式`Singleton`、建造者模式`Builder`
   
   - A: Factory
  
   ```java
    public interface Sender {  
      public void Send();  
    }

    public class MailSender implements Sender {  
      @Override  
      public void Send() {  
          System.out.println("this is mailsender!");  
      }  
    }

    public class SmsSender implements Sender {  
      @Override  
      public void Send() {  
          System.out.println("this is sms sender!");  
      }  
    }

    public class SendFactory {  
      public Sender produce(String type) {  
          if ("mail".equals(type)) {  
              return new MailSender();  
          } else if ("sms".equals(type)) {  
              return new SmsSender();  
          } else {  
              System.out.println("请输入正确的类型!");  
              return null;  
          }  
      }  
    } 

    public class FactoryTest {  
      public static void main(String[] args) {  
          SendFactory factory = new SendFactory();  
          Sender sender = factory.produce("sms");  
          sender.Send();  
      }  
    }
   ```

   - B: Singleton

   ```java
    class Teacher {
      private Teacher() {}
      private static Teacher t = null ;
      public synchronized static Teacher getTeacher() {
        if(t==null) {
          t = new Teacher() ;
        }
        return t ;
      }
    }
    
    public class TeacherDemo {
      public static void main(String[] args) {
        Teacher t1 = Teacher.getTeacher() ;
        Teacher t2 = Teacher.getTeacher() ;
        System.out.println(t1==t2);
        System.out.println(t1);
        System.out.println(t2);
      }
    }
   ```

   - C: Builder

   ```java
    public class Builder {
      static class Student{
          String name = null ;
          int number = -1 ;
          String sex = null ;
          int age = -1 ;
          String school = null ;
          static class StudentBuilder{
              String name = null ;
              int number = -1 ;
              String sex = null ;
              int age = -1 ;
              String school = null ;
              public StudentBuilder setName(String name) {
                  this.name = name;
                  return  this ;
              }

              public StudentBuilder setNumber(int number) {
                  this.number = number;
                  return  this ;
              }

              public StudentBuilder setSex(String sex) {
                  this.sex = sex;
                  return  this ;
              }

              public StudentBuilder setAge(int age) {
                  this.age = age;
                  return  this ;
              }

              public StudentBuilder setSchool(String school) {
                  this.school = school;
                  return  this ;
              }
              public Student build() {
                  return new Student(this);
              }
          }
          public Student(StudentBuilder builder){
              this.age = builder.age;
              this.name = builder.name;
              this.number = builder.number;
              this.school = builder.school ;
              this.sex = builder.sex ;
          }
      }
      public static void main( String[] args ){
          Student a = new Student.StudentBuilder().setAge(13).setName("LiHua").build();
          Student b = new Student.StudentBuilder().setSchool("sc").setSex("Male").setName("ZhangSan").build();
      }
    }
   ```

4. 排序算法
   > 冒泡排序`BubbleSort`，选择排序`SelectSort`，插入排序`InsertSort` : `O(n2)`，快速排序`QuickSort` : `O(N*logN)`
   - A: BubbleSort
   ```java
    public static void SortA(int [] arr){
        int temp;
        for(int i=0; i<arr.length-1; i++){
            for(int j=arr.length-1; j>i; j--){
                if(arr[j] < arr[j-1]){
                    temp = arr[j];
                    arr[j] = arr[j-1];
                    arr[j-1] = temp;
                }
            }
        }
    }
   ```
   - B: SelectSort
   ```java
    public static void SortB(int array[],int lenth){
      for(int i=0;i<lenth-1;i++){
          int minIndex = i;
          for(int j=i+1;j<lenth;j++){
             if(array[j]<array[minIndex]){
                 minIndex = j;
             }
          }
          if(minIndex != i){
              int temp = array[i];
              array[i] = array[minIndex];
              array[minIndex] = temp;
          }
      }
    }
   ```
   - C: InsertSort
   ```java
    public static void SortC(int array[],int lenth){
      int temp;  
      for(int i=0;i<lenth-1;i++){
          for(int j=i+1;j>0;j--){
              if(array[j] < array[j-1]){
                  temp = array[j-1];
                  array[j-1] = array[j];
                  array[j] = temp;
              }else{
                  break;
              }
          }
      }
    }
   ```
   - D: QuickSort
   ```java
    public static void SortD(int a[],int l,int r){
        if(l>=r)
          return;
        int i = l; int j = r; int key = a[l];
        while(i<j){
            while(i<j && a[j]>=key)
                j--;
            if(i<j){
                a[i] = a[j];
                i++;
            }
            while(i<j && a[i]<key)
                i++;
            if(i<j){
                a[j] = a[i];
                j--;
            }
        }
        a[i] = key;
        SortD(a, l, i-1);
        SortD(a, i+1, r);
    }
   ```
5. Android四大组件包括以下哪些？（多选）
   - [X] Activity
   - [ ] Controller
   - [X] Service
   - [ ] Model
   - [X] BroadcastReceiver
   - [ ] Intent
   - [X] ContentProvider
   - [ ] View
6. 一个安卓界面即为一个`?`
  > Activity
8. `?`负责后台任务的运行
  > Service
9.  `?`负责广播
  > BroadcastReceiver
10. `?`负责应用的对外分享数据功能
  > ContentProvider
11. `?`充当四大组件信使，完成数据的组件传递及开启组件的功能
  > Intent
12. 使用XML文件对安卓应用界面进行布局，常用的布局方式(`layout`)有（多选）：
    - [X] LinearLayout
    - [ ] TableLayout
    - [X] FrameLayout
    - [X] RelativeLayout
    - [X] GridLayout
    - [X] PercentLinearLayout
    - [ ] AbsoluteLayout
    - [ ] PositionLayout
    - [X] ConstraintLayout
13. 接口数据一般采用`?`（数据格式）
  > XML/JSON
14. JSON解析用`?`（包名或库名）
  > XML解析可用到SAX、PULL等，JSON解析可用到JSON原生解析`JSONObject`、GSON或者FASTJSON
15. Android网络图片请求进行三级缓存，分别为？加载顺序？
  > 内存缓存、SD卡缓存和网络缓存，先加载内存缓存，如没有依次加载磁盘缓存和网络缓存
16. 图片加载框架有哪些`?`他们之间的区别是什么？
  > Glide、Picasso、Fresco、Universal-Image-Loader
17. 网络框架有哪些`?`他们之间的区别是什么？
  > Xutils、OKhttp、Volley、Retrofit<br>
  **Xutils**<br>
  这个框架非常全面，可以进行网络请求，可以进行图片加载处理，可以数据储存，还可以对view进行注解，使用这个框架非常方便，但是缺点也是非常明显的，使用这个项目，会导致项目对这个框架依赖非常的严重，一旦这个框架出现问题，那么对项目来说影响非常大的<br>
  **OKhttp**<br>
  Android开发中是可以直接使用现成的api进行网络请求的。就是使用HttpClient,HttpUrlConnection进行操作。okhttp针对Java和Android程序，封装的一个高性能的http请求库，支持同步，异步，而且okhttp又封装了线程池，封装了数据转换，封装了参数的使用，错误处理等。API使用起来更加的方便。但是我们在项目中使用的时候仍然需要自己在做一层封装，这样才能使用的更加的顺手。<br>
  **Volley**<br>
  Volley是Google官方出的一套小而巧的异步请求库，该框架封装的扩展性很强，支持HttpClient、HttpUrlConnection， 甚至支持OkHttp，而且Volley里面也封装了ImageLoader，所以如果你愿意你甚至不需要使用图片加载框架，不过这块功能没有一些专门的图片加载框架强大，对于简单的需求可以使用，稍复杂点的需求还是需要用到专门的图片加载框架。Volley也有缺陷，比如不支持post大数据，所以不适合上传文件。不过Volley设计的初衷本身也就是为频繁的、数据量小的网络请求而生。<br>
  **Retrofit**<br>
  Retrofit是Square公司出品的默认基于OkHttp封装的一套RESTful网络请求框架，RESTful是目前流行的一套api设计的风格， 并不是标准。Retrofit的封装可以说是很强大，里面涉及到一堆的设计模式,可以通过注解直接配置请求，可以使用不同的http客户端，虽然默认是用http ，可以使用不同Json Converter 来序列化数据，同时提供对RxJava的支持，使用Retrofit + OkHttp + RxJava + Dagger2 可以说是目前比较潮的一套框架，但是需要有比较高的门槛。
18. Http请求中包含哪些内容？，上传文件和发起请求的post请求的区别？
  > request请求数据包含:url,method(Get,POST,PUT,PATCH,DELETE),header,param,query<br>
  response响应数据包含:header(content-type),body<br>
  区别在于requestHeader的content-type和requestbody不同:<br>
  上传文件的post请求，header的`content-type`为`multipart/form-data`；requestbody为`FormData`数据<br>
  发起请求的post，header的`content-type`为`application/x-www-form-urlencoded`；requestbody为`key1=value1&key2=value2`数据
19. Android异步消息处理机制？
  > 异步消息处理机制主要是用来解决子线程更新UI的问题<br>
    主要有四个部分：<br>
    ①. Message （消息）<br>
    在线程之间传递，可在内部携带少量信息，用于不同线程之间交换数据
    可以使用what、arg1、arg2字段携带整型数据
    obj字段携带Object对象<br>
    ②. Handler （处理者）<br>
    主要用于发送和处理消息，sendMessage（）用来发送消息，最终会回到handleMessage（）进行处理<br>
    ③. MessageQueue （消息队列）<br>
    主要存放所有通过Handler发送的消息，它们会一直存在于队列中等待被处理
    每个线程只有一个MessageQueue<br>
    ④. Looper （循环器）<br>
    调用loop（）方法后，会不断从MessageQueue 取出待处理的消息，然后传递到handleMessage进行处理
20. ANR是什么？怎样避免和解决ANR？
  > **Application Not Responding**<br>
    避免ANR最核心的一点就是在主线程减少耗时操作。通常需要从那个以下几个方案下手：<br>
    a）使用子线程处理耗时IO操作<br>
    b）降低子线程优先级，使用Thread或者HandlerThread时，调用Process.setThreadPriority（Process.THREAD_PRIORITY_BACKGROUND）设置优先级，否则仍然会降低程序响应，因为默认Thread的优先级和主线程相同<br>
    c）使用Handler处理子线程结果，而不是使用Thread.wait()或者Thread.sleep()来阻塞主线程<br>
    d）Activity的onCreate和onResume回调中尽量避免耗时的代码<br>
    e）BroadcastReceiver中onReceiver代码也要尽量减少耗时操作，建议使用intentService处理。intentService是一个异步的，会自动停止的服务，很好解决了传统的Service中处理完耗时操作忘记停止并销毁Service的问题
21. RecyclerView和ListView的区别？
  > RecyclerView可以完成ListView,GridView的效果，还可以完成瀑布流的效果。同时还可以设置列表的滚动方向（垂直或者水平）；<br>
  RecyclerView中view的复用不需要开发者自己写代码，系统已经帮封装完成了。<br>
  RecyclerView可以进行局部刷新。<br>
  RecyclerView提供了API来实现item的动画效果。<br>
  在性能上：<br>
  如果需要频繁的刷新数据，需要添加动画，则RecyclerView有较大的优势。<br>
  如果只是作为列表展示，则两者区别并不是很大。
22. 如何在服务器接口未准备好的情况下调试接口？
  > 提前定义接口格式，比如swagger.yaml等，并使用模拟数据服务开发调试
23. 有没有了解过以下语言/包/技术/框架？（了解的请勾选）
    - [X] Cordova
    - [ ] Reative Native
    - [ ] flutter
    - [ ] Kotlin
    - [ ] Android Jetpack
    - [ ] AndroidX
    - [X] Retrofit + RxJava

#### 学习/职业经历相关
1. Java语言用了多久？开发入门方式是怎样的？（大学课程、网络自学、培训机构）
2. 对自己的职业能力与方向大致判断下？感兴趣的有哪些技术方向（网页收藏、博客或公众号订阅）？职业规划是怎样的？
3. 自身经历中用过哪些开发工具？自学过或试用过哪些技术？分别是何种渠道自学的？
4. 在工作中做过的自认为比较有挑战的需求是什么，是怎么实现的？
5. 简单列举下几个工作中遇到的Bug是怎样的，及解决过程？
6. 在团队中担任怎样的角色，团队的开发流程是怎样的？
7. 有开源项目吗？参与的角色？
8. 平时怎么获取新知识？
9. 有没有自己独立负责技术架构与选型的项目？选择依据有哪些？
10. 有没有用过/熟悉的Linux系统命令行工具？哪些？
11. 有没有阅读过英文文档（README或documents），打开看看？

#### REF
- [java常见面试题](https://www.shiqidu.com/d/763)
- [2019最新Android面试题](https://blog.csdn.net/wen_haha/article/details/88362469)
- [Android 2019届应届生最新面试题](https://blog.csdn.net/he372926/article/details/82108627)
- [Java基础：设计模式](https://blog.csdn.net/Future_LL/article/details/88368415)

### Android Studio开发任务

请在此[项目源码](https://gitlab.com/shanquan/interview)基础上完成sample页面的开发（用java，不能用html/js），实现与sample2页面相同的功能，组件与交互也需相同，主题外观不必完全一致。

### 2020-06-22 My First Interview

面试准备工作参见项目
内网：
svn: http://10.6.78.246:8080/svn/repo1/interview --username yy --password 1234
git: http://10.12.5.188:10023/appdev/interview
git(private): https://gitlab.com/byd1/appdev/interview

svn项目里的README.md是只有题目没有答案，给面试者看的；
git项目里的master分支是面试者版本，answer分支是面试官版本；

以下链接是每个题目带答案的，面试官看的~~（由vscode的Markdown All in One插件根据answer分支中的README.md文件自动生成）
https://devdata-1252923336.cos.ap-guangzhou.myqcloud.com/html/README-answer.html 

参考README.md文档，设置题型如下：
1.	Java Android基本知识考察，包含题型及数目如下：
题目编号1、2：Java语言基本数据类型（2题）
题3：面向对象(泛型、抽象类、内部类、接口)的考题
题4：设计模式
题5：排序算法
题6~12：Android界面及基础组件
题14~18：Http接口及请求
题19~24：项目经验、开发技能综合考察
2.	学习、工作经历、发展能力了解及考察，参见学习/职业经历相关问题类
3.	真实项目演练，参见Android Studio开发任务，考查开发工具使用（svn/androidStudio）、需求理解与沟通能力、代码阅读与理解能力

宋涛面试详情及评价：
1.	电话面试，自述有Android PDA和ERP软件开发经验，查考了Android基础知识和网络请求知识，题6、7、8、9、10、11、12、13、14、15、18、19均回答正确
2.	项目演练遇到一些小插曲，由于各种限制虽然未能完成项目中的考题代码，但是能看出具备Android Studio开发经验、代码阅读和理解能力也OK，遇到问题多次主动与我沟通，在需求理解和沟通能力上也符合。其Android PDA开发的工作经历确属实。
3.	初步评估能力和经验属于初级Android开发，所以很多较深的问题没有详谈，但具有发展潜力。

小插曲及经验：
1.	电话面试的时候，我提醒过他过来面试的时候我们会用Macbook Air，如果之前没有使用经验建议提前了解下。但是面试时他对Mac操作还是遇到不少问题
2.	来的过程中，让我等了将近1个小时，虽然有提前30分钟告诉我司机导航错了路可能会晚点，但结合前一点也说明了面试前的准备不足或对此次面试不够重视
3.	我提前在Mac上创建了新用户，面试前将svn示例项目下载并跑了一遍，但是万没想到会议室连不了办公网（咨询了前台，说网线有办公网，wifi是外网，但Macbook没有网口只能连wifi），所以没有办法连办公网，不能考查svn工具使用，好在我提前有准备Plan B，最终还是将项目下载成功。
4.	示例项目为在我们自己的Android开发框架下搭建的Android\Webview混合开发设计，Webview提供网页界面作为需求，要求用Java语言重写Webview中的界面和调接口处理，实现一致的功能
5.	15点开始的面试，由于迟到正式面试开始时间为16点左右，于是直接让他写代码，由于Mac不熟练他多次反馈缺少鼠标操作不变（这点我确实没有考虑到），为节约时间我在他边写代码边问他工作经历情况。而且面试途中他有一个来电，是其他公司的面试邀请电话，他居然还和别的公司完成了一次电面约好了时间（我很无语）。尽管如此，他遇到问题马上与我沟通，开发中按步骤开发、测试与验证，足以证明Android PDA开发经验和能力。
6.	由于项目实操本来属于开卷（可以联网查资料），而且时间不好把控。如面试者居住地离大亚湾较远，建议可以将电话面试步骤改为视频面试，增加沟通效果，并提供示例项目给面试者，如面试者提交项目代码并代码运行OK，理论上可以免去实操，如公司流程不允许也可由宝龙同事代为完成人事要求的实地面试步骤。以便高效率招到合适人才。

由于是首次做面试官，费了不少时间和功夫，也获得了一些经验，故分享给各位（JAVA面试官们）。 