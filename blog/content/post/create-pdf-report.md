---
title: Create PDF Report
author: Bill Farmer
type: post
date: 2018-09-18T19:27:46+01:00
categories:
    - Hacking
keywords:
    - wordpress
    - plugin
    - shortcode
    - tcpdf
---

### Introduction

As part of the [Questionnaire Maker][1] plugin it is necessary to
create a pdf report. The best tool to do this appears to be
[TCPDF][2], which appears to be part of at least some Linux
distributions as `php-tcpdf`. It is not included in WordPress.

### Implementation

As both the plugin and TCPDF are hosted on GitHub, the best way to
implement the plugin, and TCPDF if it is not available on the web
server, is to clone the repositories and use symbolic links to provide
the plugin and the library in the right places. The same method could
be used to implement WordPress.

```shell
user@server:~ $ git clone https://github.com/billthefarmer/questionnaire.git
user@server:~ $ cd /var/www/html/wordpress/wp-content/plugins/
user@server:/var/www/html/wordpress/wp-content/plugins $ ln -s ~/questionnaire/wordpress questionnaire_maker
user@server:/var/www/html/wordpress/wp-content/plugins $ cd ~
user@server:~ $ git clone https://github.com/tecnickcom/TCPDF.git tcpdf
user@server:~ $ cd questionnaire/wordpress
user@server:~/questionnaire/wordpress $ ln -s ~/tcpdf
```

The plugin includes code to load TCPDF if it is present and show a
message if it isn't. Using the path `tcpdf/tcpdf.php` will pick it up
either from `/usr/share/php`, if installed, or from the symbolic link.

```php
// Include TCPDF, if present
$tcpdf_present = include_once 'tcpdf/tcpdf.php';
```

After more work on this project I needed another library which makes
use of [composer][3] for dependencies. TCPDF doesn't have any, but you
can create a `composer.json` in the plugin and load TCPDF.

```shell
user@server:~/questionnaire/wordpress $ php composer.phar require tecnickcom/tcpdf
```

Using composer, the code to load libraries changes to pick them up
from the vendor folder.

```php
// Include composer libraries, if present
$vendor_present = include_once 'vendor/autoload.php'
```

### Displaying errors

To avoid the dreaded WordPress White Screen of Death (WSOD), you can
temporarily put a couple of lines at the top of the plugin to display
PHP errors.

```php
error_reporting(E_ALL);
ini_set('display_errors', 1);
```

### Creating the document

The code to create a pdf document is fairly straightforward. The
examples given always seem to specify everything, but by making note
of the defaults, that isn't necessary.

```php
        // Create document
        $pdf = new TCPDF();
        $pdf->setPageUnit('pt');

        // Remove default header/footer
        $pdf->setPrintHeader(false);
        $pdf->setPrintFooter(false);

        $margin = 72; // 1"
        $pageWidth = $pdf->getPageWidth();
        $pageHeight = $pdf->getPageHeight();
        $textWidth = $pageWidth - ($margin * 2);

        // Set margins
        $pdf->SetMargins($margin, $margin, $margin);
        $pdf->SetAutoPageBreak(true, $margin);
```

TCPDF doesn't seem to include a function to move the insertion point
down a line, you need to nest three functions to get the right result.
The functions to add text make use of the `MultiCell()` function. Also
the `SetFont()` function is used to set the font type.

```php
        // New line
        function new_text_line($pdf)
        {
            $pdf->Ln($pdf->getCellHeight($pdf->getFontSize()));
        };

        // Add text
        function add_text_object($pdf, $text, $forename = '', $lastname = '')
        {
            if (!empty($text->type))
                $pdf->SetFont('', $text->type);

            else
                $pdf->SetFont('', '');

            if (!empty($text->size))
                $pdf->SetFontSize($text->size);

            if (!empty($text->color))
                $pdf->SetTextColor($text->color);

            if (!empty($text->y))
                $pdf->SetY($text->y);

            $subst = str_replace(['~forename~', '~lastname~'],
                                      [$forename, $lastname], $text->text);

            $pdf->MultiCell(0, 0, $subst, 0, 'L');
        };

        // Add report entry
        function add_entry($pdf, $entry, $value)
        {
            $desc = $entry->desc;
            $type = $entry->$value->type;
            $text = $entry->$value->text;
            $image = $entry->$value->image;

            $y = $pdf->GetY();
            $pdf->Image($path . $image, $margin, $y, $textWidth, 0,
                        'png', '', 'N');

            $pdf->MultiCell(0, 0, $desc, 0, 'L');
            new_text_line($pdf);
            $pdf->SetFont('', 'B');
            $pdf->MultiCell(0, 0, $type, 0, 'L');
            new_text_line($pdf);
            $pdf->SetFont('', '');
            $pdf->MultiCell(0, 0, $text, 0, 'L');
            new_text_line($pdf);
        };
```

To display images requires a set of rules to place the image on the
page. If the width is empty or zero, text width, else width. If the
height is empty or zero, height in proportion to width, else
height. If x is empty or zero, left margin, if negative, right margin,
else x. If y is empty or zero, top margin, negative, bottom margin,
else y.

```php
        // Add image
        function add_image_object($pdf, $image, $margin, $textWidth,
                                  $pageHeight, $pageWidth, $path)
        {
            $width = (!empty($image->width))? $image->width: $textWidth;
            $height = (!empty($image->height))? $image->height: 0;
            $x = (!empty($image->x))? ($image->x < 0)?
               $pageWidth - $margin - $width: $image->x: $margin;
            $y = (!empty($image->y))? ($image->y < 0)?
               $pageHeight - $margin - $height: $image->y: $margin;
            $type = (!empty($image->type))? $image->type: '';
            $link = (!empty($image->link))? $image->link: '';
            $pdf->Image($path . $image->src, $x, $y, $width, $height,
                        $type, $link);
        };
```

The report starts with a preamble.

```php

        // Preamble
        foreach ($pages as $page)
        {            
            $pdf->AddPage();

            // Text
            foreach ($page->text as $text)
                add_text_object($pdf, $text, $forename, $lastname);

            // Images
            foreach ($page->images as $image)
                add_image_object($pdf, $image, $margin, $textWidth,
                                 $pageHeight, $pageWidth, $path);
        }
```

Then the report body. Because each section starts with an image, start
a new page if near the bottom.

```php

        // Report
        $pdf->AddPage();

        if ($B)
            add_entry($pdf, $answers->B, $B, $margin, $textWidth, $path);

        if ($C)
            add_entry($pdf, $answers->C, $C, $margin, $textWidth, $path);

        if ($pdf->GetY() >= $pageHeight - ($margin * 3))
            $pdf->AddPage();
    // ...
```

Then the last page.

```php

        $pdf->AddPage();

        // Last page text
        foreach ($last->text as $text)
            add_text_object($pdf, $text);

        // Last page images
        foreach ($last->images as $image)
            add_image_object($pdf, $image, $margin, $textWidth,
                             $pageHeight, $pageWidth, $path);
```

Output the document, and return the path to it.

```php

        // Output document
        $pdf->Output($path . 'report/' . $filename, 'F');

        // Return file uri
        return plugins_url('report/' . $filename, __FILE__);
```

[1]: https://billthefarmer.github.io/blog/post/questionnaire
 [2]: https://tcpdf.org
 [3]: https://getcomposer.org
