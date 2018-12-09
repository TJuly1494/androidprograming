# config

### manifest
```
        <activity android:name=".SettingActivity"></activity>
```

### res/values/styles.xml
```액티비티의 스타일을 정의하는 리소스파일
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>

        <item name="windowNoTitle">false</item>
        <item name="windowActionBar">true</item>
    </style>

</resources>
```

### res/values/arrays.xml
```문자열배열을 저장하는 리소스 파일
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="array_name">
        <item>오바마카카오톡</item>
        <item>카카오톡</item>
        <item>카톡</item>
        <item>카톡왔숑</item>
    </string-array>
</resources>
```

### res/values/colors.xml
```색상값을 저장하는 리소스파일

```

### res/values/dimens.xml
```각종 사이즈값을 저장하는 리소스파일
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#3F51B5</color>
    <color name="colorPrimaryDark">#303F9F</color>
    <color name="colorAccent">#FF4081</color>
</resources>
```

### res/values/strings.xml
```문자열 값을 저장하는 리소스파일
<resources>
    <string name="app_name">total</string>
    <string name="main_title">카카오톡</string>
</resources>
```

### res/xml/setting.xml
```설정화면을 정의한 레이아웃파일
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <PreferenceCategory android:title="새 메시지 알림">

        <SwitchPreference
            android:defaultValue="false"
            android:key="message_alarm"
            android:title="메시지 알림" />
        <ListPreference
            android:defaultValue="오바마카카오톡"
            android:entries="@array/array_name"
            android:entryValues="@array/array_name"
            android:key="alarm_sound"
            android:title="알림음" />
        <SwitchPreference
            android:defaultValue="false"
            android:key="sound"
            android:title="소리" />
        <SwitchPreference
            android:defaultValue="false"
            android:key="vibrator"
            android:title="진동" />
    </PreferenceCategory>
</PreferenceScreen>
```

### res/menu/main_menu.xml
```메인액티비티의 ActionBar를
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:title=""
        android:id="@+id/config_main_btn"
        android:icon="@drawable/outline_settings_24"
        app:showAsAction="always">
    </item>
</menu>
```

### MainActivity.java
```
package com.example.tjuly.total;

import android.app.Activity;
import android.content.Intent;
import android.content.SharedPreferences;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.preference.PreferenceManager;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

public class ConfigActivity extends AppCompatActivity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_config);

        Button alarmBtn = findViewById(R.id.alarm_btn);
        Button configClearBtn = findViewById(R.id.config_clear_btn);

        alarmBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SharedPreferences pref = PreferenceManager.getDefaultSharedPreferences(ConfigActivity.this);

                boolean messageAlarm = pref.getBoolean("message_alarm",false);
                boolean sound = pref.getBoolean("sound",false);
                MediaPlayer mp = null;
                if(messageAlarm && sound){
                    String alarmSound = pref.getString("alarm_sound","오바마카카오톡");
                    switch (alarmSound){
                        case "오바마카카오톡" :
                            mp = MediaPlayer.create(ConfigActivity.this,R.raw.kakao_obama);
                            break;
                        case "카카오톡" :
                            mp = MediaPlayer.create(ConfigActivity.this,R.raw.kakaotalk);
                            break;
                        case "카톡" :
                            mp = MediaPlayer.create(ConfigActivity.this,R.raw.katok);
                            break;
                        case "카톡왔숑" :
                            mp = MediaPlayer.create(ConfigActivity.this,R.raw.katok2);
                            break;
                    }
                    mp.start();
                    Toast.makeText(ConfigActivity.this,alarmSound,Toast.LENGTH_SHORT).show();
                }
            }
        });

        configClearBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SharedPreferences pref = PreferenceManager.getDefaultSharedPreferences(ConfigActivity.this);
                SharedPreferences.Editor editor = pref.edit();
                editor.clear().commit();
            }
        });
    }
    //액션바를 main_menu.xml 로 초기화시켜줌
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main_menu,menu);
        return true;
    }
    //액션바의 버튼이 클릭되었을시
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
            //설정 버튼이 클릭되었을시
        if(item.getItemId()==R.id.config_main_btn){
            Intent i = new Intent(ConfigActivity.this,SettingActivity.class);
            startActivity(i);
        }
        return super.onOptionsItemSelected(item);
    }
}

```

### SettingFragment.java
```설정화면을 관장하는 Fragment
package com.example.tjuly.total;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.ListPreference;
import android.preference.PreferenceFragment;
import android.preference.PreferenceManager;
import android.preference.SwitchPreference;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;

public class SettingFragment extends PreferenceFragment implements SharedPreferences.OnSharedPreferenceChangeListener {

    SwitchPreference messageAlarmSwitch;
    SwitchPreference sound;
    SwitchPreference vibrator;
    ListPreference alarmSound;

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.setting);

        SharedPreferences pref = PreferenceManager.getDefaultSharedPreferences(getActivity());
        messageAlarmSwitch = (SwitchPreference)findPreference("message_alarm");
        sound = (SwitchPreference) findPreference("sound");
        vibrator = (SwitchPreference) findPreference("vibrator");
        alarmSound = (ListPreference) findPreference("alarm_sound");

        pref.registerOnSharedPreferenceChangeListener(this);

        if(!pref.getBoolean("message_alarm",false)){
            sound.setEnabled(false);
            vibrator.setEnabled(false);
            alarmSound.setEnabled(false);
        } else {
            sound.setEnabled(true);
            vibrator.setEnabled(true);
            alarmSound.setEnabled(true);
        }
        alarmSound.setSummary(pref.getString("alarm_sound",""));
    }

    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        if(!sharedPreferences.getBoolean("message_alarm",false)){
            sound.setEnabled(false);
            vibrator.setEnabled(false);
            alarmSound.setEnabled(false);
        } else {
            sound.setEnabled(true);
            vibrator.setEnabled(true);
            alarmSound.setEnabled(true);
        }
        alarmSound.setSummary(sharedPreferences.getString("alarm_sound",""));
    }
}
```

### SettingActivity.java
```
package com.example.tjuly.total;

import android.app.Activity;
import android.os.Bundle;
import android.support.annotation.Nullable;

public class SettingActivity extends Activity{

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_setting);
    }
}
```

### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/alarm_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="알람" />

    <Button
        android:id="@+id/config_clear_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="설정초기화" />
</LinearLayout>
```
