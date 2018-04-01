# RecycleView 教程1

我们见过在ScrollView中可以显示和操纵一列滚动的元素items，如联系人列表、播放列表、相册、字典、购物车、文章列表等。RecyclerView是一个更有效的显示滚动列表的View（也是ViewGroup的子类），不同于ScrollView为每个（即使不可见的）item创造单独的View.RecyclerView创造限定数量的View并重用这些view，从而提供效率，给用户更好的流畅的体验。

### 一 创建数据集create dataSet
  ```java
    private final LinkedList<String> wordList = new LinkedList<>();
    private int count;
    
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        for(int i = 0 ; i<20;i++) {
            wordList.add("word"+count++);
            Log.d("wordList", wordList.getLast());
        }
    }
  ```
 ### 二 创建 RecycleView
 
 为了在RecycleView中显示数据，涉及下列几个部分：
 
1）. **Data**: 可来自比如前面的仿真数据、文件、数据库、网络等
    
2) **一个RecyclerView**：滚动列表item的滚动视图
    
3) **每个item的Layout**：每个item元素如何显示
    
4) **一个layout manager**：用于如何布局所有元素，布局可以是vertical或horizontal,可以是a grid网格或瀑布流. 一共有3钟
    
5) **一个adapter**：是**data**和**RecyclerView**之间的桥梁,用于从data里得到数据，放到一个对应item的ViewGroup的容器ViewHolder中。
    
6) **一个ViewHolder**：位于adapter内部，用于存储对应一个item的Views.
   
   其机理可用下面的图示表示：
```
    Data-->Adapter(含有ViewHolder)-->RecycleView
```

** 1. build.gradle (Module: app)添加2行**
``` java   
    implementation 'com.android.support:recyclerview-v7:27.0.2'
    implementation 'com.android.support:design:27.0.2'
```
    
**2. 修改activity_main.xml的布局为"android.support.design.widget.CoordinatorLayout",并添加 RecycleView**

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="vertical">
<android.support.v7.widget.RecyclerView
android:id="@+id/recyclerview"
android:layout_width="match_parent"
android:layout_height="match_parent">
</android.support.v7.widget.RecyclerView>
</android.support.design.widget.CoordinatorLayout>
```
** 3.为每个item创建布局文件** 
 选择app/res/layout文件夹, 右键 New > Layout resource file,修改Layout为LineaLayout。添加TextView。结果如下
 ```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="6dp">

    <TextView
        android:id="@+id/word"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textSize="24sp"
        android:textStyle="bold"/>
</LinearLayout>
 ```
 
 **Create a style from the TextView attributes**
 鼠标定位在TextView，然后**Right-click >Refactor > Extract > Style.**
 勾选 the Launch **'Use Style Where Possible' box**.点击**OK**.
 
```xml
    <TextView
        android:id="@+id/word"
        style="@style/word_title" />
```

3. 创建带有ViewHolder的Adapter. **New->class**并extends 自 RecyclerView.Adapter<WordListAdapter.WordViewHolder>
```java
public class WordListAdapter extends RecyclerView.Adapter<WordListAdapter.WordViewHolder>
{
}
```
点击灯泡，选择**Implement methods**
```
public class WordListAdapter extends RecyclerView.Adapter<WordListAdapter.WordViewHolder> {

    @Override
    public WordListAdapter.WordViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        return null;
    }

    @Override
    public void onBindViewHolder(WordListAdapter.WordViewHolder holder, int position) {

    }

    @Override
    public int getItemCount() {
        return 0;
    }
}
```
将onCreateViewHolder和onBindViewHolder都引用WordViewHolder

** 3. Create the view holder **

```java
 class WordViewHolder extends RecyclerView.ViewHolder {
        private TextView wordItemView;
        private WordListAdapter adapter;
        public WordViewHolder(ViewGroup itemView, WordListAdapter adapter) {
            super(itemView);
            wordItemView = (TextView) itemView.findViewById(R.id.word);
            this.adapter = adapter;
        }
    }
```

**4. 为WordListAdapter添加constrctor并补充其他代码 **

构造函数
```java
    private LinkedList<String> wordList;
    private LayoutInflater layoutInflater;

    public WordListAdapter(Context context, LinkedList<String> wordList) {
        this.layoutInflater = LayoutInflater.from(context);
        this.wordList = wordList;
    }
```
```java
 @Override
    public void onBindViewHolder(WordListAdapter.WordViewHolder holder, int position) {

        String current = wordList.get(position);
        holder.wordItemView.setText(current);
    }

    @Override
    public int getItemCount() {
        return wordList.size();
    }
```

**修改MAinActivity的OnCreate函数**，添加：
```java
        RecyclerView recyclerView = findViewById(R.id.recyclerview);
        recyclerView.setAdapter(new WordListAdapter(this,wordList));
        recyclerView.setLayoutManager(new LinearLayoutManager(this));
```
运行该程序，看看结果

**5. 添加交互功能**
修改WordViewHolder实现View.OnClickListener事件监听
```java
 class WordViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener{
        private TextView wordItemView;
        private WordListAdapter adapter;
        public WordViewHolder(ViewGroup itemView, WordListAdapter adapter) {
            super(itemView);
            wordItemView = (TextView) itemView.findViewById(R.id.word);
            this.adapter = adapter;

            itemView.setOnClickListener(this); //设置监听器
        }

        @Override
        public void onClick(View v) {
            int pos = getLayoutPosition();//得到item的位置
            String itemWord = wordList.get(pos); //得到item的内容即文本
            wordList.set(pos, "Clicked " + itemWord);    //修改内容
            adapter.notifyDataSetChanged();//通知RecycleView修改了内容
        }
    }
```


## 三、通过FloatingActionButton按钮添加数据到RecycleView中
1. 在布局中添加FloatingActionButton
```
<android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        android:clickable="true"
        android:src="@drawable/ic_android_black_24dp"/>
```

2. 添加onClick
 为FloatingActionButton添加onClick处理器
```
 FloatingActionButton fab = findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int count = wordList.size();
                wordList.addLast("+ Word " + count);
                recyclerView.getAdapter().notifyItemInserted(count);
                recyclerView.smoothScrollToPosition(count);
            }
        });
```

## 补充
**为item添加Decoration**
```
       recyclerView.addItemDecoration(new MyItemDecoration());
   ...
   class MyItemDecoration extends RecyclerView.ItemDecoration {
        @Override
        public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
            super.getItemOffsets(outRect, view, parent, state);
            outRect.set(0, 0, 0, getResources().getDimensionPixelOffset(R.dimen.decoration_width));
        }
    }
  }
```
