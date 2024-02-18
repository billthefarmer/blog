---
title: Flutter app bar search widget
author: Bill Farmer
type: post
date: 2024-02-06T08:37:04Z
url: /flutter-search-widget/
categories:
    - Hacking
keywords:
    - flutter
    - android
    - dart
---

As an exercise I decided to reimplement my [Buses][1] app in
flutter. To do this I need an open street map library, a beautiful
soup library, a location library, an OS ref library, and a search
widget. All of these are available in [pub.dev][2], except a suitable
search widget. All implementations of [SearchBar][3] in the docs include an
annoying dropdown suggestions feature, which appears to be mandatory.

![BusApp](images/2024/02/BusApp.png)

However it is possible to take the SearchBar and include it in an
[AppBar][4], change the icons and end up with something which looks
and acts very similarly to an android search widget. To do this, add
code to the actions of the app bar which either shows an icon button
or the search bar, controlled by a boolean which is set by pressing
the search button. The search bar is wrapped by an [Expanded][5]
widget to stop it overflowing the display. The search bar has a
leading button which dismisses it, a conditional trailing button to
clear the search field and a button to do the search.

```dart
class BusApp extends StatefulWidget {
  const BusApp({super.key});
  @override
  _BusAppState createState() => _BusAppState();

class _BusAppState extends State<BusApp> {
  const controller = TextEditingController();
  bool searching = false;
  bool empty = true;

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    var converter = LatLongConverter();
    return Scaffold(
      appBar: AppBar(
        title: Text('BusApp'),
        actions: [
          if (searching)
          Expanded(
            child: SearchBar(
              controller: controller,
              leading: IconButton(
                icon: const Icon(Icons.arrow_back),
                onPressed: () {
                  setState(() =>
                    searching = false
                  );
                }
              ),
              trailing: [
                if (!empty)
                IconButton(
                  icon: const Icon(Icons.close),
                  onPressed: () {
                    controller.clear();
                  }
                ),
                IconButton(
                  icon: const Icon(Icons.arrow_forward_ios),
                  onPressed: () {
                    setState(() =>
                      searching = false
                    );
                  }
                ),
              ],
              hintText: 'Search…',
              onChanged: (value) {
                setState(() =>
                  empty = value.isEmpty
                );
              },
              onSubmitted: (value) {
                setState(() =>
                  searching = false
                );
                doSearch(value);
              },
            )
          )
          else
          IconButton(
            icon: const Icon(Icons.search),
            onPressed: () {
              setState(() =>
                searching = true
              );
            }
          )
        ],
      ),
      body: // …
    );
  }
}
```

This seems to work quite well, although the app can be a bit slow
starting on an old phone.

 [1]: https://github.com/billthefarmer/buses
 [2]: https://pub.dev/
 [3]: https://api.flutter.dev/flutter/material/SearchBar-class.html
 [4]: https://api.flutter.dev/flutter/material/AppBar-class.html
 [5]: https://api.flutter.dev/flutter/widgets/Expanded-class.html
