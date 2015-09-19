---
layout: post
title: 异步加载网络图片
category: android
keywords: android,图片,异步
---
<script type="text/javascript">
    hljs.initHighlightingOnLoad();
</script>

最近在学习Android开发，记录一下异步加载图片的实现.
首先我们要了解一下工具类AsyncTask,通过继承该类可以实现一个异步任务，包括以下几个方法：
1、execute(Params... params);执行一个异步任务，需要我们在代码中调用此方法，触发异步任务的执行。
2、onPreExecute();在execute(Params... params)被调用后立即执行，一般用来在执行后台任务前对UI做一些标记。
3、doInBackground(Params... params);在onPreExecute()完成后立即执行，用于执行较为费时的操作，此方法将接收输入参数和返回计算结果。在执行过程中可以调用publichProgress(Progress... values)来更新进度信息。
4、onProgressUpdate(Progress... values);在调用publishProgress时，此方法被执行，直接将进度信息更新到UI组件上。
5、onPostExecute(Result result);当后台操作结束时，此方法将会被调用，计算结果将做为参数传递到此方法中，直接将结果显示到UI组件上。

下面来看下异步加载网络图片示例：

```java
public class AsyncTaskActivity extends Activity {

    private ImageView imageView;
    private ProgressBar progressBar;
    private static final String URL =
            "http://f.hiphotos.baidu.com/baike/w%3D268/sign=67b842bad5ca7bcb7d7bc02986086b3f/5243fbf2b2119313a5c884c967380cd791238d3b.jpg";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_async_task);

        imageView = (ImageView)findViewById(R.id.iv_image);
        progressBar = (ProgressBar)findViewById(R.id.pb_bar);

        new MyAsyncTask().execute(URL);

    }

    class MyAsyncTask extends AsyncTask<String, Void, Bitmap>{

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            progressBar.setVisibility(View.VISIBLE);//显示ProgressBar

        }

        /**
         * 只有这个方法在其他线程中运行，不能在这里更新UI
         * @param params
         * @return
         */
        @Override
        protected Bitmap doInBackground(String... params) {
            String url = params[0];
            Bitmap bitmap = null;
            URLConnection conn;
            InputStream is;
            try {
                conn = new URL(url).openConnection();
                is = conn.getInputStream();
                BufferedInputStream bis = new BufferedInputStream(is);
                bitmap = BitmapFactory.decodeStream(bis);

                bis.close();
                is.close();
            }catch (Exception e){
                e.printStackTrace();
            }
            return bitmap;
        }

        /**
         * 操作UI
         * @param bitmap
         */
        @Override
        protected void onPostExecute(Bitmap bitmap) {
            super.onPostExecute(bitmap);
            progressBar.setVisibility(View.GONE);
            imageView.setImageBitmap(bitmap);
        }

        @Override
        protected void onProgressUpdate(Void... values) {
            super.onProgressUpdate(values);
        }
    }
}
```

在AndroidManifest.xml中加入访问网络的权限：

```xml
<uses-permission android:name="android.permission.INTERNET"/> 
```

上面代码实现了加载网络图片，同时加载的时候会出现加载的一个图标。
我们在使用的时候，有几点需要特别注意：

1、异步任务的实例必须在UI线程中创建。
2、execute()方法必须在UI线程中调用。
3、不要手动调用除execute()的其它几个方法。
4、不能在doInBackground()方法中更改UI组件的信息。
5、一个任务实例只能执行一次，如果执行第二次将会抛出异常。


















