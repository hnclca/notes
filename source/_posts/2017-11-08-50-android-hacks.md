## 50个Android秘诀

[TOC]

### Working your way around layouts
##### 01.weightSum与layout_weight
**局限**：仅适用于LinearLayout
*	layout_weight: 权重
*	weightSum：权重总和，缺省时等于所有layout_weight的总和

##### 02.include与ViewStub
###### include标签
**要点**：layout_*属性覆盖时，必须声明layout_width与layout_height
**技巧**：被include的布局内声明layout_width与layout_height为0dp
###### ViewStub
* layout--声明被填充的布局资源
* inflatedId--ViewStub.inflate()或ViewStub.setVisibility()返回被填充的ViewId

##### 03.custom ViewGroup
###### 继承ViewGroup与ViewGroup.LayoutParams
```
    public class CascadeLayout exends ViewGroup
    // 静态内部类
    public static class LayoutParams extends ViewGroup.LayoutParams
        // 子视国的位置
        int x;
        int y;
```

###### 定义自定义属性
```
# attrs.xml
<declare-styleable name="CascadeLayout">
	<attr name="horizontal_spacing" format="dimension" />
    <attr name="vertical_spacing" format="dimension" />
</declare-styleable>
```

###### 使用自定义属性
1.	声明命名空间
```
xmlns:cascade="http://schemas.android.com/apk/res-auto"
```
2.	声明自定义View类名，完全限定类名
```
<com.manning.adroidhacks.hack003.view.CascadeLayout>
```
3.	使用自定义属性
```
cascade:horizontal_spacing="30dp"
cascade:vertical_spacing="20dp"
```

###### 获取自定义属性
```
// 通过XML方式创建视图实例时调用
    public CascadeLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        TypeArray a = context.obtainStyledAttributes(attrs, R.styleable.CascadeLayout);
        try {
            mHorizontalSpacing = a.getDimensonPixelSize(R.styleable.CascadeLayout_horizontal_spacing, getResources().getDimensionPixelSize(R.dimen.cascade_horizontal_spacint));
            ...
        } finally {
            a.recycle(); // 用try-finally保证TypeArray实例的回收；
        }
    }
```

###### void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
```
    int width = 0;
    int height = getPaddingTop();
    final int count = getChildCount();
    View child;
    LayoutParams lp;
    for(int i = 0; i < count; i++) {
        child = getChildAt(i);
        measuredChild(child, widthMeasureSpec, heightMeasureSpec);
        LayoutParams lp = (LayoutParams)child.getLayoutParams();
        width = getPaddingLeft() + mHorizontalSpacing * i;
        lp.x = width;
        lp.y = height;
        width += child.getMeasureWidth();
        height += mVerticalSpacing;
    }
    width += getPaddingRight();
    height += getChildAt(getChildCount() - 1).getMeasuredHeight() + getPaddingBottom();

    setMeasuredDimension(resolveSize(width, widthMeasureSpec), resolveSize(height, heightMeasureSpec));
```
###### void onLayout(boolean changed, int l, int t, int r, int b)
```
    final int count = getChildCount();
    View child;
    LayoutParams lp;
    for(int i = 0; i < count; i++) {
        child = getChildAt(i);
        lp = (LayoutParams)child.getLayoutParams();
        child.layout(lp.x, lp.y, lp.x + child.getMeasuredWidth(), lp.y + child.getMeasuredHeight);
    }
```

###### 子视图添加自定义属性
####### 定义
```
# attrs.xml
<declare-styleable name="CascadeLayout_LayoutParams">
# 前缀是layout_，布局属性，与视图属性无关，被添加到LayoutParams的属性表中
	<attr name="layout_vertical_spacing" format="dimension" />
</declare-styleable>
```
###### 读取
``` 
public LayoutParams(Context context, AttribueSet attrs) {
	super(context, attrs);
    TypeArray a = context.obtainStyledAttributes(attrs, R.styleable.CascadeLayout_LayoutParams);
    try {
    	verticalSpacing = a.getDimensionPixelSize(R.styleable.CascadeLayout_LayoutParams_layout_vertical_spacing, -1);
    } finally {
    	a.recycle();
    }
}
```

##### 04.Preferences
###### XML布局
```
<PreferenceScreen
	xmlns:android="http://schemas.android.com/apk/res/android"
    android:key="pref_first_preferencescreen_key"
    android:title="Preferences">
    // 分组
    <PreferenceCategory
    	anroid:title="Application">
        <EditTextPreference 
        	android:key="pref_username"
            android:summary="Username"
            android:title="Username"/>
        // 发送Intent的选项
    	<Preference 
        	android:key="pref_rate"
            android:summary="Rate the app in the store"
            android:title="Rate the app"/>
        // 自定义Preference
        <com.manning.androidhacks.hack004.preference.AboutDialog 
        	android:dialogIcon="@drawable/ic_launcher"
            android:dialogTitle="About"
            android:key="pref_about_key"
            android:negativeButtonText="@null" 
            android:title="About"/>
    </PreferenceCategory>
</PreferenceScreen>
```

###### 继承PreferenceActivity
```
public class MainActivity extends PreferenceActivity implements OnSharedPreferenceChangeListener {
	@Override
    public void onCreate(Bundle savedInstanceState) {
    	super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.prefs);
        ...
        Preference ratePref = findPreference("pref_rate");
        Uri uri = Uri.parse("market://details?id=" + getPackageName());
        Intent goToMarket = new Intent(Intent.ACTION_VIEW, uri);
        ratePref.setIntent(goToMarket);
    }
    
    @Override
    protected void onResume() {
    	getPreferenceScreen().getSharedPreferences().registerOnSharedPreferenceChangeListener(this);
    }
    
    @Override
    protected void onPause() {
    	getPreferenceScreen().getSharedPreferences().unregisterOnSharedPreferenceChangeListener(this);
    }
    
    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
    	if(key.equals("pref_username")) {
        	updateUserText();
        }
    }
    
    private void updateUserText() {
    	EditTextPreference pref;
        pref = (EditTextPreference)findPreference("pref_username");
        String user = pref.getText();
        if(user == null) {
        	user = "?";
        }
        pref.setSummary(String.format("Username: %s", user));
    }
}
```

###### 自定义Preference控件
```
public class EmailDialog extends DialogPreference {
	Context mContext;
    
    public EmailDialog(Context context) {
    	this(context, null);
    }
    
    public EmailDialog(Context context, AttributeSet attrs) {
    	this(context, attrs, 0)
    }
    
    public EmailDialog(Context context, AttributeSet attrs, in defStyle) {
    	super(context, attrs, defStyle);
        mContext = context;
    }
    
    @Override
    public void onClick(DialogInterface dialog, int which) {
    	super.onClick(dialog, which);
        if(DialogInterface.BUTTON_POSITIVE == which}) {
        	LaunchEmailUtil.launchEmailToIntent(mContext);
        }
    }
}
```


### Creating cool animations
##### 05.TextSwitcher与ImageSwitcher
```
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
    Animation in = AnimationUtils.loadAnimation(this, android.R.anim.fade_in);
    Animation out = AnimationUtils.loadAnimation(this, android.R.anim.fade_out);
    mTextSwitcher = (TextSwitcher)findViewById(R.id.your_textview);
    mTextSwitcher.setFactory(new ViewFactory(){
    	public View makeView() {
        	TextView t = new TextView(YourActivity.this);
            t.setGravity(Gravity.CENTER);
            return t;
        }
    });
    mTextSwitcher.setInAnimation(in);
    mTextSwitcher.setOutAnimation(out);
```

##### 06.ViewGroup
核心类：LayoutAnimationController
*	find ListView
*	create AnimationSet: AlphaAnimation + TranslateAnimation
*	create LayoutAnimationController
*	set LayoutAnimationController to ListView

##### 07.Canvas
核心要点：call invalidate in onDraw(Canvas canvas)
> Canvas:可视为Surface的替身与接口，图形绘制在Surface上。其封装了所有的绘图调用。通过Canvas，绘制到Surface上的内容首先存储到与之关联的Bitmap中，该Bitmap最终会呈现到窗口上。

##### 08.Ken Burns
关键点：
*	create AnimationSet randomly and start
*	anim.addListener(this) in onAnimationEnd
*	FrameLayout.addView/removeView in onAnimationEnd Event
*	call  in onAnimationEnd Event


### View tips and tricks
##### 09.EditText
避免日期验证，用Button/TextView + DatePicker进行日期输入；

##### 10.TextView格式化文本
*	HTML标签--Html.fromHtml
	*	仅支持部分标签
*	SpannableString类

##### 11.Text glowing effects
*	Typeface--设置字体
```
    AssetManager assets = context.getAssets();
    final Typeface font = Typeface.createFromAsset(asset,FONT_DIGITAL_7);
    setTypeface(font)
```
*	shadow效果
```
android:shadowColor="#00ff00"
android:shadowDx="0"
android:shadowDy="0"
android:shadowRadius="10"
```

##### 12.Round borders
ShapeDrawable类
```
<shape xmlns:android="http://schema.android.com/apk/res/android"
	android:shape="rectangle">
    <solid android:color="#AAAAAA" />
    <corners android:radius="15dp" />
</shape>
```

##### 13.Measure view's size in the onCreate method
```
view.post(new Runnable() {
	@Override
    public void run() {
    	Log.d(TAG, "view has width: " + view.getWidth() + 
        	" and height: " + view.getHeight());
    }
});
```

##### 14.VideoView onConfigurationChanged
*	竖屏--通过占位View获取位置与大小设置VideoView
*	横屏--获取屏幕的宽高
*	onConfigurationChanged -- android:configChanges="orientation"

##### 15.Removing the background
```
<style name="Theme.NoBackground" parent="android:Theme">
	<item name="android:windowNoTitle">true</item>
    <item name="android:windowBackground">@null</item>
</style>
```

##### 16.Toast's position
```
Toast toast = Toast.makeText(this, "Botton Right", Toast.LENGTH_SHORT);
toast.setGravity(Gravity.BOTTOM|Gravity.RIGHT, 0, 0);
toast.show();
```

##### 17.Gallery
填充较长表单
*	Gallery + Adapter
*	setDelegate onResume/onPause
*	gallery无法添加页面切换动画
```
// 取巧方式
mGallery.onKeyDown(KeyEvent.KEYCODE_DPAD_RIGHT, new KeyEvent(0,0));
```

### Tools
##### 18.Removing log statements
ProGuard
```
-assumenosideeffects class android.util.Log {
	public static *** d(...);
}
```
##### 19.Hierarchy Viewer Tool
分析视图树--高度、测量、布局、绘制

### Patterns
##### 20.MVP patterns
*	Activity/Fragment implement View
*	Activity/Fragment has Presenter Field
*	Presenter.setView(this) in Activity/Fragment
*	Presenter has Model Field

##### 21.BroadcastReceiver
*	instantiate Receiver and IntentFilter in onCreate
*	register/unregister Receiver in onResume/onPause
*	updateUI in onReceive

##### 22.Architecture Pattern
*	jar
*	android lib
*	android application

##### 23.The SyncAdapter Pattern
###### AsyncTask
*	转屏易崩溃
*	并发数量有限制
*	仅在后台任务简单且不依赖于执行结果时使用

###### Service
*	与Activity交互
	*	是否与Activity绑定
	*	是否使用Handler
	*	是否使用Intent
	*	是否共享数据库
*	何时启动Service
*	运行时需要检查连接
*	持久化数据

###### SyncAdapter
*	后台自动同步--Android平台注册
*	服务器身份验证--设置中账户与同步
*	网络自动重连
*	后台同步偏好设置--设置中账户与同步

```
// data： DatabaseHelper, TodoContentProvider, TodoDAO
public class DatabaseHelper extends SQLiteOpenHelper
super(context, DATABASE_NAME, null, DATABASE_VERSION)
public void onCreate(SQLiteDatabase db)
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
```

```
public class TodoContentProvider extends ContentProvider
public static final String AUTHORITY = TodoContentProvider.class.getCanonicalName();
private static final int TODO = 1;
private static final int TODO_ID = 2;
public static final String CONTENT_TYPE = "vnd.android.cursor.dir/vnd.androidhacks.todo";
public static final String CONTENT_TYPE_ID = "vnd.android.cursor.item/vnd.androidhacks.todo";
public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITY + "/" + TODO_TABLE_NAME);
static {
	sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
    sUriMatcher.addURI(AUTHORITY, TODO_TABLE_NAME, TODO);
    sUriMatcher.addURI(AUTHORITY, TODO_TABLE_NAME + "/#", TODO_ID);
    
    projectionMap = new HashMap<String, String>();
    projectionMap.put(COLUMN_ID, COLUMN_ID);
    ...
}
public boolean onCreate() {
	dbHelper = new DatabaseHelper(getContext());
    return true;
}
public Cursor query(...) {
	SQLiteQueryBuilder qb = new SQLiteQueryBuilder();
    qb.setTables...
    qb.setProjectionMap...
    qb.appendWhere...
    
    SQLiteDatabase db = dbHelper.getReadableDatabase();
    Cursor c = qb.query(db, ...);
    c.setNotificationUri(getContext().getContentResolver(), uri);
    return c;
}

public class TodoDAO
private TodoDAO()
public static TodoDAO getInstance
public void addNewTodo(...)

// TodoAdapter
public class TodoAdapter extends CursorAdapter
private static Cursor getManagedCursor(Activity activity) {
	return activity.managedQuery(TodoContentProvider.CONTENT_URI, PROJECTION_IDS_TITLE_AND_STATUS, TodoContentProvider.COLUMN_STATUS_FLAG + " != " + StatusFlag.DELETE, null, TodoContentProvider.DEFAULT_SORT_ORDER);
}
TodoDAO.getInstance().deleteTodo(mActivity.getContentResolver(), id);

// AccountManager
mAccountManager = AccountManager.get(this);
Account[] accounts = mAccountManager.getAccountsByType(AuthenticationActivity.PARAM_ACCOUNT_TYPE);
String password = mAccountManager.getPassword(accounts[0]);
Account account = new Account(username, PARAM_ACCOUNT_TYPE);
mAccountManager.addAccountExplicitly(account, password, null);
mAccountManager.setPassword(account, password);

// Authenticator
public class Authenticator extends AbstractAccountAuthenticator
public Bundle addAccount
public Bundle getAuthToken // 登陆服务器时调用
loginResponse = LoginServiceImpl.sendCredentials(account.name, password);
verified = LoginServiceImpl.hasLoggedIn(loginResponse);

// AuthenticatorActivity
handleLogin
onAuthenticationonResult
finishLogin
setAccountAuthenticatorResult
setResult

// AuthenticationService
<service android:name=".authenticator.AuthenticationService" android:exported="true">
	<intent-filter>
    	<action android:name="android.accounts.AccountAuthenticator"/>
    </intent-filter>
    
    <meta android:name="android.accounts.AccountAuthenticator"
        android:resource="@xml/authenticator"/>
</service>
<account-authenticator
	xmlns:android=...
    android:accountType="com.manning.androidhacks.hack023"
    android:icon=...
    android:smallIcon=...
    android:label=... />
    
// SyncAdapter
<service android:name=".service.TodoSyncAdapter" android:exported="true">
	<intent-filter>
    	<action android:name="android.content.SyncAdapter"/>
    </intent-filter>
    
    <meta android:name="android.content.SyncAdapter"
        android:resource="@xml/todo_sync_adapter"/>
</service>
<sync-adapter
	xmlns:android=...
    android:contentAuthority="com.manning.androidhacks.hack023.provider.TodoContentProvider"
    android:accountType="com.manning.androidhacks.hack023" />
    
public class TodoSyncAdapter extends AbstractThreadedSyncAdapter
public void onPerformSync
List<Todo> data = fetchData();
syncRemoteDeleted(data);
syncFromServerToLocalStorage(data);
syncDirtyToServer(mTodoDAO.getDirtyList(mContentResolver));

// TodoSyncService
synchronized (sSyncAdapterLock) {
	if (sSyncAdapter == null) {
    	sSyncAdapter = new TodoSyncAdapter(getApplicationContext(), true);
    }
}
sSyncAdapter.getSyncAdpaterBinder();
```

### Working with lists and adapters
##### 24.Handling empty lists
*	ListView and EmptyView are siblings
*	mListView.setEmptyView(mEmptyView);

##### 25.ViewHolder
* convertView == null
* convertView.setTag(viewHolder)/getTag()
* viewHolder.textView.setText...
* static class ViewHolder

##### 26.Section headers
* avoid override getViewTypeCount
* itemView = headers + itemView
* mListView.setOnScrollListener

##### 27.Communicating with an adapter
*	public static interface NumbersAdapterDelegate
*	public void setDelegate(NumbersAdapterDelegate delegate)
*	mAdapter.setDelegate in onResume/onPause
*	Activity/Fragment implements NumbersAdapterDelegate

##### 28.ListView's header
*	mHeader = inflator.inflate(R.layout.header, mListView, false);
*	mHeader.setLayoutParams(new ListView.Params(ListView.LayoutParams.MATCH_PARENT, ListView.LayoutParams.WRAP_CONTENT));
*	mListView.addHeaderView(mHeader, null, false);

##### 29.Handing orientation changes inside a ViewPager
*	mViewPager.setOnPageChangeListener(...);
*	setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
*	setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_SENSOR);

##### 30.ListView's choicemode
*	mListView.getCheckedItemPosition();
*	itemView implements Checkable
```
// ListView 源码
    if (mChoiceMode != CHOICE_MODE_NONE && mCheckStates != null) {
        if (child instanceof Checkable) {
            ((Checkable) child).setChecked(mCheckStates.get(position))
        }
    }
```
*	disable clickable in childs of itemView
```
android:clickable = "false"
android:focusable = "false"
android:focusableInTouchMode = "false"
```

### Useful libraries
##### 31.Aspect-oriented programming
##### 32.Cocos2D-x

### Interacting with other languages
##### 33.Objective-C
##### 34.Scala

### Ready-to-use snippets
##### 35.Multiple Intents
##### 36.Getting use information when receiving feedback
##### 37.Adding an mp3 to the media content-provider
##### 38.Adding a refresh action to the action bar
##### 39.Getting Denpendencies
##### 40.LIFO image loading

### Beyond database basics
##### 41.ORMLite
##### 42.Creating custom functions in SQLite
##### 43.Batching database operations

### Avoiding fragmentation
##### 44.Handing lights-out mode
##### 45.Using new APIs in older devices
##### 46.Backward-compatible notifications
##### 47.Creating tabs with fragments

### Building tools
##### 48.Handing dependencies with apache maven
##### 49.Installing dependencies in a rooted device
##### 50.Using Jenkins to deal with device diversity