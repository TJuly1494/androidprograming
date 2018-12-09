# sqlite

### manifest
```
        <activity android:name=".AddStudentActivity"></activity>
```

### build.gradle(project)
```
        classpath 'com.android.tools.build:gradle:3.2.0' //appcompat 체크 햇을 시 버전 3.2.0으로 변경
        classpath "io.realm:realm-gradle-plugin:3.5.0" //추가
```

### build.grade(app)
```
//상단에 추가
apply plugin: 'realm-android'
```

### arrays.xml
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="cls_list">
        <item>1반</item>
        <item>2반</item>
        <item>3반</item>
    </string-array>
</resources>
```

### MainActivity.java
```
package kr.ac.mjc.splite_example;

import android.content.Intent;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.PixelCopy;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.ListView;

import io.realm.Realm;
import io.realm.RealmResults;

public class MainActivity extends AppCompatActivity implements View.OnClickListener,AdapterView.OnItemClickListener {
    StudentAdapter studentAdapter;

    final int REQUEST_ADD_STUDENT=1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button addSudentBtn=findViewById(R.id.add_student_btn);
        ListView listView=findViewById(R.id.listview);

        Realm.init(this);
        Realm realm=Realm.getDefaultInstance();

        RealmResults<Student> studentList=realm.where(Student.class).findAll();
        studentAdapter=new StudentAdapter(this,studentList);
        listView.setAdapter(studentAdapter);
        addSudentBtn.setOnClickListener(this);
        listView.setOnItemClickListener(this);
    }


    @Override
    public void onClick(View v) {
        if(v.getId()==R.id.add_student_btn){
            Intent intent=new Intent(this,AddStudentActivity.class);
            //startActivity(intent);
            startActivityForResult(intent,REQUEST_ADD_STUDENT);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        if(requestCode==REQUEST_ADD_STUDENT && resultCode==RESULT_OK){
            studentAdapter.notifyDataSetChanged(); //데이터가 바뀌엇으니 새로 만들어라는 지시
        }
        super.onActivityResult(requestCode, resultCode, data);
    }

    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        Student student=studentAdapter.getItem(position);
        Realm realm=Realm.getDefaultInstance();
        realm.beginTransaction();
        realm.where(Student.class).equalTo("studentNumber", student.getStudentNumber()).findFirst().deleteFromRealm();
        realm.commitTransaction();
        studentAdapter.notifyDataSetChanged();
    }
}
```

### Student.java
```
package kr.ac.mjc.splite_example;

import io.realm.RealmObject;
import io.realm.annotations.PrimaryKey;

public class Student extends RealmObject{ //realm에서 저장할수 있는 클래스가 됨

    private String name;
    @PrimaryKey //studentNumber에 프리어머리키 지정
    private String studentNumber;
    private int cls;

    //제너레이터 - 게터와 세터 - 모두지정
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getStudentNumber() {
        return studentNumber;
    }

    public void setStudentNumber(String studentNumber) {
        this.studentNumber = studentNumber;
    }

    public int getCls() {
        return cls;
    }

    public void setCls(int cls) {
        this.cls = cls;
    }
}

```

### AddStudentActivity.java
```
package kr.ac.mjc.splite_example;

import android.app.Activity;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.Toast;

import io.realm.Realm;

public class AddStudentActivity extends Activity  implements View.OnClickListener{

    EditText nameEt;
    EditText studentNumberEt;
    Spinner clsSp;
    Button addStudentBtn;


    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_student);

        nameEt=findViewById(R.id.name_et);
        studentNumberEt=findViewById(R.id.student_number_et);
        clsSp=findViewById(R.id.cls_sp);
        addStudentBtn=findViewById(R.id.student_add_btn);

        addStudentBtn.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if(v.getId()==R.id.student_add_btn){
            String name=nameEt.getText().toString();
            if(name.length()==0){
                showToast("이름을 입력해주세요.");
                return;
            }
            String studentNumber=studentNumberEt.getText().toString();
            if(studentNumber.length()==0){
                showToast("학번을 입력해주세요.");
                return;
            }
            String clsSt = (String)clsSp.getSelectedItem();
            int clsInt=1;
            switch (clsSt) {
                case "1반":
                    clsInt = 1;
                    break;
                case "2반":
                    clsInt = 2;
                    break;
                case "3반":
                    clsInt = 3;
                    break;
            }
            Student student=new Student();
            student.setName(name);
            student.setStudentNumber(studentNumber);
            student.setCls(clsInt);

            Realm realm=Realm.getDefaultInstance();

            Student findStudent=realm.where(Student.class).equalTo("studentNumber", studentNumber).findFirst(); //중복되는 학번이 있는지 확인!!
            if(findStudent!=null){
                showToast("이미 존재하는 학번입니다.");
                return;
            }

            realm.beginTransaction(); //실행하여 간단하게
            realm.copyToRealm(student); //무슨 값을 넣은 건지
            realm.commitTransaction(); //값을 입력

            setResult(RESULT_OK); //값이 잘 전달되었다고
            finish();
        }
    }
    public void showToast(String msg){
        Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
    }
}
```

### StudentAdapter
```
package kr.ac.mjc.splite_example;

import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;

import java.util.List;

import dalvik.system.InMemoryDexClassLoader;

public class StudentAdapter extends BaseAdapter{

    List<Student> studentList;
    Context mContext;

    public StudentAdapter(Context context,List<Student> sl){
        this.mContext=context;
        this.studentList=sl;
    }

    @Override
    public int getCount() {
        return studentList.size();
    }

    @Override
    public Student getItem(int position) {
        return studentList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        StudentItemLayout itemLayout;
        if(convertView==null){
            itemLayout=new StudentItemLayout(mContext);
        }
        else{
            itemLayout=(StudentItemLayout)convertView;
        }
        Student student=getItem(position);
        itemLayout.setStudent(student);
        return itemLayout;
    }
}
```

### StudentItemLayout.java
```
package kr.ac.mjc.splite_example;

import android.content.Context;
import android.view.ContextThemeWrapper;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.LinearLayout;
import android.widget.TextView;

import org.w3c.dom.Text;

public class StudentItemLayout extends LinearLayout {

    TextView nameTv;
    TextView studentNumberTv;
    TextView clsTv;

    public StudentItemLayout(Context context) {
        super(context);
        LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        ViewGroup rootView = (ViewGroup) inflater.inflate(R.layout.item_student, this, true);
        nameTv=rootView.findViewById(R.id.name_tv);
        studentNumberTv=rootView.findViewById(R.id.student_number_tv);
        clsTv=rootView.findViewById(R.id.cls_tv);
    }

    public void setStudent(Student student){
        nameTv.setText(student.getName());
        clsTv.setText(String.format("%d반",student.getCls()));
        studentNumberTv.setText(student.getStudentNumber());
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

    <Button
        android:id="@+id/add_student_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="학생추가" />

    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1"/>
</LinearLayout>
```

### Item_student.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <TextView
        android:id="@+id/name_tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:text="TextView"
        android:textSize="20sp" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:id="@+id/cls_tv"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:padding="5dp"
            android:text="TextView" />

        <TextView
            android:id="@+id/student_number_tv"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:padding="5dp"
            android:text="TextView" />
    </LinearLayout>
</LinearLayout>
```

### activity_add_student.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <EditText
        android:id="@+id/name_et"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="이름"
        android:inputType="textPersonName" />

    <EditText
        android:id="@+id/student_number_et"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="학번"
        android:inputType="number" />

    <Spinner
        android:id="@+id/cls_sp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:entries="@array/cls_list" />

    <Button
        android:id="@+id/student_add_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="추가하기" />
</LinearLayout>
```
