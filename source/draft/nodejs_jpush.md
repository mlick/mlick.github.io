# 通过NodeJs+JPush实现安卓的异地登陆

因公司业务需要，需要实现一个异地登陆然后下线的功能，说的可能有点糊涂，可以先看下实现的效果图：

## 前期准备
- 下载[NodeJs](https://nodejs.org/en/)并安装
- 安装[JPush(极光)](https://github.com/jpush/jpush-api-nodejs-client)的nodejs版
- 下载[极光的Android SDK](http://docs.jiguang.cn/client/android_sdk/)
- 利用[极光账号](https://www.jiguang.cn/app/list)创建一个应用

废话就不多说，直接就上代码吧！

## 代码编写
### 利用npm安装JPush
- 方法一
```
npm install jpush-sdk
```
- 方法二
```
{
  "dependencies": {
    "jpush-sdk": "*"
  }
}
npm install
```

**注: 如果发现下载慢的情况，可以利用代理 **
```
npm install 后加 --registry http://registry.cnpmjs.org info underscore
```

### 编写服务器推送代码
```
var JPush = require("../lib/JPush/JPush.js");
var client = JPush.buildClient(your appkey, your app master secret);
client.push().setPlatform(JPush.ALL)
    .setAudience(JPush.ALL)
    .setNotification('异地登陆', JPush.android('你的账号在异地登陆了，请重新登陆。时间： ' + new Date().toLocaleString(), null, 1, {type: 'kick'}))
    .send(function(err, res) {
        if (err) {
            console.log(err.message);
        } else {
            console.log('Sendno: ' + res.sendno);
            console.log('Msg_id: ' + res.msg_id);
        }
    });
```
由于我这里只用到了安卓端，所以`Notification`里我只设置了安卓端的，其它参数就不解释了，具体的参数详情可以[查看官方Api文档](https://github.com/jpush/jpush-api-nodejs-client/blob/master/doc/api.md)

### 编写安卓端的代码
安卓端的代码可以直接在极光官网上下载demo然后继承到项目中去，这里只说几点特别注意的地方。
- 最好在项目的`Application`中进行初始化
```
public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        JPushInterface.setDebugMode(true);    // 设置开启日志,发布时请关闭日志
        JPushInterface.init(this);            // 初始化 JPush
    }
}
```

- 最好在`MainActivity的onResume()`中添加如下代码：
```
   @Override
    protected void onResume() {
        super.onResume();
        isForeground = true;
        JPushInterface.resumePush(getApplicationContext());
    }
```

- 在接收到消息要弹框时，最好要先判断一下应用是否在前端运行
```
 private void processPushMessage(Context context, Bundle bundle) {
        if (!MainActivity.isForeground) {// 不在前台的话，就不弹框
            return;
        }
        String extras = bundle.getString(JPushInterface.EXTRA_EXTRA);
        try {
            JSONObject jsonObject = new JSONObject(extras);
            if ("kick".equals(jsonObject.getString("type"))) {
                Intent pushIntent = new Intent(context, NotifyDialogActivity.class);
                pushIntent.putExtra("bundle", bundle);
                pushIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                context.startActivity(pushIntent);
            }
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```
- 注意包名在`AndroidManifest`要保持一致，否则可能会接收不到消息
- 注意不仅包名和平台的包名一致`AndroidManifest中的key`也要和自己在平台上声明的`key`保持一致



- 还有就是这里的弹框不是`Dialog`而是`DialogActivity`，详情可以查看[我的项目源码](https://github.com/mlick/LiteLibrary)
