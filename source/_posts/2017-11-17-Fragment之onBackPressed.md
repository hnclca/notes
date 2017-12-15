---
title: Fragment之onBackPressed
comments: false
toc: true
date: 2017-11-17 16:18:08
tags:
	- key code
	- android
---

### 后退导航
```
// 在activity中重载
@Override
public void onBackPressed() {

    int count = getFragmentManager().getBackStackEntryCount();

    if (count == 0) {
        super.onBackPressed();
        //additional code
    } else {
        getFragmentManager().popBackStack();
    }

}
```
###### 参考资料
[how-to-implement-onbackpressed-in-fragments](https://stackoverflow.com/questions/5448653/how-to-implement-onbackpressed-in-fragments)

<!-- more -->

### 后退执行额外操作
```
// activity中创建回调接口，重载onBackPressed方法
    override fun onBackPressed() {
        for (fragment in supportFragmentManager.fragments) {
            if (fragment is OnBackPressedListener) {
                fragment.onBackPressed()
            }
        }

        super.onBackPressed()
    }

    interface OnBackPressedListener {
        fun onBackPressed()
    }
```
###### 参考资料
[using-onbackpressed-in-android-fragments](https://stackoverflow.com/questions/18190047/using-onbackpressed-in-android-fragments)
