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

```android
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support:design:28.0.0'
```

编写布局文件
```android
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
```android
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

        // 如果viewpager是处于fragment之中就需要用getChildFragmentManager()，
        处于activity之中用getSupportFragmentManager()
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
        myViewPager.setOffscreenPageLimit(1);
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
        
        viewPager.postDelayed(Runnable action, long delayMillis)
        viewPager自带延迟执行的函数
        
#### 3.懒加载
 
        即在viewPager滑动到当前fragmeng时才加载数据，未滑动时候不加载，减少请求量。
        使用到fragment中的生命周期函数setUserVisibleHint(boolean isVisibleToUser)
        
        示例:
        @Override
        public void setUserVisibleHint(boolean isVisibleToUser) {
            super.setUserVisibleHint(isVisibleToUser);
            if (isVisibleToUser && isFirstLoad) {
                loadData();
            }
        }
        
        但这种方法在初次加载fragment时对第一个fragment无效，因为加载第一个fragment时setUserVisibleHint的执行时间比onCreateView
    要早，因此需要对第一个fragment特殊判断。
    
#### 4.其他注意的点

 **遇到使用viewPager出现空白页面的两种情况：**
    
     1.处于fragment之中的viewpager就用了getSupportFragmentManager() X

       使用getChildFragmentManager() √
 
    2.viewpager下的Fragment样式xml中有组件的id重复，**注意不要重复**
        
**FragmentStatePagerAdapter VS FragmentPagerAdapter：**
    
    FragmentStatePagerAdapter：在每次切换页面的时候，是将fragment进行回收，适合页面较多的fragment使用，
                               这样就不会消耗更多的内存。
                               
    FragmentPagerAdapter：在每次切换页面的时候，是将fragment进行分离，适合页面较少的fragment使用以保存一些内存，
                          对系统内存不会有多大影响。
                          
    
