---
layout: post
title: pulltorefresh
category: android
keywords: pulltorefresh,android,github
---

在做Android APP的时候，少不了下拉列表的展示，自己实现的话可能比较麻烦，github上已经有写好的Library,直接拿来用就好了。
下面来看看它是怎么使用的：
这里只介绍主要的功能代码，其它完整的代码需要的朋友可以在我的github上找到
 
[pulltorefresh](https://github.com/MurphyNeo/Android)

首先我们需要在github下载[Android-PullToRefresh](https://github.com/chrisbanes/Android-PullToRefresh),如果是用eclipse的话,直接将里面的library导入到eclipse即可,如果是用android studio的话就麻烦一点了。
虽然可以直接导入android studio,但可能会有想不到的问题。
1、同样是将library导入eclipse中
2、在eclipse中Export,选择Generate Gradle build files
3、然后将eclipse导出的导入到android studio
4、android studio中选择file->import model

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

    public void pullToRefresh(View view){
        Intent intent = new Intent(this, MainFragmentActivity.class);
        intent.putExtra(Constants.Fragment.FRAGMENT_INDEX, PulltorefreshFragment.INDEX);
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
            case PulltorefreshFragment.INDEX:
                tag = PulltorefreshFragment.class.getSimpleName();
                fr = fm.findFragmentByTag(tag);
                if(fr == null){
                    fr = new PulltorefreshFragment();
                }
                break;
        }
        fm.beginTransaction().replace(R.id.main_fragment, fr, tag).commit();
    }
}
```

PulltorefreshFragment.java

```java
import android.example.com.googleandroid.R;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.text.format.DateUtils;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import com.handmark.pulltorefresh.library.ILoadingLayout;
import com.handmark.pulltorefresh.library.PullToRefreshBase;
import com.handmark.pulltorefresh.library.PullToRefreshBase.Mode;
import com.handmark.pulltorefresh.library.PullToRefreshBase.OnRefreshListener2;
import com.handmark.pulltorefresh.library.PullToRefreshListView;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * Created by Neo on 15/9/19.
 */
public class PulltorefreshFragment extends Fragment {

    public static final int INDEX = 1;

    private ArrayAdapter<String> mAdapter;
    private PullToRefreshListView mListView;
    private List<String> mDataList = new ArrayList<String>(Arrays.asList("下拉刷新"));

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.pulltorefresh, container, false);
        mListView = (PullToRefreshListView)view.findViewById(R.id.pull_refresh_list);
        return view;
    }

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        mAdapter = new ArrayAdapter<String>(getActivity(), android.R.layout.simple_list_item_1, mDataList);
        mListView.setAdapter(mAdapter);

        /*
        Mode.BOTH:同时支持上拉下拉
        Mode.PULL_FROM_START:只支持下拉
        Mode.PULL_FROM_END:只支持上拉
        如果设置为BOTH,需要设置刷新Listener为OnRefreshListener2,实现下拉方法onPullDownToRefresh()
        和上拉方法onPullUpToRefresh()。
        如果为非BOTH,则可以实现OnRefreshListener2或者OnRefreshListener.
         */
        mListView.setMode(Mode.BOTH);

        /*
        设置刷新时显示的文字
        注意：上拉和下拉时getLoadingLayoutProxy()中的参数反过来了
        一个是(true, false)一个是(false, true)
         */
        ILoadingLayout downPull = mListView.getLoadingLayoutProxy(true, false);
        downPull.setPullLabel("下拉刷新");
        downPull.setReleaseLabel("松开刷新");
        downPull.setRefreshingLabel("正在刷新");
        ILoadingLayout upPull = mListView.getLoadingLayoutProxy(false, true);
        upPull.setPullLabel("上拉刷新");
        upPull.setReleaseLabel("松开刷新");
        upPull.setRefreshingLabel("正在刷新");

        /*
        setOnRefreshListener()设置刷新监听
        setOnLastItemVisibleListener()设置到底部监听
        setOnPullEventListener()设置事件监听
        其中还有很多其它的监听,这里就不一一列出了
         */
        mListView.setOnRefreshListener(new OnRefreshListener2<ListView>() {
            @Override
            public void onPullDownToRefresh(PullToRefreshBase<ListView> refreshView) {
                String label = DateUtils.formatDateTime(getActivity(), System.currentTimeMillis(),
                        DateUtils.FORMAT_SHOW_TIME | DateUtils.FORMAT_SHOW_DATE | DateUtils.FORMAT_ABBREV_ALL);
                refreshView.getLoadingLayoutProxy().setLastUpdatedLabel(label);
                new GetDataTask().execute();
            }

            @Override
            public void onPullUpToRefresh(PullToRefreshBase<ListView> refreshView) {
                String label = DateUtils.formatDateTime(getActivity(), System.currentTimeMillis(),
                        DateUtils.FORMAT_SHOW_TIME | DateUtils.FORMAT_SHOW_DATE | DateUtils.FORMAT_ABBREV_ALL);
                refreshView.getLoadingLayoutProxy().setLastUpdatedLabel(label);
                new GetDataTask().execute();
            }
        });

        /*
        设置自动刷新
        注意：这个一定要写在setOnRefreshListener后面,不然的话是不会监听到的,看到的界面就是一直在加载中
         */
        mListView.setRefreshing(true);
    }

    private class GetDataTask extends AsyncTask<Void, Void, List<String>> {
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
        protected void onPostExecute(List<String> list) {
            super.onPostExecute(list);
            mDataList.addAll(list);
            mAdapter.notifyDataSetChanged();
            //设置刷新完成
            mListView.onRefreshComplete();
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
        android:text="pullToRefresh"
        android:onClick="pullToRefresh"/>
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

pulltorefresh.xml-->PullToRefreshListView的界面

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" 
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.handmark.pulltorefresh.library.PullToRefreshListView
        android:id="@+id/pull_refresh_list"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        >

    </com.handmark.pulltorefresh.library.PullToRefreshListView>
</LinearLayout>
```

## 总结
我们使用这个第三方Library,可能下拉或上拉的效果并不是我们想要的,如果是要改变动画或显示文件的话,就需要改变Library中的代码了。
比如现在很多APP下拉时都没显示文字,只有动画,我们就可以在pull_to_refresh_header_vertical.xml中个性布局文件。每个人的需求都不一样,更多的功能还需要我们自己去改造。显然pulltorefresh已经为我们提供了很多,我们只需要做一些小的修改。

