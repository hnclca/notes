---
title: Architecture Components
comments: false
toc: true
date: 2017-12-18 15:57:48
tags:
	- library
	- android
---

��׿�ܹ������һϵ�а�����������ƽ�׳�ԡ��ײ��ԡ���ά����apps�Ŀ�ļ��ϡ�
![](/assets/images/2017/12/recommanded-architecture.png)

<!-- more -->

### ���������

#### ��ӹȸ�Maven�ֿ�
``` gradle
allprojects {
    repositories {
        jcenter()
        google()
    }
}
```

#### ��Ӽܹ����
##### �������ڸ�֪Lifecycle-aware
``` gradle
implementation "android.arch.lifecycle:runtime:1.0.3"
annotationProcessor "android.arch.lifecycle:compiler:1.0.0"

// ����
implementation "android.arch.lifecycle:common-java8:1.0.0"
```

##### LiveData��ViewModel
``` gradle
// �ÿ��Ѱ���android.arch.lifecycle:runtime��
implementation "android.arch.lifecycle:extensions:1.0.0"

// ʹ��ReactiveStreams API
implementation "android.arch.lifecycle:reactivestreams:1.0.0"

// �ڲ����п��ƺ�̨�߳�
testImplementation "android.arch.core:core-testing:1.0.0"
```

##### �־û�Room
``` gradle
implementation "android.arch.persistence.room:runtime:1.0.0"
annotationProcessor "android.arch.persistence.room:compiler:1.0.0"

// ����RoomǨ��
testImplementation "android.arch.persistence.room:testing:1.0.0"

// ֧��RxJava
implementation "android.arch.persistence.room:rxjava2:1.0.0"
```

##### ��ҳPaging
``` gradle
implementation "android.arch.paging:runtime:1.0.0-alpha4-1"
```

### ������
*	android.arch.core.executor.testing
����������ͬ������ִ����

*	android.arch.core.util
Function�����ӿ�

*	android.arch.lifecycle
�������ڸ�֪���Lifecycle�����ݳ������LiveData�����ݹ������ViewModel

*	android.arch.paging
��ҳ����ԴDataSource, ��ҳ�����б�PagedList, ��ҳ����������PagedListAdapter

*	android.arch.persistence.db

*	android.arch.persistence.db.framework
SupportSQLiteOpenHelper.Factoryʵ����

*	android.arch.persistence.room
���ݿ����ӳ���Room: RoomDatabase���࣬ע���ʶDatabase, Entities, Dao�����ࣨע�⴦����ʵ�֣�

*	android.arch.persistence.room.migration
Migration����

*	android.arch.persistence.room.testing
MigrationTestHelperǨ�Ʋ��Ը�����

*	android.support.v7.recyclerview.extensions
