Step 0: Install Laravel Socialite

```bash
composer require laravel/socialite

```

Step 1: Configure .env

Social login er jonne provider theke Client ID, Client Secret, and Redirect URL lagbe.

Google
```bash

GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
GOOGLE_REDIRECT_URI=http://127.0.0.1:8000/login/google/callback
```
Facebook
```bash

FACEBOOK_CLIENT_ID=your-facebook-client-id
FACEBOOK_CLIENT_SECRET=your-facebook-client-secret
FACEBOOK_REDIRECT_URI=http://127.0.0.1:8000/login/facebook/callback
```


Step 2: Configure config/services.php

```bash
'google' => [
    'client_id' => env('GOOGLE_CLIENT_ID'),
    'client_secret' => env('GOOGLE_CLIENT_SECRET'),
    'redirect' => env('GOOGLE_REDIRECT_URI'),
],

'facebook' => [
    'client_id' => env('FACEBOOK_CLIENT_ID'),
    'client_secret' => env('FACEBOOK_CLIENT_SECRET'),
    'redirect' => env('FACEBOOK_REDIRECT_URI'),
],

```

Step 3: Clear Config Cache

```bash
php artisan config:clear
php artisan cache:clear
php artisan route:clear

```

Step 4: Create Routes

routes/web.php:

use App\Http\Controllers\SocialController;
```bash
Route::get('login/{provider}', [SocialController::class, 'redirect'])->name('social.redirect');
Route::get('login/{provider}/callback', [SocialController::class, 'callback'])->name('social.callback');

```

Step 5: Create Controller
```bash
php artisan make:controller SocialController
```

app/Http/Controllers/SocialController.php:
```bash
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Laravel\Socialite\Facades\Socialite;
use App\Models\User;
use Illuminate\Support\Facades\Auth;

class SocialController extends Controller
{
    // Redirect user to provider login page
    public function redirect($provider)
    {
        return Socialite::driver($provider)->redirect();
    }

    // Handle callback from provider
    public function callback($provider)
    {
        try {
            $socialUser = Socialite::driver($provider)->stateless()->user();

            // Check if user already exists
            $user = User::where('email', $socialUser->getEmail())->first();

            if (!$user) {
                // Create new user
                $user = User::create([
                    'name' => $socialUser->getName(),
                    'email' => $socialUser->getEmail(),
                    'password' => bcrypt(uniqid()), // random password
                    'provider' => $provider,
                    'provider_id' => $socialUser->getId(),
                ]);
            }

            Auth::login($user, true); // login user

            return redirect()->intended('/home'); // after login

        } catch (\Exception $e) {
            return redirect('/login')->withErrors('Login failed: ' . $e->getMessage());
        }
    }
}
```

Step 6: Update Users Table (Migration)
If you want to store social provider info, add columns:
```bash

php artisan make:migration add_provider_fields_to_users_table --table=users

```

Migration example:
```bash
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('provider')->nullable();
        $table->string('provider_id')->nullable();
    });
}
```
Step 7: Add Login Buttons in Blade
```bash
<a href="{{ route('social.redirect', ['provider' => 'google']) }}" class="btn btn-danger">
    Login with Google
</a>

<a href="{{ route('social.redirect', ['provider' => 'facebook']) }}" class="btn btn-primary">
    Login with Facebook
</a>
```
