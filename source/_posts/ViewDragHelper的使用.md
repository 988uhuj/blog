title: ViewDragHelper的使用
date: 2015-06-21 13:31:53
tags: 
- Android
- View
---
ViewDragHelper是v4包中的一个工具类，类路径为`android.support.v4.widget`。主要作用是帮助开发者更方便的实现view的拖动效果。在v4包中相应的使用有`DrawerLayout`和`SlidingPaneLayout`。
<!-- more-->
在不使用ViewDragHelper的情况下实现View的拖动效果一般需要处理这几步：
1. 在父类的ViewGroup中根据手势位置判断是否拦截手势
2. 拦截完手势事件的处理
3. View状态位置的更新
4. 惯性滑动
5. ...

在[MenuDrawer](https://github.com/SimonVT/android-menudrawer)中便是根据自定义的逻辑处理drawer的各个状态，有兴趣的也可以研究MenuDrawer的源码。

使用ViewDragHelper方式来实现
1. 绑定ViewGroup和ViewDragHelper
2. 将事件传递给ViewDragHelper
3. 在ViewDragHelper.Callback中处理手势逻辑

开发者只需关注Callback中各个位置及状态值得更新即可。相比于自定义实现，ViewDragHelper所做的便是将手势事件拦截以及事件处理封装在自己内部，已回调的形式返回开发者所需的各个属性值，从而省去开发者花费在事件拦截事件处理上的时间。

---

下面实现一个简单的横向拖动View的例子。

效果图
![hdrag](https://github.com/chenupt/blog/blob/master/res/hdrag.gif?raw=true)

1. 实现一个自定义ViewGroup。因为拖动事件涉及到父类状态，边境大小等，封装在ViewGroup中更能方便的控制View。ViewDragHelper也是依托于ViewGroup的事件拦截完成View状态的回调。
```java
public class DragLayout extends FrameLayout {
    ...
}
```
2. 初始化绑定ViewGroup和ViewDragHelper。
```java
private void init() {
    viewDragHelper = ViewDragHelper.create(this, 1.f, callback);
}
```
参数1为创建的ViewGroup
参数2为拖动的敏感度。敏感的定义看下面源码可知，1.0f为正常值(normal)，值越大越敏感。 mTouchSlop为拖动的开始值，当超过手势的move值便认为开始滑动。
```java
helper.mTouchSlop = (int) (helper.mTouchSlop * (1 / sensitivity));
```
参数3为ViewDragHelper的Callback实现。主要的拖动处理逻辑都在Callback中实现。

3. 将ViewGroup的事件传递给ViewDragHelper处理
```java
@Override
public boolean onInterceptTouchEvent(MotionEvent ev) {
    return viewDragHelper.shouldInterceptTouchEvent(ev);    
}

@Override
public boolean onTouchEvent(MotionEvent event) {
    viewDragHelper.processTouchEvent(event);
    return true;    // 需消费touch事件，否则ViewGroup将不会接收Down的后续事件
}
```
4. 前面准备完便可在实现Callback在回调中处理自己的逻辑了。
```java
    private ViewDragHelper.Callback callback = new ViewDragHelper.Callback() {
    
        @Override
        public boolean tryCaptureView(View child, int pointerId) {
            return child == targetView; // targetView 为所要拖动的view
        }
        
        @Override
        public int getViewHorizontalDragRange(View child) {
            return getWidth();
        }
        
        /**
         * 该方法用于限制子view沿水平拖曳的手势.默认的实现是,不允许水平手势.如果有类继承了该类,
         * 必须覆盖重写该方法,并且提供值去限制该拖曳手势.
         */
        @Override
        public int clampViewPositionHorizontal(View child, int left, int dx) {
            return left;    // 返回手势点，在这里可进行范围的一些限制
        }

        @Override
        public void onViewPositionChanged(View changedView, int left, int top, int dx, int dy) {
            super.onViewPositionChanged(changedView, left, top, dx, dy);
        }

        @Override
        public void onViewReleased(View releasedChild, float xvel, float yvel) {
            super.onViewReleased(releasedChild, xvel, yvel);
            viewDragHelper.settleCapturedViewAt(getPaddingLeft(), getPaddingRight());
            invalidate();
        }

    };
```

上面实现的为几个主要的回调函数。调用顺序也是从上至下。

* 当手势是在目标view的范围内（tryCaptureView）,
* 判断该方向是否允许拖动（getViewHorizontalDragRange），
* 当手势位置改变时（clampViewPositionHorizontal），返回新的view坐标（return），
* 相应View位置的改变（onViewPositionChanged），
* 当手势释放（onViewReleased），获取释放的加速度（xvel），
* 相应可以设置释放后最终view位置（settleCapturedViewAt）。

如果需要释放View有滚动效果，则还需要复写`computeScroll`，这是因为ViewDragHelper内部使用了`Scroller`来处理view的滚动。
```java
@Override
public void computeScroll() {
    if (viewDragHelper.continueSettling(true)) {
        ViewCompat.postInvalidateOnAnimation(this); // 滚动还未停止继续刷新
    }
}
```
简单几步便可实现拖动的效果。

