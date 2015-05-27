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

After the package installed, add a new service provider to the `providers` array in `config/app.php` file.

```php
'providers' => array(
	'Pingpong\Menus\MenusServiceProvider'
),
```

Add new alias for `Menu` facade to the `aliases` array at the same file.

```php
'aliases' => array(
	'Menu' => 'Pingpong\Menus\MenuFacade',
)
```

Then, publish configuration by running:

```
php artisan vendor:publish
```

<a name="creating-a-menu"></a>
## Creating A Menu

You can define your menus in `app/Support/menus.php` file. That file will loaded automatically by this package.

To create a menu, simply call the `create` method from `Menu` facade. The first parameter is the menu name and the second parameter is callback for defining menu items.

```php
Menu::create('navbar', function($menu)
{
	// define your menu items here
});
````

**Defining Menu Items**

As explained before, we can defining menu item in the callback by accessing `$menu` variable, which the variable is instance of `Pingpong\Menus\MenuBuilder` class.

To defining a plain URL, you can use `->url()` method.
```php
Menu::create('navbar', function($menu)
{
              // URL , Title,  Attributes
	$menu->url('home', 'Home', ['target' => 'blank']);
});
```

If you have named route, you define the menu item by calling `->route()` method.
```php
Menu::create('navbar', function($menu)
{
	$menu->route(
		'users.show', // route name
		'View Profile', // title
		['id' => 1], // route parameters
		['target' => 'blank'] // attributes
	);
});
```

You can also defining the menu item via array by calling `->add()` method.
```php
Menu::create('navbar', function($menu)
{
	$menu->add([
		'url' => 'about',
		'title' => 'About',
		'attributes' => [
			'target' => '_blank'
		]
	]);

	$menu->add([
		'route' => ['profile', ['user' => 'gravitano']],
		'title' => 'Visit My Profile',
		'attributes' => [
			'target' => '_blank'
		]
	]);
});
```

**Menu Dropdown**

To create a dropdown menu, you can call to `->dropdown()` method and passing the first parameter by title of dropdown and the second parameter by closure callback that retrive `$sub` variable. The `$sub` variable is the the instance of `Pingpong\Menus\MenuItem` class.

```
Menu::create('navbar', function($menu)
{
	$menu->url('/', 'Home');
	$menu->dropdown('Settings', function ($sub) {
		$sub->url('settings/account', 'Account');
		$sub->url('settings/password', 'Password');
		$sub->url('settings/design', 'Design');
	});
});
```

You can also create a dropdown inside dropdown by using `->dropdown()` method. This will allow to to create a multilevel menu items.
```
Menu::create('navbar', function($menu)
{
	$menu->url('/', 'Home');
	$menu->dropdown('Account', function ($sub) {
		$sub->url('profile', 'Visit My Profile');
		$sub->dropdown('Settings', function ($sub) {
			$sub->url('settings/account', 'Account');
			$sub->url('settings/password', 'Password');
			$sub->url('settings/design', 'Design');
		});
		$sub->url('logout', 'Logout');
	});
});
```

**Divider and Dropdown Header**

You may also define a divider for each menu item. You can divide between menu item by using `->divider()` method.
```
Menu::create('navbar', function($menu)
{
	$menu->url('/', 'Home');
	$menu->divider();
	$menu->url('profile', 'Profile')
});
```

You may also add a dropdown header for the specified menu item by using `->header()` method.
```
Menu::create('navbar', function($menu)
{
	$menu->url('/', 'Home')
	$menu->url('Settings', function ($sub) {
		$sub->header('ACCOUNT');
		$sub->url('/settings/design', 'Design');
		$sub->divider();
		$sub->url('logout', 'Logout');
	});
});
```

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