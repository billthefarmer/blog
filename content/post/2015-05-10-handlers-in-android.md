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

  1. The main activity implements <a href="http://developer.android.com/reference/android/os/Handler.Callback.html" target="_blank">Handler.Callback</a>, and has a
  
    <a href="http://developer.android.com/reference/android/os/Handler.Callback.html#handleMessage%28android.os.Message%29" target="_blank">handleMessage</a> function.
  2. When the worker thread is created, include a reference to the main activity.
  3. In the worker thread constructor, create a <a href="http://developer.android.com/reference/android/os/Handler.html" target="_blank">Handler</a>, including the reference to the main activity.
  4. When some results need passing back, send a <a href="http://developer.android.com/reference/android/os/Message.html" target="_blank">Message</a> back to the main activity using the handler.

    
    public class MyActivity extends <a href="http://developer.android.com/reference/android/app/Activity.html" target="_blank">Activity</a> implements <a href="http://developer.android.com/reference/android/os/Handler.Callback.html" target="_blank">Handler.Callback</a>
    {
        //...
        // On create
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            //...
            WorkerTHread worker = new WorkerThread(this);
        //...
        public boolean <a href="http://developer.android.com/reference/android/os/Handler.Callback.html#handleMessage%28android.os.Message%29">handleMessage</a>((<a href="http://developer.android.com/reference/android/os/Message.html">Message</a> msg)
        {
        //...
    public class WorkerThread extends Thread
    {
        <a href="http://developer.android.com/reference/android/os/Handler.html" target="_blank">Handler</a> handler;
        //...
        // Constructor
        public WorkerThread(Handler.Callback callback)
        {
            handler = new <a href="http://developer.android.com/reference/android/os/Handler.html#Handler%28android.os.Handler.Callback%29" target="_blank">Handler</a>(callback);
            //...
        private void someFunction()
        {
            handler.<a href="http://developer.android.com/reference/android/os/Handler.html#sendEmptyMessage%28int%29" target="_blank">sendEmptyMessage</a>(WHATEVER);
            // or
            <a href="http://developer.android.com/reference/android/os/Message.html" target="_blank">Message</a> msg = handler.<a href="http://developer.android.com/reference/android/os/Handler.html#obtainMessage%28int,%20java.lang.Object%29" target="_blank">obtainMessage</a>(WHATEVER, thingy);
            handler.<a href="http://developer.android.com/reference/android/os/Handler.html#sendMessage%28android.os.Message%29" target="_blank">sendMessage</a>(msg);
            //...
    

The handler will send the message back to the activity in the UI thread, so you can do whatever with the contents.