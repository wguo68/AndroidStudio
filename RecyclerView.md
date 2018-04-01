
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
