#Android项目的目录结构
* Activity：应用被打开时显示的界面
* src：项目代码
* R.java：项目中所有资源文件的资源id
* Android.jar：Android的jar包，导入此包方可使用Android的api
* libs：导入第三方jar包
* assets：存放资源文件，比方说mp3、视频文件
* bin：存放编译打包后的文件
* res：存放资源文件，存放在此文件夹下的所有资源文件都会生成资源id
* drawable：存放图片资源
* layout：存放布局文件，把布局文件通过资源id指定给activity，界面就会显示出该布局文件定义的布局
* menu：定义菜单的样式
* Strings.xml：存放字符串资源，每个资源都会有一个资源id

###Android的配置文件（清单文件）
* 指定应用的包名

		package="com.itheima.helloworld"
	* data/data/com.itheima.helloworld(上面代码指定的包名)
	* 应用生成的文件都会存放在此路径下

* Android的四大组件在使用前全部需要在清单文件中配置
* <Application/>的配置对整个应用生效
* <activity/>的配置对该activity生效

---
#DDMS
* Dalvik debug monitor service
* Dalvik调试监控服务

---
#常用的adb指令
###Android debug bridge：安卓调试桥
* adb start-server:启动adb进程
* adb kill-server：杀死adb进程
* adb devices：查看当前与开发环境连接的设备，此命令也可以启动adb进程
* adb install XXX.apk：往模拟器安装apk
* adb uninstall 包名：删除模拟器中的应用
* adb shell:进入linux命令行	
* ps：查看运行进程
* ls：查看当前目录下的文件结构
* netstat -ano：查看占用端口的进程

---------------
#电话拨号器
>功能：用户输入一个号码，点击拨打按钮，启动系统打电话的应用把号码拨打出去
###1. 定义布局
1. 组件必须设置宽高，否则不能通过编译

		android:layout_width="wrap_content"
        android:layout_height="wrap_content"
2. 如果要在java代码中操作某个组件，则组件需要设置id，这样才能在代码中通过id拿到这个组件

		android:id="@+id/et_phone"
###2. 给按钮设置点击侦听

1. 给按钮设置侦听

		 //通过id拿到按钮对象
        Button bt_call = (Button) findViewById(R.id.bt_call);
        //给按钮设置点击
        bt_call.setOnClickListener(new MyListener());

###3. 得到用户输入的号码

		//得到用户输入的号码，先拿到输入框组件
			EditText et_phone = (EditText) findViewById(R.id.et_phone);
			String phone = et_phone.getText().toString();

###4. 把号码打出去
1. Android系统中基于动作机制，来调用系统的应用，你告诉系统你想做什么动作，系统就会把能做这个动作的应用给你，如果没有这个应用，会抛异常
2. 设置动作，通过意图告知系统

		//把号码打出去
			//先创建一个意图对象
			Intent intent = new Intent();
			//设置动作，打电话
			intent.setAction(Intent.ACTION_CALL);
			intent.setData(Uri.parse("tel:" + phone));
			//把意图告诉系统
			startActivity(intent);

3. 添加权限

		<uses-permission android:name="android.permission.CALL_PHONE"/>

----------
#点击事件的四种写法
###第一种
* 定义一个MyListener实现onClickListener接口

		Button bt1 = (Button) findViewById(R.id.bt1);
        bt1.setOnClickListener(new MyListener());

###第二种
* 定义一个匿名内部类实现onClickListener接口

		Button bt2 = (Button) findViewById(R.id.bt2);
        bt2.setOnClickListener(new OnClickListener() {
			
			@Override
			public void onClick(View v) {
				System.out.println("第二种");
				
			}
		});

###第三种
* 让当前activity实现onClickListener接口

		Button bt3 = (Button) findViewById(R.id.bt3);
        bt3.setOnClickListener(this);

###第四种
* 给Button节点设置onClick属性，

		 android:onClick="click"
 
* 然后在activity中定义跟该属性值同名的方法

		public void click(View v){
			System.out.println("第四种");
		}

--------
#短信发送器
> 功能：用户输入号码和短信内容，点击发送按钮，调用短信api把短信发送给指定号码
###1. 定义布局
* 输入框的提示

		android:hint="请输入号码"  
###2. 完成点击事件
* 先给Button组件设置onClick属性
* 
		onClick="send"
* 在Activity中定义此方法
* 
		public void send(View v){}
###3. 获取到用户输入的号码和内容

		EditText et_phone = (EditText) findViewById(R.id.et_phone);
    	EditText et_content = (EditText) findViewById(R.id.et_content);
    	String phone = et_phone.getText().toString();
    	String content = et_content.getText().toString();
###4. 调用发送短信的api

		//调用发送短信的api
    	SmsManager sm = SmsManager.getDefault();
    	
    	//发送短信
    	sm.sendTextMessage(phone, null, content, null, null);
* 添加权限

		 <uses-permission android:name="android.permission.SEND_SMS"/>
* 如果短信过长，需要拆分

		List<String> smss = sm.divideMessage(content);

