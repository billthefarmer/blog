---
title: Android Text Search
author: Bill Farmer
type: post
date: 2017-10-26T11:57:27+01:00
url: /android-text-search/
categories:
  - Hacking
keywords:
  - text
  - search
  - android
---

The Android guide for [Search Overview][1] is oriented towards
searching external content rather than text. There is a mention of
[Using the Search Dialog][2]. However all the bits you need are
there. To get a search dialog in the toolbar that pops up when needed,
you need a seach item in the menu. It should be the first item, unless
there is a good reason otherwise.

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu
    xmlns:android="http://schemas.android.com/apk/res/android"
  <item
      android:id="@+id/search"
      android:showAsAction="ifRoom|collapseActionView"
      android:title="@string/search"
      android:actionViewClass="android.widget.SearchView"
      android:icon="@drawable/ic_action_search" />
```

The search dialog will still pop up if the search item is in the menu
rather than the toolbar. The dialog pops up and collapses all by
itself in response to user interaction without any developer code. To
implement the search, you need an [OnQueryTextListener][3], which is
called by the [SearchView][4].

```java
    // onCreateOptionsMenu
    @Override
    public boolean onCreateOptionsMenu(Menu menu)
    {
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.main, menu);

        searchItem = menu.findItem(R.id.search);
        searchView = (SearchView) searchItem.getActionView();

        if (searchView != null)
        {
            searchView.setSubmitButtonEnabled(true);
            searchView.setImeOptions(EditorInfo.IME_ACTION_GO);
            searchView.setOnQueryTextListener(new QueryTextListener());
        }

        return true;
    }
```

Get an extra submit button in the toolbar, change the keyboard button
to a right arrow, and set the listener

### Interactive Case Insensitive Search

```java
    // QueryTextListener
    private class QueryTextListener
        implements SearchView.OnQueryTextListener
    {
        private BackgroundColorSpan span = new
            BackgroundColorSpan(Color.YELLOW);
        private Editable editable;
        private Pattern pattern;
        private Matcher matcher;
        private String text;
        private int index;
        private int height;

        // onQueryTextChange
        @Override
        @SuppressWarnings("deprecation")
        public boolean onQueryTextChange (String newText)
        {
            // Use web view functionality
            if (shown)
                markdownView.findAll(newText);

            // Use regex search and spannable for highlighting
            else
            {
                height = scrollView.getHeight();
                editable = textView.getEditableText();
                text = textView.getText().toString();

                // Reset the index and clear highlighting
                if (newText.length() == 0)
                {
                    index = 0;
                    editable.removeSpan(span);
                }

                // Get pattern
                pattern = Pattern.compile(newText,
                                          Pattern.CASE_INSENSITIVE |
                                          Pattern.LITERAL |
                                          Pattern.UNICODE_CASE);
                // Find text
                matcher = pattern.matcher(text);
                if (matcher.find(index))
                {
                    // Get index
                    index = matcher.start();

                    // Get text position
                    int line = textView.getLayout()
                        .getLineForOffset(index);
                    int pos = textView.getLayout()
                        .getLineBaseline(line);

                    // Scroll to it
                    scrollView.scrollTo(0, pos - height / 2);

                    // Highlight it
                    editable
                        .setSpan(span, index, index +
                                 newText.length(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
                }
            }

            return true;
        }

        // onQueryTextSubmit
        @Override
        public boolean onQueryTextSubmit (String query)
        {
            // Use web view functionality
            if (shown)
                markdownView.findNext(true);

            // Use regex search and spannable for highlighting
            else
            {
                // Find next text
                if (matcher.find())
                {
                    // Get index
                    index = matcher.start();

                    // Get text position
                    int line = textView.getLayout()
                        .getLineForOffset(index);
                    int pos = textView.getLayout()
                        .getLineBaseline(line);

                    // Scroll to it
                    scrollView.scrollTo(0, pos - height / 2);

                    // Highlight it
                    editable
                        .setSpan(span, index, index +
                                 query.length(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
                }

                // Reset matcher
                if (matcher.hitEnd())
                    matcher.reset();
            }

            return true;
        }
    }
```

This implementation searches both a [WebView][5], and a
[TextView][6]. The search is interactive, the highlighting and scroll
position moves as the user types. The search button on the keyboard
provides the 'find next' function. The web view search functionality
is built in, all the developer has to do is call the relevant methods,
[findAll()][8] and [findNext()][7]. The method `findAll()` is
deprecated as of android 16, but this code is supporting android 14,
which is the reason for the `@SuppressWarnings("deprecation")`.

To provide nearly identical functionality in a text view requires the
use of regular expression search, and highlighting using a spannable
char sequence. The pattern uses the `CASE_INSENSITIVE`, `LITERAL` and
`UNICODE_CASE` flags so the search is case insensitive. The current
search index is provided to the matcher `find()` method so the search
stays in position if the user deletes search text, duplicating web
view functionality.

### Interactive Regular Expression Search

```java
    // QueryTextListener
    private class QueryTextListener
        implements SearchView.OnQueryTextListener
    {
        private BackgroundColorSpan span = new
            BackgroundColorSpan(Color.YELLOW);
        private Editable editable;
        private Matcher matcher;
        private Pattern pattern;
        private String text;
        private int index;
        private int height;

        // onQueryTextChange
        @Override
        @SuppressWarnings("deprecation")
        public boolean onQueryTextChange (String newText)
        {
            // Use regex search and spannable for highlighting
            height = scrollView.getHeight();
            editable = textView.getEditableText();
            text = textView.getText().toString();

            // Check text
            if (text.length() == 0)
                return false;

            // Reset the index and clear highlighting
            if (newText.length() == 0)
            {
                index = 0;
                editable.removeSpan(span);
                return false;
            }

            // Check pattern
            try
            {
                pattern = Pattern.compile(newText, Pattern.MULTILINE);
                matcher = pattern.matcher(text);
            }

            catch (Exception e)
            {
                return false;
            }

            // Find text
            if (matcher.find(index))
            {
                // Get index
                index = matcher.start();

                // Check layout
                if (textView.getLayout() == null)
                    return false;

                // Get text position
                int line = textView.getLayout()
                    .getLineForOffset(index);
                int pos = textView.getLayout()
                    .getLineBaseline(line);

                // Scroll to it
                scrollView.scrollTo(0, pos - height / 2);

                // Highlight it
                editable
                    .setSpan(span, matcher.start(), matcher.end(),
                             Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            else
                index = 0;

            return true;
        }

        // onQueryTextSubmit
        @Override
        public boolean onQueryTextSubmit (String query)
        {
            // Find next text
            if (matcher.find())
            {
                // Get index
                index = matcher.start();

                // Get text position
                int line = textView.getLayout()
                    .getLineForOffset(index);
                int pos = textView.getLayout()
                    .getLineBaseline(line);

                // Scroll to it
                scrollView.scrollTo(0, pos - height / 2);

                // Highlight it
                editable
                    .setSpan(span, matcher.start(), matcher.end(),
                             Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            else
            {
                matcher.reset();
                index = 0;
            }

            return true;
        }
    }
```

The regular expression may become invalid as the user types, so the
`Pattern.compile()` statement is wrapped with a `try` statement to
trap any exceptions. The `matcher.find(index)` statement with an index
always searches from the index, the `matcher.find()` statement without
the index does 'find next'. The check of the text view layout is to
deal with a null pointer exception when the user loads a file. The
`matcher.reset()` statement makes the search begin again from the
start.

```java
    // onOptionsItemSelected
    @Override
    public boolean onOptionsItemSelected(MenuItem item)
    {
        switch (item.getItemId())
        {
        // ...

        default:
            return super.onOptionsItemSelected(item);
        }

        // Close text search
        if (searchItem.isActionViewExpanded())
            searchItem.collapseActionView();

        return true;
    }
```

If the highlight isn't removed when the user does something via the
menu, like loading another file, it won't go away, so it is removed
before that happens. Collapsing the search view clears the search
text, so the callback removes the highlight.

### Find text in files

```java

    // findAll
    public void findAll()
    {
        // Get search string
        final String search = searchView.getQuery().toString();

        Pattern pattern = Pattern.compile(search,
                                          Pattern.CASE_INSENSITIVE |
                                          Pattern.LITERAL |
                                          Pattern.UNICODE_CASE);

        // Get entry list
        List<Calendar> entries = getEntries();

        // Create a list of matches
        List<String> matches = new ArrayList<String>();

        // Check the entries
        for (Calendar entry: entries)
        {
            File file = getDay(entry.get(Calendar.YEAR),
                               entry.get(Calendar.MONTH),
                               entry.get(Calendar.DATE));

            Matcher matcher = pattern.matcher(read(file));
            if (matcher.find())
                matches.add(DateFormat.getDateInstance(DateFormat.MEDIUM)
                         .format(entry.getTime()));
        }

        // If found pop up a dialog
        if (!matches.isEmpty())
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.setTitle(R.string.findAll);
            final String[] choices = matches.toArray(new String[0]);
            builder.setItems(choices, new DialogInterface.OnClickListener()
                {
                    public void onClick (DialogInterface dialog, 
                                         int which)
                    {
                        String choice = choices[which];
                        DateFormat format =
                            DateFormat.getDateInstance(DateFormat.MEDIUM);

                        // Get the entry chosen
                        try
                        {
                            Date date = format.parse(choice);
                            Calendar entry = Calendar.getInstance();
                            entry.setTime(date);
                            changeDate(entry);

                            // Put the search text back - why it
                            // disappears I have no idea or why I have
                            // to do it after a delay
                            searchView.postDelayed(new Runnable()
                                {
                                    // run
                                    @Override
                                    public void run()
                                    {
                                        searchView.setQuery(search, false);
                                    }
                                }, FIND_DELAY);
                        }

                        catch (Exception e) {}
                    }
                });
            builder.setNegativeButton(android.R.string.cancel, null);
            builder.show();
        }
    }
```

In this application the method `getEntries()` returns a list of
`Calendar` objects corresponding to stored files. The method
`getDay()` returns a `File` object for the file corresponding to that
date and the method `read()` returns the text in the file. The code
builds a list of strings corresponding the the matching files to
present to the user.

If the list isn't empty an `AlertDialog` is built to display the
list. The `OnClickListener` parses the date and changes the app date
to the selected entry. For reasons best known to whoever wrote the
android `SearchView` code, the search text disappears while all this
is going on, so it is put back after a delay, otherwise it doesn't
work.

[1]: https://developer.android.com/guide/topics/search/index.html
 [2]: https://developer.android.com/guide/topics/search/search-dialog.html#SearchDialog
 [3]: https://developer.android.com/reference/android/widget/SearchView.OnQueryTextListener.html
 [4]: https://developer.android.com/reference/android/widget/SearchView.html
 [5]: https://developer.android.com/reference/android/webkit/WebView.html
 [6]: https://developer.android.com/reference/android/widget/TextView.html
 [7]: https://developer.android.com/reference/android/webkit/WebView.html#findNext(boolean)
 [8]: https://developer.android.com/reference/android/webkit/WebView.html#findAll(java.lang.String)
 
