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
user@server:~ $ cd questionnaire
user@server:~/questionnaire $ ln -s ~/tcpdf
```

The plugin includes code to load TCPDF if it is present and show a
message if it isn't. Using the path `tcpdf/tcpdf.php` will pick it up
either from `/usr/share/php`, if installed, or from the symbolic link.

```php
// Include TCPDF, if present
$tcpdf_present = include_once 'tcpdf/tcpdf.php';
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
        function add_text_object($pdf, $text, $forename, $lastname)
        {
            if ($text->type)
                $pdf->SetFont('', $text->type);

            else
                $pdf->SetFont('', '');

            if ($text->size)
                $pdf->SetFontSize($text->size);

            if ($text->color)
                $pdf->SetTextColor($text->color);

            if ($text->y)
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

            $pdf->SetFont('', 'B');
            $pdf->MultiCell(0, 0, $type, 0, 'L');
            new_text_line($pdf);
            $pdf->SetFont('', '');
            $pdf->MultiCell(0, 0, $desc, 0, 'L');
            new_text_line($pdf);
            $pdf->MultiCell(0, 0, $text, 0, 'L');
            new_text_line($pdf);
        };
```

 [1]: https://billthefarmer.github.io/blog/post/questionnaire
 [2]: https://tcpdf.org
