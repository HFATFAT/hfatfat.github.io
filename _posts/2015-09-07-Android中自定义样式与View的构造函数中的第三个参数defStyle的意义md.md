---
layout:     post
title:      Android中自定义样式与View的构造函数中的第三个参数defStyle的意义Android Studio
date:       2015-09-07 19:56:17
author:     HFF  
summary:    Android中自定义样式与View的构造函数中的第三个参数defStyle的意义
categories: jekyll
thumbnail:  heart
tags:
 - Android
---

#####Android中自定义样式与View的构造函数中的第三个参数defStyle的意义
非常好的一篇，多读几遍。
######原文链接<http://www.cnblogs.com/angeldevil/p/3479431.html>
零、序

　　系统自带的View可以在xml中配置属性，对于写的好的Custom View同样可以在xml中配置属性，为了使自定义的View的属性可以在xml中配置，需要以下4个步骤：

通过<declare-styleable>为自定义View添加属性

在xml中为相应的属性声明属性值

在运行时（一般为构造函数）获取属性值

将获取到的属性值应用到View

　　怎么将获取到的属性值应用到View就不用说了，自己定义的属性什么用处自己肯定是清楚的，所以接下来看一下前三点。

一、自定义Style

　　通过<declare-styleable>元素声明Custom View需要的属性即可，下面是一个例子，文件是res/values/attrs.xml


<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="Customize">
        <attr name="attr_one" format="string" />
        <attr name="attr_two" format="string" />
        <attr name="attr_three" format="string" />
        <attr name="attr_four" format="string" />
    </declare-styleable>
 
    <attr name="CustomizeStyle" format="reference" />
</resources>

　　在上述xml中，我们声明了Customize与CustomizeSyle，Customize包含了attr_one、attr_two、attr_three与attr_four四个attribute，CustomizeStyle也是一个attribute，但是却没有声明在declare-styleable中。

　　定义在declare-styleable中与直接用attr定义没有实质的不同，上述xml中，无论attr_one - attr_four是否声明在declare-styleable中，系统都会为我们在R.attr中生成5个attribute


public static final class attr {
    public static final int CustomizeStyle=0x7f010004;
    public static final int attr_one=0x7f010000;
    public static final int attr_two=0x7f010001;
    public static final int attr_three=0x7f010002;
    public static final int attr_four=0x7f010003;
}

　　不同的是，如果声明在declare-styleable中，系统还会为我们在R.styleable中生成相关的属性。


public static final class styleable {
    public static final int[] Customize = {
        0x7f010000, 0x7f010001, 0x7f010002, 0x7f010003
    };
    public static final int Customize_attr_one = 0;
    public static final int Customize_attr_two = 1;    
    public static final int Customize_attr_three = 2;
    public static final int Customize_attr_four = 3;
}

 　　如上所示，R.styleable.Customize是一个int[]，而里面的元素的值正好和R.attr.attr_one - R.attr.attr_four一一对应，而R.styleable.Customize_attr_one等4个值就是R.attr.attr_one-R.attr.attr_four在R.styleable.Customize数组中的索引。这个数组和索引在第三步运行时获得属性值时会用到，将attr分组声明在declare-styleabe中的作用就是系统会自动为我们生成这些东西，如果不声明在declare-styleable中，我们完全可以在需要的时候自己构建这个数组，由于数组是自己构建的，每个属性的下标索引也就很清楚了，只是比较麻烦。以上一家之言，不过从使用及实验难上看确实是这样的。

二、在xml中为相应的属性声明属性值

        我们知道，在xml中为属性赋值有几种不同的方式

直接在layout中使用属性

设置style并在style中设置属性

Application和Activity可以指定theme，可以在theme中指定在当前Application或Activity中属性的默认值

        下面就分别看一下这三种方式

1. 直接在layout中使用属性
        在xml layout中使用自定义属性和使用系统属性差不多，不过属性所属的namespace不同，比如像下面这样。


<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:ad="http://schemas.android.com/apk/res/com.angeldevil.customstyle"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity" >

    <com.angeldevil.customstyle.CustomTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        ad:attr_one="attr one in xml"
        style="@style/ThroughStyle"
        android:text="@string/hello_world" />

</RelativeLayout>

　　像layout_width等属性属于android的namespace，自定义的属性属于当前程序的namespace，只需像声明android的namespace一样声明当前程序的namespace就好，只需要把上面红色部分的android换成当前程序的包名。应用属性的时候也需要注意属性的namespace。

2. 设置style并在style中设置属性
　　看上面xml中绿色的那一行，我们为CustomTextView声明了一个style：ThroughStyle，这个Style很简单，只是指定了两个属性的值

<style name="ThroughStyle">
    <item name="attr_one">attr one from style</item>
    <item name="attr_two">attr two from style</item>
</style>
　　注意，在style中我们声明了attr_one的值，同时在xml中也直接向attr_one赋了值，最终用哪一个有个优先级的问题，后面在第三节：在运行时获取属性值中再说，接下来看下第三种方式。

3. theme中指定在当前Application或Activity中属性的默认值

<!-- Application theme. -->
<style name="AppTheme" parent="AppBaseTheme">
    <!-- All customizations that are NOT specific to a particular API-level can go here. -->
    <item name="attr_one">attr one from theme</item>
    <item name="attr_two">attr two from theme</item>
    <item name="attr_three">attr three from theme</item>
    <item name="CustomizeStyle">@style/CustomizeStyleInTheme</item>
</style>

<style name="CustomizeStyleInTheme">
    <item name="attr_one">attr one from theme reference</item>
    <item name="attr_two">attr two from theme reference</item>
    <item name="attr_three">attr three from theme reference</item>
</style>

 　　在上述xml中，我们在AppTheme中为attr_one、attr_two与attr_three指定了值，但是同时也为CustomizeStyle指定了一个reference，而在这个reference中为attr_one、attr_two与attr_three指定了值，CustomizeStyle就是我们在attrs.xml中定义的一个属性。在theme中为属性设置值的方式有两这种，直接在theme中定义或通过另一个attribute引用一个style，这两种方式还是有区别的，在下面的获取属性值一节中可以看到。

三、在运行时获取属性值

        在styles.xml中，我们同时定义一个DefaultCustomizeStyle的style，它的作用等下可以看到。

<style name="DefaultCustomizeStyle">
    <item name="attr_one">attr one from defalut style res</item>
    <item name="attr_two">attr two from defalut style res</item>
    <item name="attr_three">attr three from defalut style res</item>
</style>
　　先看下CustomTextView的代码


public class CustomTextView extends TextView {
    private static final String TAG = CustomTextView.class.getSimpleName();

    public CustomTextView(Context context) {
        super(context);
    }

    public CustomTextView(Context context, AttributeSet attrs) {
        this(context, attrs, R.attr.CustomizeStyle);
    }

    public CustomTextView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.Customize, defStyle, R.style.DefaultCustomizeStyle);
        String one = a.getString(R.styleable.Customize_attr_one);
        String two = a.getString(R.styleable.Customize_attr_two);
        String three = a.getString(R.styleable.Customize_attr_three);
        String four = a.getString(R.styleable.Customize_attr_four);
        Log.i(TAG, "one:" + one);
        Log.i(TAG, "two:" + two);
        Log.i(TAG, "three:" + three);
        Log.i(TAG, "four:" + four);
        a.recycle();
    }
}

 　　主要代码都在第三个函数中，这里也没做什么，只是通过Context的obtainStyledAttributes获得了前面定义的4个属性的值并打印了出来。

View的第三个构造函数的第三个参数defStyle
　　如果在Code中实例化一个View会调用第一个构造函数，如果在xml中定义会调用第二个构造函数，而第三个函数系统是不调用的，要由View（我们自定义的或系统预定义的View，如此处的CustomTextView和Button）显式调用，比如在这里我们在第二个构造函数中调用了第三个构造函数，并将R.attr.CustomizeStyle传给了第三个参数。

　　第三个参数的意义就如同它的名字所说的，是默认的Style，只是这里没有说清楚，这里的默认的Style是指它在当前Application或Activity所用的Theme中的默认Style，以系统中的Button为例说明。


public Button(Context context) {
    this(context, null);
}

public Button(Context context, AttributeSet attrs) {
    this(context, attrs, com.android.internal.R.attr.buttonStyle);
}

public Button(Context context, AttributeSet attrs, int defStyle) {
    super(context, attrs, defStyle);
}

 　　在Code中实例化View会调用第一个构造函数，在XML中定义会调用第二个构造函数，在Button的实现中都调用了第三个构造函数，并且defStyle的值是com.android.internal.R.attr.buttonStyle。buttonStyle是系统中定义的一个attribute，系统默认有一个Theme，比如4.0中是Theme.Holo

<style name="Theme.DeviceDefault" parent="Theme.Holo" >
    ...
    <item name="buttonStyle">@android:style/Widget.DeviceDefault.Button</item>
    ....
</style>
　　上面是系统默认的Theme，为buttonStyle指定了一个Style


<style name="Widget.DeviceDefault.Button" parent="Widget.Holo.Button" >
</style>

<style name="Widget.Holo.Button" parent="Widget.Button">
<item name="android:background">@android:drawable/btn_default_holo_dark</item>
    <item name="android:textAppearance">?android:attr/textAppearanceMedium</item>
    <item name="android:textColor">@android:color/primary_text_holo_dark</item>
    <item name="android:minHeight">48dip</item>
    <item name="android:minWidth">64dip</item>
</style>

 　　这个Style定义了系统中Button的默认属性，如background等。

　　从文档中第三个构造函数的说明中也可以看到，这个构造函数的作用是View的子类提供这个类的基础样式

View(Context context, AttributeSet attrs, int defStyleAttr)

Perform inflation from XML and apply a class-specific base style.

　　上面说的都比较抽象，还是直接看实例代码来的清楚明白，实验用的代码上面全都贴完了，这里直接看结果，但在这之前要先看一个函数：obtainStyledAtributes。

obtainStyledAtributes
　　我们要获取的属性值都是通过这个函数返回的TypedArray获得的，这是官方说明：obtainStyledAttributes，以下是函数原型：

public TypedArray obtainStyledAttributes (AttributeSet set, int[] attrs, int defStyleAttr, int defStyleRes)
　　4个参数的意思分别是：

　　　　set：属性值的集合

　　　　attrs：我们要获取的属性的资源ID的一个数组，如同ContextProvider中请求数据库时的Projection数组，就是从一堆属性中我们希望查询什么属性的值

　　　　defStyleAttr：这个是当前Theme中的一个attribute，是指向style的一个引用，当在layout xml中和style中都没有为View指定属性时，会从Theme中这个attribute指向的Style中查找相应的属性值，这就是defStyle的意思，如果没有指定属性值，就用这个值，所以是默认值，但这个attribute要在Theme中指定，且是指向一个Style的引用，如果这个参数传入0表示不向Theme中搜索默认值

　　　　defStyleRes：这个也是指向一个Style的资源ID，但是仅在defStyleAttr为0或defStyleAttr不为0但Theme中没有为defStyleAttr属性赋值时起作用

　　链接中对这个函数说明勉强过得去，这里简要概括一下。对于一个属性可以在多个地方指定它的值，如XML直接定义，style，Theme，而这些位置定义的值有一个优先级，按优先级从高到低依次是：

直接在XML中定义>style定义>由defStyleAttr和defStyleRes指定的默认值>直接在Theme中指定的值

　　看这个关系式就比较明白了，defStyleAttr和defStyleRes在前面的参数说明中已经说了，“直接在Theme中指定的值”的意思在下面的示代码中可以看到。

实验验证
　　相关的代码都前面都已经贴上了，不过为了方便查看，这里再把相关的XML一起贴一遍


main_activity.xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:ad="http://schemas.android.com/apk/res/com.angeldevil.customstyle"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity" >

    <com.angeldevil.customstyle.CustomTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        ad:attr_one="attr one in xml"
        style="@style/ThroughStyle"
        android:text="@string/hello_world" />

</RelativeLayout>

attrs.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <declare-styleable name="Customize">
        <attr name="attr_one" format="string" />
        <attr name="attr_two" format="string" />
        <attr name="attr_three" format="string" />
        <attr name="attr_four" format="string" />
    </declare-styleable>

    <attr name="CustomizeStyle" format="reference" />

</resources>

styles.xml
<resources>

    <style name="AppBaseTheme" parent="android:Theme.Light">
    </style>
    <!-- Application theme. -->
    <style name="AppTheme" parent="AppBaseTheme">
        <!-- All customizations that are NOT specific to a particular API-level can go here. -->
        <item name="attr_one">attr one from theme</item>
        <item name="attr_two">attr two from theme</item>
        <item name="attr_three">attr three from theme</item>
        <item name="CustomizeStyle">@style/CustomizeStyleInTheme</item>
    </style>

    <style name="CustomizeStyleInTheme">
        <item name="attr_one">attr one from theme reference</item>
        <item name="attr_two">attr two from theme reference</item>
        <item name="attr_three">attr three from theme reference</item>
    </style>

    <style name="ThroughStyle">
        <item name="attr_one">attr one from style</item>
        <item name="attr_two">attr two from style</item>
    </style>
    
    <style name="DefaultCustomizeStyle">
        <item name="attr_one">attr one from defalut style res</item>
        <item name="attr_two">attr two from defalut style res</item>
        <item name="attr_three">attr three from defalut style res</item>
    </style>

</resources>

 　　在Code中是这样获取属性的


public CustomTextView(Context context, AttributeSet attrs) {
    this(context, attrs, R.attr.CustomizeStyle);
}

public CustomTextView(Context context, AttributeSet attrs, int defStyle) {
    super(context, attrs, defStyle);
        
    TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.Customize, defStyle,
                R.style.DefaultCustomizeStyle);
    ...
}

　　CustomizeStyle是定义的一个attribute，DefaultCustomizeStyle是定义的一个style。

　　从代码中可以看到，R.attr.CustomizeStyle就是前面提到的defStyleAttr，R.style.DefaultCustomizeStyle就是defStyleRes。

　　在Styles.xml中我们为App定义了一个Theme：AppTheme，在这个Theme中直接为attr_one、attr_two、attr_three定义了属性值，这个就是前面关系式中说的直接在Theme中指定的值。

　　在AppTheme中同时定义了CustomizeStyle，引用了一个Style，在CustomTextView的构造函数中分别打印了attr_one、attr_two、attr_three、attr_four的值，所以下面我们就可以通过Log输出验证一下前面的关系式，如果一个attribute在多个位置都定义了值，究竟哪一个起作用。

　　

　　如上图所示：

attr_one同时在xml、style、defStyleAttr、defStyleRes与Theme中直接定义了值，但起作用的是在xml中的定义
attr_two同时在style、defStyleAttr、defStyleRes与Theme中直接定义了值，但起用的是在style中的定义
attr_three同时在defStyleAttr、defStyleRes与Theme中直接定义了值，但起作用的是defStyleAttr
attr_four在defStyleRes中定义了，但结果仍是0。
　　这可以说明：

1. 直接在XML中定义>style定义>由defStyleAttr定义的值>defStyleRes指定的默认值、直接在Theme中指定的值

2. defStyleAttr（即defStyle）不为0且在当前Theme中可以找到这个attribute的定义时，defStyleRes不起作用，所以attr_four虽然在defStyleRes（DefaultCustomizeStyle）中定义了，但取到的值仍为null。

　　为了看一下defStyleRes的作用，1. 我们在AppTheme中加上attr_four的定义，2. 向obtainStyledAttributes的第三个参数传入0，或者移除AppTheme中CustomizeStyle的定义，结果是一样的


<style name="AppTheme" parent="AppBaseTheme">
    <item name="attr_one">attr one from theme</item>
    <item name="attr_two">attr two from theme</item>
    <item name="attr_three">attr three from theme</item>
    <item name="attr_four">attr four from theme</item>
</style>

 　　由于defStyleAttr为0，或者defStyleAttr不为0但是我们没有为这个属性赋值，所以defStyleRes起作用，当一个属性没有在XML和Style中赋值时，系统会在defStyleRes（此处为DefaultCustomizeStyle）查找属性的值。

　　

　　我们看到attr_three的值来自defStyleRes，而attr_four的值来自Theme中的直接定义（DefaultCustomizeStyle定义了attr_one、atrr_two、attr_three） ，这就说明

1. defStyleAtrtr即defStyle为0或Theme中没有定义defStyle时defStyleRes才起作用

2. defStyleRes>在Theme中直接定义

 结论

 　　从前面的说明可以得到以下结论：

要为自定义View自定义属性，可以通过declare-styleable声明需要的属性
为了在Theme设置View的默认样式，可以同时定义一个format为reference的属性att_a，在定义Theme时为这个attribute指定一个Style，并且在View的第二个构造函数中将R.attr.attr_a 作为第三个参数调用第三个构造函数
可以定义一个Style作为Theme中没有定义attr_a时View属性的默认值。
可以在Theme中直接为属性赋值，但优先级最低
当defStyleAttr（即View的构造函数的第三个参数）不为0且在Theme中有为这个attr赋值时，defStyleRes（通过obtainStyledAttributes的第四个参数指定）不起作用
属性值定义的优先级：xml>style>Theme中的默认Sytle>默认Style（通过obtainStyledAttributes的第四个参数指定）>在Theme中直接指定属性值
　　
[代码下载：CustomStyle.zip](http://files.cnblogs.com/files/angeldevil/CustomStyle.zip)

