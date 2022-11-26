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
By default, we will see uploaded photos in `public/storage` folder, if don't see, run php artisan storage:link
`Note:` lots of features we can eanable from `config/jetstream.php` and `config/fortify.php` and use it without any coading.






## Authorization

### Gates



### Policies

### Authorizing