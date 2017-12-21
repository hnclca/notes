---
title: android.arch.persistence.room
comments: false
toc: true
date: 1970-01-01 08:00:00
tags:
  - references
  - android
---

Database -- 标识数据库类注解，必须是RoomDatabase抽象子类。
Entity -- 标识数据库实体类注解
Dao -- 标识数据访问对象(DAO)注解

``` java
// File: User.java
 @Entity
 public class User {
   @PrimaryKey
   private int uid;
   private String name;
   @ColumnInfo(name = "last_name")
   private String lastName;
   // getters and setters are ignored for brevity but they are required for Room to work.
 }
 // File: UserDao.java
 @Dao
 public interface UserDao {
   @Query("SELECT * FROM user")
   List<User> loadAll();
   @Query("SELECT * FROM user WHERE uid IN (:userIds)")
   List<User> loadAllByUserId(int... userIds);
   @Query("SELECT * FROM user where name LIKE :first AND last_name LIKE :last LIMIT 1")
   User loadOneByNameAndLastName(String first, String last);
   @Insert
   void insertAll(User... users);
   @Delete
   void delete(User user);
 }
 // File: AppDatabase.java
 @Database(entities = {User.java})
 public abstract class AppDatabase extends RoomDatabase {
   public abstract UserDao userDao();
 }

 AppDatabase db = Room
     	.databaseBuilder(getApplicationContext(),
     			AppDatabase.class, "database-name")
     	.build();
```

<!-- more -->
#### Annotations
##### Database
**摘要**：
标识数据库注解，必须是抽象类，并继承RoomDatabase。
使用Room.databaseBuilder和Room.inMemoryDatabaseBuilder获取实现类。
推荐使用Dao进行数据库操作。相较于直接运行SQL查询语句，使用Dao可以抽象数据库操作到一个易于模拟测试的逻辑层。它自动转换Cursor为应用类，无需在执行数据访问时使用低级database APIs。
Room在应用编译时验证Dao中的SQL查询语句，发生错误时会及时通知。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
int	version()
Class[]	entities()

// 设置注解处理器参数(room.schemaLocation)导出Schema。
boolean	exportSchema()
```

**示例代码**：
``` java
 // User and Book are classes annotated with @Entity.
 @Database(version = 1, entities = {User.class, Book.class})
 abstract class AppDatabase extends RoomDatabase() {
     // BookDao is a class annotated with @Dao.
     abstract public BookDao bookDao();
     // UserDao is a class annotated with @Dao.
     abstract public UserDao userDao();
     // UserBookDao is a class annotated with @Dao.
     abstract public UserBookDao userBookDao();
 }
```

##### Entity
**摘要**：
标识数据库实体类注解，该类存在对应的数据库表。
必须有至少一个PrimaryKey注解标识字段，也可使用primaryKeys()定义主键。
每个实体类必须有一个无参构造器或参数与字段一致的有参构造器。有参构造器不必使用所有字段作为参数，但一个字段如果不出现在参数时，那么字段不能是公有或有公有设置器setter。如果有匹配的构造器，Room就会调用。如果不想使用构造器，使用Ignore注解。
使用Ignore注解无需持久化的字段。
被transient修饰的字段，自动是Ignore的，除非拥有ColumnInfo, Embedded和 Relation注解。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
String	tableName()
String[]	primaryKeys()
ForeignKey[]	foreignKeys()
Index[]	indices()
/**
* 默认是false
* 设置为true，将继承所有父类的索引，哪怕有父类的该值设置为false。
* 如果父类的索引删除了，在编译时将收到INDEX_FROM_PARENT_FIELD_IS_DROPPED 和INDEX_FROM_PARENT_IS_DROPPED警告。
*/
boolean	inheritSuperIndices()
```

**示例代码**：
``` java
 @Entity
 public class User {
   @PrimaryKey
   private final int uid;
   private String name;
   @ColumnInfo(name = "last_name")
   private String lastName;

   public User(int uid) {
       this.uid = uid;
   }
   public String getLastName() {
       return lastName;
   }
   public void setLastName(String lastName) {
       this.lastName = lastName;
   }
 }
```

##### Ignore
**摘要**：
标识在Room处理逻辑中忽略的元素。
可在多个地方使用，比如添加给一个字体类的字段，Room则不会处理该字段的持久化。

**库名**：
android.arch.persistence.room:common:1.0.0

##### ColumnInfo
**摘要**：
允许自定义标识字段相关联的数据列。绍指定列名或者改变列类型关联。

**库名**：
android.arch.persistence.room:common:1.0.0

**常量**：
``` java
// 字段名作为列名。
String	INHERIT_FIELD_NAME

// 存储数据类型：整数或逻辑值、浮点数、字符串、二进制数据、未定义。
int	INTEGER
int	REAL
int	TEXT
int	BLOB
int	UNDEFINED

// 排序规则：区分大小写、不区分大小写、忽略尾部空白区分大小写、未指定。
int	BINARY
int	NOCASE
int	RTRIM
int	UNSPECIFIED
```

**公有方法**：
``` java
String	name()
boolean	index()
// 列的排序规则，构造数据库时使用
int	collate()
// 列的数据类型，构造数据库时使用
int	typeAffinity()
```

##### ColumnInfo.Collate
**摘要**：
列排序规则。

**库名**：
android.arch.persistence.room:common:1.0.0

##### ColumnInfo.SQLiteTypeAffinity
**摘要**：
列的数据类型常量。

**库名**：
android.arch.persistence.room:common:1.0.0

##### PrimaryKey
**摘要**：
标识实体类的字段为主键。
如果想使用联合主键，使用primaryKeys()方法。
每个实体都必须定义主键，除非它的父类已定义。如果父类与子类都定义了主键，子类的主键将覆盖父类的主键。
如果在Embeded标识的字段时使用主键标识，则所有继承该字段的列将成为联合主键，包括其后代字段。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
// 设置为true时，SQLite将生成唯一ID。数据类型应为INTEGR。
// 字段类型是long或int时，插入方法会视0为未设置状态。
// 字段类型是Long或Int时，插入方法会视null为未设置状态。
boolean	autoGenerate()
```

##### ForeignKey
**摘要**：
标识实体类的字段为外键。
外键允许指定实体类间的约束关系，修改数据库时SQLite会验证关系正确性。
当外键指定时，SQLite要求指向的列是父表唯一索引的一部分或是表的主键。必须在父实体中创建一个包含引用列的唯一索引，在编译过程中，Room会验证，如果没有将打印错误。
推荐在子表中创建索引，以避免父表修改时全表扫描。子表没有合适的索引，Room会打印
MISSING_INDEX_ON_FOREIGN_KEY_CHILD警告。
在事务完成前外键约束被延迟，在批量插入的单个事物中特别有用。外键约束是即时性的，可通过deferred()设置延迟性，也可以使用defer_foreign_keys设置延迟，这取决于具体的事务操作。

**库名**：
android.arch.persistence.room:common:1.0.0

**常量**：
``` java
// 父表删除或更新时可能动作：无动作、限制(父表操作)、外键列值为NULL、外键列值为默认值、级联操作。
int	NO_ACTION
int	RESTRICT
int	SET_NULL
int	SET_DEFAULT
int	CASCADE
```

**公有方法**：
``` java
// 引用的父表
Class	entity()
String[]	parentColumns()
String[]	childColumns()
boolean	deferred()

// 父表删除或更新时动作。
int	onDelete()
int	onUpdate()
```

##### ForeignKey.Action
**摘要**：
定义父表引用列删除和更新时，外键列动作常量。

**库名**：
android.arch.persistence.room:common:1.0.0

##### Embedded
**摘要**：
用于标识实体类或POJO类的字段含有可被SQL语句直接使用的嵌套字段。
如果嵌套字段的子字段与外面字段命名冲突，可使用prefix()指定前缀。注意指定前缀后，即使使用ColumnInfo指定列名，前缀也会被应用。
如果嵌套字段的子字段拥有PrimaryKey注解，在自身所属的实体类中并不会被视为主键。
如果嵌套字段及子字段值为null，则被设置为null。
即使使用TypeConverters执行null转换为非null值，当嵌套字段的所有子字段在Cursor中为null时，类型转换不用被调用，嵌套字段也不会被构建。可使用NonNull注解避免嵌套类的这一行为。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
String	prefix()
```

**示例代码**：
``` java
   public class Coordinates {
       double latitude;
       double longitude;
   }
   public class Address {
       String street;
       @Embedded(prefix = "foo_")
       Coordinates coordinates;
   }
```

##### Relation
**摘要**：
用于POJO自动获取关联类的便捷注解。
当POJO从查询中返回时，Room会获取所有关联数据。
只能标识List或Set类型的字段。
不能用于仅用构造器初始化的字段，必须是公有的或有公有设置器。
只能用于POJO类，不能用于实体类，避免在实体创建时产生错误。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
// 获取数据的实体类。
Class	entity()
// POJO中引用的字段。如果要访问Embedded字段的子项，类似user.id一样使用。
String	parentColumn()
// 实体类中对应列。
String	entityColumn()
// 可用于指定返回的可获取实体类的子字段。
String[]	projection()
```

**示例代码**：
``` java
 @Entity
 public class Pet {
     @ PrimaryKey
     int id;
     int userId;
     String name;
     // other fields
 }
 public class UserNameAndAllPets {
   public int id;
   public String name;
   @Relation(parentColumn = "id", entityColumn = "userId")
   public List<Pet> pets;
 }

 @Dao
 public interface UserPetDao {
     @Query("SELECT id, name from User")
     public List<UserNameAndAllPets> loadUserAndPets();
 }
```
返回数据类型与Entity不对应
``` java
 public class User {
     int id;
     // other fields
 }
 public class PetNameAndId {
     int id;
     String name;
 }
 public class UserAllPets {
   @Embedded
   public User user;
   @Relation(parentColumn = "id", entityColumn = "userId", entity = Pet.class)
   public List<PetNameAndId> pets;
 }
 @Dao
 public interface UserPetDao {
     @Query("SELECT * from User")
     public List<UserAllPets> loadUserAndPets();
 }
```
指定返回字列
``` java
 public class UserAndAllPets {
   @Embedded
   public User user;
   @Relation(parentColumn = "id", entityColumn = "userId", entity = Pet.class,
           projection = {"name"})
   public List<String> petNames;
 }
```

##### Index
**摘要**：
声明实体类的索引。
添加索引会提升查询速度，但减慢插入或更新速度。
使用index()属性给单个字段添加索引，或使用indices()添加联合索引。
在嵌套字段的子字段上声明索引，该索引在所属实体类上不生效。
父类上的索引仅在inheritSuperIndices()值为true时方可继承。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
String	name()
boolean	unique()
String[]	value()
```

##### Dao
**摘要**：
标记为数据访问对象。
用于定义数据库交互操作，包括了一系列的查询方法。
必须是接口或抽象类，如果被Database引用，Room在编译时生成实现类。
Database作为唯一参数的构造器是可选的。
推荐根据数据库创建的数据表实现多个Dao类，

**库名**：
android.arch.persistence.room:common:1.0.0

##### Query
**摘要**：
标记查询方法。
当方法调用时，注解包含的SQL语句将被执行，Room会在编译时进行验证。
方法的参数会绑定到SQL语句中。
Room仅支付命名绑定参数```:name```避免方法参数与查询绑定参数混乱。
Room自动绑定方法参数到绑定参数。这要求方法参数名与绑定参数名一致。
作为SQLite绑定参数的扩展，Room支持列表参数绑定。运行时会根据方法参数的个数匹配绑定参数的个数来构建正确的查询语句。
支持三种类型的查询语句：SELECT、UPDATE和DELETE。
SELECT语句查询，Room会自动推断方法的返回类型，生成查询结果转换为方法返回类型的代码。对单个结果查询，返回类型可以是任意java对象，多个返回结果时，返回类型则是List或Array。此外，查询可返回Cursor或LiveData包装的查询结果。
如果使用RxJava2，也可返回Flowable&lt;T&gt;或Publisher&lt;T&gt;。因为响应流不允许null，如果查询返回空类型，它不会分发任何数据。可返回Flowable&lt;T[]&gt;或Publisher&lt;List&lt;T&gt;&gt;解决这个限制。
Flowable&lt;T&gt;和Publisher&lt;T&gt;都会观察数据库变化，并发送数据改变消息。如果无需观察数据变化，可使用Maybe&lt;T&gt;或Single&lt;T&gt;。如果Single&lt;T&gt;查询返回null，Room会抛出EmptyResultSetException。
UPDATE和DELETE返回类型是void或int，如果是int，该值代表受此次查询影响的行数。
可返回任意的POJOs类型，只要POJOs的字段匹配查询结果的列名。如果查询结果与POJOs字段名不匹配，只要存在一个字段匹配，Room会打印CURSOR_MISMATCH警告。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
String	value()
```

##### Insert
**摘要**：
标记插入方法。方法的参数会插入到数据库。参数必须是实体类或实体类的集合或数组。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
// 发生冲突时执行方法。默认是终止。
int	onConflict()
```

**示例代码**：
``` java
 @Dao
 public interface MyDao {
     @Insert(onConflict = OnConflictStrategy.REPLACE)
     public void insertUsers(User... users);
     @Insert
     public void insertBoth(User user1, User user2);
     @Insert
     public void insertWithFriends(User user, List<User> friends);
 }
```

##### Update
**摘要**：
标记更新方法。方法的参数会更新到数据库，前提是已经存在，通过主键比较判断。如果不存在，数据库不会发生任何改变。参数必须是实体类或实体类的集合或数组。

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
// 发生冲突时执行方法。默认是终止。
int	onConflict()
```

##### Delete
**摘要**：
标记删除方法。从数据库删除方法的参数。参数必须是实体类或实体类的集合或数组。

**库名**：
android.arch.persistence.room:common:1.0.0

**示例代码**：
``` java
@Dao
 public interface MyDao {
     @Delete
     public void deleteUsers(User... users);
     @Delete
     public void deleteAll(User user1, User user2);
     @Delete
     public void deleteWithFriends(User user, List<User> friends);
 }
```

##### OnConflictStrategy
**摘要**：
处理Dao方法的冲突处理策略集合。

**库名**：
android.arch.persistence.room:common:1.0.0

**常量**：
``` java
int	REPLACE
int	ROLLBACK
int	ABORT
int	FAIL
int	IGNORE
```

##### Transaction
**摘要**：
标识事务方法。非抽象方法。事务方法内部会调用由子类实现的方法完成事务。所有的参数和返回类型都被保存，事务如果没有发生错误，则标识成功。

两种情况下推荐使用事务：
*	查询结果非常大时，为保证结果的一致性，否则当查询结果超出单个CursorWindow时，查询结果会因为数据库的游标窗口切换而损坏。
*	如果返回结果类型为拥有Relation的POJO对象时，字段会分开查询，为保证结果一致性，最好在单个事务中执行。

如果是异步查询，如返回类型为LiveData或RxJava的流类型，事务会在执行时处理，而不是调用时处理。
Insert, Update和Delete方法上加上事务注解没有任何作用，因为它们一直在单个事务中执行。类似的Query方法但执行UPDATE或DELETE语句时，也自动包装在事物中执行。

**库名**：
android.arch.persistence.room:common:1.0.0

**示例代码**：
``` java
class ProductWithReviews extends Product {
     @Relation(parentColumn = "id", entityColumn = "productId", entity = Review.class)
     public List<Review> reviews;
 }
 @Dao
 public interface ProductDao {
     @Transaction @Query("SELECT * from products")
     public List<ProductWithReviews> loadAll();
 }
```

##### SkipQueryVerification
**摘要**：
跳过数据库验证。
如果标识Database，那么它所有查询的验证在编译时都将被跳过。
如果标识Dao，那么它所有查询方法的验证在编译时都将被跳过。
如果标识Dao中的某个方法，那么它执行的SQL语句的验证在编译时都将被跳过。
如果Room不能理解你的查询，并且你100%确信它是正确的，请将该注解作为最后的解决手段。移除验证后会限制Room的功能，因为它不能理解查询响应。

**库名**：
android.arch.persistence.room:common:1.0.0

##### TypeConverter
**摘要**：
标识类型转换。一个类可以有多个类型转换方法。
每个转换方法接收一个参数，返回值不能为void。

**库名**：
android.arch.persistence.room:common:1.0.0

**示例代码**：
``` java
 // example converter for java.util.Date
 public static class Converters {
    @TypeConverter
    public Date fromTimestamp(Long value) {
        return value == null ? null : new Date(value);
    }

    @TypeConverter
    public Long dateToTimestamp(Date date) {
        if (date == null) {
            return null;
        } else {
            return date.getTime();
        }
    }
 }
```

##### TypeConverters
**摘要**：
指定Room可使用的类型转换方法。类型转换方法作用范围，取决于添加的位置：
*	Database -- 所有的Daos和Entities；
*	Dao -- 所有的方法；
*	Entity -- 所有的字段；
*	POJOs -- 所有的字段；
*	Entity Field -- 仅指定字段；
*	Dao方法 -- 方法所有的参数；
*	Dao方法参数 -- 仅指定参数；

**库名**：
android.arch.persistence.room:common:1.0.0

**公有方法**：
``` java
Class[]<?>	value()
```

#### Classes
##### RoomDatabase
**摘要**：
RoomDatabase的基类。所有标识为数据库的类必须继承该类。
RoomDatabase提供了直接访问数据库的底层实现，但推荐使用Daos。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
RoomDatabase()
```

**可继承属性**：
``` java
protected SupportSQLiteDatabase	mDatabase
protected List<RoomDatabase.Callback>	mCallbacks
```

**公有方法**：
``` java
// 初始化、关闭。
void	init(DatabaseConfiguration configuration)
boolean	isOpen()
void	close()

// 查询及编译语句
Cursor	query(SupportSQLiteQuery query)
Cursor	query(String query, Object[] args)
SupportSQLiteStatement	compileStatement(String sql)

// 事务相关
void	beginTransaction()
void	endTransaction()
boolean	inTransaction()
<V> V	runInTransaction(Callable<V> body)
void	runInTransaction(Runnable body)
void	setTransactionSuccessful()

SupportSQLiteOpenHelper	getOpenHelper()
InvalidationTracker	getInvalidationTracker()
```

**可继承方法**：
``` java
// 创建时调用。
abstract InvalidationTracker	createInvalidationTracker()

// 数据库打开后由生成代码调用。
void	internalInitInvalidationTracker(SupportSQLiteDatabase db)

// 创建数据库助手来访问数据库。
abstract SupportSQLiteOpenHelper	createOpenHelper(DatabaseConfiguration config)
```

##### RoomDatabase.Builder<T extends RoomDatabase>
**摘要**：
RoomDatabase构建器。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**公有方法**：
``` java
T	build()
Builder<T>	allowMainThreadQueries()
Builder<T>	openHelperFactory(SupportSQLiteOpenHelper.Factory factory)
Builder<T>	addCallback(RoomDatabase.Callback callback)
// 添加迁移方案。
Builder<T>	addMigrations(Migration... migrations)

// 旧版本迁移到新版本时的Migration找不到时，允许Room摧毁式重建。
Builder<T>	fallbackToDestructiveMigration()
```

##### RoomDatabase.Callback
**摘要**：
RoomDatabase回调。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
RoomDatabase.Callback()
```

**公有方法**：
``` java
void	onCreate(SupportSQLiteDatabase db)
void	onOpen(SupportSQLiteDatabase db)
```

##### RoomDatabase.MigrationContainer
**摘要**：
保存迁移方案的窗口。允许查询两个版本间的迁移方案。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
RoomDatabase.MigrationContainer()
```

**公有方法**：
``` java
void	addMigrations(Migration... migrations)
List<Migration>	findMigrationPath(int start, int end)
```

##### DatabaseConfiguration
**摘要**：
RoomDatabase配置类。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**公有属性**：
``` java
// 连接数据库的上下文。
public final Context	context
// 内存数据库名字为null。
public final String	name
public final SupportSQLiteOpenHelper.Factory	sqliteOpenHelperFactory
public final boolean	allowMainThreadQueries
public final List<RoomDatabase.Callback>	callbacks
// 如果为true, 迁移方案找不到时Room会崩溃。
public final boolean	requireMigration
public final RoomDatabase.MigrationContainer	migrationContainer
```

##### RoomWarnings
**摘要**：
Room产生的一系列警告。
可使用SuppressWarnings注解禁用这些警告。

**库名**：
android.arch.persistence.room:common:1.0.0

**构造器**：
``` java
RoomWarnings()
```

**常量**：
``` java
String	CANNOT_CREATE_VERIFICATION_DATABASE
String	CURSOR_MISMATCH
String	DEFAULT_CONSTRUCTOR
String	INDEX_FROM_EMBEDDED_ENTITY_IS_DROPPED
String	INDEX_FROM_EMBEDDED_FIELD_IS_DROPPED
String	INDEX_FROM_PARENT_FIELD_IS_DROPPED
String	INDEX_FROM_PARENT_IS_DROPPED
String	MISSING_INDEX_ON_FOREIGN_KEY_CHILD
String	MISSING_JAVA_TMP_DIR
String	MISSING_SCHEMA_LOCATION
String	PRIMARY_KEY_FROM_EMBEDDED_IS_DROPPED
String	RELATION_QUERY_WITHOUT_TRANSACTION
String	RELATION_TYPE_MISMATCH
```

##### InvalidationTracker
**摘要**：
持有会被查询修改的数据表的列表，并通知这些数据表的回调。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**公有方法**：
``` java
void	refreshVersionsAsync()
void	addObserver(InvalidationTracker.Observer observer)
void	removeObserver(InvalidationTracker.Observer observer)
```

##### InvalidationTracker.Observer
**摘要**：
监听数据库变化的观察者。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
InvalidationTracker.Observer(String[] tables)
```

**可继承构造器**：
``` java
InvalidationTracker.Observer(String firstTable, String... rest)
```

**公有方法**：
``` java
abstract void	onInvalidated(Set<String> tables)
```

##### Room
**摘要**：
Room的工具类。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
Room()
```

**常量**：
``` java
// Room保存元信息的主表。
String	MASTER_TABLE_NAME
```

**公有方法**：
``` java
// 创建持久化数据库。
static <T extends RoomDatabase> Builder<T>	databaseBuilder(Context context, Class<T> klass, String name)
// 创建内存数据库。
static <T extends RoomDatabase> Builder<T>	inMemoryDatabaseBuilder(Context context, Class<T> klass)
```

##### RxRoom
**摘要**：
给Room添加RxJava2的工具类。

**库名**：
android.arch.persistence.room:rxjava2:1.0.0

**构造器**：
``` java
RxRoom()
```

**常量**：
``` java
public static final Object	NOTHING
```

**公有方法**：
``` java
static Flowable<Object>	createFlowable(RoomDatabase database, String... tableNames)
```

#### Exceptions
##### EmptyResultSetException
**摘要**：
当查询需要返回结果，但数据库查询的结果集为空时，Room抛出。

**库名**：
android.arch.persistence.room:rxjava2:1.0.0

**继承关系**：
*	java.lang.RuntimeException

**构造器**：
``` java
EmptyResultSetException(String message)
```