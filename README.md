# SocketDemo
综合运用Socket+JDBC+IO，实现客户端登陆，向服务端上传文件，并保存在数据库中
###（本项目为javaSE项目，不涉及任何框架、前后端的知识）
## 1、项目分析<br>
主要通过分析项目需要实现的内容，来初步判断我们需要建立那些包。<br>
项目介绍：客户端向服务端注册账号，注册后登陆账号（也可以有账号直接登陆），登陆成功后向服务器端上传文件，服务器端收到后保存在数据库中。<br>

a、因为这是一个用socket建立客户端和服务端通信的例子，所以大概需要socket建立客户端和服务端的通信，通过他来实现客户端和服务端数据、信息交互。<br>
b、因为同时还要考虑多个用户登陆的情况，所以还需要在服务端启动循环监听，启动多线程，收到一个客户端发来的数据即要启动一个线程来处理数据，所以会用到多线程的技术。<br>
c、既然要上传文件，就要用到IO，同时将客户端发送来的信息保存在数据中就要用到数据库连接，也就是jdbc。<br>
以上就是大概需求的技术储备。<br>
## 2、搭建项目框架<br>
先给大家看我组织好的框架是什么样的<br>
 ! [image] (https://github.com/nize1989/pic/blob/master/SocketDemo.png) 
a、从最底层开始搭建，用户注册、登陆校验需要将用户名和密码保存在数据库中，所以需要在数据库中建立一张保存用户的表，主要有三个字段：id(自增字段),用户名和密码。
同时，用户登陆成功后需要上传文件，则需要另外一张表来保存文件。所以第一步，我们需要在数据库中建立如上两张表。数据库建立好后，需要在项目中建立两个它对应的实体类可以都放在entiy包下。<br>
b、建立好数据库后，我们需要建立一个与数据库交互的包，借用javaweb的话来说就是建立一个DAO包，下面只实现最基本的向数据库插入用户/文件、查询记录等功能。在这里需要用到与数据库连接的工具，我们单独拿出来建立一个DBUtils类，谁要与数据库连接调用它下面的方法就好了。<br>
c、然后我们根据实际来组织数据的Service逻辑，这个项目比较简单，组织逻辑和DAO差不多（较大型的项目组织逻辑较复杂，必须与操作数据的包分开），只是多了些逻辑判断。<br>
d、到这里我们可以写这个项目的关键部分，也就是客户端和服务端交互的部分了。在客户端里（指的是与服务端通信的类，我将与服务端通信的类与给客户展示的类分开，方便职责清晰的各司其职），主要包括三个板块：注册函数模块、登陆函数模块、发送和接收数据模块。三个板块的程序分别实现。比较重要的一点是：客户端和服务端之间传递的数据需要统一封装，就是将文件类、用户类数据、再加上发送附加信息和返回附加信息包装成一个类，由他统一在客户端和服务端之间传递。<br>
e、在服务端中循环监听，启动多线程，在多线程中决定调用Service，是调用userService还是调用fileService，是调用userService中的注册程序还是登陆程序，这个都交给多线程判断。并根据调用返回的数据修改返回数据类，最后将这个数据类返回给客户端。<br>
f、组织给客户展示的类的逻辑，也就是socketstart类，主要功能是：1、启动客户端；2、给用户端呈现交互的初始界面（后面请输入密码，用户名啥的，交给调用的具体函数实现）；<br>
3、组织登陆函数、注册函数、文件上传函数的调用逻辑。这个与上面的d里面的客户端通信类有交集，写的时候应该先分清楚再动手。<br>
## 3、分析到这里，我们就可开始写码砖啦（上面我是想的差不多才动手，有些很细节的地方也没想清楚，可以在写的过程中调整）<br>



