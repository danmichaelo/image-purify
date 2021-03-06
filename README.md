<p align="center">
<a href="https://travis-ci.org/despark/image-purify"><img src="https://travis-ci.org/despark/image-purify.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/despark/image-purify"><img src="https://poser.pugx.org/despark/image-purify/v/stable" alt="Latest Stable Version" style="max-width:100%;"></a>
<a href="https://packagist.org/packages/despark/image-purify"><img src="https://poser.pugx.org/despark/image-purify/downloads" alt="Total Downloads" style="max-width:100%;"></a>
<a href="https://packagist.org/packages/despark/image-purify"><img src="https://poser.pugx.org/despark/image-purify/license" alt="License" style="max-width:100%;"></a>
</p>

## About Image Purifier
Image optimization library inspired by [psliwa/image-optimizer](https://github.com/psliwa/image-optimizer).
It allows processing image paths by automatically detecting the file type and feeding it to certain chain.

## Libraries
Currently we support the following server side libraries for optimization. You must install them on your server in order to use this library.
Of you want to add more you can do so by using your own command.
- [mozjpeg](https://github.com/mozilla/mozjpeg)
- [pngquant](https://pngquant.org/)
- [optipng](http://optipng.sourceforge.net/)
- [giflossy](https://github.com/pornel/giflossy)*

<em>\*Giflossy is a fork of [gifsicle](https://www.lcdf.org/gifsicle/) so you can use the main library by excluding the --lossy argument [(see options)](#factory-options)</em>

## Exmple Usage
Initialize the purifier with sensible defaults
```php
$options = [];
$purifierFactory = new ImagePurifierFactory($options, $logger);

$purifier = $purifierFactory->create();
$purifier->purify('path/to/image.png');
```

The `ImagePurifierFactory` takes two optional arguments `$options` and `$logger`.

#### Factory Options 
This is an anatomy of an factory option
```php
[
// This is the chain config. You can define your own chain and feed it commands
'chains' => [
    JpegChain::class => [ // Chains must be real classes which contains commands to be executed
        'commands' => [
            'mozJpeg' => [
                'bin' => 'cjpeg', // Path to the executable. The lbrary tries to resolve it itself
                'arguments' => ['-optimize', '-progressive'], // What arguments to run
                'customClass' => MozJpeg::class, // If you want you can give custom class that must implements our CommandInterface
            ],
        ],
        'first_only' => false, // If this is true only the first command will be executed
    ],
    ...
],
'suppress_errors' => false // If set to true exceptions will be catched and only logs will be written,
]);
```

#### Logger
You can pass custom logger to the `ImagePurifierFactory` which must implement `Psr\Log\LoggerInterface`

## Good to know
Pngquant will exit with code `98` if the file was already optimized (at least this is what we experienced on tests).
This is for you to handle if the errors are not suppressed. If this happens we throw CommandException with special code `98`
*In general it is a good idea to make sure files are optimized only once.
Example:
```php
try{
    $purifier->purify($filePath);
}catch(CommandException $e){
    if($e->getCode() === 98){
        // handle png quant already processed
    }
}
```

## Advanced usage
To be done

## Tests and coverage
You can run the tests by executing 
```bash
composer test
```

We aim at 100% test coverage.
