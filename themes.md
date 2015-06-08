Laravel 5 Themes
===============

- [Installation](#installation)
- [Usage](#usage)
  - [Get All Themes](#get-all-themes)
  - [Set Active Theme](#set-active-theme)
  - [Get Current Theme](#get-current-theme)
  - [Check Theme](#check-theme)
  - [Set Themes Path](#set-themes-path)
  - [Get Themes Path](#get-themes-path)
  - [Get Theme's Path](#get-theme-path)
  - [Get Theme's View](#get-theme-view)
  - [Get Theme's Lang](#get-theme-lang)
- [Artisan Commands](#artisan-commands)
  - [theme:make](#theme-make-command)
  - [theme:list](#theme-list-command)
  - [theme:cache](#theme-cache-command)
  - [theme:publish](#theme-publish-command)

<a name="installation"></a>
## Installation

Open your composer.json file, and add the new required package.
```
   "pingpong/themes": "~2.0"
```
Next, open a terminal and run.
```
composer update
```

Next, Add new service provider in `config/app.php`.

```php
  'Pingpong\Themes\ThemesServiceProvider',
```

Next, Add new aliases in `app/config/app.php`.

```php
   'Theme' => 'Pingpong\Themes\ThemeFacade',
```

Next, publish the asset. The asset is an example theme.
```
php artisan vendor:publish
```

Done.

<a name="usage"></a>
## Usage

<a name="get-all-themes"></a>
Get all themes.
```php
Theme::all();
```

<a name="set-active-theme"></a>
Set theme active.
```php
Theme::set('default');

Theme::setCurrent('default');
```

<a name="get-current-theme"></a>
Get current theme active.
```php
Theme::getCurrent();
```

<a name="check-theme"></a>
Check theme.
```php
Theme::has('simple')

Theme::exists('other-theme');
```

<a name="set-themes-path"></a>
Set theme path.
```php
$path = public_path('themes');

Theme::setPath($path);
```

<a name="get-theme-path"></a>
Get theme path.
```php
Theme::getThemePath('default');
```

<a name="get-themes-path"></a>
Get themes path.
```php
Theme::getPath();
```

<a name="get-theme-view"></a>
Get view from current active theme.
```php
Theme::view('index');

Theme::view('folders.view');
```

<a name="get-theme-lang"></a>
Get translation value from active theme.
```php
Theme::lang('group.name');
```


<a name="artisan-commands"></a>
## Artisan Commands

<a name="theme-make-command"></a>
Generate a new theme.
```
php artisan theme:make foo
```

<a name="theme-list-command"></a>
Show all available themes.
```
php artisan theme:list
```

<a name="theme-cache-command"></a>
Cache all themes.
```
php artisan theme:cache
```

<a name="theme-publish-command"></a>
Publish all theme's assets.
```
php artisan theme:publish
```

Publish all theme's assets from the specified theme.

```
php artisan theme:publish theme-name
```
