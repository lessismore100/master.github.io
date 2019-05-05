---
title: android_text_bold_style_slightly_2019
date: 2019-05-05 18:32:56
tags:
---

`+++android粗体显示问题+++`

**一段对话作为开场：**

**设计师：** 这个文案有点粗(字体有点粗，不要会错意嚎)，不要这个粗，要比较纤细的那种粗！

**开发：** ......

**设计师：** 就是看起来比较纤细的那种粗！

**开发：** ......

**设计师：** 比如......

**开发：** `android:textStyle="bold"`这就是粗体，就这一种可设置粗，不好看吗？

**设计师：** 为什么加粗会这么粗，iOS没有这么粗啊，不要这么粗，要纤细的那种粗！比较纤细的那种粗！......

**开发：** ！@#￥%……&*（）~~~~~~~内心很彷徨，内心有点小绝望，这个怎么给他说明白呢？

---
以上是开发和设计师的一段`戏`，开发给了一个粗，设计师要另外一个粗，此粗非彼粗啊。。。



*听到问题后，我主动来调解下。*


> 首先在英文环境下，android和ios表现的一样优秀，在中文情况下就差强人意了。

为什么会比iOS的粗，因为是系统字体不一样的原因，iOS 用的是Helvetica，4.0之前是 Droid Sans Fallback字体，Android 4.0 之后西文字体升级为 Roboto，其字体本身就设计加粗效果就是这么粗。就没有办法了吗？有的

* 可以换字体，比如light字体。但是不支持局部的加粗，不满足某些条件，比较难办，况且加字体这事，android上请慎重……
* 第二种，也是目前采用的方案，继承`CharacterStyle`，实现`updateDrawState`方法，设置`tp.setFakeBoldText(true);`是一种伪粗体效果，比原字体加粗的效果弱一点。当然这里还可以有第三种办法就是`tp.setStyle(Paint.Style.FILL_AND_STROKE); tp.setStrokeWidth(2);`设置1-5控制字体加粗的程度，可以控制是否实心什么的，自己尝试去做更多的效果吧

先来个效果图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190505153953928.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x5bHRpZ2Vy,size_16,color_FFFFFF,t_70)





### 实现


##### 1. 自定义textview类
```

public class SlightlyBoldTextView extends android.support.v7.widget.AppCompatTextView {

    public SlightlyBoldTextView(Context context) {
        super(context);
        setSlightBoldStyle();
    }

    public SlightlyBoldTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
//        attrs.getAttributeIntValue();
        setSlightBoldStyle();
    }

    public SlightlyBoldTextView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        setSlightBoldStyle();
    }

    private void setSlightBoldStyle(){
        Log.i("bold","setSlightBoldStyle...");
        SpannableString str = new SpannableString(getText().toString());
        str.setSpan(new SlightlyBoldSpan(),0,getText().length(), Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
        setText(str);
    }

    public class SlightlyBoldSpan extends CharacterStyle {

        @Override
        public void updateDrawState(TextPaint tp) {
            /** 以下两种方法选其一 */
            // first
            tp.setFakeBoldText(true);

            // second
            tp.setStyle(Paint.Style.FILL_AND_STROKE);
            tp.setStrokeWidth(10);//控制字体加粗的程度
        }
    }
}
```

##### 2. 在layout/xml中加入自定义textview(或者忽略此步直接第三步中findViewById改为创建一个TextView对象即可)

```

<你的包名.SlightlyBoldTextView
    android:id="@+id/tvBoldSlightBold"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    android:textSize="20sp"
    android:text="字体效果-boldsmall"
    />
```

##### 3. 给TextView设置spannable达到bold效果

```

TextView tvBoldSmall = findViewById(R.id.tvBoldSmall);

SpannableString str = new SpannableString(tvBoldSmall.getText().toString());
str.setSpan(new FakeBoldSpan(),0,tvBoldSmall.getText().length(), Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
tvBoldSmall.setText(str);
```


ok，基本实现。欢迎拍砖。

TAG：Android  TextView  Spannable  String  CharacterStyle