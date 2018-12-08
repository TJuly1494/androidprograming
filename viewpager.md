# View Pager

### build.gradle(app)
```
    implementation 'com.android.support:support-v4:28.0.0'
    //ViewPager 는 안드로이드에 내장된 클래스가 아님으로 support 패키지를 추가해준다
```

### MainActivity.java
```
package com.example.tjuly.total;

import android.app.Activity;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.view.ViewPager;
import android.view.ViewGroup;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_viewpager);

        ViewPager viewPager = findViewById(R.id.viewpager);
        ImageAdapter adapter = new ImageAdapter(this);
        viewPager.setAdapter(adapter);
        adapter.notifyDataSetChanged();
    }
}
```

### ImageAdapter.java
```
package com.example.tjuly.total;

import android.content.Context;
import android.graphics.drawable.Drawable;
import android.support.annotation.NonNull;
import android.support.v4.view.PagerAdapter;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.LinearLayout;

public class ImageAdapter extends PagerAdapter {

    Context mContext;
    LayoutInflater inflater;

    int[] imagelist ={
      R.drawable.image1,
      R.drawable.image2,
      R.drawable.image3,
      R.drawable.image4,
      R.drawable.image5
    };

    public ImageAdapter(Context context){
        this.mContext=context;
        inflater=(LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    @Override
    public int getCount() {
        return imagelist.length;
    }

    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object o) {
        return view == (View)o;
    }

    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        ViewGroup rootView = (ViewGroup)inflater.inflate(R.layout.pager_item,container,false);
        ImageView imageView = rootView.findViewById(R.id.image);
        Drawable drawable = mContext.getResources().getDrawable(imagelist[position]);
        imageView.setImageDrawable(drawable);
        container.addView(rootView);

        return rootView;
    }

    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        container.removeView((LinearLayout)object);
    }
}
```

### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

### pager_item.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1"
        app:srcCompat="@mipmap/ic_launcher" />
</LinearLayout>
```
