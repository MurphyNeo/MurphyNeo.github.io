---
layout: post
title: ActionBar Tab(ViewPager+Fragment)
category: android
keywords: actionBar,android,Horizontalpaging
---

今天来实现一个actionbar下用tab标签,虽然这种方式已经被弃用了,只有用一下就会明白为什么它会被弃用...

需要完整代码的朋友可以在我的github上找到

[actionbar](https://github.com/MurphyNeo/Android)

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

    private void publicIntent(int index){
        Intent intent = new Intent(this, MainFragmentActivity.class);
        intent.putExtra(Constants.Fragment.FRAGMENT_INDEX, index);
        startActivity(intent);
    }

    public void actionBar(View view){
        publicIntent(Constants.Index.ACTIONBAR_INDEX);
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
            case Constants.Index.ACTIONBAR_INDEX:
                tag = ActionBarFragment.class.getSimpleName();
                fr = fm.findFragmentByTag(tag);
                if(fr == null){
                    fr = new ActionBarFragment();
                }
                break;
        }
        fm.beginTransaction().replace(R.id.main_fragment, fr, tag).commit();
    }
}
```

ActionBarFragment.java

```java
import android.app.ActionBar;
import android.app.FragmentTransaction;
import android.example.com.googleandroid.R;
import android.example.com.googleandroid.pulltorefresh.PulltorefreshFragment;
import android.example.com.googleandroid.swiperefresh.SwiperefreshFragment;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentPagerAdapter;
import android.support.v4.view.MenuItemCompat;
import android.support.v4.view.ViewPager;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;
import android.widget.Toast;
import java.util.Locale;

/**
 * Created by Neo on 15/9/20.
 */
public class ActionBarFragment extends Fragment implements ActionBar.TabListener{

    SectionsPagerAdapter mSectionsPagerAdapter;

    ViewPager mViewPager;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //通知系统允许这个fragment使用menu,不然是不会调用下面的onCreateOptionsMenu方法的
        setHasOptionsMenu(true);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.actionbar, container, false);
        return view;
    }

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        final ActionBar actionBar = getActivity().getActionBar();
        actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);

        mSectionsPagerAdapter = new SectionsPagerAdapter(getActivity().getSupportFragmentManager());

        mViewPager = (ViewPager)view.findViewById(R.id.view_pager);
        mViewPager.setAdapter(mSectionsPagerAdapter);
        //setOnPageChangeListener已经过时,用addOnPageChangeListener代替
        mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {
                actionBar.setSelectedNavigationItem(position);
            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
//        mViewPager.setOnPageChangeListener(new ViewPager.SimpleOnPageChangeListener(){
//            @Override
//            public void onPageSelected(int position) {
//                actionBar.setSelectedNavigationItem(position);
//            }
//        });

        //设置tab的文字
        for(int i = 0; i < mSectionsPagerAdapter.getCount(); i++) {
            actionBar.addTab(actionBar.newTab().setText(mSectionsPagerAdapter.getPageTitle(i)).setTabListener(this));
        }
    }

    @Override
    public void onTabSelected(ActionBar.Tab tab, FragmentTransaction ft) {
        mViewPager.setCurrentItem(tab.getPosition());
    }

    @Override
    public void onTabUnselected(ActionBar.Tab tab, FragmentTransaction ft) {

    }

    @Override
    public void onTabReselected(ActionBar.Tab tab, FragmentTransaction ft) {

    }

    public class SectionsPagerAdapter extends FragmentPagerAdapter {

        public SectionsPagerAdapter(FragmentManager fm) {
            super(fm);
        }

        @Override
        public Fragment getItem(int position) {
            Fragment fragment;
            if (position == 0){
                fragment = new SwiperefreshFragment();
            }else if(position == 1) {
                fragment = new PulltorefreshFragment();
            }else{
                fragment = new DummySectionFragment();
                Bundle args = new Bundle();
                args.putInt(DummySectionFragment.ARG_SECTION_NUMBER, position + 1);
                fragment.setArguments(args);
            }

            return fragment;
        }

        @Override
        public int getCount() {
            return 3;
        }

        @Override
        public CharSequence getPageTitle(int position) {
            Locale l = Locale.getDefault();
            switch (position){
                case 0:
                    return "Swiperefresh";
                case 1:
                    return "Pulltorefresh";
                case 2:
                    return "Title3";
            }
            return super.getPageTitle(position);
        }
    }

    public static class DummySectionFragment extends Fragment {
        public static final String ARG_SECTION_NUMBER = "section_number";

        public DummySectionFragment(){

        }

        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                 Bundle savedInstanceState) {
            View view = inflater.inflate(R.layout.viewpager_fragment, container, false);
            TextView textView = (TextView)view.findViewById(R.id.section_label);
            textView.setText(Integer.toString(getArguments().getInt(ARG_SECTION_NUMBER)));
            return view;
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
        android:text="ActionBar"
        android:onClick="actionBar"/>
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

actionbar.xml-->使用google下拉刷新的界面

```xml
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/view_pager">

</android.support.v4.view.ViewPager>
```

viewpager_fragment.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/section_label"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</RelativeLayout>
```



