# AIDL不同应用内进程通信

上一章我们讲述了同一应用下的进程通信,本章我们要讲的是不同应用内的进程通信。

##### 服务端

首先我们把Food.java文件放到aidl文件夹中，然后在build.gradle文件中的android{},加入如下代码：

```java
sourceSets{
        main{
            java.srcDirs=['src/main/java', 'src/main/aidl']
        }
    }
```

接着我们把service增加intent-filter中，这样应用就能通过隐式意图启动服务。

```java
 <service
            android:name=".AIDLService"
            android:process=":aidlserivce">
            <intent-filter>
                <action android:name="action.aidltest"/>
            </intent-filter>
            </service>
```

##### 客户端

1.我们要创建一个新的应用，然后把服务端的aidl文件夹copy到客户端的代码结构中，这时，aidl文件夹的目录不能修改。

点击同步，Androidstudio会自动生成相应的java文件供我们使用。

接下来我们就可以用隐式意图来启动服务了。

2.创建一个和原来一样的ServiceConnection，如下图:

```java
/**
 * Created by huangdaju on 17/8/8.
 */

public class MyServiceConnection implements ServiceConnection {

    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        IMyAidlInterface iMyAidlInterface = IMyAidlInterface.Stub.asInterface(service);
        try {
            Food food = new Food();
            food.setName("花椰菜");
            food.setPrice(1.5f);
            iMyAidlInterface.setFood(food);
            Food food1 = iMyAidlInterface.danner();
            System.out.println("food1:" + food1.toString());
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {

    }
}
```

3.绑定服务，bindService\(mIntent, mXiayuConnection, BIND\_AUTO\_CREATE\),如下代码：

```java

public class MainActivity extends AppCompatActivity {
    MyServiceConnection myServiceConnection = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Intent intent = new Intent();
        intent.setAction("action.aidltest");
        intent.setPackage("com.zhiping.alibaba.aidltest");
        myServiceConnection = new MyServiceConnection();
        bindService(intent, myServiceConnection, BIND_AUTO_CREATE);
    }

    @Override
    protected void onDestroy() {
        if (myServiceConnection != null) { //关闭activity时，解除服务绑定
            unbindService(myServiceConnection);
            myServiceConnection = null;
        }
        super.onDestroy();

    }
}
```



