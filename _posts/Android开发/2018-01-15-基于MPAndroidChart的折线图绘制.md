---
layout: post
title:  "基于MPAndroidChart的折线图绘制"
date:   2018-01-15
categories: Java
tag: Android开发
---

* content
{:toc}


&emsp;&emsp;出于需要，APP里面需要一个根据给定数据绘制折线图的功能。上网查找了相关资料后惊奇的发现了功能强大的MPAndroidChart。在此与大家一起分享一下使用MPAndroidChart的方法。

MPAndroidChart[源码](https://github.com/PhilJay/MPAndroidChart)

---

引入相关依赖

&emsp;&emsp;要使用MPAndroidChart是要需要在工程里面引入相关的依赖。官方文档里面给出了四种引入方式，这里以开发环境为Android Studio为例（即Gradle dependency）。

&emsp;&emsp;首先在project level的build.gradle里面添加：

```
allprojects {
	repositories {
		maven { url "https://jitpack.io" }
	}
}
```

&emsp;&emsp;其次在app level的build.gradle里面添加：

```
dependencies {
	implementation 'com.github.PhilJay:MPAndroidChart:v3.0.3'
}
```

---

开始画图

&emsp;&emsp;MPAndroidChart的一大方便之处就是，画图就像添加一个控件那么方便。首先在.xml文件里面添加LineChart“控件”。

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.xkw.draw_data.MainActivity">

    <!--折线图-->
    <com.github.mikephil.charting.charts.LineChart
        android:id="@+id/chart1"
        android:layout_height="match_parent"
        android:layout_width="match_parent"
        />
</android.support.constraint.ConstraintLayout>
```

&emsp;&emsp;然后，主程序里面实例化一个折线图并与该“控件”绑定，传入相关数据即可完成绘图。

```java
public class MainActivity extends AppCompatActivity implements OnChartGestureListener,OnChartValueSelectedListener{

    private LineChart mChart;
    private LineData data;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mChart = (LineChart) findViewById(R.id.chart1);
        mChart.setOnChartGestureListener(this);
        mChart.setOnChartValueSelectedListener(this);
        mChart.setDrawGridBackground(false);
        // 无描述文本
        mChart.getDescription().setEnabled(false);

        // 使能点击
        mChart.setTouchEnabled(true);

        // 使能拖动和缩放
        mChart.setDragEnabled(true);
        mChart.setScaleEnabled(true);

        // 如果为false，则x，y两个方向可分别缩放
        mChart.setPinchZoom(true);

        /**
         1.添加assets文件   project->src->main位置，右键new->Directory->assets
         2.在assets文件中   new->Directory->Fonts文件
         3.在Fonts文件中复制下载好的ttf文件，例如OpenSans-Regular.ttf
         */
        //设置上下限
        Typeface tf = Typeface.createFromAsset(getBaseContext().getAssets(), "Fonts/OpenSans-Regular.ttf");

        LimitLine ll1 = new LimitLine(90f, "Upper Limit");
        ll1.setLineWidth(4f);
        ll1.enableDashedLine(10f, 10f, 0f);
        ll1.setLabelPosition(LimitLine.LimitLabelPosition.RIGHT_TOP);
        ll1.setTextSize(10f);
        ll1.setTypeface(tf);

        LimitLine ll2 = new LimitLine(80f, "Lower Limit");
        ll2.setLineWidth(4f);
        ll2.enableDashedLine(10f, 10f, 0f);
        ll2.setLabelPosition(LimitLine.LimitLabelPosition.RIGHT_BOTTOM);
        ll2.setTextSize(10f);
        ll2.setTypeface(tf);

        //设置x轴位置
        XAxis xAxis = mChart.getXAxis();
        xAxis.setPosition(XAxis.XAxisPosition.BOTTOM);

        //去除右边的y轴
        YAxis yAxisRight = mChart.getAxisRight();
        yAxisRight.setEnabled(false);
        YAxis yAxisLeft = mChart.getAxisLeft();
        yAxisLeft.addLimitLine(ll1);
        yAxisLeft.addLimitLine(ll2);
        init();
    }

    private void init(){
        //初始化数据
        String xl[] ={"1","2","3","4","5","6","7","8","9","10"}; //横轴数据
        String yl[] ={"80","85","80","90","95","88","90","91","92","93"}; //竖轴数据
        data = getData(xl,yl);
        mChart.setData(data);
        mChart.animateX(2000);//动画时间
    }

    private LineData getData(String[] xx, String[] yy){
        ArrayList<Entry> yVals = new ArrayList<Entry>();
        for (int i = 0; i < yy.length; i++) {
            // 要显示的数据
            yVals.add(new Entry(Float.parseFloat(xx[i]),Float.parseFloat(yy[i])));
        }
        //一条曲线对应一个LineDataSet
        LineDataSet set1 = new LineDataSet(yVals, "前五次的评分");
        set1.setMode(LineDataSet.Mode.CUBIC_BEZIER);//设置曲线为圆滑的线
        set1.setCubicIntensity(0.2f);
        set1.setDrawCircles(true);  //设置有圆点
        set1.setLineWidth(2f);    //设置线的宽度
        set1.setCircleSize(5f);   //设置小圆的大小
        set1.setDrawFilled(true);//设置包括的范围区域填充颜色
        set1.setCircleColor(Color.rgb(244, 117, 117));
        set1.setColor(Color.rgb(244, 117, 117));
        ArrayList<ILineDataSet> dataSets = new ArrayList<ILineDataSet>();
        dataSets.add(set1); // add the datasets

        // create a data object with the datasets
        LineData data = new LineData(dataSets);
        return data;
    }

    @Override
    public void onChartGestureStart(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture) {

    }

    @Override
    public void onChartGestureEnd(MotionEvent me, ChartTouchListener.ChartGesture lastPerformedGesture) {
        if(lastPerformedGesture != ChartTouchListener.ChartGesture.SINGLE_TAP)
            mChart.highlightValues(null); // or highlightTouch(null) for callback to onNothingSelected(...)
    }

    @Override
    public void onChartLongPressed(MotionEvent me) {

    }

    @Override
    public void onChartDoubleTapped(MotionEvent me) {

    }

    @Override
    public void onChartSingleTapped(MotionEvent me) {

    }

    @Override
    public void onChartFling(MotionEvent me1, MotionEvent me2, float velocityX, float velocityY) {

    }

    @Override
    public void onChartScale(MotionEvent me, float scaleX, float scaleY) {

    }

    @Override
    public void onChartTranslate(MotionEvent me, float dX, float dY) {

    }

    @Override
    public void onValueSelected(Entry e, Highlight h) {

    }

    @Override
    public void onNothingSelected() {

    }
}
```

结果示意

![](https://img-blog.csdn.net/20180115202200341?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

本文[源码](http://download.csdn.net/download/kewei168/10206920)
