---
title: Android Recent Files
author: Bill Farmer
type: post
date: 2017-12-15T11:53:57Z
url: /android-recent-files/
categories:
  - Hacking
keywords:
  - android
  - recent
  - files
---

There are several parts to this - creating the list of files, saving
the list in the preferences, restoring the list on startup and
creating the list in the menu.

```java

    // onCreate
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);

        SharedPreferences preferences =
            PreferenceManager.getDefaultSharedPreferences(this);

        // Create a map of scroll positions by file name
        Set<String> pathSet = preferences.getStringSet(PREF_PATHS, null);
        pathMap = new HashMap<String, Integer>();

        if (pathSet != null)
            for (String path : pathSet)
                pathMap.put(path, preferences.getInt(path, 0));

        removeList = new ArrayList<String>();

        // ...
    }
```

The android preference system only allows a list of strings to be
stored as a set, so the last modified date of each file is used below
to order the list. The code also saves the scroll position of each
file using the file name as a key. The remove list is used to remove
old entries.

```java

    // savePath
    private void savePath(String path)
    {
        // Save the current position
        pathMap.put(path, scrollView.getScrollY());

        // Get a list of files
        List<Long> list = new ArrayList<Long>();
        Map<Long, String> map = new HashMap<Long, String>();
        for (String name : pathMap.keySet())
        {
            File file = new File(name);
            list.add(file.lastModified());
            map.put(file.lastModified(), name);
        }

        // Sort in reverse order
        Collections.sort(list);
        Collections.reverse(list);

        int count = 0;
        for (long date : list)
        {
            String name = map.get(date);

            // Remove old files
            if (count >= MAX_PATHS)
            {
                pathMap.remove(name);
                removeList.add(name);
            }

            count++;
        }
    }
```

The list of file names is ordered by the last modified date and the
old ones are added to the remove list.

```java

    // onPause
    @Override
    public void onPause ()
    {
        super.onPause();

        SharedPreferences preferences =
            PreferenceManager.getDefaultSharedPreferences(this);
        SharedPreferences.Editor editor = preferences.edit();

        // ...

        // Add the set of recent files
        editor.putStringSet(PREF_PATHS, pathMap.keySet());

        // Add a position for each file
        for (String path : pathMap.keySet())
            editor.putInt(path, pathMap.get(path));

        // Remove the old ones
        for (String path : removeList)
            editor.remove(path);

        editor.apply();

        // ...
   }
```
