### CoordinatorLayout 让应用有复杂的联动效果
#### 示例
![](https://www.z4a.net/images/2019/09/25/20190925_111732.gif)

&emsp;coordinateLayout遵循Material Design风格，一般用于长页面，尤其是页面中的上部分是普通的控件，下部分是列表的情况。分以下部分介绍coordinateLayout：

1.组件介绍

2.使用方法

3.注意的点


#### 正文

#### 1.组件介绍
&emsp;使用CoordinatorLayout必须搭配AppBarLayout、CollapsingToolbarLayout、Toolbar使用。

**CoordinatorLayout**

&emsp;CoordinatorLayout是作为一个顶层的布局，让子布局有联动的效果。基本上没有特殊的属性需要设置。

**AppBarLayout**

&emsp;AppBarLayout是垂直的LinearLayout，指定它的setScrollFlags(int)或者在xml中设置app:layout_scrollFlags，会让子元素根据它的不同设置产生不同滚动效果。一般与CoordinatorLayout使用，**而且必须是CoordinatorLayout的直接子元素**

&emsp;**而且AppBarLayout必须搭配具有滚动行为的兄弟View**，通过设置其layout_behavior为ScrollingViewBehavior，以便AppBarLayout感知何时滚动。在示例中的例子中viewpager就是具有滚动行为的兄弟view


**CollapsingToolbarLayout**

&emsp;CollapsingToolbarLayout在CoordinatorLayout中是作为AppBarLayout的直接子元素，它有以下的功能：

- 折叠标题：当布局完全可见时，标题变大，布局滚动到屏幕外，标题会折叠变小。通过setTitle(charSequece)设置标题。

- 视差滚动


&emsp;xml属性介绍：

>app：layout_scrollFlags=scroll|enterAlways|exitUntilCollapsed|enterAlwaysCollapsed|snap

&emsp;这五种不是独立使用，要结合使用，必须首先设置scroll，才会触发滚顶。具体组合样式的区别可以看[这篇博客](https://blog.csdn.net/eyishion/article/details/80282204)

>app:contentScrim

&emsp;设置当完全CollapsingToolbarLayout折叠(收缩)后的背景颜色。一般用于有title的时候，折叠完title的背景就会出现，并且在折叠过程中会产生视觉差

>app:layout_collapseMode=pin|parallax


&emsp;CollapsingToolbarLayout的子元素当设置这个属性为pin时，表示一直固定，即子元素在xml中的样子，滚动过程中不会改变。
而设置为parallax的时候，配合上：

>layout_collapseParallaxMultiplier(视差因子) - 设置视差滚动因子，值为：0~1。


&emsp;就会产生滚动的视觉差变化,一般越处于屏幕中心越大,偏离屏幕中心变小

**Toolbar**

&emsp;toolbar是折叠后的顶部栏，让折叠完全后的布局与顶部导航栏有一定距离，toolbar也一直会固定在最顶端

&emsp在toolbar中有一个xml属性比较重要，就是
>app:layout_collapseMode=pin|parallax
一般设置为pin,才可以一直固定在屏幕上。


#### 2.使用方法
```android
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/coordinator"
    android:background="@color/background"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/background"
        app:theme="@style/ToolbarTheme"
        >
        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsingToolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:contentScrim="?attr/colorPrimary"
            android:background="@color/background"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <ImageView
                android:id="@id/my_index_background"
                android:layout_width="match_parent"
                android:layout_height="150dp"
                android:scaleType="fitXY"
                android:background="@drawable/index"/>

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="25dp"
                app:titleTextColor="#ffffff"
                app:theme="@style/ToolbarTheme"
                android:gravity="center_vertical"
                app:layout_collapseMode="pin"
                app:popupTheme="@style/AppTheme.PopupOverlay" />
        </android.support.design.widget.CollapsingToolbarLayout>


        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="15dp"
            android:layout_marginRight="15dp"
            android:layout_marginTop="10dp"
            android:background="@drawable/rectangle_with_radius"
            android:orientation="vertical">
            <android.support.design.widget.TabLayout
                android:id="@+id/my_tab"
                android:layout_width="match_parent"
                app:tabTextColor="@color/colorBlack"
                app:tabSelectedTextColor="@color/colorBlack"
                app:tabIndicatorColor="@color/my_like_underline"
                app:tabIndicatorFullWidth="false"
                android:layout_height="wrap_content">
            </android.support.design.widget.TabLayout>
        </LinearLayout>
    </android.support.design.widget.AppBarLayout>

    <android.support.v4.view.ViewPager
        android:id="@+id/myView"
        android:layout_marginTop="10dp"
        android:layout_marginLeft="15dp"
        android:layout_marginRight="15dp"
        android:background="@color/background"
        android:layout_width="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" // 这里一定要设置
        android:layout_height="wrap_content">
    </android.support.v4.view.ViewPager>
</android.support.design.widget.CoordinatorLayout>
```
&emsp;通过以上布局就可以实现联动效果，至于viewpager和tablayout的结合使用方法可看上一篇文章。


#### 3.注意的点

- 遇到的coordinateLayout无法滚动的情况：

&emsp;&emsp;在需要滚动的组件：例如listView、recylerView、viewPager等中必须设置**app:layout_behavior="@string/appbar_scrolling_view_behavior"**，原因如在对AppBarLayout的介绍所示。

- 在网上有看到说CoordinatorLayout必须与recylerView使用，不可以与listView使用，在我的实践里是可以的。

- Tips:
>Appbar.setExpanded(boolean expanded, boolean animate）

&emsp;&emsp;可以在代码中设置Appbar是否展开，可以快速跳转。animate默认为true
