# Android App架构设计

开发Android App的时候，项目负责人经常会苦恼使用什么架构的设计？

* 我的App需要应用哪些设计架构
* MVC、MVP等架构讲的是什么？区别是什么？

# 1.架构设计的目的 {#0}

通过设计使程序模块化，做到模块内部的高聚合和模块间的低耦合。这样做的好处是使得程序开发过程中，开发人员只需要专注于当前业务，提高程序开发的效率，并且更容易进行后续的测试以及定位问题。但设计不能违背目的，对于不同量级的工程，具体架构的实现方式必然不同的。

# 2.MVC设计架构 {#1}

![](/assets/中心主题.png)



## 2.1 MVC简介 {#2}

MVC全名是Model View Controller，如图，是模型\(model\)－视图\(view\)－控制器\(controller\)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。

其中M层处理数据，业务逻辑等；V层处理界面的显示结果；C层起到桥梁的作用，来控制V层和M层通信以此来达到分离视图显示和业务逻辑层。



## 2.2 Android中的MVC {#3}

Android中界面部分也采用了当前比较流行的MVC框架，在Android中：

* 视图层\(View\)

一般采用XML文件进行界面的描述，这些XML可以理解为AndroidApp的View。使用的时候可以非常方便的引入。同时便于后期界面的修改。逻辑中与界面对应的id不变化则代码不用修改，大大增强了代码的可维护性。

* 控制层\(Controller\)

Android的控制层的重任通常落在了众多的Activity的肩上。这句话也就暗含了不要在Activity中写代码，要通过Activity交割Model业务逻辑层处理，这样做的另外一个原因是Android中的Actiivity的响应时间是5s，如果耗时的操作放在这里，程序就很容易被回收掉。

* 模型层\(Model\)

我们针对业务模型，建立的数据结构和相关的类，就可以理解为AndroidApp的Model，Model是与View无关，而与业务相关的。对数据库的操作、对网络等的操作都应该在Model里面处理，当然对业务计算等操作也是必须放在的该层的。就是应用程序中二进制的数据。

```java
public class MainActivity extends ActionBarActivity implements OnWeatherListener, View.OnClickListener {

    private WeatherModel weatherModel;
    private EditText cityNOInput;
    private TextView city;
    ...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        weatherModel = new WeatherModelImpl();
        initView();
    }

    //初始化View
    private void initView() {
        cityNOInput = findView(R.id.et_city_no);
        city = findView(R.id.tv_city);
        ...
        findView(R.id.btn_go).setOnClickListener(this);
    }

    //显示结果
    public void displayResult(Weather weather) {
        WeatherInfo weatherInfo = weather.getWeatherinfo();
        city.setText(weatherInfo.getCity());
        ...
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_go:
                weatherModel.getWeather(cityNOInput.getText().toString().trim(), this);
                break;
        }
    }

    @Override
    public void onSuccess(Weather weather) {
        displayResult(weather);
    }

    @Override
    public void onError() {
        Toast.makeText(this, 获取天气信息失败, Toast.LENGTH_SHORT).show();
    }

    private T findView(int id) {
        return (T) findViewById(id);
    }
}
```

从上面代码可以看到，Activity持有了WeatherModel模型的对象，当用户有点击Button交互的时候，Activity作为Controller控制层读取View视图层EditTextView的数据，然后向Model模型发起数据请求，也就是调用WeatherModel对象的方法 getWeather（）方法。当Model模型处理数据结束后，通过接口OnWeatherListener通知View视图层数据处理完毕，View视图层该更新界面UI了。然后View视图层调用displayResult（）方法更新UI。至此，整个MVC框架流程就在Activity中体现出来了。



**Model模型**

来看看WeatherModelImpl代码实现

```java
public interface WeatherModel {
    void getWeather(String cityNumber, OnWeatherListener listener);
}

................

public class WeatherModelImpl implements WeatherModel {
    /*这部分代码范例有问题，网络访问不应该在Model中，应该把网络访问换成从数据库读取*/
    @Override
    public void getWeather(String cityNumber, final OnWeatherListener listener) {

        /*数据层操作*/
        VolleyRequest.newInstance().newGsonRequest(http://www.weather.com.cn/data/sk/ + cityNumber + .html,
                Weather.class, new Response.Listener<weather>() {
                    @Override
                    public void onResponse(Weather weather) {
                        if (weather != null) {
                            listener.onSuccess(weather);
                        } else {
                            listener.onError();
                        }
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        listener.onError();
                    }
                });
    }
}
```

以上代码看出，这里设计了一个WeatherModel模型接口，然后实现了接口WeatherModelImpl类。controller控制器Activity调用WeatherModelImpl类中的方法发起网络请求，然后通过实现OnWeatherListener接口来获得网络请求的结果通知后View视图层更新UI。至此，Activity就将View视图显示和Model模型数据处理隔离开了。activity担当Controller完成了model和view之间的协调作用。

至于这里为什么不直接设计成类里面的一个getWeather\(\)方法直接请求网络数据？你考虑下这种情况：现在代码中的网络请求是使用Volley框架来实现的，如果哪天老板非要你使用Afinal框架实现网络请求，你怎么解决问题？难道是修改 getWeather（）方法的实现？ no no no，这样修改不仅破坏了以前的代码，而且还不利于维护， 考虑到以后代码的扩展和维护性，我们选择设计接口的方式来解决着一个问题，我们实现另外一个WeatherModelWithAfinalImpl类，继承自WeatherModel，重写里面的方法，这样不仅保留了以前的WeatherModelImpl类请求网络方式，还增加了WeatherModelWithAfinalImpl类的请求方式。Activity调用代码无需要任何修改。

## MVC的缺点 {#6}

在Android开发中，Activity并不是一个标准的MVC模式中的Controller，它的首要职责是加载应用的布局和初始化用户 界面，并接受并处理来自用户的操作请求，进而作出响应。随着界面及其逻辑的复杂度不断提升，Activity类的职责不断增加，以致变得庞大臃肿，

为此我们引入MVP框架。

# 3.MVP设计架构 {#5}

在App开发过程中，经常出现的问题就是某一部分的代码量过大，虽然做了模块划分和接口隔离，但也很难完全避免。从实践中看到，这更多的出现在UI部分，也就是Activity里。想象一下，一个2000+行以上基本不带注释的Activity，我的第一反应就是想吐。Activity内容过多的原因其实很好解释，因为Activity本身需要担负与用户之间的操作交互，界面的展示，不是单纯的Controller或View。而且现在大部分的Activity还对整个App起到类似iOS中的【ViewController】的作用，这又带入了大量的逻辑代码，造成Activity的臃肿。为了解决这个问题，让我们引入MVP框架。

## MVP简介 {#7}

MVP从更早的MVC框架演变过来，与MVC有一定的相似性：Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。如图：

![](/assets/View.png)



MVP框架由3部分组成：View负责显示，Presenter负责逻辑处理，Model提供数据。在MVP模式里通常包含3个要素（加上View interface是4个）：

* View:负责绘制UI元素、与用户进行交互\(在Android中体现为Activity\)

* Model:负责存储、检索、操纵数据\(有时也实现一个Model interface用来降低耦合\)

* Presenter:作为View与Model交互的中间纽带，处理与用户交互的负责逻辑。

* View interface:需要View实现的接口，View通过View interface与Presenter进行交互，降低耦合，方便进行单元测试

> Tips：\*View interface的必要性
>
> 回想一下你在开发Android应用时是如何对代码逻辑进行单元测试的？是否每次都要将应用部署到Android模拟器或真机上，然后通过模拟用 户操作进行测试？然而由于Android平台的特性，每次部署都耗费了大量的时间，这直接导致开发效率的降低。而在MVP模式中，处理复杂逻辑的Presenter是通过interface与View\(Activity\)进行交互的，这说明我们可以通过自定义类实现这个interface来模拟Activity的行为对Presenter进行单元测试，省去了大量的部署及测试的时间。

## MVC → MVP {#8}

当我们将Activity复杂的逻辑处理移至另外的一个类（Presenter）中时，Activity其实就是MVP模式中的View，它负责UI元素的初始化，建立UI元素与Presenter的关联（Listener之类），同时自己也会处理一些简单的逻辑（复杂的逻辑交由 Presenter处理）。

MVP的Presenter是框架的控制者，承担了大量的逻辑操作，而MVC的Controller更多时候承担一种转发的作用。因此在App中引入MVP的原因，是为了将此前在Activty中包含的大量逻辑操作放到控制层中，避免Activity的臃肿。

两种模式的主要区别：

* （最主要区别）View与Model并不直接交互,而是通过与Presenter交互来与Model间接交互。而在MVC中View可以与Model直接交互
* 通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑。而Controller是基于行为的，并且可以被多个View共享，Controller可以负责决定显示哪个View
* Presenter与View的交互是通过接口来进行的，更有利于添加单元测试。

![](http://pic001.cnblogs.com/images/2012/1/2012040113391482.jpg)

因此我们可以发现MVP的优点如下：

1、模型与视图完全分离，我们可以修改视图而不影响模型；

2、可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter内部；

3、我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁；

4、如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）。

具体到Android App中，一般可以将App根据程序的结构进行纵向划分，根据MVP可以将App分别为模型层\(M\)，UI层\(V\)和逻辑层\(P\)。

UI层一般包括Activity，Fragment，Adapter等直接和UI相关的类，UI层的Activity在启动之后实例化相应的Presenter，App的控制权后移，由UI转移到Presenter，两者之间的通信通过BroadCast、Handler或者接口完成，只传递事件和结果。

举个简单的例子，UI层通知逻辑层（Presenter）用户点击了一个Button，逻辑层（Presenter）自己决定应该用什么行为进行响应，该找哪个模型（Model）去做这件事，最后逻辑层（Presenter）将完成的结果更新到UI层。

## MVP代码实例 {#11}

建立Bean

```JAVA
public class UserBean {
     private String mFirstName;
     private String mLastName;
     public UserBean(String firstName, String lastName) {
            this. mFirstName = firstName;
            this. mLastName = lastName;
     }
     public String getFirstName() {
            return mFirstName;
     }
     public String getLastName() {
            return mLastName;
     }
}
```

建立Model

（处理业务逻辑，这里指数据读写），先写接口，后写实现

```JAVA
public interface IUserModel {
     void setID(int id);

     void setFirstName(String firstName);

     void setLastName(String lastName);

     int getID();

     UserBean load(int id);// 通过id读取user信息,返回一个UserBean
}
```

Presenter控制器

建立presenter（主导器，通过iView和iModel接口操作model和view），activity可以把所有逻辑给presenter处理，这样java逻辑就从手机的activity中分离出来。

```JAVA
public class UserPresenter {
     private IUserView mUserView;
     private IUserModel mUserModel;

     public UserPresenter(IUserView view) {
            mUserView = view;
            mUserModel = new UserModel();
     }

     public void saveUser( int id, String firstName, String lastName) {
            mUserModel.setID(id);
            mUserModel.setFirstName(firstName);
            mUserModel.setLastName(lastName);
     }

     public void loadUser( int id) {
           UserBean user = mUserModel.load(id);
            mUserView.setFirstName(user.getFirstName()); // 通过调用IUserView的方法来更新显示
            mUserView.setLastName(user.getLastName());
     }
}

```

View视图

建立view（更新ui中的view状态），这里列出需要操作当前view的方法，也是接口

```JAVA
public interface IUserView {
     int getID();

     String getFristName();

     String getLastName();

     void setFirstName(String firstName);

     void setLastName(String lastName);
}
```

activity中实现iview接口，在其中操作view，实例化一个presenter变量。

```JAVA
public class MainActivity extends Activity implements OnClickListener,IUserView {

     UserPresenter presenter;
     EditText id,first,last;
     @Override
     protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
           setContentView(R.layout. activity_main);
           
           findViewById(R.id. save).setOnClickListener( this);
           findViewById(R.id. load).setOnClickListener( this);
            id = (EditText) findViewById(R.id. id);
            first = (EditText) findViewById(R.id. first);
            last = (EditText) findViewById(R.id. last);
           
            presenter = new UserPresenter( this);
     }

     @Override
     public void onClick(View v) {
            switch (v.getId()) {
            case R.id. save:
                 presenter.saveUser(getID(), getFristName(), getLastName());
                 break;
            case R.id. load:
                 presenter.loadUser(getID());
                 break;
            default:
                 break;
           }
     }

     @Override
     public int getID() {
            return new Integer( id.getText().toString());
     }

     @Override
     public String getFristName() {
            return first.getText().toString();
     }

     @Override
     public String getLastName() {
            return last.getText().toString();
     }

     @Override
     public void setFirstName(String firstName) {
            first.setText(firstName);
     }

     @Override
     public void setLastName(String lastName) {
            last.setText(lastName);
     }

}
```



## MVVM 架构 {#13}

MVVM可以算是MVP的升级版，其中的VM是ViewModel的缩写，ViewModel可以理解成是View的数据模型和Presenter的合体，ViewModel和View之间的交互通过Data Binding完成，而Data Binding可以实现双向的交互，这就使得视图和控制层之间的耦合程度进一步降低，关注点分离更为彻底，同时减轻了Activity的压力。如下图：

![](/assets/Viewmodel.png)



## MVC-&gt;MVP-&gt;MVVM演进过程 {#14}

MVC -&gt; MVP -&gt; MVVM 这几个软件设计模式是一步步演化发展的，MVVM 是从 MVP 的进一步发展与规范，MVP 隔离了MVC中的 M 与 V 的直接联系后，靠 Presenter 来中转，所以使用 MVP 时 P 是直接调用 View 的接口来实现对视图的操作的，这个 View 接口的东西一般来说是 showData、showLoading等等。M 与 V已经隔离了，方便测试了，但代码还不够优雅简洁，所以 MVVM 就弥补了这些缺陷。在 MVVM 中就出现的 Data Binding 这个概念，意思就是 View 接口的 showData 这些实现方法可以不写了，通过 Binding 来实现。



如果把这三者放在一起比较，先说一下三者的共同点，也就是Model和View：

* Model：数据对象，同时，提供本应用外部对应用程序数据的操作的接口，也可能在数据变化时发出变更通知。Model不依赖于View的实现，只要外部程序调用Model的接口就能够实现对数据的增删改查。

* View：UI层，提供对最终用户的交互操作功能，包括UI展现代码及一些相关的界面逻辑代码。



三者的差异在于如何粘合View和Model，实现用户的交互操作以及变更通知

* Controller

Controller接收View的操作事件，根据事件不同，或者调用Model的接口进行数据操作，或者进行View的跳转，从而也意味着一个Controller可以对应多个View。Controller对View的实现不太关心，只会被动地接收，Model的数据变更不通过Controller直接通知View，通常View采用观察者模式监听Model的变化。

* Presenter

Presenter与Controller一样，接收View的命令，对Model进行操作；与Controller不同的是Presenter会反作用于View，Model的变更通知首先被Presenter获得，然后Presenter再去更新View。一个Presenter只对应于一个View。根据Presenter和View对逻辑代码分担的程度不同，这种模式又有两种情况：Passive View和Supervisor Controller。

* ViewModel

注意这里的“Model”指的是View的Model，跟MVVM中的一个Model不是一回事。所谓View的Model就是包含View的一些数据属性和操作的这么一个东东，这种模式的关键技术就是数据绑定（data binding），View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上。这种模式实际上是框架替应用开发者做了一些工作，开发者只需要较少的代码就能实现比较复杂的交互。



## 一点心得 {#17}

个人理解，在广义地谈论MVC架构时，并非指本文中严格定义的MVC，而是指的MV\*，也就是视图和模型的分离，只要一个框架提供了视图和模型分离的功能，我们就可以认为它是一个MVC框架。在开发深入之后，可以再体会用到的框架到底是MVC、MVP还是MVVM。



