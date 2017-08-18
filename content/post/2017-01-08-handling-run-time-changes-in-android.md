---
title: Handling run time changes in Android
author: Bill Farmer
type: post
date: 2017-01-08T18:49:31+00:00
url: /handling-run-time-changes-in-android/
categories:
  - Hacking

---
Two articles, [Handling Runtime Changes][1] and [Two articles, [Handling Runtime Changes][1] and][2] recommend the use of fragments to retain data and background tasks in Android during the life of an app. There are two problems with this:

  * An android fragment is quite a complex heavyweight object to use just for retaining data and possibly a background task, and the fragment management is not simple.
  * Fragments are not retained if the associated activity is discarded by calling finish().

A simpler and less complex solution is to use a static singleton, mentioned here in a different context: [Setting Up a RequestQueue][3]. This is quite simple to implement, like this:

    
    // Activity
        // On create
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
    
    	// Get data instance
    	instance = Data.getInstance(this);
            // ...
    
        // On resume
        @Override
        protected void onResume()
        {
            super.onResume();
    
    	// Connect callbacks
    	instance = Data.getInstance(this);
     
            // Check data instance
            if (instance != null)
            {
                // Get the saved select list
                List list = instance.getList();
                // ...
    
                // Get the saved value map
                valueMap = instance.getMap();
            }
        // ...
    
        // On pause
        @Override
        protected void onPause()
        {
            super.onPause();
            // ...
    
            // Save the select list and value map in the data instance
            if (instance != null)
            {
                instance.setList(selectList);
                instance.setMap(valueMap);
            }
    
    	// Disconnect callbacks
    	instance = Data.getInstance(null);
        }
    
    // Data singleton
    
    // Data class
    public class Data
    {
        private static Data instance;
    
        private Map<String, Double> map;
        private List list;
    
        private TaskCallbacks callbacks;
    
    
        // Constructor
        private Data()
        {
        }
    
        // Get instance
        public static Data getInstance(TaskCallbacks callbacks)
        {
            if (instance == null)
                instance = new Data();
    
            instance.callbacks = callbacks;
            return instance;
        }
    
        // Set list
        public void setList(List list)
        {
            this.list = list;
        }
    
        // Get list
        public List getList()
        {
            return list;
        }
    
        // ...
        // Start parse task
        protected void startParseTask(String url)
        {
            ParseTask parseTask = new ParseTask();
            parseTask.execute(url);
        }
    
        // ParseTask class
        protected class ParseTask
            extends AsyncTask<String, String, Map<String, Double>>
        {
        // ...
        // TaskCallbacks interface
        interface TaskCallbacks
        {
            void onProgressUpdate(String... date);
            void onPostExecute(Map<String, Double> map);
        }
    }
    

The data instance will be retained while the app is running and can be used to avoid expensive operations like downloading data more than once.

 [1]: https://developer.android.com/guide/topics/resources/runtime-changes.html
 [2]: http://www.androiddesignpatterns.com/2013/04/retaining-objects-across-config-changes.html
 [3]: https://developer.android.com/training/volley/requestqueue.html