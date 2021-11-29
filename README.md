# NotePad
## 实验过程如下：


### 1.NoteList中显示条目增加时间戳显示

	  SimpleCursorAdapter.ViewBinder viewBinder = new SimpleCursorAdapter.ViewBinder() {
            @Override
            public boolean setViewValue(View view, Cursor cursor, int i) {
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE)) {
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE)){
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                return false;
            }
        };

#### 时间戳是显示在每条Note的下面，所以在noteslist_item.xml添加一个用于显示时间戳的TextView

	<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingLeft="10dp">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="10dp"
            android:text="创建时间：">
        </TextView>
        <TextView
            android:id="@+id/text_CREATE_DATE"
            android:layout_width="100dp"
            android:textSize="10dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingLeft="5dip"
            android:singleLine="true" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="10dp"
            android:text="修改时间：">
        </TextView>
        <TextView
            android:id="@+id/text_MODIFICATION_DATE"
            android:layout_width="wrap_content"
            android:textSize="10dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingLeft="5dip"
            android:singleLine="true" />

    </LinearLayout>

#### 在java文件NoteList.java中关于显示Note的函数里加上时间的显示

   	 private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_NOTE, // 2
            NotePad.Notes.COLUMN_NAME_CREATE_DATE, // 3
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 4
    };

    private String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,NotePad.Notes.COLUMN_NAME_NOTE, NotePad.Notes.COLUMN_NAME_CREATE_DATE,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;

    private int[] viewIDs = { R.id.text_TITLE,R.id.text_NOTE ,R.id.text_CREATE_DATE ,R.id.text_MODIFICATION_DATE };



    /** The index of the title column */
    private static final int COLUMN_INDEX_TITLE = 1;

    /**
     * onCreate is called when Android starts this Activity from scratch.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // The user does not need to hold down the key to use menu shortcuts.
        setDefaultKeyMode(DEFAULT_KEYS_SHORTCUT);
        /* If no data is given in the Intent that started this Activity, then this Activity
         * was started when the intent filter matched a MAIN action. We should use the default
         * provider URI.
         */
        // Gets the intent that started this Activity.
        Intent intent = getIntent();
        // If there is no data associated with the Intent, sets the data to the default URI, which
        // accesses a list of notes.
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        /*
         * Sets the callback for context menu activation for the ListView. The listener is set
         * to be this Activity. The effect is that context menus are enabled for items in the
         * ListView, and the context menu is handled by a method in NotesList.
         */
        getListView().setOnCreateContextMenuListener(this);
        /* Performs a managed query. The Activity handles closing and requerying the cursor
         * when needed.
         *
         * Please see the introductory note about performing provider operations on the UI thread.
         */
        Cursor cursor = managedQuery(
                getIntent().getData(),            // Use the default content URI for the provider.
                PROJECTION,                       // Return the note ID and title for each note.
                null,                             // No where clause, return all records.
                null,                             // No where clause, therefore no where column values.
                NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
        );
        /*
         * The following two arrays create a "map" between columns in the cursor and view IDs
         * for items in the ListView. Each element in the dataColumns array represents
         * a column name; each element in the viewID array represents the ID of a View.
         * The SimpleCursorAdapter maps them in ascending order to determine where each column
         * value will appear in the ListView.
         */


        /*
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE } ;
        // The view IDs that will display the cursor columns, initialized to the TextView in
        // noteslist_item.xml
        int[] viewIDs = { android.R.id.text1 };
        // Creates the backing adapter for the ListView.*/

        String[] dataColumns={ NotePad.Notes.COLUMN_NAME_TITLE ,NotePad.Notes.COLUMN_NAME_NOTE, NotePad.Notes.COLUMN_NAME_CREATE_DATE,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };

        int[] viewIDs = { R.id.text_TITLE,R.id.text_NOTE ,R.id.text_CREATE_DATE ,R.id.text_MODIFICATION_DATE };

        SimpleCursorAdapter adapter
                = new SimpleCursorAdapter(
                this,                             // The Context for the ListView
                R.layout.noteslist_item,          // Points to the XML for a list item
                cursor,                           // The cursor to get items from
                dataColumns,
                viewIDs
        );
        SimpleCursorAdapter.ViewBinder viewBinder = new SimpleCursorAdapter.ViewBinder() {
            @Override
            public boolean setViewValue(View view, Cursor cursor, int i) {
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE)) {
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE)){
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                return false;
            }
        };
        adapter.setViewBinder(viewBinder);

        // Sets the ListView's adapter to be the cursor adapter that was just created.
        setListAdapter(adapter);

#### 在NoteEditor.java里也添加获取时间的功能（这里是编辑Note时获取的时间），不然无法正确显示编辑Note后的时间

       private final void updateNote(String text, String title) {
        ContentValues values = new ContentValues();
        long now = System.currentTimeMillis();
        Date date = new Date(now);
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yy-MM-dd HH:mm:ss");
        String dateFormat = simpleDateFormat.format(date);
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateFormat);
        }
        
        
 ## 2.添加笔记查询功能 
 
 #### 先在顶部的菜单栏中添加查找的图标，找到菜单的xml文件，list_options_menu.xml，添加一个搜索的item
 
 	<item
        android:id="@+id/menu_search"
        android:icon="@android:drawable/ic_menu_search"
        android:title="Search"
        app:showAsAction="always" />
        
  #### 再新建一个Activity——NoteSearch,用于查找功能的布局和功能实现

  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
    />
    <ListView
        android:id="@+id/list_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
    />
    
</LinearLayout>

#### 新建一个NoteSearch.java ：

public class NoteSearch extends Activity implements SearchView.OnQueryTextListener
{
    ListView listView;
    SQLiteDatabase sqLiteDatabase;
    /**
     * The columns needed by the cursor adapter
     */
    private static final String[] PROJECTION = new String[]{
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_NOTE, //添加内容
            NotePad.Notes.COLUMN_NAME_CREATE_DATE,//添加修改时间
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//添加修改时间
    };

    public boolean onQueryTextSubmit(String query) {
        Toast.makeText(this, "您选择的是："+query, Toast.LENGTH_SHORT).show();
        return false;
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_search);
        SearchView searchView = findViewById(R.id.search_view);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        listView = findViewById(R.id.list_view);

        sqLiteDatabase = new NotePadProvider.DatabaseHelper(this).getReadableDatabase();

        searchView.setSubmitButtonEnabled(true);
        searchView.setQueryHint("查找");
        searchView.setOnQueryTextListener(this);

    }
    public boolean onQueryTextChange(String string) {
        String selection1 = NotePad.Notes.COLUMN_NAME_TITLE+" like ? or "+NotePad.Notes.COLUMN_NAME_NOTE+" like ?";
        String[] selection2 = {"%"+string+"%","%"+string+"%"};
        Cursor cursor = sqLiteDatabase.query(
                NotePad.Notes.TABLE_NAME,
                PROJECTION, // The columns to return from the query
                selection1, // The columns for the where clause
                selection2, // The values for the where clause
                null,          // don't group the rows
                null,          // don't filter by row groups
                NotePad.Notes.DEFAULT_SORT_ORDER // The sort order
        );
        // The names of the cursor columns to display in the view, initialized to the title column
        String[] dataColumns = {
                NotePad.Notes._ID, // 0
                NotePad.Notes.COLUMN_NAME_TITLE, // 1
                NotePad.Notes.COLUMN_NAME_NOTE, //添加内容
                NotePad.Notes.COLUMN_NAME_CREATE_DATE,//添加修改时间
                NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//添加修改时间
        } ;
        // The view IDs that will display the cursor columns, initialized to the TextView in
        // noteslist_item.xml

        int[] viewIDs = { R.id.text_TITLE,R.id.text_NOTE ,R.id.text_CREATE_DATE ,R.id.text_MODIFICATION_DATE };
        // Creates the backing adapter for the ListView.
        SimpleCursorAdapter adapter
                = new SimpleCursorAdapter(
                this,                             // The Context for the ListView
                R.layout.noteslist_item,         // Points to the XML for a list item
                cursor,                           // The cursor to get items from
                dataColumns,
                viewIDs
        );
        SimpleCursorAdapter.ViewBinder viewBinder = new SimpleCursorAdapter.ViewBinder() {
            @Override
            public boolean setViewValue(View view, Cursor cursor, int i) {
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE)) {
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                if(i == cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE)){
                    TextView textView = (TextView) view;
                    SimpleDateFormat time_format = new SimpleDateFormat("yyyy-MM-dd- hh:mm:ss");
                    Date date = new Date(cursor.getLong(i));
                    String time = time_format.format(date);
                    textView.setText(time);
                    return true;
                }
                return false;
            }
        };
        adapter.setViewBinder(viewBinder);
        // Sets the ListView's adapter to be the cursor adapter that was just created.
        listView.setAdapter(adapter);
        return true;
    }
}

#### 在NoteList中找到onOptionsItemSelected方法，在switch中添加搜索的case语句:
 
    case R.id.menu_search:
    Intent intent = new Intent();
    intent.setClass(NotesList.this,NoteSearch.class);
    NotesList.this.startActivity(intent);
    return true;
    
#### 最后要在AndroidManifest.xml注册NoteSearch：

    <activity
        android:name="NoteSearch"
        android:label="@string/title_notes_search">
    </activity>






       



