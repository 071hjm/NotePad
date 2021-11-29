# NotePad
## 实验过程如下：


### 1.NoteList中显示条目增加时间戳显示

	   Date date = new Date(now);
    SimpleDateFormat format = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
    format.setTimeZone(TimeZone.getTimeZone("GMT+08:00"));
    String formatDate = format.format(date);

###  添加判断条件

	   if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, formatDate);
        }

    if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, formatDate);
    }

#### 时间戳是显示在每条Note的下面，所以在noteslist_item.xml添加一个用于显示时间戳的TextView

	<TextView
        android:id="@+id/text1_date"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textSize="17dp"
        android:paddingLeft="5dip"
    />

#### 在java文件NoteList.java中关于显示Note的函数里加上时间的显示

   	private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE //日期
    };
    private static final int COLUMN_INDEX_TITLE = 1;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setDefaultKeyMode(DEFAULT_KEYS_SHORTCUT);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        getListView().setOnCreateContextMenuListener(this);
        Cursor cursor = managedQuery(
            getIntent().getData(),            
            PROJECTION,                       
            null,                             
            null,                             
            NotePad.Notes.DEFAULT_SORT_ORDER 
        );
        String[] dataColumns = {
                NotePad.Notes.COLUMN_NAME_TITLE,
                NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE
        } ;
        int[] viewIDs = {
                android.R.id.text1,
                R.id.text1_date
        };
        SimpleCursorAdapter adapter
            = new SimpleCursorAdapter(
                      this,                             
                      R.layout.noteslist_item,         
                      cursor,                           
                      dataColumns,
                      viewIDs
              );
        setListAdapter(adapter);
    }

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
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE//时间
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
        //设置该SearchView显示搜索按钮
        searchView.setSubmitButtonEnabled(true);

        //设置该SearchView内默认显示的提示文本
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
                NotePad.Notes.COLUMN_NAME_TITLE,
                NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE
        } ;
        // The view IDs that will display the cursor columns, initialized to the TextView in
        // noteslist_item.xml
        int[] viewIDs = {
                android.R.id.text1,
                android.R.id.text2
        };
        // Creates the backing adapter for the ListView.
        SimpleCursorAdapter adapter
                = new SimpleCursorAdapter(
                this,                             // The Context for the ListView
                R.layout.noteslist_item,         // Points to the XML for a list item
                cursor,                           // The cursor to get items from
                dataColumns,
                viewIDs
        );
        // Sets the ListView's adapter to be the cursor adapter that was just created.
        listView.setAdapter(adapter);
        return true;
    }}

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






       



