### tablayout+viewpager+fragment
#### 示例
![](https://www.z4a.net/images/2019/09/24/1277f34881a080fdd.md.jpg)

tablayout+viewpager+fragment是主流app上都会见到的布局，分以下部分介绍该组合：

1.使用方法

2.常用函数

3.懒加载

4.其他注意的点


#### 正文

#### 1.使用方法

首先引入工具包

```
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support:design:28.0.0'
```

编写布局文件
```
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center">
		
        <android.support.design.widget.TabLayout
            android:layout_width="match_parent"
            android:background="@color/colorBottom"
            app:tabTextColor="@color/fouragray"
            app:tabIndicatorFullWidth="false"
            android:layout_gravity="center"
            app:tabMode="scrollable"
            app:tabIndicatorHeight="0dp"
            android:layout_height="match_parent"/>
			
		<android.support.v4.view.ViewPager
			android:layout_weight="1"
			android:layout_width="match_parent"
			android:layout_height="0dp">
	</LinearLayout>
```
tablayout与viewpager不在同一个父元素都可以

编写java代码
```
    private List<Fragment>myFragment;
    private ViewPager myViewPager;
    private TabLayout tab;
    private List<String> title; // tab的名字，推荐、全部、原创……
	
    private void initData(){
        myFragment = new ArrayList<>();
        title = new ArrayList<>();
		
        // 示例图中tab的通过接口获取，为方便文章里直接add
        title.add("推荐");
        title.add("全部");
        title.add("原曲");
		
        for(int i=0;i<3;i++) {
            myFragment.add(new Fragment());
        }
    }
	
    private void initViewPager(){

        // 如果viewpager是处于fragment之中就需要用getChildFragmentManager()，处于activity之中用getSupportFragmentManager()
        mAdapter = new FragmentStatePagerAdapter(getChildFragmentManager()) { 
		
            //选中的item, 返回该fragment
            @Override
            public Fragment getItem(int position) {
                return myFragment.get(position);

            }

            @Override
            public int getCount() {
                return myFragment.size();
            }
			
            // 设置tab的文字
            @Override
            public CharSequence getPageTitle(int position) {
                return recommendTitle.get(position);
            }
        };
		
        myViewPager.setAdapter(mAdapter);
        myViewPager.setOffscreenPageLimit(myFragment.size() -1);
        myViewPager.setCurrentItem(0);
        tab.setupWithViewPager(myViewPager);
        initTab();
    }
	
    public void initTab() {

        tab.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab selectedTab) {
                LinearLayout tabLayout = (LinearLayout)((ViewGroup) tab.getChildAt(0)).getChildAt(selectedTab.getPosition());
                TextView tabTextView = (TextView) tabLayout.getChildAt(1);
                tabTextView.setTypeface(Typeface.defaultFromStyle(Typeface.BOLD)); // 选中字体加粗

            }
            @Override
            public void onTabUnselected(TabLayout.Tab tab) {
                LinearLayout tabLayout = (LinearLayout)((ViewGroup) tab.getChildAt(0)).getChildAt(tab.getPosition());
                TextView tabTextView = (TextView) tabLayout.getChildAt(1);
                tabTextView.setTypeface(Typeface.defaultFromStyle(Typeface.NORMAL));// 非选中字体不加粗
            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {

            }
        });

        // 通过title一个一个新建tab
        for(int i=0;i<title.size();i++){
            tab.addTab(tab.newTab());
        }

        int tabCount =tab.getTabCount();
        LinearLayout tabLayout = (LinearLayout)((ViewGroup) tab.getChildAt(0)).getChildAt(0);
        TextView tabTextView = (TextView) tabLayout.getChildAt(1);
        tabTextView.setTypeface(tabTextView.getTypeface(),Typeface.BOLD);  // 初始化时第一个加粗

    }
```
通过上述方法，即可实现左右滑动tab展示不同界面的效果

#### 2.常用函数
    
        viewPager.setOffscreenPageLimit(int)
        
        设置viewpager缓存页面数量，默认值是1，即便代码设置为0也是会当作1来处理。
        该函数就可以缓存当前fragment的前后int的fragment，其余fragment将被回收,避免过多使用内存
    
    
        viewPager.setCurrentItem(int item, boolean smoothScroll)
        设置当前页面，第二个参数可选，**该函数可以用于回到用户上一次停留的页面**
    
