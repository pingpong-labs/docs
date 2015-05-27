Laravel Menus
========================

- [Installation](#installation)
- [Creating A Menu](#creating-a-menu)
- [Setting up Menu Presenter](#setting-up-menu-presenter)
- [Rendering Menu to The View](#rendering-menu-to-the-view)

<a name="installation"></a>
## Installation

You can install the through composer command line.

```
composer require pingpong/menus
```

After that, open the file `app/config/app.php` and add a new service provider in `providers` array.
```php
   
	'providers' => array(

		'Illuminate\Foundation\Providers\ArtisanServiceProvider',
		'Illuminate\Auth\AuthServiceProvider',		
		...
		'Illuminate\View\ViewServiceProvider',
		'Illuminate\Workbench\WorkbenchServiceProvider',
		
		// here
		'Pingpong\Menus\MenusServiceProvider'

	),
```
Then add the class alias in `aliases`.
```php

	'aliases' => array(

		'App'             => 'Illuminate\Support\Facades\App',
		'Artisan'         => 'Illuminate\Support\Facades\Artisan',		
		...		
		'Validator'       => 'Illuminate\Support\Facades\Validator',
		'View'            => 'Illuminate\Support\Facades\View',
		
		// here
		'Menu'          =>  'Pingpong\Menus\MenuFacade',
	)
```
Then, publish configuration by running:
```
php artisan vendor:publish
```

<a name="creating-a-menu"></a>
## Creating A Menu

You can define your menus in `app/Support/menus.php` file. That file will loaded automatically by this package.

To create a menu, simply call the `create` method from `Menu` facade.

```php
Menu::create('navbar', function($menu)
{
    // using array
	$menu->add([
		'route'	=>	'home',
		'title'	=>	'Home',
		'icon'  =>  'fa fa-dashboard'
	]);

	// menu with target to url
	$menu->url('/', 'Home');

	// with additional attributes
	$menu->url('/', 'Home', ['class' => 'nav-link']);

	// menu with target to registered route
	$menu->route('home', 'Home');

	// with additional route parameters and attributes
	$menu->route('home', 'Home', null, ['class' => 'nav-link']);

	$menu->route('users.show', Auth::user()->name, Auth::id(), ['class' => 'nav-link']);

	$menu->route('users.show', 'My Profile', ['username' => 'gravitano'], ['class' => 'nav-link']);

	$menu->route('products.show', 'View Product', 1, ['class' => 'nav-link']);

	// dropdown menu
	$menu->dropdown('Settings', function($sub)
	{
	    $sub->url('settings/account', 'Account');
	    $sub->route('settings.profile', 'Profile');
	    $sub->route('logout', 'Logout');
	});

	// multi level menu (nested)
	$menu->dropdown('Category', function($sub)
	{
	    $sub->url('category/programming', 'Programming');

	    $sub->url('category/screencasts', 'Screencasts');

	    $sub->dropdown('Sport News', function($sub)
	    {
	        $sub->url('category/football', 'Football');
	        $sub->url('category/basket-ball', 'Basket Ball');
	    });

	    $sub->dropdown('Title', function($sub)
	    {
	        $sub->url('link', 'Link');
	        $sub->dropdown('Title', function($sub)
	        {
	            $sub->dropdown('Title N', function($sub)
	            {
	                // more nested menu here
	            });
	        });
	    });
	});
});
````

**Make Lots of menu**

This package allows you to create a menu with a lot of different styles. Here's an example.

```php
Menu::create('menu1', function($menu)
{

	$menu->route('home', 'Home');

	$menu->url('profile', 'Profile');
});

Menu::create('menu2', function($menu)
{
	$menu->route('home', 'Home');

	$menu->url('profile', 'Profile');
});
```

<a name="rendering-a-menu"></a>
### Rendering A Menu

To call up the menu you can use `render` or `get` method.

```php
Menu::render('navbar');

Menu::get('menu1');

Menu::get('menu2');
```

**Menu Style.**

By default the generated menu style is bootstrap navbar. In addition there are also several different menu styles and is already available by default are `navbar`, `navbar-right`, `nav-pills` and `nav-tab`. To set the style menu you can use the method `style`. Examples like this.

```php
Menu::create('navbar', function($menu)
{
	$menu->style('nav-pills');

	$menu->route('home', 'Home');

	$menu->url('profile', 'Profile');
});
```

**Make A Costum Presenter**

You can create your own presenter class. Make sure your presenter is extends to `Pingpong\Menus\Presenters\Presenter`, that class is also `implements` to 'Pingpong\Menus\Presenters\PresenterInterface'. For example this is zurb topbar presenter. 

```php

use Pingpong\Menus\Presenters\Presenter;

class ZurbTopBarPresenter extends Presenter
{
	/**
	 * {@inheritdoc }
	 */
	public function getOpenTagWrapper()
	{
		return  PHP_EOL . '<section class="top-bar-section">' . PHP_EOL;
	}

	/**
	 * {@inheritdoc }
	 */
	public function getCloseTagWrapper()
	{
		return  PHP_EOL . '</section>' . PHP_EOL;
	}

	/**
	 * {@inheritdoc }
	 */
	public function getMenuWithoutDropdownWrapper($item)
	{
		return '<li'.$this->getActiveState($item).'><a href="'. $item->getUrl() .'">'.$item->getIcon().' '.$item->title.'</a></li>';
	}

	/**
	 * {@inheritdoc }
	 */
	public function getActiveState($item)
	{
		return \Request::is($item->getRequest()) ? ' class="active"' : null;
	}

	/**
	 * {@inheritdoc }
	 */
	public function getDividerWrapper()
	{
		return '<li class="divider"></li>';
	}

	/**
	 * {@inheritdoc }
	 */
	public function getMenuWithDropDownWrapper($item)
	{
		return '<li class="has-dropdown">
		        <a href="#">
		         '.$item->getIcon().' '.$item->title.'
		        </a>
		        <ul class="dropdown">
		          '.$this->getChildMenuItems($item).'
		        </ul>
		      </li>' . PHP_EOL;
		;
	}
}

```
For use costum presenter, you can use the `setPresenter` method, for example like this.
```php
Menu::create('zurb-top-bar', function($menu)
{
	$menu->setPresenter('ZurbTopBarPresenter');

	$menu->route('home', 'Home');

	$menu->url('profile', 'Profile');
});
```

Or you can set it at the time of calling the menu, like this.

```php
Menu::render('zurb-top-bar', 'ZurbTopBarPresenter');

Menu::get('zurb-top-bar', 'ZurbTopBarPresenter');
```

**Register A New Style Menu**

This Style is like an alias to a presenter. You can register your style from your costum presenter in the configuration file in  `app/config/packages/pingpong/menus/config.php`. Like this.

```php
return array(
	'navbar'		=>	'Pingpong\Menus\Presenters\Bootstrap\NavbarPresenter',
	'navbar-right'	=>	'Pingpong\Menus\Presenters\Bootstrap\NavbarRightPresenter',
	'nav-pills'		=>	'Pingpong\Menus\Presenters\Bootstrap\NavPillsPresenter',
	'nav-tab'		=>	'Pingpong\Menus\Presenters\Bootstrap\NavTabPresenter',

	'zurb-top-bar'	=>	'ZurbTopBarPresenter',
);
```

Then you can use a style like this. Same as section **Menu Style** above.

```php
Menu::create('zurb-top-bar', function($menu)
{
	$menu->style('zurb-top-bar');
	$menu->add([
		'route'	=>	'home',
		'title'	=>	'Home',
	]);
	$menu->url('profile', 'Profile');

	$menu->route('settings', 'Settings');
});
```

**Get The Menu Instance**

To get an instance of an existing menu, you can use the `instance` method. Here's an example.

```php
$menu = Menu::instance('zurb-top-bar');

// You can also make additions to the menu again

$menu->add(['title' => 'Settings', 'route' => 'settings']);

$menu->url('profile', 'Profile');

$menu->route('settings', 'Settings');
```