# http

### manifest
```
 <uses-permission android:name="android.permission.INTERNET"></uses-permission>
       <activity android:name=".NewsActivity"></activity>
```

### build.gradle(app)
```Http 요청을위해 okhttp 라이브러리추가 xml 파싱을 위해 gson-xml 라이브러리추가
    implementation 'com.squareup.okhttp3:okhttp:3.11.0'
    implementation 'com.stanfy:gson-xml-java:0.1.+' //rss 내용
    implementation 'com.github.bumptech.glide:glide:4.8.0' //glide 오픈소스에서 가져온것!
```

### MainActivity.java
```
package kr.ac.mjc.okhttp;

import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.os.Handler;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.TextView;

import com.stanfy.gsonxml.GsonXml;
import com.stanfy.gsonxml.GsonXmlBuilder;
import com.stanfy.gsonxml.XmlParserCreator;

import org.xmlpull.v1.XmlPullParser;
import org.xmlpull.v1.XmlPullParserFactory;

import java.io.IOException;
import java.util.ArrayList;

import okhttp3.Call;
import okhttp3.Callback;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class MainActivity extends Activity implements Callback{ //콜백 메서드를 불러옴!

    Button requestBtn;
    EditText urlEt;
    //TextView outputTv;
    ListView listView;
    NewsAdapter adapter;
    ArrayList<Item> mNewsList = new ArrayList<Item>();

    Handler handler=new Handler();

    XmlParserCreator parserCreator = new XmlParserCreator() {
        @Override
        public XmlPullParser createParser() {
            try {
                return XmlPullParserFactory.newInstance().newPullParser();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        requestBtn=findViewById(R.id.request_btn);
        urlEt=findViewById(R.id.url_et);
        //outputTv=findViewById(R.id.output_tv);

        listView=findViewById(R.id.listview);
        adapter=new NewsAdapter(this,mNewsList);
        listView.setAdapter(adapter);

        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                Item item=adapter.getItem(position);
                Intent i=new Intent(MainActivity.this,NewsActivity.class);
                i.putExtra("url",item.getLink());
                //Intent i=new Intent(Intent.ACTION_VIEW, Uri.parse(item.getLink()));
                startActivity(i);
            }
        });

/*
        requestBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String url=urlEt.getText().toString();
                OkHttpClient client=new OkHttpClient();
                Request request=new Request.Builder().url(url).build();

                client.newCall(request).enqueue(MainActivity.this);
            }
        });
        */
        requestBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                OkHttpClient client=new OkHttpClient();
                Request request=new Request.Builder().url("https://news.sbs.co.kr/news/ReplayRssFeed.do?prog_cd=R1&plink=RSSREADER").build();

                client.newCall(request).enqueue(MainActivity.this);
            }
        });
    }

    @Override
    public void onFailure(Call call, IOException e) {

    }
/*
    @Override
    public void onResponse(Call call, Response response) throws IOException { //http 예제
        if(response.code()==200){
            final String html=response.body().string();
            handler.post(new Runnable() {
                @Override
                public void run() {
                    outputTv.setText(html); //변수가 바깥쪽에 있기때문에 알트엔터로  final을 붙임
                }
            });
        }
    }
    */
    @Override
    public void onResponse(Call call, Response response) throws IOException { //rss예제
        if(response.code()==200){
            final String xml=response.body().string();
            GsonXml gsonXml=new GsonXmlBuilder()
                            .setXmlParserCreator(parserCreator)
                            .setSameNameLists(true)
                            .create();
            Rss rss=gsonXml.fromXml(xml, Rss.class);
            mNewsList.clear();
            mNewsList.addAll(rss.channel.item);
            handler.post(new Runnable() {
                @Override
                public void run() {
                    adapter.notifyDataSetChanged();
                }
            });
      }
  }
}
```

### Rss.java
```RSS 전체를 구조화시킨 class
package kr.ac.mjc.okhttp;

public class Rss {
    Channel channel;
}
```

### Channel.java
```
package kr.ac.mjc.okhttp;

import java.util.ArrayList;

public class Channel {
    ArrayList<Item> item=new ArrayList<Item>();
}
```

### Item.java
```
package kr.ac.mjc.okhttp;

public class Item {
    private String title;
    private String link;
    private String description;
    private Enclosure enclosure;  //직접접근 못하게 private 붙여줌

    public Enclosure getEnclosure() {   //제너레이터 - 게터 앤드 세터-enclosure 지정
        return enclosure;
    }

    public void setEnclosure(Enclosure enclosure) {
        this.enclosure = enclosure;
    }

    //제너래이터 - 게터 앤드 세터 - 모두 지정 - 엔터
    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getLink() {
        return link;
    }

    public void setLink(String link) {
        this.link = link;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

### Enclosure.java
```뉴스의 이미지정보를 저장하는 class
package kr.ac.mjc.okhttp;

import com.google.gson.annotations.SerializedName;

public class Enclosure {
    @SerializedName("@url")
    private String url;

    public String getUrl() {  //제너레이터 - 게터 앤드 세터
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }
}
```

### NewsItemLayout.java
```
package kr.ac.mjc.okhttp;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

import com.bumptech.glide.Glide;

public class NewsItemLayout extends LinearLayout {
    Context mContext;
    TextView titleTv;
    TextView descriptionTv;
    ImageView imageiv;

    public NewsItemLayout(Context context) {
        super(context);
        LayoutInflater inflate=(LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        ViewGroup rootView=(ViewGroup)inflate.inflate(R.layout.news_item, this, true);
        titleTv=findViewById(R.id.title_tv);
        descriptionTv=findViewById(R.id.description_tv);
        imageiv=rootView.findViewById(R.id.image_iv);
    }

    public void setItem(Item item){
        titleTv.setText(item.getTitle());
        descriptionTv.setText(item.getDescription());

        if(item.getEnclosure()!=null && item.getEnclosure().getUrl()!=null){
            String url=item.getEnclosure().getUrl();
            imageiv.setVisibility(VISIBLE);
            Glide.with(imageiv).load(url).into(imageiv);
        }
        else {
            imageiv.setVisibility((GONE));
            Glide.with(imageiv).clear(imageiv);
        }
    }

    public void setTitle(String title){
        titleTv.setText(title);
    }

    public void setDescription(String description){
        descriptionTv.setText(description);
    }
}
```

### NewsAdapter.java
```
package kr.ac.mjc.okhttp;

import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;

import java.util.ArrayList;

public class NewsAdapter extends BaseAdapter {
    Context mContext;
    ArrayList<Item> mNewsList = new ArrayList<Item>();

    public NewsAdapter(Context context,ArrayList<Item> newsList){
        this.mContext=context;
        this.mNewsList=newsList;
    }

    @Override
    public int getCount() {
        return mNewsList.size();
    }

    @Override
    public Item getItem(int position) {
        return mNewsList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        NewsItemLayout newsItemLayout;
        if(convertView==null){
            newsItemLayout=new NewsItemLayout(mContext);
        } else {
            newsItemLayout=(NewsItemLayout) convertView;
        }
        Item item = getItem(position);
        //newsItemLayout.setTitle(item.getTitle());
        //newsItemLayout.setDescription(item.getDescription());
        newsItemLayout.setItem(item);

        return newsItemLayout;
    }
}
```

### NewsActivity.java
```
package kr.ac.mjc.okhttp;

import android.app.Activity;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.webkit.WebChromeClient;
import android.webkit.WebView;
import android.webkit.WebViewClient;

public class NewsActivity extends Activity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_news);
        WebView webView = findViewById(R.id.webview);
        webView.getSettings().setJavaScriptEnabled(true);
        webView.setWebViewClient(new WebViewClient());
        webView.setWebChromeClient(new WebChromeClient());

        String url = getIntent().getStringExtra("url");
        webView.loadUrl(url);
    }
}
```

### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <EditText
            android:id="@+id/url_et"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1" />

        <Button
            android:id="@+id/request_btn"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="요청" />

    </LinearLayout>

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal" >

            <TextView
                android:id="@+id/output_tv"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_weight="1" />

        </LinearLayout>
    </ScrollView>

    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1" />
</LinearLayout>
```

### activity_news.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <WebView
        android:id="@+id/webview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

### news_item.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">

        <ImageView
            android:id="@+id/image_iv"
            android:layout_width="60dp"
            android:layout_height="60dp" />
    <TextView
        android:id="@+id/title_tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:text="TextView"
        android:textSize="20sp" />
    </LinearLayout>
    <TextView
        android:id="@+id/description_tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ellipsize="end"
        android:lines="3"
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        android:text="TextView" />
</LinearLayout>
```
