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
<pre>
    public class MyActivity extends [Activity][5] implements [Handler.Callback][1]
    {
        //...
        // On create
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            //...
            WorkerThread worker = new WorkerThread(this);
        //...
        public boolean [handleMessage][2]([Message][4] msg)
        {
        //...
    public class WorkerThread extends Thread
    {
        [Handler][3] handler;
        //...
        // Constructor
        public WorkerThread(Handler.Callback callback)
        {
            handler = new [Handler][6](callback);
            //...
        private void someFunction()
        {
            handler.[sendEmptyMessage][7](WHATEVER);
            // or
            [Message][4] msg = handler.[obtainMessage][8](WHATEVER, thingy);
            handler.[sendMessage][9](msg);
            //...
</pre>
The handler will send the message back to the activity in the UI thread, so you can do whatever with the contents.

 [1]: http://developer.android.com/reference/android/os/Handler.Callback.html
 [2]: http://developer.android.com/reference/android/os/Handler.Callback.html#handleMessage%28android.os.Message%29
 [3]: http://developer.android.com/reference/android/os/Handler.html
 [4]: http://developer.android.com/reference/android/os/Message.html
 [5]: http://developer.android.com/reference/android/app/Activity.html
 [6]: http://developer.android.com/reference/android/os/Handler.html#Handler%28android.os.Handler.Callback%29
 [7]: http://developer.android.com/reference/android/os/Handler.html#sendEmptyMessage%28int%29
 [8]: http://developer.android.com/reference/android/os/Handler.html#obtainMessage%28int,%20java.lang.Object%29
 [9]: http://developer.android.com/reference/android/os/Handler.html#sendMessage%28android.os.Message%29
