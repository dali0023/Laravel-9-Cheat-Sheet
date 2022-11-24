### Controllers

Generating a new Controller
`php artisan make:controller ControllerNameController`

```php
class HomeController extends Controller
{
    public function index(){
        return view('home');
    }
    public function contact(){
        return view('contact');
    }
}
```
### Single Action Controllers
`php artisan make:controller HomeSingleController --invokable`
```php
class HomeSingleController extends Controller
{
    public function __invoke() // `__invoke` is a magic PHP method that allows the object to be called like a function,
    {
        return view('home');
    }
}
Route::get('home', 'HomeSingleController'); // routes/web.php
```
