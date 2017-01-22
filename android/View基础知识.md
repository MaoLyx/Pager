#view的事件体系

>在Android 中View不是四四大组件之一，但是它有这非常重要的地位。Android体统中为开发者提供了多种多样的空间如：TextView,Buttom,ListView等等。但是在实际开发中可能还是不能满足所有的需求，所有必不可少的要自定义控件。在自定义控件之前要对View有一个清晰的理解所以在这里记录下View的笔记，方便以后查阅。


##View的基础知识
###什么是view
view是所有Andriod控件的基类。View是界面的一个抽象类，它代表一个控件。ViewGruop是View的子类，而它是LinearLayout及RelativeLayout等的基类。ViewGruop如命表示它一个控件组，它可以包含多个控件。
###View的坐标
View的坐标是由View的位置主要是他的四个属性来决定的如下图：

![view坐标与父容器的关系](https://github.com/MaoLyx/Pager/blob/master/android/image/view%E5%9D%90%E6%A0%87%E4%B8%8E%E7%88%B6%E5%AE%B9%E5%99%A8%E7%9A%84%E5%85%B3%E7%B3%BB.png?raw=true)

这样就很容易得出快高个View坐标的关系：`width=right-left` `height=bottom-top`

如何获取这几个属性呢？调用相应的get方法，如getRight();getLeft();等等。

###MotionEvent和Touchslop

*  **MotionEvent:**  手指接触屏幕后的一系列事件，常有的事件有:
		ACTION_DOWN//手指刚刚接触显示屏<br>
		ACTION_UP//手指抬起<br>
		ACTION_MOVE//手指在屏幕上移动<br>
	同时我们可以通过**MotionEvent**来获取点击事件发生的坐标x,y;通过getX,getY或getRawX，getRawY来获取；<br>
	getX和getY得到的是相对于控件左上角的坐标；<br>
	getRawX和getRawY得到的是相对于屏幕的的坐标；
*  **TouchSlop:**  这是一个常量，是系统能识别最小滑动距离

###VelocityTracker、GestureDetector和Scroller

- **VelocityTracker :**  翻译为：速度追踪器<br>
速度追踪器，用于追踪手指在滑动过程的速度，包括水平和竖直方向的速度。它的使用很简单，在View的onTouchEvnet方法中追踪当前单击的速度：<br>

		VelocityTracker velocityTracker = VelocityTracker.obtain();
		velocityTracker.addMovement(event);

接着我们要知道滑动速度时，这个时候用如下的方法来获得当前的速度：

	velocityTracker.computeCurrentVelocity(1000);
	int xVelocity =(int) velocityTracker.getXVelocity();
	int yVelocity =(int) velocityTracker.getYVelocity();
		
在这一步中有两点需要注意，第一点，获得速度之前必须要先计算速度，所以要先调用computeCurrentVeolocity方法；第二点，这里的速度是指一段时间内手指滑过的像素数；<br>`速度 = （终点位置-起点位置）/时间段`<br>
最后当不需要它时需要调用clear方法重置并回收内存：

	velocityTracker.clear();
	velocityTracker.recycle();
	

- GestureDetector
  
手势探测：手势探测用于检测用户的单击、滑动、长按等行为。使用GestureDetector的方法如下：<br>

	GestureDetector mGestureDetector = new GestureDetector(this);
	//解决长按屏幕后无法拖动的现象
	mGestureDetector.setIsLongPressEnabled(flase);
接着在目标的View的onTouchEvent方法中如下实现：

	boolean consume = mGestureDetector.onTouchEvent(event);
	return consume;
做完以上两步就可以选择性的实现OnGestrueListener和OnDoubleTapListener中的方法；

| 方法名 | 描述 | 所属接口 |
|:-----:|:---|:-------:|
|onDown | 手指轻触屏幕的一瞬间，有一个ACTION_DOWN触发|OnGestrueListener|
|onShowPress|手指亲触屏幕，尚未松开或拖动，由一个ACTION_DOWN触发<br>**强调的是没有松开和拖动的状态**|OnGestureListener|
|onSingleTapUp|手指（轻触屏幕后）松开，伴随一个ACTION_UP而触发，这是个单击行为|OnGestureListener|
|onScroll|手指按下屏幕并拖动，由一个ACTION\_DOWN和多个ACTION_MOVE触发|OnGestureListener|
|onLongPress|用户长按屏幕不放|OnGestureListure|
|onFling|用户按下后、快速滑动然后松开，由1个ACTION\_DOWN、多个ACTION\_MOVE和1个ACTION_UP触发|OnGestuerListenrt|
|onDoubleTap|双击，由2次连续的点击组成，它不可能和onSingleTapConfirmed共存|OnDoubleTapListener|
|onSingleTapConfirmed|严格的单击行为|OnDoubleTapListener|
|onDoubleTapEvnet|表示发生了双击行为，在双击的期间，ACTION_DOWN,ACTION\_MOVE和ACTION\_UP都会触发次回调|OnDoubleTapListener|

###Scroller

弹性滑动对象，用于View的弹性滑动。当使用View的scrollTo/scrollBy方法来进行滑动时，其过程是瞬间就完成的没有过渡效果。在这个情况下可以使用Scroller来实现这个过渡效果。Scroller本身无法让View弹性滑动，它需要和View的computeScroll方法配合才能共同完成这个功能。使用Scroller的基本方法如下：

	Scroller scroller = new Scroller(mContext);
	
	/**缓慢滚动到指定位置
	*param destX 目标坐标的X坐标
	*param destY 目标坐标的Y坐标
	*dest 是 destination 简写
	*/
	private void smoothScrollTo(int destX,int destY){
		int scrollX = getScrollX();
		int delta = destX - scrollX;
		//1000ms内滑向destX,效果是慢慢滑动
		mScroller.startScroll(scrollX,0,delta,0,1000);
		invalidate();
	}
	
	@override
	public void computeScroll(){
		if(mScroller.computeScrollOffset()){
			scrollTo(mSroller.getCurrX(),mScroller.getCurrY());
			postInvalidate();
		}
	}

##View的滑动
由于手机的屏幕有限，所以View需要将一些内容同通滑动来显示或隐藏；实现这个功能有三中方式：

1. 通过View本身的scrollTo或scrollBy方法实现滑动；
2. 通过动画来实现;
3. 通过改变View的LayoutParams使得View从新布局来实现滑动；

###使用scrollBy、scrollTo
View中提供了两个方法来实现滑动scrollTo、scrollBy来实现滑动的功能，源码如下：

	/**
     * Set the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the x position to scroll to
     * @param y the y position to scroll to
     */
    public void scrollTo(int x, int y) {
        if (mScrollX != x || mScrollY != y) {
            int oldX = mScrollX;
            int oldY = mScrollY;
            mScrollX = x;
            mScrollY = y;
            invalidateParentCaches();
            onScrollChanged(mScrollX, mScrollY, oldX, oldY);
            if (!awakenScrollBars()) {
                postInvalidateOnAnimation();
            }
        }
    }

    /**
     * Move the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the amount of pixels to scroll by horizontally
     * @param y the amount of pixels to scroll by vertically
     */
    public void scrollBy(int x, int y) {
        scrollTo(mScrollX + x, mScrollY + y);
    }
    
 scrollTo和ScrollBy只能改变View内容的位置而不能改变View在布局中的位置；在滑动的过程中我们要明白两个参数：mScrollX和mScrollY的改变，这两个参数可以通过getScrollX和getScrollY方法分别得到。下图可以形象的说明：
 
 !(mScrollY和mScrollX的变换规律示意)[]
	
###使用动画
通过动画我们能够让一个View进行平移，而平移就是一种滑动。使用动画来移动View,主要是操作View的TranslationX和TranslationY属性，即可以采用传统的View动画，也可以用属性动画，如果采用属性动画的话，为了能够兼容3.0以下的版本，需要使用开源动画库nineoldandriods([http:nineoldandroids.com](http:nineoldandroids.com))View动画的是对View的影像做操作，它并不能真正的改变View的位置参数，如果希望动画的状态得以保留必须将fillAfter属性设置为true，否则动画结束后动画结果会消失。

使用View动画并不能真正改变View的位置，当一个Button通过View动画向右移动100px,并且这个View设置有点击事件，之后你会发现点击事件无法触发onClick事件，而原来的位置任然可以触发onClick事件。因为不管Button怎么做变换，但是它的位置信息并不会随着动画而改变，因此在系统眼里，这个Button并没有发生任何改变，它的真身仍然在原始位置。

###改变布局参数
通过改变LayoutParams的方式去实现View的滑动。

	MarginLayoutParams params = （MarginLayoutParams）mButton.getLayoutParams();
	params.width += 100;
	params.leftMargin += 100;
	mButton.requestLayout();
	//或是mButton.setLayoutParams(params);

###三种滑动的比较
以上三种滑动的 差异为：

- scrollTo、scrollBy：操作简单，适合对View的内容滑动；
- 动画：操作简单适合没有交互和实现复杂的动画效果；
- 给变布局：操作复杂，适用于有交互的View；

##弹性滑动

###使用Scroller
Scroller的使用方法在上文提到了，下面是源码分析：

	Scroller scroller = new Scroller(mContext);
	
	/**缓慢滚动到指定位置
	*param destX 目标坐标的X坐标
	*param destY 目标坐标的Y坐标
	*dest 是 destination 简写
	*/
	private void smoothScrollTo(int destX,int destY){
		int scrollX = getScrollX();
		int delta = destX - scrollX;
		//1000ms内滑向destX,效果是慢慢滑动
		mScroller.startScroll(scrollX,0,delta,0,1000);
		invalidate();
	}
	
	@override
	public void computeScroll(){
		if(mScroller.computeScrollOffset()){
			scrollTo(mSroller.getCurrX(),mScroller.getCurrY());
			postInvalidate();
		}
	}
//135-0820-2647
这里的工作原理是：我们构造一个Scroller对象调用它的startScroll方法时，Scroller内部什么也没有做，它只是保存了传递进去的几个参数。如下：

	/**
     * Start scrolling by providing a starting point, the distance to travel,
     * and the duration of the scroll.
     * 
     * @param startX Starting horizontal scroll offset in pixels. Positive
     *        numbers will scroll the content to the left.
     * @param startY Starting vertical scroll offset in pixels. Positive numbers
     *        will scroll the content up.
     * @param dx Horizontal distance to travel. Positive numbers will scroll the
     *        content to the left.
     * @param dy Vertical distance to travel. Positive numbers will scroll the
     *        content up.
     * @param duration Duration of the scroll in milliseconds.
     */
    public void startScroll(int startX, int startY, int dx, int dy, int duration) {
        mMode = SCROLL_MODE;
        mFinished = false;
        mDuration = duration;
        mStartTime = AnimationUtils.currentAnimationTimeMillis();
        mStartX = startX;
        mStartY = startY;
        mFinalX = startX + dx;
        mFinalY = startY + dy;
        mDeltaX = dx;
        mDeltaY = dy;
        mDurationReciprocal = 1.0f / (float) mDuration;
    }
 
这里就说的很清楚了，startScroll方法只是保存了一些状态，所以仅仅调用这个方法是没有办法让View滑动起来的；所以在startScroll的下面调用invalidate()这个方法。这个方法会导致View调用draw方法重绘View在draw方法中，用调用computeScroll，computeScroll方法在View中是一个空实现，这里要自己实现；具体机制是，View重绘后再draw方法中调用computeScroll，computeScroll向Scroller获取当前的scrollX和scrollY，然后通过ScrollTo方法实现滑动，接着调用postInvalidate方法进行重绘并重复之前的动作；

下面是computeScrollOffset()方法的实现。

	 /**
     * Call this when you want to know the new location.  If it returns true,
     * the animation is not yet finished.
     */ 
    public boolean computeScrollOffset() {
        if (mFinished) {
            return false;
        }

        int timePassed = (int)(AnimationUtils.currentAnimationTimeMillis() - mStartTime);
    
        if (timePassed < mDuration) {
            switch (mMode) {
            case SCROLL_MODE:
                final float x = mInterpolator.getInterpolation(timePassed * mDurationReciprocal);
                mCurrX = mStartX + Math.round(x * mDeltaX);
                mCurrY = mStartY + Math.round(x * mDeltaY);
                break;
            case FLING_MODE:
                final float t = (float) timePassed / mDuration;
                final int index = (int) (NB_SAMPLES * t);
                float distanceCoef = 1.f;
                float velocityCoef = 0.f;
                if (index < NB_SAMPLES) {
                    final float t_inf = (float) index / NB_SAMPLES;
                    final float t_sup = (float) (index + 1) / NB_SAMPLES;
                    final float d_inf = SPLINE_POSITION[index];
                    final float d_sup = SPLINE_POSITION[index + 1];
                    velocityCoef = (d_sup - d_inf) / (t_sup - t_inf);
                    distanceCoef = d_inf + (t - t_inf) * velocityCoef;
                }

                mCurrVelocity = velocityCoef * mDistance / mDuration * 1000.0f;
                
                mCurrX = mStartX + Math.round(distanceCoef * (mFinalX - mStartX));
                // Pin to mMinX <= mCurrX <= mMaxX
                mCurrX = Math.min(mCurrX, mMaxX);
                mCurrX = Math.max(mCurrX, mMinX);
                
                mCurrY = mStartY + Math.round(distanceCoef * (mFinalY - mStartY));
                // Pin to mMinY <= mCurrY <= mMaxY
                mCurrY = Math.min(mCurrY, mMaxY);
                mCurrY = Math.max(mCurrY, mMinY);

                if (mCurrX == mFinalX && mCurrY == mFinalY) {
                    mFinished = true;
                }

                break;
            }
        }
        else {
            mCurrX = mFinalX;
            mCurrY = mFinalY;
            mFinished = true;
        }
        return true;
    }

实现具体不说，就返回值非常重要`Call this when you want to know the new location.  If it returns true,the animation is not yet finished.`这句简单的说就是但返回**true**的时候当前的动画还没有结束；


先写到这里View的事件分发、View的滑动冲突明后天在补上。