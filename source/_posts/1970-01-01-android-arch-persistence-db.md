---
title: android.arch.persistence.db
comments: false
toc: true
tags:
  - android
  - references
date: 1970-01-01 08:00:00
---

SupportSQLiteDatabase -- 替代SQLiteDatabase

<!-- more -->
#### Interfaces
##### SupportSQLiteDatabase
**摘要**：
数据库抽象类，移除框架依赖，允许更换底层SQL版本。模拟SQLiteDatabase行为。

**库名**：
android.arch.persistence:db:1.0.0

**继承关系**：
*	java.io.Closeable

**公有方法**：
``` java
// 事务相关
abstract void	beginTransaction()
abstract void	beginTransactionNonExclusive()
abstract void	beginTransactionWithListener(SQLiteTransactionListener transactionListener)
abstract void	beginTransactionWithListenerNonExclusive(SQLiteTransactionListener transactionListener)
abstract void	endTransaction()
abstract boolean	inTransaction()
abstract void	setTransactionSuccessful()

// 操作
abstract void	execSQL(String sql, Object[] bindArgs)
abstract void	execSQL(String sql)
abstract SupportSQLiteStatement	compileStatement(String sql)
abstract Cursor	query(SupportSQLiteQuery query, CancellationSignal cancellationSignal)
abstract Cursor	query(SupportSQLiteQuery query)
abstract Cursor	query(String query)
abstract Cursor	query(String query, Object[] bindArgs)
abstract long	insert(String table, int conflictAlgorithm, ContentValues values)
abstract int	update(String table, int conflictAlgorithm, ContentValues values, String whereClause, Object[] whereArgs)
abstract int	delete(String table, String whereClause, Object[] whereArgs)

// 日志
abstract boolean	enableWriteAheadLogging()
abstract void	disableWriteAheadLogging()
abstract boolean	isWriteAheadLoggingEnabled()

// 数据库信息
abstract List<Pair<String, String>>	getAttachedDbs()
abstract long	setMaximumSize(long numBytes)
abstract long	getMaximumSize()
abstract void	setPageSize(long numBytes)
abstract long	getPageSize()
abstract String	getPath()
abstract void	setVersion(int version)
abstract int	getVersion()
abstract boolean	isDatabaseIntegrityOk()
abstract boolean	isDbLockedByCurrentThread()
abstract boolean	isOpen()
abstract boolean	isReadOnly()
abstract boolean	needUpgrade(int newVersion)
abstract void	setLocale(Locale locale)
abstract void	setForeignKeyConstraintsEnabled(boolean enable)
abstract void	setMaxSqlCacheSize(int cacheSize)
abstract boolean	yieldIfContendedSafely(long sleepAfterYieldDelay)
abstract boolean	yieldIfContendedSafely()
```

##### SupportSQLiteOpenHelper
**摘要**：
映射SQLiteOpenHelper的接口。由于SQLiteOpenHelper类需要实现指定方法，所以使用SupportSQLiteOpenHelper.Factory创建实例类和SupportSQLiteOpenHelper.Callback。

**库名**：
android.arch.persistence:db:1.0.0

**公有方法**：
``` java
abstract void	close()
abstract String	getDatabaseName()
abstract SupportSQLiteDatabase	getReadableDatabase()
abstract SupportSQLiteDatabase	getWritableDatabase()
abstract void	setWriteAheadLoggingEnabled(boolean enabled)
```

##### SupportSQLiteOpenHelper.Factory
**摘要**：
使用SupportSQLiteOpenHelper.Configuration创建SupportSQLiteOpenHelper的工厂类。

**库名**：
android.arch.persistence:db:1.0.0

**直接子类**：
*	android.arch.persistence.db.framework.FrameworkSQLiteOpenHelperFactory

**公有方法**：
``` java
abstract SupportSQLiteOpenHelper	create(SupportSQLiteOpenHelper.Configuration configuration)
```

##### SupportSQLiteProgram
**摘要**：
映射SQLiteProgram类。

**库名**：
android.arch.persistence:db:1.0.0

**继承关系**：
*	java.lang.AutoCloseable

**直接子类**：
*	android.arch.persistence.db.SupportSQLiteStatement

**公有方法**：
绑定数据到SQL语句。
``` java
abstract void	bindBlob(int index, byte[] value)
abstract void	bindDouble(int index, double value)
abstract void	bindLong(int index, long value)
abstract void	bindNull(int index)
abstract void	bindString(int index, String value)
abstract void	clearBindings()
```

##### SupportSQLiteStatement
**摘要**：
映射SQLiteStatement行为接口。

**库名**：
android.arch.persistence:db:1.0.0

**继承关系**：
*	android.arch.persistence.db.SupportSQLiteProgram

**公有方法**：
``` java
abstract void	execute()
abstract long	executeInsert()
abstract int	executeUpdateDelete()
abstract long	simpleQueryForLong()
abstract String	simpleQueryForString()
```

##### SupportSQLiteQuery
**摘要**：
类型绑定查询，优于rawQuery，允许绑定类型安全参数。

**库名**：
android.arch.persistence:db:1.0.0

**继承关系**：

**直接子类**：
*	android.arch.persistence.db.SimpleSQLiteQuery

**公有方法**：
``` java
abstract void	bindTo(SupportSQLiteProgram statement)
abstract String	getSql()
```

#### Classes
##### SupportSQLiteOpenHelper.Callback
**摘要**：
处理数据库连接时各种生命周期事件，类似SQLiteOpenHelper。

**库名**：
android.arch.persistence:db:1.0.0

**构造器**：
``` java
SupportSQLiteOpenHelper.Callback(int version)
```

**公有属性**：
``` java
public final int	version
```

**公有方法**：
``` java
void	onConfigure(SupportSQLiteDatabase db)
abstract void	onCreate(SupportSQLiteDatabase db)
void	onOpen(SupportSQLiteDatabase db)
abstract void	onUpgrade(SupportSQLiteDatabase db, int oldVersion, int newVersion)
void	onDowngrade(SupportSQLiteDatabase db, int oldVersion, int newVersion)
void	onCorruption(SupportSQLiteDatabase db)
```

##### SupportSQLiteOpenHelper.Configuration
**摘要**：
使用SupportSQLiteOpenHelper.Factory创建SupportSQLiteOpenHelper的配置。

**库名**：
android.arch.persistence:db:1.0.0

**公有属性**：
``` java
public final SupportSQLiteOpenHelper.Callback	callback
public final Context	context
public final String	name
```

**公有方法**：
``` java
static SupportSQLiteOpenHelper.Configuration.Builder	builder(Context context)
```

##### SupportSQLiteOpenHelper.Configuration.Builder
**摘要**：
SupportSQLiteOpenHelper.Configuration的构建类。

**库名**：
android.arch.persistence:db:1.0.0

**公有方法**：
``` java
SupportSQLiteOpenHelper.Configuration	build()
SupportSQLiteOpenHelper.Configuration.Builder	callback(SupportSQLiteOpenHelper.Callback callback)
SupportSQLiteOpenHelper.Configuration.Builder	name(String name)
```

##### SimpleSQLiteQuery
**摘要**：
SupportSQLiteQuery的简单实现，

**库名**：
android.arch.persistence:db:1.0.0

**继承关系**：
*	android.arch.persistence.db.SupportSQLiteQuery

**构造器**：
``` java
SimpleSQLiteQuery(String query, Object[] bindArgs)
SimpleSQLiteQuery(String query)
```

**公有方法**：
``` java
static void	bind(SupportSQLiteProgram statement, Object[] bindArgs)
```

##### SupportSQLiteQueryBuilder
**摘要**：
创建SELECT查询语句的简单查询构建器。

**库名**：
android.arch.persistence:db:1.0.0

**公有方法**：
``` java
static SupportSQLiteQueryBuilder	builder(String tableName)
SupportSQLiteQuery	create()
SupportSQLiteQueryBuilder	columns(String[] columns)
SupportSQLiteQueryBuilder	selection(String selection, Object[] bindArgs)
SupportSQLiteQueryBuilder	having(String having)
SupportSQLiteQueryBuilder	orderBy(String orderBy)
SupportSQLiteQueryBuilder	groupBy(String groupBy)
SupportSQLiteQueryBuilder	limit(String limit)
SupportSQLiteQueryBuilder	distinct()
```
