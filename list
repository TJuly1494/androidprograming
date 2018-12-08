#List View
### MainActivity.java
'''
package com.example.tjuly.total;

import android.app.Activity;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.Toast;

import java.util.ArrayList;

public class Mainctivity extends Activity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list);

        ListView listView = findViewById(R.id.listview);

        ArrayList<ListItem> dataList = new ArrayList<ListItem>();

        for(int i=0;i<100;i++){
            Drawable drawable;
            if(i%2==0){
                drawable = getResources().getDrawable(R.drawable.image1);
            } else {
                drawable = getResources().getDrawable(R.drawable.image2);
            }
            ListItem item = new ListItem(drawable,"제목"+i,"내용"+i,"가격"+i);
            dataList.add(item);
        }

        final ListAdapter adapter = new ListAdapter(this,dataList);
        listView.setAdapter(adapter);
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                ListItem item = adapter.getItem(position);
                Toast.makeText(MainActivity.this,item.getTitle(),Toast.LENGTH_SHORT).show();
            }
        });
    }
}
'''
###ListItem.java
'''
package com.example.tjuly.total;

import android.graphics.drawable.Drawable;

public class ListItem {
    private Drawable image;
    private String title;
    private String data1;
    private String data2;

    public ListItem(Drawable image, String title, String data1, String data2) {
        this.image = image;
        this.title = title;
        this.data1 = data1;
        this.data2 = data2;
    }

    public Drawable getImage() {
        return image;
    }

    public void setImage(Drawable image) {
        this.image = image;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getData1() {
        return data1;
    }

    public void setData1(String data1) {
        this.data1 = data1;
    }

    public String getData2() {
        return data2;
    }

    public void setData2(String data2) {
        this.data2 = data2;
    }
}
'''
###ListItemLayout.java
'''package com.example.tjuly.total;

import android.content.Context;
import android.graphics.drawable.Drawable;
import android.view.LayoutInflater;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

public class ListItemLayout extends LinearLayout {

    private ImageView imageView;
    private TextView titleTv;
    private TextView data1Tv;
    private TextView data2Tv;

    public ListItemLayout(Context context) {
        super(context);
        LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        ViewGroup rootview = (ViewGroup) inflater.inflate(R.layout.list_item,this,true);

        imageView = rootview.findViewById(R.id.imageView);
        titleTv = rootview.findViewById(R.id.title_tv);
        data1Tv = rootview.findViewById(R.id.data1_tv);
        data2Tv = rootview.findViewById(R.id.data2_tv);
    }

    public void setImage(Drawable img){
        imageView.setImageDrawable(img);
    }

    public void setTitle(String text){
        titleTv.setText(text);
    }

    public void setData1(String text){
        data1Tv.setText(text);
    }

    public void setData2(String text){
        data2Tv.setText(text);
    }
}
'''
###ListAdapter.java
'''
package com.example.tjuly.total;

import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;

import java.util.ArrayList;

public class ListAdapter extends BaseAdapter{

    Context mContext;

    ArrayList<ListItem> dataList = new ArrayList<ListItem>();

    public ListAdapter(Context context,ArrayList<ListItem> list){
        this.mContext=context;
        this.dataList=list;
    }

    @Override
    public int getCount() {
        return dataList.size();
    }

    @Override
    public ListItem getItem(int position) {
        return dataList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return 0;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ListItemLayout itemView;

        if(convertView==null){
            itemView = new ListItemLayout(mContext);
        } else {
            itemView=(ListItemLayout) convertView;
        }

        ListItem listitem = getItem(position);
        itemView.setImage(listitem.getImage());
        itemView.setTitle(listitem.getTitle());
        itemView.setData1(listitem.getData1());
        itemView.setData2(listitem.getData2());

        return itemView;
    }
}
'''
###activity_main.xml
'''
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ListView
        android:id="@+id/listview"
        android:layout_width="368dp"
        android:layout_height="551dp"
        tools:layout_editor_absoluteX="8dp"
        tools:layout_editor_absoluteY="8dp" />
</android.support.constraint.ConstraintLayout>
'''
###list_item.xml
'''
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="80dp"
        android:layout_height="80dp"
        app:srcCompat="@mipmap/ic_launcher" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:orientation="vertical">

        <TextView
            android:id="@+id/title_tv"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="TextView" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/data1_tv"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:layout_weight="1"
                android:text="TextView" />

            <TextView
                android:id="@+id/data2_tv"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:layout_weight="1"
                android:text="TextView" />
        </LinearLayout>
    </LinearLayout>
</LinearLayout>
'''
