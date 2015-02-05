---
layout: post
title: Android Widget开发
description: "Android Widget中使用ListView"
modified: 2015-02-04
tags: [Android, Widget, 开发]
---

### 最近想对自己原先做的一款Android应用更新，优化一下原先的代码，再添加一些新功能。由于之前有用户反馈希望能够加上桌面小插件的功能，这次就先添加这个功能啦。

原先在一款智能电视应用上开发过Widget，所以对这个东西还有些印象。Widget就是我们看到的Android小插件。最早出来的时候，可以说是眼前一亮的感觉，颠覆了之前传统手机的桌面。但是现在被一些无良公司开发的越来越耗资源、耗电、耗流量了。废话不多说了，直接开干吧。

这次想做的Widget是想做成一个列表状的，上一次做的时候我记得Widget是没有ListView的支持的。时隔多日，想看看对ListView支持了否，发现Android从3.0版本开始支持啦~那这次就尝试用一下吧。

##Step 1
创建你想要的Widget的布局文件，我的为`widget_day.xml`。由于我的需求，需要在里边放一个ListView，布局就像这样
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/white" >

    <ListView
        android:id="@+id/list_widget_content"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fadeScrollbars="true"
        android:smoothScrollbar="true" >
    </ListView>

</RelativeLayout>
{% endhighlight %}
>因为不是所有牛奶都叫特仑苏，所以不是所有控件都能用在Widget上的，Google官方列出了能够使用的控件。<br/>
A RemoteViews object (and, consequently, an App Widget) can support the following layout<br/> 
classes:<br/>
FrameLayout<br/>
LinearLayout<br/>
RelativeLayout<br/>
GridLayout<br/>
And the following widget classes:<br/>
AnalogClock<br/>
Button<br/>
Chronometer<br/>
ImageButton<br/>
ImageView<br/>
ProgressBar<br/>
TextView<br/>
ViewFlipper<br/>
ListView<br/>
GridView<br/>
StackView<br/>
AdapterViewFlipper<br/>
Descendants of these classes are not supported.<br/>
RemoteViews also supports ViewStub, which is an invisible, zero-sized View you can use to lazily inflate layout resources at runtime.

创建你的Widget类，实现**AppWidgetProvider**便可以作为一个Widget了。
{% highlight java %}
public class DayWidget extends AppWidgetProvider
{% endhighlight %}

在`res`下创建`xml`文件夹，添加对于Widget属性配置的xml文件，名字随意，我这边为`day.xml`，内容大致为这样
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:initialLayout="@layout/widget_day"
    android:minHeight="@dimen/widget_day_height"
    android:minWidth="@dimen/widget_day_Widget"
    android:updatePeriodMillis="1800000" >
</appwidget-provider>
{% endhighlight %}
这里用到的属性主要为:

 * **android:initialLayout** Widget的布局文件
 * **android:minHeight** Widget的高度
 * **android:minWidth** Widget的宽度
 * **android:updatePeriodMillis** Widget的更新时间

其中**minHeight**和**minWidth**的参数，Google是有规范的。网上有些的是74 * n - 2。但是可能是过时的版本了，官网上看，现在的标准是这样的:

of Cells<br/>(Columns or Rows) | Available Size (dp)<br/>(minWidth or minHeight)
-------------------------------|------------------------------------------------
1                              |40dp
2                              |110dp
3                              |180dp
4                              |240dp
…                              |…
n                              |70 * n - 30

完成之后，在`AndroidManifest.xml`注册一下
{% highlight xml %}
<!-- Widget -->
<receiver
    android:name="com.karel.days.widget.DayWidget"
    android:icon="@drawable/ic_launcher"
    android:label="@string/app_name" >
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>

    <meta-data
        android:name="android.appwidget.provider"
        android:resource="@xml/day" />
</receiver>
{% endhighlight %}
其中接收的**android.appwidget.action.APPWIDGET_UPDATE**是系统发出通知Widget更新的广播。**AppWidgetProvider**中的**onReceive**会接收处理这个广播。

**android:resource**就是对应的Widget属性文件。

到这里没问题的话已经能Run起来看到Widget了~

##Step 2
接下来就是Widget的具体实现了。

首先看下接口**AppWidgetProvider**提供的方法

 * **onReceive(Context context, Intent intent)** 这个之前已经说过，是接收更新广播的，当然可以自己再拓展，监听自己的广播做些偷鸡摸狗的小事情~
 
 * **onUpdate()** 收到广播之后就会执行到这个方法，进行更新界面。把要更新的逻辑加在这里应该是个不错的选择。
 
 * **onAppWidgetOptionsChanged()** 当Widget被第一次放置在Launcher上或者它的大小被改变时，会执行这个方法。
 
 * **onDeleted(Context, int[])** 当Widget的一个实例被删掉了会执行这个方法，应该是要监听**ACTION_APPWIDGET_DELETED**这个广播的。
 
 * **onEnabled(Context context)** 当Widget第一次被创建会被调用到。举个栗子，如果Widget创建了两个实例，那么只会在第一个实例创建的时候才会被调用到。
 
 * **onDisabled(Context)** 和onEnable相反，当Widget的最后一个实例被移除的时候才会调用到。
 
这里我主要关注**onUpdate**里的方法，下面是我更新Widget的做法

{% highlight java %}
@Override
public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
	// TODO Auto-generated method stub
	Log.d(TAG, "enter onUpdate");
	for (int widgetId : appWidgetIds) {

		RemoteViews remoteViews = new RemoteViews(context.getPackageName(),
				R.layout.widget_day);
		Intent adapterIntent = new Intent(context, DayWidgetService.class);
		// Set list's adapter
		remoteViews.setRemoteAdapter(R.id.list_widget_content,
				adapterIntent);
		// views.setEmptyView(R.id.list_widget_content, R.id.tv_empty);

		// Register an onClickListener
		Intent intent = new Intent(context, HomeActivity.class);
		PendingIntent pendingIntent = PendingIntent.getActivity(context,
				0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
		remoteViews.setPendingIntentTemplate(R.id.list_widget_content,
				pendingIntent);
		appWidgetManager.updateAppWidget(widgetId, remoteViews);
	}
	super.onUpdate(context, appWidgetManager, appWidgetIds);
}
{% endhighlight %}

从中已经看到了一些比较重要的东西了，这两句就是对ListView设置Adapter。**DayWidgetService**就完成了传统ListView的Adapter作用。

{% highlight java %}
Intent adapterIntent = new Intent(context, DayWidgetService.class);
// Set list's adapter
remoteViews.setRemoteAdapter(R.id.list_widget_content,adapterIntent);
{% endhighlight %}

那么看下**DayWidgetService**里做了些什么。

**DayWidgetService**继承自**RemoteViewsService**，重写一个方法**onGetViewFactory**即可，返回**RemoteViewsFactory**。

看起来花头都在**RemoteViewsFactory**里面!

自己创建一个**DayWidgetFactory**实现**RemoteViewsService.RemoteViewsFactory**的方法。会发现这里的方法和Adapter中的惊人相似，的确这两个东西是有关联的。

最重要的一个方法是**getViewAt(int position)**，返回一个**RemoteViews**。和Adapter中的getView作用一样，返回ListView中一个个内容的。我的实现主要如下：
{% highlight java %}
@Override
public public RemoteViews getViewAt(int position) getViewAt(int position) {
	// TODO Auto-generated method stub
	if (position >= mDays.size()) {
		Log.w(TAG, "position > mDays.size");
		return null;
	}
	RemoteViews view = new RemoteViews(mContext.getPackageName(),
			R.layout.unit_list_home_content);

	if (mDays != null && position < mDays.size() && mContext != null) {
		try {
			String name = mDays.get(position).getName();
			int top = mDays.get(position).getTop();
			String date = mDays.get(position).getDate();
			view.setTextViewText(R.id.txt_unit_name, name);
			int day = DateUtils.getInterval(mSimpleDateFormat
					.parse(date));
			if (day <= 0) {
				view.setTextViewText(
						R.id.txt_unit_date,
						mContext.getString(R.string.past)
								+ Math.abs(day));
				view.setTextColor(R.id.txt_unit_date, mContext
						.getResources().getColor(R.color.dark_yellow));
			} else {
				view.setTextViewText(
						R.id.txt_unit_date,
						mContext.getString(R.string.future)
								+ Math.abs(day));
				view.setTextColor(R.id.txt_unit_date, mContext
						.getResources().getColor(R.color.dark_green));
			}
			if (top == CommonData.TOP_TURE) {
				view.setViewVisibility(R.id.img_unit_top, View.VISIBLE);
			} else {
				view.setViewVisibility(R.id.img_unit_top,
						View.INVISIBLE);
			}
			//onItemClick
			Intent fillInIntent = new Intent();
	        view.setOnClickFillInIntent(R.id.layout_unit_content, fillInIntent);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
			Log.e(TAG, "getView", e);
		}
	}

	return view;
}
{% endhighlight %}

还有个比较重要的就是**onDataSetChanged()**方法，用来通知容器数据有更新。外部调用方法为

{% highlight java %}
appWidgetManager.notifyAppWidgetViewDataChanged(widgetId, R.id.list_widget_content);
{% endhighlight %}

>Collections数据刷新的流程图<br/>
![Appwidget Collections]({{ site.url }}/images/2015_02_04/img_1.png)

##Step 3
最后一步是添加ListView中Item的点击事件，刚才的代码中已经能看到了，主要是两处地方。
第一处是**DayWidgetService**中:

{% highlight java %}
//onItemClick
Intent fillInIntent = new Intent();
    view.setOnClickFillInIntent(R.id.layout_unit_content, fillInIntent);
{% endhighlight %}

第二处是**DayWidget**中:

{% highlight java %}
// Register an onClickListener
Intent intent = new Intent(context, HomeActivity.class);
PendingIntent pendingIntent = PendingIntent.getActivity(context,
		0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
remoteViews.setPendingIntentTemplate(R.id.list_widget_content,
		pendingIntent);
{% endhighlight %}

这两处就可以实现ListView的onItemClicked的作用，实现点击跳转。如果只是普通控件比如Button，只需实现第二处就行了。

