#View的事件分发机制

###事件的传递规则
点击事件的分发，其实就是对MotionEvent事件的分发过程。当一个MotionEvent产生以后，系统需要把这个事件传递给一个具体的View，这个传递的过程就是分发过程。点击事件的分发过程是由三个很重要方法来共同完成：dispatchTouchEvet、onInterceptTouchEvnet和onTouchEvent。

**public boolean dispatchTouchEvent(MotionEvent ev)**

用来进行事件分发。如果事件能够传递给当前View，那么此方法一定被调用，返回结果受当前View的onTouchEvent和下级View的dispatchTouchEvent方法的影响，表示是否消耗当前事件。

**public boolean onInterceptTouchEvent(MotionEvent event)**<br>
在dispatchTouchEvent方法内部调用，用来判断是否拦截某个事件，如果当前的View拦截了某个事件，那么在同一个事件序列当中，此方法不会被再次调用，返回结果表示是否拦截当前事件。<br>

**public boolean onTouchEvent(MotionEvnet event)**<br>
在dispatchTouchEvent方法中调用，用来处理点击事件，返回结果表示是否消耗当前事件，如果不消耗，则在同一个事件序列中，当前View无法再次接收到事件。

	public boolean dispatchTouchEvent(MotionEvent event){
		boolean consume = false;
		if(onInterceptTouchEvent(event)){
			consume = onTouchEvent(event);
		}else{
			consume = child.dispatchTouchEvent(event);
		}
		return consume;
	}
上述伪碼将他们三个的关系基本上描述的很清楚了。对于一个根Viewgroup来说，事件产生后，事件首先会传递给他，这是dispatchTouchEvent就会调用，如果这个ViewGroup的onInterceptTouchEvent方法返回true就表示它要拦截当前事件，接着事件就交给这个Viewgroup来处理，即调用onTouchEvent方法就被调用处理事件；如果这个ViewGroup的onInterceptTouchEvent方法返回false就表示不拦截交给其子View的处理；

当一个View需要处理事件时，如果它设置了onTouchListener中的onTouch方法会被调用。这时事件如何处理还要看onTouch的返回值，如果返回false，则当前的View的onTouchEvent方法会被调用。如果返回true，那么onTouchEvent的方法就不会被调用。

事件传递的顺序是：Activity-->Window-->View

