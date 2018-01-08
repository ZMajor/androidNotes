#服务两种启动方式
* startService：服务被启动之后，跟启动它的组件没有一毛钱关系
* bindService：跟启动它的组件同生共死
* 绑定服务和解绑服务的生命周期方法：onCreate->onBind->onUnbind->onDestroy

---
#找领导办证
* 把服务看成一个领导，服务中有一个banZheng方法，如何才能访问？
* 绑定服务时，会触发服务的onBind方法，此方法会返回一个Ibinder的对象给MainActivity，通过这个对象访问服务中的方法
* 绑定服务

		Intent intent = new Intent(this, BanZhengService.class);
    	bindService(intent, conn, BIND_AUTO_CREATE);
* 绑定服务时要求传入一个ServiceConnection实现类的对象
* 定义这个实现类

		class MyServiceconn implements ServiceConnection{
		@Override
		public void onServiceConnected(ComponentName name, IBinder service) {
			zjr = (PublicBusiness) service;
		}
		@Override
		public void onServiceDisconnected(ComponentName name) {	
		}
    	
    }
* 创建实现类对象

		 conn = new MyServiceconn();
* 在服务中定义一个类实现Ibinder接口，以在onBind方法中返回

		class ZhongJianRen extends Binder implements PublicBusiness{
		public void QianXian(){
			//访问服务中的banZheng方法
			BanZheng();
		}	
		public void daMaJiang(){
			
		}
	}
* 把QianXian方法抽取到接口PublicBusiness中定义

---
#两种启动方法混合使用
* 用服务实现音乐播放时，因为音乐播放必须运行在服务进程中，可是音乐服务中的方法，需要被前台Activity所调用，所以需要混合启动音乐服务
* 先start，再bind，销毁时先unbind，在stop

---
##使用服务注册广播接收者
* Android四大组件都要在清单文件中注册
* 广播接收者比较特殊，既可以在清单文件中注册，也可以直接使用代码注册
* 有的广播接收者，必须代码注册
	* 电量改变
	* 屏幕锁屏和解锁

* 注册广播接收者

		//创建广播接收者对象
		receiver = new ScreenOnOffReceiver();
		//通过IntentFilter对象指定广播接收者接收什么类型的广播
		IntentFilter filter = new IntentFilter();
		filter.addAction(Intent.ACTION_SCREEN_OFF);
		filter.addAction(Intent.ACTION_SCREEN_ON);
		
		//注册广播接收者
		registerReceiver(receiver, filter);
* 解除注册广播接收者

		unregisterReceiver(receiver);
* 解除注册之后，广播接收者将失去作用

##本地服务：服务和启动它的组件在同一个进程
##远程服务：服务和启动它的组件不在同一个进程
* 远程服务只能隐式启动，类似隐式启动Activity，在清单文件中配置Service标签时，必须配置intent-filter子节点，并指定action子节点

#AIDL
* Android interface definition language
* 安卓接口定义语言
* 作用：跨进程通信
* 应用场景：远程服务中的中间人对象，其他应用是拿不到的，那么在通过绑定服务获取中间人对象时，就无法强制转换，使用aidl，就可以在其他应用中拿到中间人类所实现的接口

##支付宝远程服务
1. 定义支付宝的服务，在服务中定义pay方法
2. 定义中间人对象，把pay方法抽取成接口
3. 把抽取出来的接口后缀名改成aidl
4. 中间人对象直接继承Stub对象
5. 注册这个支付宝服务，定义它的intent-Filter

##需要支付的应用
1. 把刚才定义好的aidl文件拷贝过来，注意aidl文件所在的包名必须跟原包名一致
2. 远程绑定支付宝的服务，通过onServiceConnected方法我们可以拿到中间人对象
3. 把中间人对象通过Stub.asInterface方法强转成定义了pay方法的接口
4. 调用中间人的pay方法

##五种前台进程
1. activity执行了onresume方法，获得焦点
2. 拥有一个跟正在与用户交互的activity绑定的服务
3. 拥有一个服务执行了startForeground()方法
4. 拥有一个正在执行onCreate()、onStart()或者onDestroy()方法中的任意一个的服务
5. 拥有一个正在执行onReceive方法的广播接收者

##两种可见进程
1. activity执行了onPause方法，失去焦点，但是可见
2. 拥有一个跟可见或前台activity绑定的服务