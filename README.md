# About
一个方便扩展的下拉刷新和上拉加载的库，支持xml中配置全局默认的HeaderView和FooterView，提供拖动回调监听，获取滚动距离后方便扩展各种加载效果<br>

## Gradle
`implementation 'com.fanwe.android:pulltorefresh:1.1.0-rc2'`

## 简单效果
![](http://thumbsnap.com/i/8AyEAjrW.gif?0725)<br>
支持覆盖的默认配置：<br>
* strings
```xml
<string name="lib_ptr_state_pull_to_refresh_header">下拉刷新</string>
<string name="lib_ptr_state_pull_to_refresh_footer">上拉加载</string>

<string name="lib_ptr_state_release_to_refresh_header">松开刷新</string>
<string name="lib_ptr_state_release_to_refresh_footer">松开加载</string>

<string name="lib_ptr_state_refreshing_header">刷新中...</string>
<string name="lib_ptr_state_refreshing_footer">加载中...</string>

<string name="lib_ptr_state_refreshing_success_header">刷新成功</string>
<string name="lib_ptr_state_refreshing_success_footer">加载成功</string>

<string name="lib_ptr_state_refreshing_failure_header">刷新失败</string>
<string name="lib_ptr_state_refreshing_failure_footer">加载失败</string>

<!-- 指定全局HeaderView类的全名 -->
<string name="lib_ptr_header_class"></string>

<!-- 指定全局FooterView类的全名 -->
<string name="lib_ptr_footer_class"></string>
```
* colors
```xml
<!-- 默认的加载view中提示文字的颜色 -->
<color name="lib_ptr_text_loading_info">#888888</color>
```
* dimens
```xml
<!-- 默认的加载view中提示文字的大小 -->
<dimen name="lib_ptr_text_loading_info">13sp</dimen>
```
## 自定义效果
![](http://thumbsnap.com/i/GFbZkldb.gif?0707)<br>
demo中实现了简单的自定义效果
1. 自定义加载view中根据状态变化设置不同的图片
```java
@Override
public void onStateChanged(FIPullToRefreshView.State newState, FIPullToRefreshView.State oldState, PullToRefreshView view)
{
    switch (newState)
    {
        case RESET:
        case PULL_TO_REFRESH:
        case FINISH:
            getImageView().setImageResource(R.drawable.ic_pull_refresh_normal);
            break;
        case RELEASE_TO_REFRESH:
            getImageView().setImageResource(R.drawable.ic_pull_refresh_ready);
            break;
        case REFRESHING:
            getImageView().setImageResource(R.drawable.ic_pull_refresh_refreshing);
            SDViewUtil.startAnimationDrawable(getImageView().getDrawable());
            break;
    }
}
```
2. 给FPullToRefreshView对象设置加载view
```java
view_pull.setHeaderView(new CustomPullToRefreshLoadingView(this)); //自定义HeaderView
view_pull.setFooterView(new CustomPullToRefreshLoadingView(this)); //自定义FooterView
```


## Xml布局
在xml中只能给FPullToRefreshView添加一个child<br>
child可以是RecyclerView,ListView,ScrollView等...
```xml
<com.fanwe.lib.pulltorefresh.FPullToRefreshView
    android:id="@+id/view_pull"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!--RecyclerView,ListView,ScrollView...-->

</com.fanwe.lib.pulltorefresh.FPullToRefreshView>
```

## 常用方法
```java
view_pull.setDebug(true); //设置调试模式，会打印log
view_pull.setMode(FIPullToRefreshView.Mode.PULL_BOTH); //刷新模式，详细模式见源码
view_pull.setOverLayMode(false); //设置LoadingView是覆盖模式，还是拖拽模式，默认拖拽模式
view_pull.startRefreshingFromHeader(); //触发下拉刷新，此方法不受模式限制
view_pull.startRefreshingFromFooter(); //触发上拉加载，此方法不受模式限制
view_pull.stopRefreshing(); //停止刷新或者加载
view_pull.stopRefreshingWithResult(true); //停止刷新，刷新结果成功
view_pull.stopRefreshingWithResult(false); //停止刷新，刷新结果失败
view_pull.setComsumeScrollPercent(0.5f); //设置拖动距离消耗比例[0-1]，让拖动具有阻尼感，默认0.5f
view_pull.setDurationShowRefreshResult(600); //设置显示刷新结果的时长，默认600毫秒
view_pull.getScrollDistance(); //获得滚动的距离
view_pull.getDirection(); //获得滚动的方向，FROM_HEADER，FROM_FOOTER，NONE
view_pull.setHeaderView(new CustomPullToRefreshLoadingView(this)); //自定义HeaderView
view_pull.setFooterView(new CustomPullToRefreshLoadingView(this)); //自定义FooterView
view_pull.setOnRefreshCallback(new FIPullToRefreshView.OnRefreshCallback() //设置触发刷新回调
{
    @Override
    public void onRefreshingFromHeader(final PullToRefreshView view)
    {
        //头部刷新回调
    }

    @Override
    public void onRefreshingFromFooter(final PullToRefreshView view)
    {
        //底部加载回调
    }
});

//设置状态变化回调
view_pull.setOnStateChangeCallback(new FIPullToRefreshView.OnStateChangeCallback()
{
    @Override
    public void onStateChanged(FIPullToRefreshView.State newState, FIPullToRefreshView.State oldState, PullToRefreshView view)
    {
        //自定义的加载view继承库中的加载view基类后也可以收到此事件，可以根据状态展示不同的ui
    }
});

//设置view位置变化回调
view_pull.setOnViewPositionChangeCallback(new FIPullToRefreshView.OnViewPositionChangeCallback()
{
    @Override
    public void onViewPositionChanged(PullToRefreshView view)
    {
        //自定义的加载view继承库中的加载view基类后也可以收到此事件，可以根据状态和滚动距离自定义各种加载ui
    }
});
```
## 支持的方法
```java
public interface PullToRefreshView
{
    /**
     * 默认的拖动距离消耗比例
     */
    float DEFAULT_COMSUME_SCROLL_PERCENT = 0.5f;
    /**
     * 默认的显示刷新结果的时长（毫秒）
     */
    int DEFAULT_DURATION_SHOW_REFRESH_RESULT = 600;

    /**
     * 设置刷新模式
     *
     * @param mode
     */
    void setMode(Mode mode);

    /**
     * 设置刷新回调
     *
     * @param onRefreshCallback
     */
    void setOnRefreshCallback(OnRefreshCallback onRefreshCallback);

    /**
     * 设置状态变化回调
     *
     * @param onStateChangeCallback
     */
    void setOnStateChangeCallback(OnStateChangeCallback onStateChangeCallback);

    /**
     * 设置view位置变化回调
     *
     * @param onViewPositionChangeCallback
     */
    void setOnViewPositionChangeCallback(OnViewPositionChangeCallback onViewPositionChangeCallback);

    /**
     * 设置可以触发拖动的条件，设置后当view内部满足拖动，并且此对象也满足条件后才可以触发拖动
     *
     * @param pullCondition
     */
    void setPullCondition(PullCondition pullCondition);

    /**
     * 设置HeaderView和FooterView是否是覆盖的模式（默认false）
     *
     * @param overLayMode
     */
    void setOverLayMode(boolean overLayMode);

    /**
     * 是否是覆盖的模式
     *
     * @return
     */
    boolean isOverLayMode();

    /**
     * 设置拖动的时候要消耗的拖动距离比例，默认{@link #DEFAULT_COMSUME_SCROLL_PERCENT}
     *
     * @param comsumeScrollPercent [0-1]
     */
    void setComsumeScrollPercent(float comsumeScrollPercent);

    /**
     * 设置显示刷新结果的时长，默认{@link #DEFAULT_DURATION_SHOW_REFRESH_RESULT}
     *
     * @param durationShowRefreshResult
     */
    void setDurationShowRefreshResult(int durationShowRefreshResult);

    /**
     * 设置HeaderView处处于刷新状态
     */
    void startRefreshingFromHeader();

    /**
     * 设置Foot而View处处于刷新状态
     */
    void startRefreshingFromFooter();

    /**
     * 停止刷新
     */
    void stopRefreshing();

    /**
     * 停止刷新并展示刷新结果，当状态处于刷新中的时候此方法调用才有效
     *
     * @param success true-刷新成功，false-刷新失败
     */
    void stopRefreshingWithResult(boolean success);

    /**
     * 是否处于刷新中
     *
     * @return
     */
    boolean isRefreshing();

    /**
     * 返回当前的状态
     *
     * @return
     */
    State getState();

    /**
     * 返回当前的刷新模式
     *
     * @return
     */
    Mode getMode();

    /**
     * 返回HeaderView
     *
     * @return
     */
    LoadingView getHeaderView();

    /**
     * 设置HeaderView
     *
     * @param headerView
     */
    void setHeaderView(LoadingView headerView);

    /**
     * 返回FooterView
     *
     * @return
     */
    LoadingView getFooterView();

    /**
     * 设置FooterView
     *
     * @param footerView
     */
    void setFooterView(LoadingView footerView);

    /**
     * 返回要支持刷新的view
     *
     * @return
     */
    View getRefreshView();

    /**
     * 返回当前拖动方向
     *
     * @return
     */
    Direction getDirection();

    /**
     * 返回滚动的距离
     *
     * @return
     */
    int getScrollDistance();

    enum State
    {
        /**
         * 重置
         */
        RESET,
        /**
         * 下拉刷新
         */
        PULL_TO_REFRESH,
        /**
         * 松开刷新
         */
        RELEASE_TO_REFRESH,
        /**
         * 刷新中
         */
        REFRESHING,
        /**
         * 刷新结果，成功
         */
        REFRESHING_SUCCESS,
        /**
         * 刷新结果，失败
         */
        REFRESHING_FAILURE,
        /**
         * 刷新完成
         */
        FINISH,
    }

    enum Direction
    {
        NONE,
        FROM_HEADER,
        FROM_FOOTER,
    }

    enum Mode
    {
        /**
         * 支持上下拉
         */
        PULL_BOTH,
        /**
         * 只支持下拉
         */
        PULL_FROM_HEADER,
        /**
         * 只支持上拉
         */
        PULL_FROM_FOOTER,
        /**
         * 不支持上下拉
         */
        PULL_DISABLE,
    }

    interface OnStateChangeCallback
    {
        /**
         * 状态变化回调
         *
         * @param newState
         * @param oldState
         * @param view
         */
        void onStateChanged(State newState, State oldState, PullToRefreshView view);
    }

    interface OnRefreshCallback
    {
        /**
         * 下拉触发刷新回调
         *
         * @param view
         */
        void onRefreshingFromHeader(PullToRefreshView view);

        /**
         * 上拉触发刷新回调
         *
         * @param view
         */
        void onRefreshingFromFooter(PullToRefreshView view);
    }

    interface OnViewPositionChangeCallback
    {
        /**
         * view位置变化回调
         *
         * @param view
         */
        void onViewPositionChanged(PullToRefreshView view);
    }

    interface PullCondition
    {
        /**
         * 是否可以从Header处触发拖动
         *
         * @param view
         * @return
         */
        boolean canPullFromHeader(PullToRefreshView view);

        /**
         * 是否可以从Footer处触发拖动
         *
         * @param view
         * @return
         */
        boolean canPullFromFooter(PullToRefreshView view);
    }
}
```
