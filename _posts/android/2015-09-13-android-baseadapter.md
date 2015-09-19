---
layout: post
title: ListView性能优化
category: android
keywords: android,ListView,缓存机制
---

我们都知道在android中列表的显示需要三个元素：ListView、Adapter以及显示的数据，这里的Adapter就是用到了适配器模式，不管传入的是什么View在ListView中都能显示出来。下面是一个展示ListView的示例：

```java
public class BaseAdapterActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_base_adapter);
        initDatas();
    }

    private void initDatas(){
        List<ItemBean> list = new ArrayList<ItemBean>();
        for(int i = 0; i < 20; i++){
            ItemBean item = new ItemBean(R.mipmap.ic_launcher, "我是标题" + i, "我是内容" + i);
            list.add(item);
        }
        ListView listView = (ListView)findViewById(R.id.lv_main);
        listView.setAdapter(new ItemAdapter(this, list));
    }
}
```

```java
public class ItemAdapter extends BaseAdapter {
    private List<ItemBean> list;
    private LayoutInflater liflater;
    public ItemAdapter(Context context, List<ItemBean> list) {
        this.list = list;
        liflater = LayoutInflater.from(context);
    }

    @Override
    public int getCount() {
        return list.size();
    }

    @Override
    public Object getItem(int position) {
        return list.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        /*
        方式一：没有利用listview的缓存机制
        没有任何优化处理，每次都创建新的View，设置控件

        View view = liflater.inflate(R.layout.item, null);
        ImageView imageView = (ImageView)view.findViewById(R.id.iv_image);
        TextView title = (TextView)view.findViewById(R.id.tv_title);
        TextView content = (TextView)view.findViewById(R.id.tv_content);
        ItemBean bean = list.get(position);
        imageView.setImageResource(bean.getImage());
        title.setText(bean.getTitle());
        content.setText(bean.getContent());
        return view;
*/
        /*
        方式二：利用了listview的缓存机制，如果没有缓存才会创建新的view
        但是findViewById依然会浪费大量时间

        if(convertView == null){
            convertView = liflater.inflate(R.layout.item, null);
        }
        ImageView imageView = (ImageView)convertView.findViewById(R.id.iv_image);
        TextView title = (TextView)convertView.findViewById(R.id.tv_title);
        TextView content = (TextView)convertView.findViewById(R.id.tv_content);
        ItemBean bean = list.get(position);
        imageView.setImageResource(bean.getImage());
        title.setText(bean.getTitle());
        content.setText(bean.getContent());
        return convertView;
*/

        /*
        方式三：
        不仅利用了listview的缓存，更通过viewHolder类
        来实现显示数据的视图的缓存，
        避免多次通过findViewById寻找控件
         */
        ViewHolder viewHolder;
        if(convertView == null){
            viewHolder = new ViewHolder();
            convertView = liflater.inflate(R.layout.item, null);
            viewHolder.imageView = (ImageView)convertView.findViewById(R.id.iv_image);
            viewHolder.title = (TextView)convertView.findViewById(R.id.tv_title);
            viewHolder.content = (TextView)convertView.findViewById(R.id.tv_content);
            convertView.setTag(viewHolder);
        }else {
            viewHolder = (ViewHolder)convertView.getTag();
        }
        ItemBean bean = list.get(position);
        viewHolder.imageView.setImageResource(bean.getImage());
        viewHolder.title.setText(bean.getTitle());
        viewHolder.content.setText(bean.getContent());
        return convertView;
    }

    class  ViewHolder{
        private ImageView imageView;
        private TextView title;
        private TextView content;
    }
}
```

## 总结
对于刚上路的我们，平时写代码很可能就写出了方式一、二的情况，从代码中我们很容易就看出了其中的差别，对于还是新手的我们要避免一、二两种情况的发生。


















