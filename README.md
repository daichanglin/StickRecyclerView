# StickRecyclerView
实现吸顶效果的RecyclerView。
![效果1](https://upload-images.jianshu.io/upload_images/3882680-0c4b7f92d6994a64.gif?imageMogr2/auto-orient/strip)
)
![效果2](https://upload-images.jianshu.io/upload_images/3882680-8c0d3baf6ca60276.gif?imageMogr2/auto-orient/strip)
)
#注意
- 当item的高度大于或等于屏幕的一半时，会出现bug。
所以如果有以上情况的不建议使用
#Use Gradle
```
allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}

dependencies {
    implementation 'com.github.daichanglin:StickRecyclerView:1.13'
}
```
#How do I use StickRecyclerView?
xml：
```
 <com.dcl.stickrecyclerview.StickRecyclerView
            android:id="@+id/rv_01"
            android:layout_width="0dp"
            app:layout_constraintVertical_bias="0"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            android:layout_height="wrap_content">
    </com.dcl.stickrecyclerview.StickRecyclerView>
```
java代码：
```
class SelfAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>(), StickHelper {

    private var data: List<String>? = null
    private val labels by lazy { mutableMapOf<Int, String>() }
    private val Type_Label = 1          //需要吸顶的类型
    private val Type_Item = 2           //其他类型
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
      ...
    }

    override fun getItemCount(): Int {
        ...
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
      ...
    }

    override fun getItemViewType(position: Int): Int {
        if (labels.containsKey(position)) {
            return Type_Label
        } else {
            return Type_Item
        }
    }
  
    /**
     * 需要额外实现的两个方法之一
     */
    override fun isFloatType(position: Int): Boolean {
        return getItemViewType(position) == Type_Label
    }

    /**
     * 需要额外实现的两个方法之二
     */
    override fun isFloatMembers(position: Int): Boolean {
        return getItemViewType(position) == Type_Item
    }

}
```
#简要说明
##步骤
1. 使用StickRecyclerView替换原来的RecyclerView
2. adapter实现StickHelper接口。

StickRecyclerView设置stick可通过xml属性或者java代码设置，默认为true，表示启用吸顶，设置为false则表现与普通Recyclerview无异。

##提醒：
- layoutManager暂时只支持竖向的LinearLayoutManager（包括GridLayoutManager）
- 由于依赖于Recyclerview的addOnScrollListener中的onScrolled方法的回调，所以当Recyclerview被其他如上拉加载更多(如SwipeToLoadLayout)等控件包裹时，会出现无法吸顶的现象。
- StickRecyclerView的父布局不要使用LinearLayout(因为吸顶的item时间上是被添加到了父布局，如果使用LinearLayout就没了吸顶效果。推荐使用Framlayout、ConstraintLayout、RelativeLayout等作为StickRecyclerView的父布局)
- 当item的高度大于或等于屏幕的一半时，会出现bug。
##相较于其他方式实现吸顶的优缺点
###优点
- 支持复杂的吸顶view,不仅限于简单的text和drawable内容。
- 支持点击事件，实际上吸顶的item的渲染和触摸事件走的是ViewHolder中的那一套，在holder中设置的点击事件，在吸顶时依然有效。
- 使用较为简便。在写好通用的逻辑后，只需要2步，替换RecyclerView、实现StickHelper接口（接口中只有两个方法）。
###缺点
- ~~由于代码缺陷，只支持吸顶view的高度大于普通item的高度的情况（囧：下个版本会修复）。~~
- 当item的高度大于或等于屏幕的一半时，会出现bug。
- 使用继承实现，如果项目中的RecyclerView已经继承了其他的父类，则无法使用。
- 由于依赖于Recyclerview的addOnScrollListener中的onScrolled方法的回调，所以当Recyclerview被其他如下拉刷新等控件包裹时，会出现无法吸顶的现象。
- 滑动时吸顶view位置的变化通过addOnScrollListener中的onScrolled方法实现，如果Recyclerview的滚动的方式不触发onScrolled的话，则不会更新吸顶的view的位置和内容，当然一般情况下都是。
- 如果初始化Recyclerview的同时，调用了scrollToPosition（）方法，有概率会出现吸顶的view的顶部没有与Recyclerview的顶部对齐的问题（只有当Recyclerview的顶部不再其父布局的顶部时可能会出现，原因在于设置吸顶view的top时是根据Recyclerview的top来设置的，而过早的调用scrollToPosition会导致Recyclerview获取到的top错误）
