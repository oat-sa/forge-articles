<!--
created_at: '2013-09-20 08:16:25'
updated_at: '2017-05-11 08:30:00'
authors:
    - 'Vijai Pandey'
contributors:
    - 'Somsack Sipasseuth'
    - 'Jérôme Bogaerts'
    - 'Cyril Hazotte'
    - 'Antoine Robin'
    - 'Jean-Sébastien Conan'
tags:
    - 'Frontend'
    - 'Library'
    - 'JavaScript'
    - 'Math'
-->

# Enable math expression in items

![logo](../resources/third-party/badge-square.png)

> MathJAX is a library for rendering Math expressions within a web page.
> It supports both MathML and LaTex expressions.

## Install MathJax library, using the script

For users with a Unix machine, you can use the following bash scripts to download and install MathJax.

- TAO 2.5: [mathjaxdl_tao25.sh](../resources/third-party/mathjaxdl_tao25.sh)
- TAO 2.6: [mathjaxdl_tao26.sh](../resources/third-party/mathjaxdl_tao26.sh)
- TAO 3.0: [MathJax_Install_TAO_3x.sh](../resources/third-party/MathJax_Install_TAO_3x.sh)

Be sure to have `wget` (or `curl` with 3.0 version), `tar` and `gzip` installed and available in your `PATH`. Then just launch it (as a user, not root) from the root of your TAO distribution.

## Install MathJax library manually

MathML is not rendered natively in all browsers. [Firefox and Safari are among the only one that render it natively](http://caniuse.com/#feat=mathml). To ensure a consistent cross-browser experience in TAO, we rely on a third-party MathML library called [MathJax](http://www.mathjax.org/). Because of license compatibility issue, it cannot be included within the default TAO package. That is why you need to install this library separately to enable math expression in your items.

You can download it freely [here](http://docs.mathjax.org/en/latest/installation.html#obtaining-mathjax-via-an-archive) and unzip it into the following folder: `{YOUR_TAO_ROOT}/taoQtiItem/views/js/mathjax`. Your file system should look like the enclosed screenshot below.

## Shrink MathJax

MathJax is a huge lib which weighs above 20MB unzipped. It is recommended to shrink it so only required files remain to display MathML properly in TAO. Indeed, on compilation, every compiled item containing MathML would have the whole library copied. So using the instruction below can help reducing its size down to 4.2MB. This solution has been tested so math expressions are still correctly rendered on most used browsers: Firefox, Chrome, Safari and IE8.

First delete the following folders:

- mathjax/docs/
- mathjax/test/
- mathjax/unpacked/
- mathjax/fonts/HTML-CSS/TeX/otf/
- mathjax/fonts/HTML-CSS/TeX/svg/

Then in the `mathjax/config/` folder, delete all files but `TeX-AMS-MML_HTMLorMML-full.js`, which is the only config file used in TAO.

An already shrunk Mathjax archive is available [here](../resources/third-party/mathjax-shrunk.zip).

## Keep MathJax sources untouched

To keep the full MathJax package, just add the `—keep-full` option when the script is called.


