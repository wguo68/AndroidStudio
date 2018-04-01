1. 创建WordListOpenHelper
```
public class WordListOpenHelper extends SQLiteOpenHelper {

    private String database_name;
    private  int version  =1;
    private static String WORD_LIST_TABLE_CREATE ;
    private static final String WORD_LIST_TABLE = "word_entries";
    // Column names...
    public static final String KEY_ID = "_id";
    public static final String KEY_WORD = "word";
    // ... and a string array of columns.
    private static final String[] COLUMNS = { KEY_ID, KEY_WORD };

    public WordListOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
        this.database_name = name;
        this.version = version;
        WORD_LIST_TABLE_CREATE =       "CREATE TABLE " + WORD_LIST_TABLE + " (" +
                KEY_ID + " INTEGER PRIMARY KEY, " +            KEY_WORD + " TEXT );";
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(WORD_LIST_TABLE_CREATE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
    private void fillDatabaseWithData(SQLiteDatabase db){
        
    }
}

```
2. 修改WordListOpenHelper，添加数据到数据库
```
public void onCreate(SQLiteDatabase db) {
        db.execSQL(WORD_LIST_TABLE_CREATE);
        fillDatabaseWithData(db);
    }
```
其中私有函数 fillDatabaseWithData(SQLiteDatabase db)
```
 private void fillDatabaseWithData(SQLiteDatabase db){
        String[] words = {"Android", "Adapter", "ListView", "AsyncTask",
                "Android Studio", "SQLiteDatabase", "SQLOpenHelper",
                "Data model", "ViewHolder","Android Performance",
                "OnClickListener"};

        // Create a container for the data.
        ContentValues values = new ContentValues();

        //插入数据到数据路库
        for (int i=0; i < words.length; i++) {
         // Put column/value pairs into the container.
        // put() overrides existing values.
            values.put(KEY_WORD, words[i]);
            db.insert(WORD_LIST_TABLE, null, values);
        }
    }
```
添加insert(String word)
```
public long insert(String word){
        long newId = 0;
        ContentValues values = new ContentValues();
        values.put(KEY_WORD, word);
        try {
            if (mWritableDB == null) {
                mWritableDB = getWritableDatabase();
            }
            newId = mWritableDB.insert(WORD_LIST_TABLE, null, values);
        } catch (Exception e) {
            Log.d(TAG, "INSERT EXCEPTION! " + e.getMessage());
        }
        return newId;
    }
```

添加count()方法
```
public long count(){
        if (mReadableDB == null) {
            mReadableDB = getReadableDatabase();
        }
        return DatabaseUtils.queryNumEntries(mReadableDB, WORD_LIST_TABLE);
    }
```
并修改实现WordListAdapter的getItemCount()
```
   @Override
    public int getItemCount() {
       // return wordList.size();
        return (int) mDB.count();
    }
```

3. 创建WordItem类，用alt+insert生成getter和setter方法
```
public class WordItem {
    private int Id;
    private String Word;

    public int getId() {
        return Id;
    }

    public String getWord() {
        return Word;
    }

    public void setId(int id) {
        Id = id;
    }

    public void setWord(String word) {
        Word = word;
    }

    public WordItem() {
    }
}
```

4. 创建2个数据库变量，并添加query方法
```
    private SQLiteDatabase mWritableDB;
    private SQLiteDatabase mReadableDB;

   //...
   
   public WordItem query(int position) {
        String query = "SELECT * FROM " + WORD_LIST_TABLE +
                " ORDER BY " + KEY_WORD + " ASC " +
                "LIMIT " + position + ",1";
        Cursor cursor = null;
        WordItem entry = new WordItem();
        try {
            if (mReadableDB == null) {
                mReadableDB = getReadableDatabase();
            }
            cursor = mReadableDB.rawQuery(query, null);
            cursor.moveToFirst();
            entry.setId(cursor.getInt(cursor.getColumnIndex(KEY_ID)));
            entry.setWord(cursor.getString(cursor.getColumnIndex(KEY_WORD)));
        } catch (Exception e) {
            Log.d(TAG, "QUERY EXCEPTION! " + e.getMessage());
        } finally {
            cursor.close();
            return entry;
        }
    }
    
    
```
```java
  @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        Log.w(WordListOpenHelper.class.getName(),
                "Upgrading database from version " + oldVersion + " to "
                        + newVersion + ", which will destroy all old data");
        db.execSQL("DROP TABLE IF EXISTS " + WORD_LIST_TABLE);
        onCreate(db);
    }
```
### 4. Display data in the RecyclerView

1.修改WordListAdapter

修改构造函数和成员变量
```
  /*
      private LinkedList<String> wordList;
    public WordListAdapter(Context context, LinkedList<String> wordList) {
        this.layoutInflater = LayoutInflater.from(context);
        this.wordList = wordList;
    }
    */
    WordListOpenHelper mDB;
    public WordListAdapter(Context context, WordListOpenHelper db) {
        mInflater = LayoutInflater.from(context);
        mContext = context;
        mDB = db;
    }
```
修改
```
 @Override
    public void onBindViewHolder(WordListAdapter.WordViewHolder holder, int position) {
        WordItem current = mDB.query(position);
        holder.wordItemView.setText(current.getWord());
        /*
        String current = wordList.get(position);
        holder.wordItemView.setText(current);*/
    }

```
修改MainActivity
```
private WordListOpenHelper mDB;
protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
     
        mDB = new WordListOpenHelper(this,"wordlist",null,1);
        final RecyclerView recyclerView = findViewById(R.id.recyclerview);
       // recyclerView.setAdapter(new WordListAdapter(this, wordList));
        recyclerView.setAdapter( new WordListAdapter (this, mDB));
    
}
```
