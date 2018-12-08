# Backgroud Download

### Manifest
```
    <uses-permission android:name="android.permission.INTERNET"></uses-permission>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"></uses-permission>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>

    <service android:name=".DownloadService"></service>
```

### build.gradle(app)
```
    implementation 'com.squareup.okhttp3:okhttp:3.11.0'
```

### MainActivity.java
```
package com.example.tjuly.total;

import android.Manifest;
import android.app.Activity;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.os.Build;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.view.View;
import android.widget.Button;

import java.io.InputStream;

public class MainActivity extends Activity {
    //서비스를 이용해 Background 에서 용량이 큰 텍스트파일을 다운로드 받고 다운로드 경과를 Notification 에 표시해주기

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bg);

        Button downloadBtn = findViewById(R.id.download_btn);
        downloadBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
                    int readStatus = checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE);
                    int writeStatus = checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE);

                    if(readStatus!= PackageManager.PERMISSION_GRANTED || writeStatus != PackageManager.PERMISSION_GRANTED){
                        requestPermissions(new String[]{Manifest.permission.READ_EXTERNAL_STORAGE,Manifest.permission.WRITE_EXTERNAL_STORAGE},1);
                        return;
                    }
                }
                Intent i = new Intent(MainActivity.this,DownloadService.class);
                startActivity(i);
            }
        });
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```

### DownloadService.java
```
package com.example.tjuly.total;

import android.app.Notification;
import android.app.NotificationManager;
import android.app.Service;
import android.content.Intent;
import android.os.Build;
import android.os.Environment;
import android.os.Handler;
import android.os.IBinder;
import android.support.annotation.Nullable;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;

import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class DownloadService extends Service implements Runnable {

    NotificationManager mNotiManager;
    Notification mNoti;
    Handler handler = new Handler();

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public void onCreate() {
        mNotiManager=(NotificationManager)this.getSystemService(NOTIFICATION_SERVICE);
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Thread thread = new Thread(this);
        thread.start();
        mNotiManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
            mNoti = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher_background)
                    .setContentTitle("다운로드 중")
                    .setContentText("다운로드 중")
                    .setOngoing(true)
                    .setProgress(100, 0, false)
                    .build();
            mNotiManager.notify(1, mNoti);
        }
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void run() {
        OkHttpClient client = new OkHttpClient();
        Request request = new Request
                .Builder()
                .url("https://github.com/JinYongHwa/AndroidPrograming/raw/master/ch05/test2.txt")
                .build();


        try {
            String filePate = Environment
                    .getExternalStorageDirectory()
                    .getAbsolutePath() + "/test2.txt";
            File file = new File(filePate);
            FileOutputStream fileOutput = new FileOutputStream(file);
            Response response = client.newCall(request).execute(); //execute() - try/catch문
            InputStream inputStream = response.body().byteStream();
            byte[] data = new byte[4096];
            long dataLength = response.body().contentLength();
            long downloaded = 0;
            int finalPercent = 0;
            while (true) {
                int length = inputStream.read(data);
                if (length == 1) {
                    break;
                }
                fileOutput.write(data, 0, length);
                downloaded += length;
                final int percent = (int) (((double) downloaded / dataLength) * 100);
                if (finalPercent != percent) {
                    handler.post(new Runnable() {
                        @Override
                        public void run() {
                            setProgress(percent);
                        }
                    });
                    finalPercent = percent;
                }
            }
            handler.post(new Runnable() {
                @Override
                public void run() {
                    setProgress(100);
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void setProgress(int percent) {
        if (percent == 100) {
            mNotiManager.cancel(1);
            return;
        }
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
                mNoti = new Notification.Builder(this)
                        .setSmallIcon(R.drawable.ic_launcher_background)
                        .setContentTitle("다운로드 중")
                        .setContentText("다운로드 중")
                        .setOngoing(true)
                        .setProgress(100, 0, false)
                        .build();
                mNotiManager.notify(1, mNoti);include ':app'

        }
    }
}
```

### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <Button
        android:id="@+id/download_btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="다운로드" />
</LinearLayout>
```
