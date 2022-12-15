
### Inside route definition file (routes/web.php)
Basic Routing:
Route Cache Clear both is needed:
`php artisan cache:clear`
`php artisan route:cache`
```php
Route::get('/', function () {
    return view('welcome');
});

Route::get('/user', [UserController::class, 'index']);
Route::get('/user/{id}', [UserController::class, 'index']); // With Required Parameter
Route::get('/user/{id?}', [UserController::class, 'index']); // With Optional Parameter
Route::get('/posts/{post}/comments/{comment}', [UserController::class, 'index']);

Route::get('/user', [UserController::class, 'index'])->where('name', '[A-Za-z]+'); // with Regular Expression
Route::get('/user/{id}', [UserController::class, 'index'])->where('id', '[0-9]+');

Route::get('/user/profile', [UserController::class, 'show'])->name('profile'); // Named Route

// Route Groups
Route::controller(OrderController::class)->group(function () {
    Route::get('/orders/{id}', 'show');
    Route::post('/orders', 'store');
});

// php artisan route:list 
Route::redirect('/here', '/there'); // Redirect Routes:
```

### Inside Blade template
Building Layouts: Layouts Using Components
```blade
// layout.blade.php
<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @yield('slide')
        <div>
           {{ $slot }}
        </div>
    </body>
</html>

// child.php
<x-layout>
    <x-slot:title>
        Home Title
    </x-slot>

     <x-slot:slide>
        display Slide
    </x-slot>

    // This is my body content
    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```

Rendering data inside a Blade template
```php
{{ $data }} // Rendering data inside a Blade template
{!! $data !!} // Rendering unescaped data
@include('view.name') //Including another view 
@include('view.name', ['name' => 'John']) // pass data to view
<a href="{{ route('home') }}">Home</a> 
```

## Create Separate Layout for Admin
By Default, we have `app.blade.php` layout inside `views/layouts` folder.
Now we will make a `Admin Layout`

```Step 1:``` Create a `AdminLayout.php` inside `App/View/Components` Folder:
```php
<?php
namespace App\View\Components;
use Illuminate\View\Component;

class AdminLayout extends Component
{
    public function render()
    {
        return view('layouts.admin');
    }
}
```
`Step 2:` Create a `admin.blade.php` inside `views/layouts` Folder and copy and paste code from `admin.blade.php` or admin template
`Step 3`: Now use `<x-admin-layout>` in child page instead of `<x-layout>` for admin panel

```php
//child.php
<x-admin-layout>
    <x-slot:title>
        Home Title
    </x-slot>

    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-admin-layout>
```
