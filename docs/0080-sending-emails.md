#How to send an email- Contact From:

# Step-1: Setting your google account:
   - don't find option: `Less secure apps & your Google Account` because google remove it and also auto off from Jun-2022
   - We need to enable 2-Step-Verification
   - Go to `App passwords` button , you will find it just below `2-Step-Verification` button
        - click `Select App` and select `Mail`
        - Click `Select device` and select ` Other(Custom Name)`, 
        - You will find a input text field, then write there `Laravel Mailer`
        - You will find 16 charecters password like, `knfycdgjcrlliuou` copy it then paste it on `.env` file

# Step-2: Set Up `.env` File
```
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=nazmulhasan3615@gmail.com
MAIL_PASSWORD=knfycdgjcrlliuou
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="nazmulhasan3615@gmail.com"
MAIL_FROM_NAME="${APP_NAME}"
```
[See Details] (https://www.youtube.com/watch?v=PeK_tD4T3Og)

# Step-3: Create Mailable Class:
- `php artisan make:mail ContactMail`
- Update Mail file: `app->Mail->MailNotify.php`
  
```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class ContactMail extends Mailable
{
    use Queueable, SerializesModels;
    public $data = [];

    public function __construct($data)
    {
        $this->data = $data;
    }
    public function build()
    {
        return $this->from('test@gmail.com', 'Testing')
            ->subject('Contact US - ' . $this->data['subject'])
            ->view('front.contact-us.email_contact')->with('data', $this->data);
    }
}
```
# Step 4 - Add Mail Template: `front->contact-us->email_contact.blade.php`
```php
<!DOCTYPE html>
<html>
<head>
    <title>Mail Sending</title>
</head>
<body>
    <h1>Welcome Mail</h1>
    <p>{{ $data['content'] }}</p>
</body>
</html>
```
# Step - 5: Adding route
```php
Route::get('/contact-us', [ContactController::class, 'index']);
Route::post('/contact-us/store', [ContactController::class, 'store'])->name('contact.store');
```

# Step 6 - Create Contact from file
- Go To `front -> contact-us -> contact_us.blade.php`
```php
<form action="{{ route('contact.store') }}" method="post">
        @csrf
        <input type="text" name="name">
        <input type="email" name="email">
        <input type="text" name="subject">
        <textarea name="content" rows="4"></textarea>
        <button type="submit">Send Message</button>
    </form>
```

# Step 7 - Create Controller
- `php artisan make:controller ContactController`
- Open file `app->Http->Controllers->ContactController.php`

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Mail;
class ContactController extends Controller
{
    public function index(){
        return view('front.contact-us.contact_us');
    }

    public function store(Request $request)
    {
        $data = new Contact();
        $data->name = $request->name;
        $data->email = $request->email;
        $data->subject = $request->subject;
        $data->content = $request->content;
        $data->save();

        try {
            Mail::to('nazmulhasan3615@gmail.com')->send(new ContactMail($data));
            return back()->with('success', 'Great! We have received your message and would like to thank you for writing to us.');

        } catch (Exception $e) {
            return back()->with('success', 'Sorry! Please try again latter');
        }
    }
}
```
# Finally  - Run Laravel App
``` php artisan serve




