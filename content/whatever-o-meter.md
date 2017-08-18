---
title: Whatever-o-meter
author: Bill Farmer
type: page
date: 2013-11-20T18:55:54+00:00

---
## Introduction

Whatever-o-meter is a WordPress plugin that shows a tachometer-like dial with a pointer, asks a series of predefined questions which are answered by moving a slider, shows one of several predefined results, and moves the tacho pointer to a position determined by the value of the result. The project is on [Github](https://github.com/billthefarmer/whatever-o-meter").

## Documentation

Whatever-o-meter uses the shortcode [**whateverometer**] or [**whatever-o-meter**] to insert the whatever-o-meter display into the page. Other page content above and below the shortcode will appear on the page as normal. Do not use more than one whatever-o-meter shortcode on the page, only the first one is likely to work. If the shortcode is used on a page with no questions defined, the code will be replaced with the message:

    No whatever-o-meter questions defined, you need to define some custom fields.

I&#8217;ve called the parts of the page that slide up and down panels, because calling them divs, which is what they are, is too technical.

To define the initial intro panel, the questions, and the results you need to define quite a few custom fields on the page. They are:

  * **intro ** The initial text that will appear on the panel below the tacho dial and above the start button.
  * **question**  Each question has it&#8217;s own entry, as many as required within reason. They will be asked in the order given.
  * **left**  Each text has it&#8217;s own entry, as many as required. It will appear on the left below the slider. They will be displayed in the order given. There may be up to the same number of entries as questions. Optional, if not defined &#8216;No&#8217; will appear
  * **center ** Alternative spelling for them as can&#8217;t spell.
  * **centre ** Each text has it&#8217;s own entry, as many as required. It will appear in the centre below the slider. They will be displayed in the order given. There may be up to the same number of entries as questions. Optional, if not defined no centre text will appear. If an entry contains a single dash (-), no centre text will appear, allowing a centre text for following questions.
  * **right**  Each text has it&#8217;s own entry, as many as required. It will appear on the right below the slider. They will be displayed in the order given. There may be up to the same number of entries as questions. Optional, if not defined &#8216;Yes&#8217; will appear
  * **result**  Each result has it&#8217;s own entry, as many as required. The results will be distributed equally around the tacho dial. If the result text contains _%d%_, the result percentage will be substituted. If five results are used, they will correspond roughly with the tacho dial segments.
  * **weights**  A comma delimited list of weighting values for the questions. If provided, there may be up to the same number of values as questions. Optional, if not provided the weighting value for questions will default to 1.
  * **duration**  The duration of the animation in milliseconds. Optional, defaults to 2000. See [JQuery][1] docs.
  * **easing**  The easing function to be used for the dynamics of the tacho dial pointer. Optional, defaults to &#8216;easeOutQuad&#8217;. See [JQuery UI][2] docs.
  * **more**  Optional, if this field is defined a Find Out More button will appear on the results panel. The field should contain a valid URL.
  * **fb-appid ** Optional**,** if this field is defined a Facebook button will appear on the results panel which will show a Facebook feed dialog if clicked. The id must relate to an app defined on the [Facebook App Dashboard][3] for your site.
  * **fb-caption**  Optional, the caption you want to appear on the feed dialog. If not defined this will default to the page title.
  * **fb-description**  Optional, the description you want to appear in the feed dialog. If not defined, this will default to the result text. If the description contains _%d%_, the result percentage will be substituted. If the description contains %s, the whole result text will be substituted.
  * **fb-picture ** Optional, the picture you want to appear in the feed dialog. If not defined, Facebook will substitute an image from your site.
  * **addthis ** Optional, If this variable id set to _above_ and the [AddThis plugin][4] is installed, an Addthis toolbox with standard Facebook, Twitter, Pinterest, Google+ and share buttons will appear above the whatever-o-meter. If it is set to _below_, the toolbox will appear below.
  * ** fb-like**  Optional, if this is set to _above_ standard Facebook like and share buttons will appear above the whatever-o-meter. If it is set to _below_, the buttons will appear below. The like and share buttons are a standard Facebook plugin and will not use information from the fields above.
  * **colors**  Alternative spelling for them as can&#8217;t spell.
  * **colours ** The colours you would like to appear in the dial and buttons. Optional, should be a comma delimited list of five colours. The colours are the dial background colour, ticks, digits,  pointer, buttons, any colour format in the [CSS standard][5] should work: #abc, #abcdef, goldenrod, rgb(r,g,b). Defaults to palegoldenrod, black, black, black, palegoldenrod.
  * **debug ** Causes the plugin to show the values of the custom fields below the whatever-o-meter for debugging if defined.

## Specification

The whatever-o-meter initial display will show the tacho dial with the initial panel with the intro text and the start button below. Any other content on the page will appear above and below.

When the start button is clicked the initial panel will slide up and a panel with first question on it, a slider, and a next button will appear. The slider is moved or clicked and when the next button is clicked the next question will appear in the same manner. This will continue until all the questions have been asked and  answered

The result panel will slide up showing the result text and an again button. The tacho dial pointer will rotate dynamically to a position indicating the value of the result. The tacho dial od-o-meter will be updated with the value of the result. If the again button is clicked the result panel will slide down and the initial intro panel will appear. The tacho dial pointer will rotate back to the rest position and the od-o-meter set back to zero. If the Facebook button is clicked, and the **fb-appid** custom field is defined, a Facebook feed dialog will be popped up by Facebook.

 [1]: http://api.jquery.com/animate
 [2]: http://api.jqueryui.com/easings
 [3]: https://developers.facebook.com/apps
 [4]: http://wordpress.org/plugins/addthis
 [5]: http://www.w3.org/TR/css3-color/#svg-color
