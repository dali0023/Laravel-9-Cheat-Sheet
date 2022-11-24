### Controllers

Controllers are an alternative to Closures for defining the application logic

Generating a new Controller
`php artisan make:controller ControllerNameController`

```php
<?php
class HomeController extends Controller
{
    public function index(){
        return view('home');
    }
    public function contact(){
        return view('contact');
    }
}

// Single Action Controllers
class HomeSingleController extends Controller
{
    public function __invoke()
    {
        return view('home');
    }
}

Route::get('home', 'HomeSingleController'); // routes/web.php
`php artisan make:controller HomeSingleController --invokable` // generate single controller
```
### Single Action Controllers

For controllers that handle just a single action:

```php
<?php
class HomeSingleController extends Controller
{
    public function __invoke()
    {
        return view('home');
    }
}

```

Route definition (routes/web.php)

```php
Route::get('home', 'HomeSingleController');
```

Generating a single action controller

`php artisan make:controller HomeSingleController --invokable`

The `__invoke` is a magic PHP method that allows the object to be called like a function, eg.

```php
$controller = new HomeSingleController();
$controller();
```

Of course you would never create controller classes yourself, Laravel handles that. This is just an example of how invokable object (object that can be called like it would be a function) in PHP looks like.

### Routing

The action function generates a URL for the given controller action

```php
$url = action('HomeController@home');
```
