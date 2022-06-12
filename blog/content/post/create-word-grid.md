---
title: Create a Word Grid
author: Bill Farmer
type: post
date: 2022-06-11T10:55:18+01:00
url: /create-word-grid/
categories:
    - Hacking
keywords:
    - java
    - android
    - word
    - grid
---

A [popular online puzzle game][1] involves solving a five character
word grid in the least number of moves. There are probably several
ways of algorithmically generating word grids, [Donald Knuth's Dancing
Links][2] paper mentions generating a word grid. I decided to use
brute force and a randomised word list.

This uses a large number of lists and iterators and can generate a
large number of word grids. This algorithm is used in my [Gridle][3]
[android app][4].

|   | 1 | 2 | 3 | 4 | 5 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| 1 | b | r | a | w | l |
| 2 | e |   | l |   | a |
| 3 | g | h | o | s | t |
| 4 | u |   | f |   | c |
| 5 | n | o | t | c | h |

First the list of five character words.
```java

    public static final String WORDS[] =
    {
        "aback","abase","abate","abbey","abbot","abhor","abide",
        "abled","abode","abort","about","above","abuse","abyss",
        // ...
        "wrote","wrung","wryly","yacht","yearn","yeast","yield",
        "young","youth","zebra","zesty","zonal"
    };
```

The lists for the randomised word list and the alphabetised word
lists and iterators.
```java
    private static char grid[][];

    private static List<String> wordList;

    private static List<String> aList;
    private static List<String> bList;
    // ...
    private static List<String> yList;
    private static List<String> zList;

    private static Iterator<String> aIterator;
    private static Iterator<String> bIterator;
    // ...
    private static Iterator<String> yIterator;
    private static Iterator<String> zIterator;

    private static Set<String> usedSet;

    private static Random random;
```

Initialise the random number generator, word list and randomise it.
```java
        random = new Random(new Date().getTime());

        wordList = new ArrayList<String>(Arrays.asList(WORDS));

        randomise(wordList);
```

Randomise function.
```java
    private static void randomise(List<String> list)
    {
        int index = 0;
        for (String w: list)
        {
            int r = random.nextInt(list.size());
            list.set(index++, list.get(r));
            list.set(r, w);
        }
    }
```

Create and initialise alphabetical word lists.
```java
        aList = new ArrayList<String>();
        bList = new ArrayList<String>();
        // ...
        yList = new ArrayList<String>();
        zList = new ArrayList<String>();

        for (String word: wordList)
        {
            switch (word.charAt(0))
            {
            case 'a': aList.add(word); break;
            case 'b': bList.add(word); break;
            // ...
            case 'y': yList.add(word); break;
            case 'z': zList.add(word); break;
            }
        }
```

Create char array, iterate through word list and initialise iterators
and set of used words.
```java
        grid = new char[5][5];

        for (String word: wordList)
        {
            aIterator = aList.iterator();
            bIterator = bList.iterator();
            // ...
            yIterator = yList.iterator();
            zIterator = zList.iterator();

            usedSet = new HashSet<String>();
            usedSet.add(word);
```

This is the algorithm, make the current word 1 down, get a word
beginning with the first character of the current word and make it 1
across. Get a word beginning with the third character and make it 3
across and get a word beginning with the last character and make it 5
across. At each stage start again with the next word if we run out of
words.
```java
            String s = getWord(word.charAt(0));
            if (s == null)
                continue;
            grid[0] = s.toCharArray();
            grid[1] = new char[5];
            Arrays.fill(grid[1], ' ');
            s = getWord(word.charAt(2));
            if (s == null)
                continue;
            grid[2] = s.toCharArray();
            grid[3] = new char[5];
            Arrays.fill(grid[3], ' ');
            s = getWord(word.charAt(4));
            if (s == null)
                continue;
            grid[4] = s.toCharArray();

            grid[1][0] = word.charAt(1);
            grid[3][0] = word.charAt(3);
```

The `getWord(char)` function, it won't return a word that has already
been used, and fails if the iterator runs out of words.
```java
    private static String getWord(char c)
    {
        Iterator<String> iterator = getIterator(c);
        while (iterator.hasNext())
        {
            String word = iterator.next();
            if (usedSet.contains(word))
                continue;

            usedSet.add(word);
            return word;
        }

        return null;
    }
```

Now, find two words for 3 down and 5 down, they have to match three
characters, the first, third and last. Start again with the next word
if we run out of words.
```java

            word = getWord(grid[0][2], grid[2][2], grid[4][2]);
            if (word == null)
                continue;

            grid[1][2] = word.charAt(1);
            grid[3][2] = word.charAt(3);

            word = getWord(grid[0][4], grid[2][4], grid[4][4]);
            if (word == null)
                continue;

            grid[1][4] = word.charAt(1);
            grid[3][4] = word.charAt(3);
```

The `getWord(char, char, char)` and `getIterator(char)` functions.
```java
    private static String getWord(char a, char b, char c)
    {
        Iterator<String> iterator = getIterator(a);
        while (iterator.hasNext())
        {
            String word = iterator.next();
            if (usedSet.contains(word))
                continue;
            if (word.charAt(2) == b &&
                word.charAt(4) == c)
            {
                usedSet.add(word);
                return word;
            }
        }

        return null;
    }

    private static Iterator<String> getIterator(char c)
    {
        switch (c)
        {
        case 'a': return aIterator;
        case 'b': return bIterator;
        // ...
        case 'y': return yIterator;
        case 'z': return zIterator;
        }

        return null;
    }
```

The algorithm may be extended to generate seven character grids of words.

|  | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 1 | l | u | s | t | i | e | r |
| 2 | e |   | a |   | c |   | e |
| 3 | v | a | l | l | e | y | s |
| 4 | e |   | t |   | f |   | i |
| 5 | r | e | p | o | r | t | s |
| 6 | e |   | a |   | e |   | t |
| 7 | t | e | n | t | e | r | s |


 [1]: https://wafflegame.net/
 [2]: https://en.wikipedia.org/wiki/Dancing_Links
 [3]: https://github.com/billthefarmer/gridle
 [4]: https://f-droid.org/packages/org.billthefarmer.gridle/
