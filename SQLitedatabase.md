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
2. 
