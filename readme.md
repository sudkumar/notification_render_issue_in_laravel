#### Reproduction of [laravel/framework#28958](https://github.com/laravel/framework/issues/28958)

---

**What we are testing**

According to [docs](https://laravel.com/docs/5.8/notifications#previewing-mail-notifications), we can preview our notification by returning a renderable Mailable instance. But when trying to render a Notification what usage a `view`, we are getting `InvalidArgumentException`.

**Steps to test**

```
git clone git@github.com:sudkumar/notification_render_issue_in_laravel.git
cd notification_render_issue_in_laravel
cp .env.example .env
composer install
php artisan key:generate
php artisan serve
```

Now visit [http://localhost:8000](http://localhost:8000) in your browser. You will see:

```
InvalidArgumentException

View [] not found.
```

**The minimal code**

We have create a dummy notification with `php artisan make:notification Dummy` which returns our `welcome` view in its `toMail` function i.e.

```php
<?php
namespace App\Notifications;

use Illuminate\Notifications\Notification;
# use statements...

class Dummy extends Notification {
	# other methods...

	public function toMail () {
		return (new MailMessage)->view("welcome");
	}
}
```

To test this notification, we have modified the root route as follows

```php
# routes/web.php
Route::get('/', function () {
	return (new App\Notifications\Dummy)
		->toMail("dummy.user@example.com");
});
```

Now visiting [http://localhost:8000/](http://localhost:8000/) (after `php artisan serve`), we will get the `View [] not found.` error
