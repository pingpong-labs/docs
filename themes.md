---
title: Laravel 5 Themes
---

# Laravel 5 Themes

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
  - [Get Theme's Config](#get-theme-config)
- [Artisan Commands](#artisan-commands)
  - [theme:make](#theme-make-command)
  - [theme:list](#theme-list-command)
  - [theme:cache](#theme-cache-command)
  - [theme:publish](#theme-publish-command)

<a name="installation"></a>

## Installation

Open your composer.json file, and add the new required package.

```
"pingpong/themes": "~2.1"
```

Next, open a terminal and run.

```
composer update
```

Next, Add new service provider in `config/app.php`.

```php
'Pingpong\Themes\ThemesServiceProvider',
```

Next, Add new aliases in `config/app.php`.

```php
'Theme' => 'Pingpong\Themes\ThemeFacade',
```

Next, publish the package's assets.

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

<a name="get-theme-config"></a>
Get theme's config value from active theme.

```php
Theme::config('filename.key');

Theme::config('filename.key.subkey');

Theme::config('filename.key.subkey', 'default value here');
```

If your theme's config file named `config.php`, you can get the value of config little bit short.

```php
Theme::config('key');

Theme::config('key.subkey');
```

You can also get config value from other theme.

```php
// current theme
Theme::config('key');
// from other theme
Theme::config('bootstrap::key');
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
Publish all theme's assets from all themes.

```
php artisan theme:publish
```

Publish all theme's assets from the specified theme.

```
php artisan theme:publish theme-name
```
