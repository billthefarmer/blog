---
title: Resolving content uris in android
author: Bill Farmer
type: post
date: 2017-07-23T10:48:17+00:00
url: /resolving-content-uris-in-android/
categories:
  - Hacking

---
If you write an android app that handles images or any other sort of file your app may be required to deal with &#8216;content&#8217; uris (`content://`). There is a very useful utility which resolves these into &#8216;file&#8217; uris (`file:///`) [FileUtils.java][1]. This contains one external reference to [LocalStorageProvider][2], which can be resolved by replacing the reference with a string.

<pre>
    // import com.ianhanniballake.localstorage.LocalStorageProvider;
    // ...
        /**
         * @param uri The Uri to check.
         * @return Whether the Uri authority is {@link LocalStorageProvider}.
         * @author paulburke
         */
        public static boolean isLocalStorageDocument(Uri uri) {
            // return LocalStorageProvider.AUTHORITY.equals(uri.getAuthority());
            return "com.ianhanniballake.localstorage.documents"
                .equals(uri.getAuthority());
    
        }
</pre>

Alternatively, you could just comment out this function and references to it. Having done that, you can then resolve &#8216;content&#8217; uris

<pre>
    public final static String CONTENT = "content";
    // ...
            if (uri.getScheme().equalsIgnoreCase(CONTENT))
                uri = resolveContent(uri);
    // ...
        // resolveContent
        private Uri resolveContent(Uri uri)
        {
            String path = FileUtils.getPath(this, uri);
            if (path != null)
            {
                File file = new File(path);
                if (file.canRead())
                    uri = Uri.fromFile(file);
            }
            return uri;
        }
</pre>

This may not always work, but I haven&#8217;t been able to defeat it.

 [1]: https://github.com/iPaulPro/aFileChooser/blob/master/aFileChooser/src/com/ipaulpro/afilechooser/utils/FileUtils.java
 [2]: https://github.com/iPaulPro/aFileChooser/blob/master/aFileChooser/src/com/ianhanniballake/localstorage/LocalStorageProvider.java
