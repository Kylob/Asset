# use BootPress\Asset\Component as Asset;

[![Packagist][badge-version]][link-packagist]
[![License MIT][badge-license]](LICENSE.md)
[![HHVM Tested][badge-hhvm]][link-travis]
[![PHP 7 Supported][badge-php]][link-travis]
[![Build Status][badge-travis]][link-travis]
[![Code Climate][badge-code-climate]][link-code-climate]
[![Test Coverage][badge-coverage]][link-coverage]

Asset::cached() is a one-stop method for all of your asset caching needs.  This should be the first thing that you call.  It checks to see if the page is looking for a cached asset.  If it is, then it will return a response that you can ``$page->send()``.  If not, then just continue on your merry way.  When you ``$page->display()`` your html, it will look for all of your assets, and convert them to cached urls.

- If an asset is found we give it a unique (5 character) id that then becomes the "folder", and we add the ``basename()`` to the end for reference / seo sakes.
  - http://example.com/page/dir/bootstrap.css will become http://example.com/...../bootstrap.css where 'bootstrap.css' means nothing, and ..... is the actual asset location.
  - 60 alphanumeric characters (no 0's) ^ 5 (character length) gives 777,600,000 possible combinations.
- If a #fragment is located immediately after the asset, we'll remove the fragment and ...
  - If it is a .css or .js file then we will combine them together so that http://example.com/page/dir/bootstrap.css#../default.css#user/custom.css will become http://example.com/.....0.....0...../bootstrap-default-custom.css and we'll minify and serve the /page/dir/bootstrap.css, /page/default.css, and /page/dir/user/custom.css files all at once.
  - Otherwise we'll replace the name with it ie. http://example.com/page/dir/image.jpg#seo will become http://example.com/...../seo.jpg
- If you add a query string to images, we'll remove and save it with the filename ie. http://example.com/page/dir/image.jpg?w=150#seo will become http://example.com/...../seo.jpg only ..... will be different from the previous example, and the image.jpg's width will be 150 pixels.
  - To see all of the options here, check out the [Glide Quick Reference](http://glide.thephpleague.com/1.0/api/quick-reference/) guide.
- The ``filemtime()`` is saved so that when an asset changes, we can give it a new unique filename that the browser will then come looking for and cache all over again.
  - This allows us to tell browsers to never come looking for the asset again, because it will never change.
  - There is no better way to make your pages load any faster than this.
     

## Installation

Add the following to your ``composer.json`` file.

``` bash
{
    "require ": {
        "bootpress/asset": "^1.0"
    }
}
```

## Example Usage

``` php
<?php
use BootPress\Page\Component as Page;
use BootPress\Asset\Component as Asset;

$page = Page::html();
if ($asset = Asset::cached('assets')) {
    $page->send($asset);
}

$html = $page->display('<p>Content</p>');
$page->send(Asset::dispatch('html', $html));
```

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.

[badge-version]: https://img.shields.io/packagist/v/bootpress/asset.svg?style=flat-square&label=Packagist
[badge-license]: https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square
[badge-hhvm]: https://img.shields.io/badge/HHVM-Tested-8892bf.svg?style=flat-square
[badge-php]: https://img.shields.io/badge/PHP%207-Supported-8892bf.svg?style=flat-square
[badge-travis]: https://img.shields.io/travis/Kylob/Asset/master.svg?style=flat-square
[badge-code-climate]: https://img.shields.io/codeclimate/github/Kylob/Asset.svg?style=flat-square
[badge-coverage]: https://img.shields.io/codeclimate/coverage/github/Kylob/Asset.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/bootpress/asset
[link-travis]: https://travis-ci.org/Kylob/Asset
[link-code-climate]: https://codeclimate.com/github/Kylob/Asset
[link-coverage]: https://codeclimate.com/github/Kylob/Asset/coverage
