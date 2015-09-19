---
layout: post
title: google swiperefresh的使用
category: android
keywords: swiperefresh,android,google
---

在做Android APP的时候，少不了下拉列表的展示，很幸运的是google自己提供了一个下拉刷新的控件swiperefresh。
下面来看看它是怎么使用的：
这里只介绍主要的功能代码，其它完整的代码需要的朋友可以在我的github上找到
[swiperefresh](https://github.com/MurphyNeo/Android)

MainActivity.java

```java
import android.app.Activity;
import android.content.Intent;
import android.example.com.googleandroid.swiperefresh.SwiperefreshFragment;
import android.os.Bundle;
import android.view.View;

public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void swipeRefreshLayout(View view){
        Intent intent = new Intent(this, MainFragmentActivity.class);
        intent.putExtra(Constants.Fragment.FRAGMENT_INDEX, SwiperefreshFragment.INDEX);
        startActivity(intent);
    }

}
```

MainFragmentActivity.java

```java
import android.example.com.googleandroid.swiperefresh.SwiperefreshFragment;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentActivity;
import android.support.v4.app.FragmentManager;

/**
 * Created by Neo on 15/9/19.
 */
public class MainFragmentActivity extends FragmentActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        /*获取参数处理不同的fragment*/
        int frIndex = getIntent().getIntExtra(Constants.Fragment.FRAGMENT_INDEX, 0);
        String tag;
        Fragment fr;
        FragmentManager fm = getSupportFragmentManager();
        switch (frIndex){
            default:
            case SwiperefreshFragment.INDEX:
                tag = SwiperefreshFragment.class.getSimpleName();
                fr = fm.findFragmentByTag(tag);
                if(fr == null){
                    fr = new SwiperefreshFragment();
                }
                break;
        }
        fm.beginTransaction().replace(R.id.main_fragment, fr, tag).commit();
    }
}
```

SwiperefreshFragment.java

```java
import android.example.com.googleandroid.R;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.widget.SwipeRefreshLayout;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ListView;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * Created by Neo on 15/9/19.
 */
public class SwiperefreshFragment extends Fragment {

    public static final int INDEX = 0;

    private ListView mListView;
    private ArrayAdapter<String> mAdapter;
    private List<String> mDataList = new ArrayList<String>(Arrays.asList("下拉刷新"));
    private SwipeRefreshLayout mSwipeRefreshLayout;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.swiperefreshlayout, container, false);
        mSwipeRefreshLayout = (SwipeRefreshLayout)view.findViewById(R.id.swiperefresh);
        /*设置刷新时动画的颜色变化效果*/
        mSwipeRefreshLayout.setColorSchemeResources(
                android.R.color.holo_blue_bright,
                android.R.color.holo_green_light,
                android.R.color.holo_orange_light,
                android.R.color.holo_red_light
        );
        mListView = (ListView)view.findViewById(R.id.swiperefresh_list);
        return view;
    }


    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        mAdapter = new ArrayAdapter<String>(getActivity(),android.R.layout.simple_list_item_1,mDataList);
        mListView.setAdapter(mAdapter);
        /*设置刷新事件*/
        mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                new Refresh().execute();
            }
        });

    }

    private class Refresh extends AsyncTask<Void, Void, List<String>>{

        @Override
        protected List<String> doInBackground(Void... params) {
            try {
                Thread.sleep(1000);
            } catch (Exception e) {
                e.printStackTrace();
            }
            return Arrays.asList("java Web", "Android App", "Ios App");
        }

        @Override
        protected void onPostExecute(List<String> strings) {
            super.onPostExecute(strings);
            mDataList.addAll(strings);
            mAdapter.notifyDataSetChanged();
            //停止刷新
            mSwipeRefreshLayout.setRefreshing(false);
        }
    }
}
```

三个配置文件：
activity_main.xml-->启动主界面

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center">

    <Button
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:text="SwipeRefreshLayout"
        android:onClick="swipeRefreshLayout"/>
</LinearLayout>
```

main.xml-->用来展示fragment的

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/main_fragment"
        android:layout_width="fill_parent"
        android:layout_height="0px"
        android:layout_weight="2">

    </FrameLayout>
</LinearLayout>
```

swiperefreshlayout.xml-->使用google下拉刷新的界面

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.SwipeRefreshLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/swiperefresh"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >

    <ListView
        android:id="@+id/swiperefresh_list"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        >

    </ListView>

</android.support.v4.widget.SwipeRefreshLayout>
```

