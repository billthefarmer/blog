---
title: Handlers in Android
author: Bill Farmer
type: post
date: 2015-05-10T15:51:40+00:00
url: /handlers-in-android/
categories:
  - Hacking

---
### Using a handler to return results from a worker thread

This has taken me quite a while to put together. There are throw-away remarks in the Android docs about this, but no useful examples. You want to get the results back from a worker thread to your main activity so you can do stuff with them. There are several parts to this.

  1. The main activity implements [Handler.Callback][1], and has a [handleMessage][2] function.
  2. When the worker thread is created, include a reference to the main activity.
  3. In the worker thread constructor, create a [Handler][3], including the reference to the main activity.
  4. When some results need passing back, send a [Message][4] back to the main activity using the handler.

```java
    public class MyActivity extends Activity implements Handler.Callback
    {
        //...
        // On create
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            //...
            WorkerThread worker = new WorkerThread(this);
        //...
        public boolean handleMessage(Message msg)
        {
        //...
    public class WorkerThread extends Thread
    {
        Handler handler;
        //...
        // Constructor
        public WorkerThread(Handler.Callback callback)
        {
            handler = new Handler(callback);
            //...
        private void someFunction()
        {
            handler.sendEmptyMessage(WHATEVER);
            // or
            Message msg = handler.obtainMessage(WHATEVER, thingy);
            handler.sendMessage(msg);
            //...
```

The handler will send the message back to the activity in the UI thread, so you can do whatever with the contents.

 [1]: http://developer.android.com/reference/android/os/Handler.Callback.html
 [2]: http://developer.android.com/reference/android/os/Handler.Callback.html#handleMessage%28android.os.Message%29
 [3]: http://developer.android.com/reference/android/os/Handler.html
 [4]: http://developer.android.com/reference/android/os/Message.html
