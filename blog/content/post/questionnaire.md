---
title: Questionnaire Maker
author: Bill Farmer
type: post
date: 2018-08-20T17:14:32+01:00
categories:
    - Hacking
keywords:
    - hugo
    - wordpress
    - plugin
    - shortcode
---

### Introduction

Questionnaire Maker is a WordPress and Hugo plugin that asks a series
of questions and produces a PDF report. The project is on [Github][1].

### Documentation

The plugin uses the shortcodes `[questionnaire-questions]` and
`[questionnaire-report]`, or `{{</* questionnaire-questions */>}}` and
`{{</* questionnaire-report */>}}`. The shortcode will be replaced on the
page with a series of panels containing the questions and options for
the answers, or a panel with a preview of the report.

The plugin requires a large amount of structured data, currently
encoded as a Javascript object.

#### Question data
```javascript
let data =
    {intro: "Please answer the following questions as carefully and accurately as you can",
     questions:
     [{q: "I prefer curry with",
       t: "B",
       a: ["Papadums",
           "Chango Mutney"],
       v: [2, 4]},...],
     last:
     {q: "Where do you get your curry?",
      a: ["Make my own",
          "Ready meals",
          "Take away",
          "Indian restaurant",
          "Wetherspoons"],
      t: "S",
      v: [2, 4, 6, 8, 10]},
     matrix:
     {B: ["Introspective",
          "Unimaginative",
          "Extrovertive",
          "Innovative",
          "Explorative"],...}
    };
```

The fields are:

 * **intro** - Introductory text
 * **questions** - A series of questions with fields:
   * **q** - The question text
   * **t** - The type of question
   * **a** - The options for answers
   * **v** - Array of values for answers
 * **last** - The last question
 * **matrix** - Object containing possible texts for each type of question

The intro text appears on the first panel together with a start
button. The questions will appear on subsequent panels. The last
question triggers a contact panel which prompts for name and email
address.

#### Report data
```javascript
let data =
    {pages:
     [{pageno: 1,
       images:
       [{src: 'images/Header.png',
         type: 'png'},
        {src: 'images/Footer.png',
         type: 'png',
         y: -1}],
       text:
       [{text: "~forename~ ~lastname~",
         size: 24,
         type: 'bold',
         y: 532}]},
      {pageno: 2,
       images:
       [{src: 'images/Mugshot.jpg',
         type: 'jpeg',
         x: -1,
         width: 225}],
       text:
       [{text: "How to read your report",
         size: 18,
         type: 'bold'},
        {text: "Dear ~forename~",
         size: 12,
         type: 'normal',
         y: 340},...]},...],
     last:
     {images:
      [{src: 'images/Image.jpg',
        type: 'jpeg',
        y: 156,
        link: 'https://github.com/billthefarmer/questionnaire'}],
     text:
     [{text: "Note",
       type: 'bold'},
      {text: "Text paragraph",
       type: 'normal'}],
      [{text: "Copyright (c) 2018 Bill Farmer  All rights reserved",
        size: 12,
        color: 150,
        y: 496}]},
     answers:
     {B:
      {desc: "Description...",
       12:
       {type: "Type",
        text: "Paragraph of text..."},...}
    }};
```

The fields are:

 * **pages** - Preamble pages with images, with fields:
   * **pageno** - Page number
   * **images** - Array of images with fields:
     * **src** - Path to image file
     * **type** - Type of image, png or jpeg
     * **y** - Y coordinate, if -1 bottom edge, optional
     * **x** - X coordinate, if -1 right edge, optional
     * **height** - height of image, optional
     * **width** - width of image, optional. If width and no height,
       image will be scaled in proportion
     * **link** - URL of link for image
   * **text** - Text for page with fields:
      **size** - Font size in points, optional
      **type** - Type of font, optional
      **color** - Text colour, optional
      **y** Y coordinate, optional
 * **last** - Last page with images and text, same fields, no pageno
 * **answers** Paragraphs of text for the report after the preamble
   with fields:
   * **Type** - Type of answer with fields:
     * **desc** - Description of type
     * **Score** - Value of score for that type with fields:
       * **type** - Description of result
       * **text** - Paragraph of explanatory text
 
The report can contain several pages of preamble with images and
text. Images with no coordinates or dimensions will be placed full
width at the top of the page. Images with a `y` coordinate of `-1`
will be placed full width at the bottom of the page. Images with an
`x` coordinate of `-1` will be placed at the right margin of the
page. All coordinates, sizes and dimensions are in points, 1/72
inch. The fields from the contact panel may be included in text by
using `~forename~` and `~lastname~`.

The description and text for each type for each score will appear on
subsequent pages. The images and text for the last page will appear
after the results.

 [1]: https://github.com/billthefarmer/questionnaire
