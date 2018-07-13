# 学习目的
- 学会使用`DrawerLayout`
- 学会使用`NavigationView`  
- 学会使用ToolBar+DrawerLayout+NavigationView实现侧滑抽屉效果
- 学会实现Toolbar在顶部以及Toolbar被遮挡的两种效果

<!--more-->

# 效果展示
侧滑控件我们在开发的过程中经常用到,废话不多说,先上图:

![](http://paynnyvep.bkt.clouddn.com/15312917953318.gif)


# 实现过程
如果要实现上图的展示效果,需要先创建个BaseActivit类,这个类里面包含Toolbar的初始化,然后再创建DrawerLayout相关的Activity
## 创建BaseActivity及相关XML文件
- `activity_base.xml`


```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@color/colorPrimary"
            app:contentInsetStartWithNavigation="0dp"
            android:theme="@style/ToolBarStyle"
            app:titleTextColor="@color/white">

        </android.support.v7.widget.Toolbar>

        <FrameLayout
            android:id="@+id/frameLayout"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

        </FrameLayout>
    </LinearLayout>

</LinearLayout>  
```

- `BaseActivity`


```java
package com.vv.drawerlayout.ui.base;

import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.FrameLayout;

import com.vv.drawerlayout.R;

import butterknife.ButterKnife;

/**
 * @author ShenZhenWei
 * @date 2018/7/4
 */
public abstract class BaseActivity extends AppCompatActivity {
    protected Toolbar mToolbar;
    protected FrameLayout mContainerLayout;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_base);

        mToolbar = findViewById(R.id.toolbar);
        mContainerLayout = findViewById(R.id.frameLayout);
        boolean isToobar = initToolbar();
        if (isToobar) {
            setSupportActionBar(mToolbar);
            getSupportActionBar().setDisplayHomeAsUpEnabled(true);
            mToolbar.setNavigationOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    onNavigationClick();
                }
            });
        } else {
            mToolbar.setVisibility(View.GONE);
        }

        initContent(getLayoutId());
    }

    private void initContent(int layoutId) {
        if (layoutId != 0) {
            View contentView = LayoutInflater.from(this).inflate(layoutId, mContainerLayout, false);
            mContainerLayout.addView(contentView);
            ButterKnife.bind(this);
            initViews();
        }
    }


    /**
     * 初始化布局
     */
    protected abstract void initViews();

    /**
     * 获取布局控件
     *
     * @return
     */
    protected abstract int getLayoutId();

    private void onNavigationClick() {
        finish();
    }

    /**
     * 初始化Toolbar
     *
     * @return
     */
    protected abstract boolean initToolbar();
}

```

## 创建DrawerLayout相关的Activity以及相关的XML文件

- `activity_main.xml`  


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.MainActivity">

    <android.support.design.widget.NavigationView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/navigation_view"
        android:layout_gravity="left|start"
        app:headerLayout="@layout/header_layout"
        app:menu="@menu/navigation_menu">

    </android.support.design.widget.NavigationView>

</android.support.v4.widget.DrawerLayout>
```

- `header_layout.xml`


```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:gravity="center"
    android:background="@color/_0091ea"
    android:orientation="vertical"
    >
    <ImageView
        android:id="@+id/img_avatar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher_round" />

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:textColor="@color/white"
        android:text="" />

</LinearLayout>
```

- `navigation_menu.xml`


```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/menu_favorite_article"
        android:icon="@drawable/ic_favorite_gray_24dp"
        android:title="@string/favorite_article"
        app:showAsAction="ifRoom" />
    <item
        android:id="@+id/menu_about"
        android:icon="@drawable/ic_about_us_gray_24dp"
        android:title="@string/about_us"
        app:showAsAction="ifRoom" />
    <item
        android:id="@+id/menu_exit"
        android:icon="@drawable/ic_exit_to_app_gray_24dp"
        android:title="@string/exit"
        app:showAsAction="ifRoom"
        />

</menu>
```

- 如果使用Toolbar,那么需要把系统的主题换成没有ActionBar的主题,修改`style.xml`文件


```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="AppTheme" parent="AppTheme.Base"></style>


    <style name="ToolBarStyle" parent="@style/BaseToolBarStyle"></style>

</resources>

```

- `MainActivity`实现


```java
package com.vv.drawerlayout.ui;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.design.widget.NavigationView;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.view.MenuItem;

import com.vv.drawerlayout.R;
import com.vv.drawerlayout.ui.base.BaseActivity;

import butterknife.Bind;

/**
 * @author ShenZhenWei
 */
public class MainActivity extends BaseActivity {

    @Bind(R.id.navigation_view)
    NavigationView mNavigationView;
    @Bind(R.id.drawer_layout)
    DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActionBarDrawerToggle mToggle = new ActionBarDrawerToggle(this,mDrawerLayout,mToolbar,
                R.string.navigation_drawer_open,
                R.string.navigation_drawer_close);
        mToggle.syncState();
        mDrawerLayout.addDrawerListener(mToggle);
        mNavigationView.setItemIconTintList(null);
        mNavigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                return false;
            }
        });
    }

    @Override
    protected void initViews() {

    }

    @Override
    protected int getLayoutId() {
        return R.layout.activity_main;
    }

    @Override
    protected boolean initToolbar() {
        mToolbar.setTitle(R.string.home);
        mToolbar.setNavigationIcon(R.drawable.ic_menu_black_24dp);
        return true;
    }


}

```

- 运行查看效果

# 优化

但是现在我们发现侧边栏是没有选中状态的,如效果图所示,


![15307558160904_2](http://paynnyvep.bkt.clouddn.com/15307558160904_2.gif)

我们怎么才能是左边的导航栏有选择状态呢?

## 给左边的导航栏加上选中效果

* 首先在布局文件中,`NavigationView`的控件的`meun`中注明一个behavior 


```xml
    <group
        android:checkableBehavior="single">
        <item
            android:title="@string/home">

        </item>
    </group>
```

```xml
    android:checkableBehavior="single"
```

**表示每一项都是单选,如果不标明的话,NavigationView不会有选中效果.**

* 在Java文件中增加监听器

```java
mNavigationView.setNavigationItemSelectedListener(onNavigationItemSelectedListener);
```

```java
NavigationView.OnNavigationItemSelectedListener onNavigationItemSelectedListener = new NavigationView.OnNavigationItemSelectedListener() {
        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
            switch (item.getItemId()) {
                case R.id.menu_favorite_article:
                    break;
                case R.id.menu_about:
                    break;
                case R.id.menu_exit:
                    break;
                case R.id.menu_home:
                    break;
                default:
                    break;
            }
            //关闭侧边栏
            mDrawerLayout.closeDrawers();
            return true;
        }
    };
```

* 运行效果

![device-2018-07-05-103705](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-103705.png)![15307584630889_2](http://paynnyvep.bkt.clouddn.com/15307584630889_2-1.gif)

##  改变图标的颜色

我们给刚刚增加的`NavigationView`的控件的`meun`的Item增加一个`icon`

```xml
    <group
        android:checkableBehavior="single">
        <item
            android:id="@+id/menu_home"
            android:icon="@mipmap/ic_launcher"
            android:title="@string/home">

        </item>
    </group>
```

ok看下现在的效果:

![device-2018-07-05-105127](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-105127-1.png)

5我们发现`首页` 旁边的图标变成了黑色.这并不是我们想要的.

我们怎么去更改Icon的颜色呢,有两种方法:

* 在NavigationView的布局xml文件里增加`app:itemIconTint="@color/xxx"`属性,可以设置Icon的图片颜色为统一的一种颜色

`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.MainActivity">

    <android.support.design.widget.NavigationView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/navigation_view"
        android:layout_gravity="left|start"
        app:headerLayout="@layout/header_layout"
        app:menu="@menu/navigation_menu"
        app:itemIconTint="@color/_0091ea">

    </android.support.design.widget.NavigationView>

</android.support.v4.widget.DrawerLayout>
```

运行效果如下:

![device-2018-07-05-110042](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-110042.png)

我们可以看到所有的Icon的图片颜色全部换成了统一的蓝色.但是首页旁边的Icon仍不是我们想要的图标颜色.这就得需要第二种方法了.

* 我们如果想让Icon显示本省的颜色,就得在java文件里面调用以下方法:

```java
mNavigationView.setItemIconTintList(null);
```

`MainActivity`

```java
package com.vv.drawerlayout.ui;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.design.widget.NavigationView;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.view.MenuItem;

import com.vv.drawerlayout.R;
import com.vv.drawerlayout.ui.base.BaseActivity;

import butterknife.Bind;

/**
 * @author ShenZhenWei
 */
public class MainActivity extends BaseActivity {

    @Bind(R.id.navigation_view)
    NavigationView mNavigationView;
    @Bind(R.id.drawer_layout)
    DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActionBarDrawerToggle mToggle = new ActionBarDrawerToggle(this,mDrawerLayout,mToolbar,
                R.string.navigation_drawer_open,
                R.string.navigation_drawer_close);
        mToggle.syncState();
        mDrawerLayout.addDrawerListener(mToggle);
        mNavigationView.setItemIconTintList(null);
        mNavigationView.setNavigationItemSelectedListener(onNavigationItemSelectedListener);
    }

    @Override
    protected void initViews() {

    }

    @Override
    protected int getLayoutId() {
        return R.layout.activity_main;
    }

    @Override
    protected boolean initToolbar() {
        mToolbar.setTitle(R.string.home);
        mToolbar.setNavigationIcon(R.drawable.ic_menu_black_24dp);
        return true;
    }


    NavigationView.OnNavigationItemSelectedListener onNavigationItemSelectedListener = new NavigationView.OnNavigationItemSelectedListener() {
        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
            switch (item.getItemId()) {
                case R.id.menu_favorite_article:
                    break;
                case R.id.menu_about:
                    break;
                case R.id.menu_exit:
                    break;
                case R.id.menu_home:
                    break;
                default:
                    break;
            }
            //关闭侧边栏
            mDrawerLayout.closeDrawers();
            return true;
        }
    };

}

```

运行效果:

![device-2018-07-05-110546](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-110546.png)

可以看到,这个效果就是我们想要的~

## 改变Item的背景,改变Item的TextColor

* 如果要改变Item的背景,我们需要用到`NavigationView`的`.app:itemBackground=""`方法
* 如果要改变Item的TextColor,我们需要用到`NavigationView`的`app:itemTextColor=""`方法

`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.MainActivity">

    <android.support.design.widget.NavigationView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/navigation_view"
        android:layout_gravity="left|start"
        app:headerLayout="@layout/header_layout"
        app:menu="@menu/navigation_menu"
        app:itemBackground="@color/_0091ea"
        app:itemTextColor="@color/white"
        app:itemIconTint="@color/_0091ea">

    </android.support.design.widget.NavigationView>

</android.support.v4.widget.DrawerLayout>
```

效果如下:

![device-2018-07-05-111309](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-111309.png)


## 给Item之间增加分割线

我们只需要在以上基础上增加一个group_id即可.即:讲menu先放入到group组中,然后再给这个group赋上id的值,就能实现分割线效果.

`navigation_menu.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    >

    <item
        android:id="@+id/menu_favorite_article"
        android:icon="@drawable/ic_favorite_gray_24dp"
        android:title="@string/favorite_article"
        app:showAsAction="ifRoom"
        app:itemIconTint="@color/_0091ea"/>
    <item
        android:id="@+id/menu_about"
        android:icon="@drawable/ic_about_us_gray_24dp"
        android:title="@string/about_us"
        app:showAsAction="ifRoom" />
    <item
        android:id="@+id/menu_exit"
        android:icon="@drawable/ic_exit_to_app_gray_24dp"
        android:title="@string/exit"
        app:showAsAction="ifRoom"
        />

    <group
        android:id="@+id/group_home"
        android:checkableBehavior="single"
        >
        <item
            android:id="@+id/menu_home"
            android:icon="@mipmap/ic_launcher_round"

            android:title="@string/home">

        </item>
    </group>
</menu>
```

运行效果

![device-2018-07-05-111851](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-111851.png)


## 怎么实现Toolbar被遮挡的侧滑抽屉效果
我们常见的应用,像网易云音乐,QQ等都是Toolbar被遮挡的侧滑效果.

![device-2018-07-05-113031-1](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-113031-1.png)![device-2018-07-05-113158-1](http://paynnyvep.bkt.clouddn.com/1device-2018-07-05-113158-1.png)

我们实现这种效果就得让`DrawerLayout`包含在`Toolbar`外面

`activity_drawer_layout.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
        <android.support.design.widget.AppBarLayout
            android:id="@+id/appBarLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:theme="@style/AppTheme.AppBarOverlay">

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                app:popupTheme="@style/AppTheme.PopupOverlay" />

        </android.support.design.widget.AppBarLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
            <TextView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:text="@string/home"
                android:gravity="center"/>
        </LinearLayout>

    </LinearLayout>


    <android.support.design.widget.NavigationView
        android:id="@+id/navigation_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/header_layout"
        app:itemIconTint="@color/colorAccent"
        app:menu="@menu/navigation_menu">

    </android.support.design.widget.NavigationView>
</android.support.v4.widget.DrawerLayout>
```


```java
package com.vv.drawerlayout.ui;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.support.design.widget.NavigationView;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.view.MenuItem;

import com.vv.drawerlayout.R;

import butterknife.Bind;
import butterknife.ButterKnife;

/**
 * @author ShenZhenWei
 * @date 2018/7/5
 */
public class DrawerLayoutActivity extends AppCompatActivity {
    @Bind(R.id.toolbar)
    Toolbar mToolbar;
    @Bind(R.id.navigation_view)
    NavigationView mNavigationView;
    @Bind(R.id.drawer_layout)
    DrawerLayout mDrawerLayout;

    private ActionBarDrawerToggle mToggle;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_drawer_layout);
        ButterKnife.bind(this);
        //设置Toolbar
        setToolbar();

        //设置DrawerToggle 开关
        setDrawerToggle();

        //设置监听器
        setListener();
    }

    private void setListener() {
        mNavigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                mDrawerLayout.closeDrawers();
                return true;
            }
        });
    }

    private void setDrawerToggle() {
        mToggle=new ActionBarDrawerToggle(this,mDrawerLayout,mToolbar,
                R.string.navigation_drawer_open,
                R.string.navigation_drawer_close);
        mDrawerLayout.addDrawerListener(mToggle);
        //同步DrawerLayout的状态
        mToggle.syncState();
    }

    private void setToolbar() {
        setSupportActionBar(mToolbar);
        // 显示Home的图标
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    }
}

```

**注意:**
`setSupportActionBar(mToolbar);`一定要写在`mDrawerLayout.addDrawerListener(mToggle);`
之前,否则将无法响应Home页面上的Icon点击事件.

运行效果:

![15308425514883_2](http://paynnyvep.bkt.clouddn.com/15308425514883_2.gif)

# 代码

为了方便,代码已经上传[GitHub](https://github.com/pvphero/DrawerLayoutDemo),敬请下载~

