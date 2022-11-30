# Authentication using [JetStream](https://jetstream.laravel.com/2.x/introduction.html) with Livewere
###### Installation
- `composer require laravel/jetstream`
- `php artisan jetstream:install livewire`
- `npm install`
- `npm run build`
- `php artisan migrate`

To add more field for registraion, put extra fields to `App/Actions/Fortify/CreateNewUser.php`: 
```php
class CreateNewUser implements CreatesNewUsers
{
    public function create(array $input)
    {
        Validator::make($input, [
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
            'password' => $this->passwordRules(),
            'phone' => ['required', 'int', 'max:25'],
            'terms' => Jetstream::hasTermsAndPrivacyPolicyFeature() ? ['accepted', 'required'] : '',
        ])->validate();

        return User::create([
            'name' => $input['name'],
            'email' => $input['email'],
            'password' => Hash::make($input['password']),
            'phone' => $input['phone'],
            'address' => $input['address'],
        ]);
    }
}
```
Migrations in `user schema`:
```php
$table->id();
$table->string('name');
$table->string('email')->unique();
$table->string('usertype')->default(0);
$table->string('phone')->nullable();
```

##### Setting Profile Page:
Enabling Profile Photos: go to `config/jetstream.php`
```php
  'features' => [
        // Features::termsAndPrivacyPolicy(),
         Features::profilePhotos(),
        // Features::api(),
        // Features::teams(['invitations' => true]),
        Features::accountDeletion(),
    ]
```
By default, we will see uploaded photos in `public/storage` folder, if don't see, run `php artisan storage:link`.

`Note:` lots of features we can enable from `config/jetstream.php` and `config/fortify.php` and use it without any coading.


## Redirect admin, writer and users to separate page after login
###### Go to `config/fortify.php` and modify this line:
```php
// Change from 
'home' => RouteServiceProvider::HOME,

// To
'home' => function(){
    //if you want to go to a specific route
    // return route('dashboard');
    
    //or if you have a bunch of redirection options
    if (Auth::user()->hasRole('admin')) {
        return route('admin.index');
    }
    else{
        return route('dashboard');
    }
},
```


## Authorization with [Spatie](https://spatie.be/docs/laravel-permission/v5/installation-laravel)
###### Installation
- `composer require spatie/laravel-permission`
- add below code in `app/config/app.php` file:
```php
'providers' => [
    Spatie\Permission\PermissionServiceProvider::class,
];
```
- `php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"`
- `php artisan optimize:clear`
- `php artisan migrate`

We will get 4 tables:
- `roles`
- `role_has_permissions`
- `model_has_permissions`
- `model_has_roles:`
   `3 columns: role_id, model_type, model_id (user_id)`

Basic Usage:
- First, add the `Spatie\Permission\Traits\HasRoles` trait to `User` model(s):
```php
use Spatie\Permission\Traits\HasRoles;
class User extends Authenticatable
{
    use HasRoles;
}
```
Now we will create many `roles` using `seeder`:
Go To `DatabaseSeeder` and `add below lines` on top:
```php
use App\Models\User;
use Illuminate\Database\Seeder;
use Spatie\Permission\Models\Role;

class DatabaseSeeder extends Seeder
{
public function run()
    {
        // Role Creating
        Role::create(['name' => 'admin']);
        Role::create(['name' => 'writer']);
        Role::create(['name' => 'user']);

        // Create Admin
        $admin = User::create([
            'name' => 'Admin',
            'email' => 'admin@gmail.com',
            'password' => bcrypt('12345678'),
        ]);
        $admin->assignRole(['writer', 'admin']);

        // create writer's
        $writer = User::create([
            'name' => 'writer',
            'email' => 'writer@gmail.com',
            'password' => bcrypt('12345678'),
        ])->assignRole('writer');
    }
}
```
run: `php artisan migrate:fresh --seed` // it will remove all tables and run from first

###### Using a middleware:
add below lines to `app/Http/Kernel.php:`
```php
protected $routeMiddleware = [
    'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
    'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
    'role_or_permission' => \Spatie\Permission\Middlewares\RoleOrPermissionMiddleware::class,
];
```
Then you can protect your routes using middleware rules: Ex:
```php
Route::group(['middleware' => ['role:admin']], function () {
    //
});

For Admin
Route::middleware(['auth:sanctum',config('jetstream.auth_session'),'verified', 'role:admin'])->group(function () {
    Route::get('/admin/dashboard', [AdminController::class, 'index'])->name('admin.dashboard');
});

For User
Route::middleware(['auth:sanctum',config('jetstream.auth_session'),'verified', 'role:user'])->group(function () {
    Route::get('dashboard', [UserController::class, 'index'])->name('dashboard');
});
```
Alternatively, you can separate multiple roles or permission with a `| (pipe)` character:
```php
Route::group(['middleware' => ['role:admin|writer']], function () {
    //
});
```
You can protect your controllers similarly, by setting desired middleware in the `constructor`:
```php
public function __construct()
{
    $this->middleware(['role:admin']);
}
```

Assigning Roles: `$user->assignRole('writer')`;
Checking Roles: `$user->hasRole('writer')`;
Has Any User:   `$user->hasAnyRole(['writer', 'reader'])`;
if a user has all of roles:: `$user->hasAllRoles(Role::all())`;
Assigning Direct Permissions To A User: `$role = Role::findByName('writer');`


## Working with Blade directives
Permissions:
This package doesn't add any permission-specific Blade directives. Instead, use Laravel's native @can directive to check if auser has a certain permission.
```blade
@can('edit articles')
  //
@endcan
or
@if(auth()->user()->can('edit articles') && $some_other_condition)
  //
@endif
```
You can use @can, @cannot, @canany, and @guest to test for permission-related access.

###### Roles
As discussed in the Best Practices section of the docs, it is strongly recommended to always use permission directives, instead of role directives.
```blade
@role('writer')
    I am a writer!
@else
    I am not a writer...
@endrole
```
is the same as
```blade
@hasrole('writer')
    I am a writer!
@else
    I am not a writer...
@endhasrole
```
Check for any role in a list:
```blade
@hasanyrole('writer|admin')
    I am either a writer or an admin or both!
@else
    I have none of these roles...
@endhasanyrole
```




## Display Data
###### Using Eloquent:
Since Role and Permission models are extended from Eloquent models, basic Eloquent calls can be used as well:
```php
$all_users_with_all_their_roles = User::with('roles')->get();
$all_users_with_all_direct_permissions = User::with('permissions')->get();
$all_roles_in_database = Role::all()->pluck('name');
$users_without_any_roles = User::doesntHave('roles')->get();
$all_roles_except_a_and_b = Role::whereNotIn('name', ['role A', 'role B'])->get();
```


### Gates



### Policies

### Authorizing