
### 创建数据集create dataSet
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
 ### 创建 RecycleView

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
    android:layout_height="match_parent"
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
