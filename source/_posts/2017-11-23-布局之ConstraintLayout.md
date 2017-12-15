---
title: 布局之ConstraintLayout
comments: false
toc: true
date: 2017-12-13 16:17:22
tags:
	- layout
	- android
---

### 依赖库
```
constraint_layout: "com.android.support.constraint:constraint-layout:$constraint_layout_version"
```

### 定位
##### 边对齐
左（上、右、下、基线）边对齐父parent（其他子控件id）的左（上、右、下、基线）边
* layout_constraintLeft_toLeftOf
* layout_constraintLeft_toRightOf
* layout_constraintRight_toLeftOf
* layout_constraintRight_toRightOf
* layout_constraintTop_toTopOf
* layout_constraintTop_toBottomOf
* layout_constraintBottom_toTopOf
* layout_constraintBottom_toBottomOf
* layout_constraintBaseline_toBaselineOf
* layout_constraintStart_toEndOf
* layout_constraintStart_toStartOf
* layout_constraintEnd_toStartOf
* layout_constraintEnd_toEndOf

![relative-positioning-constraints.png](/assets/images/2017/11/relative-positioning-constraints.png)

<!-- more -->

##### 中心对齐
*  水平居中
```
        app:layout_constraintLeft_toLeftOf ="parent"
        app:layout_constraintRight_toRightOf ="parent"
```
*  垂直居中
```
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
```
* 百分比偏移
```
        app:layout_constraintHorizontal_bias="1"
        app:layout_constraintVertical_bias="1"
```

![centering-positioning.png](/assets/images/2017/11/centering-positioning.png)![centering-positioning-bias.png](/assets/images/2017/11/centering-positioning-bias.png)

##### 圆心定位（1.1.0版本新增）
目标仅支持子控件
* layout_constraintCircle : 目标定位圆心
* layout_constraintCircleRadius : 半径，距离圆心尺寸
* layout_constraintCircleAngle : 顺时针旋转，垂直向上角度为0，范围0-360

![Circular Positioning](/assets/images/2017/11/Circular Positioning.png)


##### 定位目标可见性
当作为定位目标的控件不可见时，依然存在于布局上面，不过width与height均为0，其上的定位约束依然有效
**注意**：其上的定位约束是圆心定位时，半径与角度均失效
![visibility-behavior.png](/assets/images/2017/11/visibility-behavior.png)

### 边距
##### 存在相应约束时生效
```
        android:layout_constraintTop_toTopOf="parent"
        android:layout_marginTop="50dp"  // 生效
        android:layout_marginRight="50dp"  // 不生效
```

##### 目标定位消失时
设置定位目标的布局属性为View.GONE时的边距属性
* layout_goneMarginStart
* layout_goneMarginEnd
* layout_goneMarginLeft
* layout_goneMarginTop
* layout_goneMarginRight
* layout_goneMarginBottom

### 尺寸
##### 目标定位控件尺寸限制（1.1版本新增）
wrap_content时生效，true强制约束在目标定位控件尺寸内，false不约束
```
        app:layout_constrainedWidth="false"
        app:layout_constrainedHeight="true"
```
![宽度不约束，高度约束](/assets/images/2017/11/constrainedHeight.png)

##### 百分比尺寸
百分比指的是相对父控件尺寸的比例
```
        android:layout_width="0dp"
        app:layout_constraintWidth_default="percent"  1.1.0-beta3以前的版本需要添加
        app:layout_constraintWidth_percent="0.5"
```
![宽度50%](/assets/images/2017/11/constraintWidth_percent_0.5.png)

##### 自身宽高比
指定相对边，默认为H，即1代表高，可设置为W，则1代表宽
```
        // 边界定位，宽高只能有一个为0dp
        android:layout_width="32dp"
        android:layout_height="0dp"
        app:layout_constraintDimensionRatio="1:2"

      // 中心线定位，宽高均为0dp
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toBottomOf="@id/icon"
        app:layout_constraintLeft_toLeftOf="@id/icon"
        app:layout_constraintRight_toRightOf="@id/icon"
        app:layout_constraintDimensionRatio="W,1:2"
```
![边界定位，比例1:2](/assets/images/2017/11/border_position_constraintDimensionRatio.png)![中心线定位，比例W,1:2](/assets/images/2017/11/center_position_constraintDimensionRatio_W.png)![中心线定位，比例H,1:2](/assets/images/2017/11/center_position_constraintDimensionRatio_H.png)

### 约束链
水平或垂直方向，从父控件的左边到右边形成的一条类似链式的约束结构，第一个子控件视作链头
约束链内的子控件宽度或高度总和小于父控件宽度或高度时，剩余空间的分配方式有以下三种

##### 松散结构--均分
链头有效
```
        app:layout_constraintHorizontal_chainStyle="spread"  // 平均分配，两端参与分配
        app:layout_constraintHorizontal_chainStyle="spread_inside"  // 平均分配，两端不参与分配
```

##### 紧凑结构--子控件间无间距
链头有效
```
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintHorizontal_bias="0.75"   // 偏移参数有效
```

##### 比重分配
所有子控件有效
```
        android:layout_width="0dp"
        app:layout_constraintHorizontal_weight="2"
```
![chains-head.png](/assets/images/2017/11/chains-head.png)
![chains-styles.png](/assets/images/2017/11/chains-styles.png)

### 辅助线
android.support.constraint.Guideline
辅助线不可见，width与height属性无意义，添加后可作为其他子控件的目标定位控件

##### 方向
```
android:orientation="horizontal"  // 水平辅助线
android:orientation="vertical"  // 垂直辅助线
```

##### 尺寸位置
```
        app:layout_constraintGuide_begin="200dp"  // 位于父控件左侧200dp
        app:layout_constraintGuide_end="200dp"  // 位于父控件右侧200dp
```

##### 比例位置
```
        app:layout_constraintGuide_percent="0.75"
```



### 参考资料
[ConstraintLayout](https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout.html)
