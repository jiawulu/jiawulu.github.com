---
layout: post
title: "android aidl"
description: ""
category: "android" 
tags: [android]
---
{% include JB/setup %}

## aidl 实践

### 1. 定义 AIDL 文件

    package com.demo.service;

    interface MathService {

        int add(int arg1,int arg2);

    }  

### 2. 定义 service provider

	public class MathServiceImpl extends Service {

    @Override
    public IBinder onBind(Intent intent) {

        Log.d("[provider]", "mathserviceImpl onBind");


        return new MathService.Stub(){

            @Override
            public int add(int arg1, int arg2) throws RemoteException {

                Log.d("[provider]", "arg1 is " + arg1 + " arg2 is " + arg2);

                int result = arg1 + arg2;

                Log.d("[provider]", "math result is " + result);

                return result;
            }
        };
    }


}


### 3. 增加 service 配置

    <service
        android:name="com.demo.service.impl.MathServiceImpl"
        android:enabled="true"
        android:exported="true" >

        <intent-filter >
            <action android:name="com.demo.service.MATH.START"/>
            <category android:name="android.intent.category.DEFAULT" />
        </intent-filter>
        </service>

### 4. 获取 service 代理

    private void startService() {
        Intent intent = new Intent("com.demo.service.MATH.START");
        bindService(intent, initConnection(), Context.BIND_AUTO_CREATE);
        startService(intent);
    }

    private ServiceConnection initConnection() {

        return new ServiceConnection(){
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                // TODO Auto-generated method stub

                mathService = MathService.Stub.asInterface(service);

                validateAIDL();

            }

            @Override
            public void onServiceDisconnected(ComponentName name) {
                // TODO Auto-generated method stub
                mathService = null;
            }

        };
    }

### 5. 简单调用

    private void validateAIDL() {
        
        if(null != mathService){

            Log.e("[consumer]", "start math service");

            try {

                int result = mathService.add(1,2);

                Log.d("[consumer]","result is " + result);

            } catch (RemoteException e) {
                e.printStackTrace();
            }
        }
        
    }

