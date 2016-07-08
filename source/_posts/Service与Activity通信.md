---
title: Service与Activity通信
date: 2016-06-14 09:54:17
tags: Service
---

#### 主要方法
- 1.`bindService`

启动的服务可以得到一个Service的一个对象实例，然后我们就可以访问Service中的方法,停止服务使用`unbindService`

- 2.`startService`

启动一个服务执行后台任务，可以用广播的方式返回数据。停止服务使用stopService

#### bindService使用细节

1. 写一个`MyService extends Service`
2. 通过`Binder`来获取`MyService`

```
public IBinder onBind(Intent intent) {
        return new MsgBinder();
    }

    /**
     * 通过Binder对象,当Activity通过调用bindService(Intent service, ServiceConnection conn,int flags),
     * 我们可以得到一个Service的一个对象实例，然后我们就可以访问Service中的方法
     */
    public class MsgBinder extends Binder {
        public MsgService getService() {
            return MsgService.this;
        }
    }
```

3. 在Activity中绑定Service并访问Service的方法

```
 case R.id.btn_bind_service:
                Intent intent = new Intent(mContext,MsgService.class);
                bindService(intent, connection, Context.BIND_AUTO_CREATE);
                break;
 case R.id.btn_download_start:
                //开始下载
                mMsgService.startDownLoad();
                //通过接口回调更新进度
                mMsgService.setListener(new MsgService.ProgressChangeListener() {
                    @Override
                    public void onChanged(int progress) {
                        mProgressBar.setProgress(progress);
                    }
                });
 //Service连接的回调接口实现
    ServiceConnection connection=new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            //通过binder拿到service对象
            mMsgService = ((MsgService.MsgBinder) service).getService();
            Logger.i("onServiceConnected");
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    };
```

#### startService使用细节
1.`MsgService2 extends Service`

2.不管onBind,重写`onStartCommand`

```
public IBinder onBind(Intent intent) {
        return null;
    }
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
        startDownLoad();
        return super.onStartCommand(intent, flags, startId);

    }
```
3.在清单注册Service,注意这时的Service必须带intent-filter,相当于id
```
 <service android:name=".service.MsgService2"  >
            <!--这个就是Service的Id了-->
            <intent-filter>
                <action android:name="com.colin.demo.service.Service2" />
            </intent-filter>
</service>
```
4.在Activity中使用:
```
 case R.id.btn_register_broadcast:
                //通过Action过滤Service
                mMsgReceiver=new MsgReceiver();
                registerReceiver(mMsgReceiver, new IntentFilter("com.colin.demo.service.Service2"));
                Logger.i("已注册广播接收器");
                break;
case R.id.btn_start_service:
                //启动service
                mIntent = new Intent("com.colin.demo.service.Service2");
                startService(mIntent);
                break;
 /**
     * 广播接收器
     * @author len
     *
     */
    public class MsgReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            //拿到进度，更新UI
            int progress = intent.getIntExtra("progress", 0);
            Logger.i("拿到进度，更新UI  ");
            mProgressBar.setProgress(progress);
        }

}
```

#### 最后
1.记得干掉服务

```
@Override
    protected void onDestroy() {
        //销毁服务
        unbindService(connection);

        //停止服务
        stopService(mIntent);
        //注销广播
        unregisterReceiver(mMsgReceiver);
        super.onDestroy();
    }
```
2.`service`和`Activity`一样要在清单里面注册

3.复习一下广播的静态注册和动态注册
```
private Intent intent = new Intent("com.colin.demo.service.Service2");
intent.putExtra("progress", progress);
sendBroadcast(intent);//代表发送某一频率的广播
```
