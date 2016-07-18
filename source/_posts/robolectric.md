---
title: Robolectric
date: 2016-07-18 17:41:07
description: 如何使用Robolectric进行单元测试
categories:
- 安卓
tags:
- 安卓
---
# Robolectric  
## 一、环境搭建
### Gradle配置  
#### 在build.gradle中的配置如下依赖关系：
<pre><code> 
testCompile 'junit:junit:4.12'
testCompile "org.robolectric:robolectric:3.0"
</code></pre>

或者  

```  
androidTestCompile 'junit:junit:4.12'
androidTestCompile "org.robolectric:robolectric:3.0"
```  

#### 自己测试时使用的Android SDK的配置

<pre><code>
android {
    compileSdkVersion 22
    buildToolsVersion "23.0.2"
    defaultConfig {
        applicationId "com.example.zhihengcui.address"
        minSdkVersion 14
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
</code></pre>

#### 自己测试时使用的Gradle版本

```
dependencies {
        classpath 'com.android.tools.build:gradle:2.0.0'
}
```

#### 通过注解配置TestRunner  
- **注意sdk = 21 的配置，目前经自己测试Robolectric3.0支持sdk-21比较稳定。**
![sdk配置错误图片](/Users/zhiheng.cui/Desktop/Robolectric/sdk_error1.png)
![sdk配置错误图片](/Users/zhiheng.cui/Desktop/Robolectric/sdk_error2.png)


```
@RunWith(RobolectricGradleTestRunner.class)  
@Config(constants = BuildConfig.class,sdk = 21)
public class NameTest{
}  
```  

#### 运行测试  

![运行测试图片](/Users/zhiheng.cui/Desktop/Robolectric/runtest.png)
## 二、测试举例  
#### 1.创建Activity实例 

- **Robolectric.setupActivity(MainActivity.class);**用来获取Activity实例  

```  
@Test  
public void testActivity(){
	MainActivity mainActivity = Robolectric.setupActivity(MainActivity.class);
	assertNotNull(mainActivity);
	assertEquals(mainActivity.getTitle(),"MainActivity");
}
```  
#### 2.生命周期  
- 例子

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    mTextView = (TextView) findViewById(R.id.main_text);
    setText("onCreate");
}
@Override
protected void onStart() {
    setText("onStart");
    super.onStart();
}
@Override
protected void onResume() {
    setText("onResume");
    super.onResume();
}
@Override
protected void onPause() {
    setText("onPause");
    super.onPause();
}
@Override
protected void onRestart() {
    setText("onRestart");
    super.onRestart();
}
@Override
protected void onStop() {
    setText("onStop");
    super.onStop();
}
@Override
protected void onDestroy() {
    setText("onDestroy");
    super.onDestroy();
}
public void setText(String str){
    mTextView.setText(str);
}
```  
- 通过ActivityController来控制生命周期的调用。
- **注意onRestart()调用之后会直接调用onStart()方法，所以测试onRestart()的时候会连续调用这两个方法，获取的测试值不是onRestart()而是onStart()**

```  
@Test
public void testLifecycle(){
	ActivityController<MainActivity> activityController = Robolectric.buildActivity(MainActivity.class).create();
	Activity activity = activityController.get();
	TextView textView = (TextView) activity.findViewById(R.id.main_text);
	Assert.assertEquals("onCreate",textView.getText().toString());
	activityController.start();
	Assert.assertEquals("onStart",textView.getText().toString());
	activityController.resume();
	Assert.assertEquals("onResume",textView.getText().toString());
	activityController.pause();
	Assert.assertEquals("onPause",textView.getText().toString());
	activityController.stop();
	Assert.assertEquals("onStop",textView.getText().toString());
//  activityController.restart();
//  Assert.assertEquals("onStart",textView.getText().toString());
    activityController.destroy();
    Assert.assertEquals("onDestroy",textView.getText().toString());
}  
```  
#### 3.Activity跳转 

```
TextView textView= (TextView) mainActivity.findViewById(R.id.add_tv);
textView.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View view) {
	  	if (view.getId() == R.id.add_tv) {
			Intent intent = new Intent(MainActivity.this, SecondActivity.class);
			startActivityForResult(intent, MAINACTIVITY_TO_SECONDACTIVITY);
		}
	}
});
```

- **textView.performClick();**用来触发点击事件。
- **ShadowActivity shadowActivity = Shadows.shadowOf(mainActivity);**用来获取mainActivity对应的ShadowActivity的instance。
- **shadowActivity.getNextStartedActivity();**用来获取mainActivity调用的startActivity的intent。
- 通过比较实际跳转的Intent和测试的Intent来判断是否跳转成功。

```  
@Test
public void testStartActivity(){
	MainActivity mainActivity = Robolectric.setupActivity(MainActivity.class);
    TextView textView= (TextView) mainActivity.findViewById(R.id.add_tv);
    textView.performClick();
	//测试Activity跳转是否成功
    ShadowActivity shadowActivity = Shadows.shadowOf(mainActivity);
   	Intent newIntent = shadowActivity.getNextStartedActivity();
   	Intent oldIntent = new Intent(mainActivity,SecondActivity.class);
    Assert.assertEquals(oldIntent,newIntent);
}
```
#### 4.Dialog  

```
//点击一个TextView,弹出对话框
new  AlertDialog.Builder(MainActivity.this)
				.setTitle("是否删除")
				.setPositiveButton("确定",null)
				.setNegativeButton("取消", null).create().show();
```
- **AlertDialog alertDialog = ShadowAlertDialog.getLatestAlertDialog();**获取点击后出现的对话框。

```
@Test
public void testDialog(){	
	//点击出现对话框
	textView.performClick();
	AlertDialog alertDialog = ShadowAlertDialog.getLatestAlertDialog();
    Assert.assertNotNull(alertDialog);
}
```
#### 5.Toast

```
 Toast.makeText(this,"we love UT",Toast.LENGTH_SHORT).show();
```
- **ShadowToast.getTextOfLatestToast()**获取Toast内容。  

```
@Test
public void testToast(){
	//点击出现Toast
	textView.performClick();
	assertEquals(ShadowToast.getTextOfLatestToast(),"we love UT");
}
```
- 下面是自定义Toast  
- 自定义的Toast界面是ImageView+TextView，这里布局代码就不贴出了

```
//点击按钮出现Toast
toastmakeText(this,"这是第"+position+"个",R.mipmap.ic_launcher);
public void toastmakeText(Context context, String str, int imageID) {
	LayoutInflater inflater = getLayoutInflater();
	View view = inflater.inflate(R.layout.toast_layout, (ViewGroup) findViewById(R.id.toast_root));
	ImageView imageView = (ImageView) view.findViewById(R.id.toast_image);
	imageView.setImageResource(imageID);
	TextView textView = (TextView) view.findViewById(R.id.toast_tv);
	textView.setText(str);
	Toast toast = new Toast(context);
	toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);
	toast.setDuration(Toast.LENGTH_SHORT);
	toast.setView(view);
	toast.show();
}
```  
- **ShadowToast.getLatestToast();获取Toast**

```
button.performClick();
Toast toast = ShadowToast.getLatestToast();
TextView tv = (TextView) toast.getView().findViewById(R.id.toast_tv);
Assert.assertTrue(tv.getText().toString().equals("这是第1个"));
```
#### 6.ListView  

```
//设置adapter，设置监听，点击Item后弹出Toast。
mContactListViewAdapter = new ContactListViewAdapter(mContactDatas, this);
mContactlistview.setAdapter(mContactListViewAdapter);
mContactlistview.setOnItemClickListener(this);
@Override
public void onItemClick(AdapterView<?> adapterView, View view, final int position, long l) {
	Toast.makeText(this,"这是第"+position+"行",Toast.LENGTH_SHORT).show();
}
```  

- **listView.performItemClick(listView.getAdapter().getView(2,null,null),2,0);**触发listView的点击事件，performItemClick的第二个参数是点击第几行的关键，设置之后才会点击到相应的位置。getView中的第一个参数表示点击的position，然后获取此位置的Item的View。

```  
@Test
public void testListView(){
	MainActivity mainActivity = Robolectric.setupActivity(MainActivity.class);
	//造数据
	List<String> mDatas = new ArrayList<>();
	for (int i=0;i<20;i++){
            mDatas.add(i+"");
    }
	//测试ListView是否点击
    ListView listView =(ListView)mainActivity.findViewById(R.id.list_view);
   	listView.setAdapter(new ListViewAdapter(mDatas,mainActivity));
	listView.performItemClick(listView.getAdapter().getView(2,null,null),2,0);
	//判断listView中的数据长度期望的数据长度是否相等。
	assertTrue(listView.getCount()==mDatas.size());
	//获取Toast的内容，判断与期望的值是否相等
	assertEquals(ShadowToast.getTextOfLatestToast(),"这是第2行");
}
```  
#### 7.日志输出
- 在被@Before标记的setUp()中设置**ShadowLog.stream = System.out;**之后Log.i()之类的日志都将会输出在控制面板中。

```
@Before
public void setUp() throws Exception {
    //输出日志
    ShadowLog.stream = System.out;
}
```
#### 8.Fragment测试
- 如果使用support-v4的Fragment，需添加以下依赖

```
testCompile 'org.robolectric:shadows-support-v4:3.0'
```  
- 否则报错如下

![报错图片][fragment_error]

- shadow-support包提供了将Fragment主动添加到Activity中的方法：**SupportFragmentTestUtil.startFragment();**  

```
@Test
public void testFragment(){
	SampleFragment sampleFragment = new SampleFragment();
 	//此api可以主动添加Fragment到Activity中，因此会触发Fragment的onCreateView()
	SupportFragmentTestUtil.startFragment(sampleFragment);
 	assertNotNull(sampleFragment.getView());
}
```
#### 9.访问资源文件

```
@Test
public void testResources() {
    Application application = RuntimeEnvironment.application;
    String activityTitle = application.getString(R.string.title_activity_simple);
    assertEquals("SimpleActivity",activityTitle);
}
```  
#### 10.BroadcastReceiver的测试
- 测试是否已经注册的时候，**注意AndroidManifest.xml中是否配置BroadcastReceiver**。 此action自定义的。

```
<receiver
    android:name=".broadcastreceiver.MyReceiver"
    android:enabled="true"
  	android:exported="false">
  	<intent-filter>
		<action android:name="android.intent.action.MY_BROADCAST" />
	</intent-filter>
</receiver>
```
- 自定义BroadcastReceiver中的逻辑  

```
public class MyReceiver extends BroadcastReceiver{
    @Override
    public void onReceive(Context context, Intent intent) {
        SharedPreferences.Editor editor = context.getSharedPreferences("my_receiver",0).edit();
        String name = intent.getStringExtra("name");
        editor.putString("name",name);
        editor.commit();
    }
}
```
- **shadowApplication.hasReceiverForIntent(intent);**测试广播是否注册成功

```
@Test
public void testFragmentTwo() throws Exception {
 	ShadowApplication shadowApplication = ShadowApplication.getInstance();
	String action = "android.intent.action.MY_BROADCAST";
	Intent intent = new Intent(action);
	intent.putExtra("name","heng");
	//测试是否已经注册广播接收者
	Assert.assertTrue(shadowApplication.hasReceiverForIntent(intent));
	//测试广播接收者的处理逻辑是否正确
	MyReceiver myReceiver = new MyReceiver();
	myReceiver.onReceive(RuntimeEnvironment.application,intent);
	SharedPreferences sharedPreferences = shadowApplication.getSharedPreferences("data",0);
	Assert.assertEquals("heng",sharedPreferences.getString("name",""));
}
```
#### 11.Service的测试  
- Service在AndroidManifest中配置

```
<service
            android:name=".service.MyService"
            android:exported="false" />
```
- 自定义的Service  

```
public class MyService extends IntentService {
    public MyService() {
        super("MyService");
    }
    @Override
    public void onHandleIntent(Intent intent) {
        SharedPreferences.Editor editor = getApplicationContext().getSharedPreferences("my_service", 0).edit();
        String phone = intent.getStringExtra("phone");
        Log.i("cui",phone);
        editor.putString("phone", phone);
        editor.commit();
    }
}
```

- 通过触发onHandleIntent()方法，用来验证Service启动后的逻辑是否正确。 
- **注意AndroidManifest.xml中是否配置service。**

```
@Test
public void testFragmentTwoService() throws Exception {
  	ShadowApplication shadowApplication = ShadowApplication.getInstance();
   	SharedPreferences preferences = shadowApplication.getSharedPreferences("my_service",0);
   	MyService myService = new MyService();
 	Intent intent = new Intent();
  	intent.putExtra("phone","666666");
    myService.onHandleIntent(intent);
    Assert.assertEquals(preferences.getString("phone",""),"666666");
}
```  
#### 12.测试真实网络请求测试  
- 测试时使用的网络请求框架是retrofit2。

```
@Before
public void setUp() throws Exception {
	ShadowLog.stream = System.out;
	//创建网络连接
	githubService = GithubService.Factory.create();
}
@Test
public void testHttpResponse() throws Exception {
	//请求数据
	Call<List<User>> call = githubService.followingUser("geniusmart");
	Response<List<User>> execute = call.execute();
	List<User> list =execute.body();
	//可输出完整的响应结果，帮助我们调试代码
 	Log.i("cui",new Gson().toJson(list));
	Assert.assertTrue(list.size()>0);
	Assert.assertNull(list.get(0).name);
}
```
#### 13.模拟网络请求
- 主要使用okhttp提供的拦截器 Interceptors ,通过该api，可以拦截网络请求，根据请求路径，不进行请求的发送，而直接返回我们自定义好的相应的response json字符串
- 首先，自定义的Interceptors的代码如下

```  
public class MockInterceptor implements Interceptor {
	//json数据的路径
    private final String responeJsonPath;
    public MockInterceptor(String responeJsonPath) {
        this.responeJsonPath = responeJsonPath;
    }
    @Override
    public Response intercept(Chain chain) throws IOException {
        String responseString = createResponseBody(chain);
        Response response = new Response.Builder()
                .code(200)
                .message(responseString)
                .request(chain.request())
                .protocol(Protocol.HTTP_1_0)
                .body(ResponseBody.create(MediaType.parse("application/json"), responseString.getBytes()))
                .addHeader("content-type", "application/json")
                .build();
        return response;
    }
    //读文件获取json字符串，生成ResponseBody
    private String createResponseBody(Chain chain) {
        String responseString = null;
        HttpUrl uri = chain.request().url();
        String path = uri.url().getPath();
        if (path.matches("^(/users/)+[^/]+(/following)$")) {
            responseString = getResponseString("users_following.json");
        }
        return responseString;
    }
    //获取本地json字符串
    private String getResponseString(String fileName) {
        return FileUtil.readFile(responeJsonPath + fileName, "UTF-8").toString();
    }
}
```
- 相应的resonse json的文件可以存放在test/resources/json/下，如下图  

   ![json文件路径][json_file_path]

- 再次，定义Http Client,并添加拦截器

```
// 此/json/对应的是建立的json文件夹
private static final String JSON_ROOT_PATH ="/json/";
@Before
public void setUp() throws Exception {
	//获取测试json文件的路径
	jsonFullPath = getClass().getResource(JSON_ROOT_PATH).toURI().getPath();
	//定义Http Client,并添加拦截器
	OkHttpClient okHttpClient = new OkHttpClient.Builder().
		addInterceptor(new MockInterceptor(jsonFullPath)).
		build();
	//设置Http Client
	Retrofit retrofit = new Retrofit.Builder()
		.baseUrl(GithubService.BASE_URL)
		.addConverterFactory(GsonConverterFactory.create())
		.client(okHttpClient)
    	.build();
	mockGithubService = retrofit.create(GithubService.class);
}
```
- 最后测试

```
@Test
public void testFollowingUser() throws Exception {
	//请求数据
	Response<List<User>> followingResponse = mockGithubService.followingUser("geniusmart").execute();
	Assert.assertEquals(followingResponse.body().get(0).login,"JakeWharton");
}
```











[fragment_error]:/Users/zhiheng.cui/Desktop/Robolectric/fragment_error.png
[json_file_path]:/Users/zhiheng.cui/Desktop/Robolectric/json.png


# 非本人原创