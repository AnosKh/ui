# Introduction

Laraloop UI is an authentication package built on top of [Laravel Fortify](https://github.com/laravel/fortify). 
The package provide the UI with [Bootstrap 5](https://v5.getbootstrap.com/) and [UIKit 3](https://getuikit.com).
You can find implemented all features of Fortify and a few custom.

This documentation assumes you are already familiar with Laravel and [Laravel Fortify](https://github.com/laravel/fortify). 
If you do not know anything about, consider familiarizing yourself with the general 
terminology and features of Laravel and [Laravel Fortify](https://github.com/laravel/fortify) before continuing.
Check official Laravel and [Laravel Fortify](https://github.com/laravel/fortify) documentation is good starting point.

- [Features](#features)
- [Installation](#installation)
- [Post Installation](#post-installation)
- [Digging Deeper](#diging-deeper)

<a name="features"></a>
## Features

- Login, Registration, Forgot password, Logout
- Authentication with Socialite OAuth
- Registration with Socialite OAuth
- Two factor authentication with normal login and with Socialite
- User Settings with:
    - Edit user profile information
    - Crop and upload profile photo
    - Change user password
    - Enable / disable two factor authentication
    - See recovery codes and regenerated recovery codes of two factor authentication
    - See browser sessions and logout out from other browser sessions
    - Delete account
    - Link/unlink Social account with Socialite
- User profile that display user information
- All form support ajax submissions using Axios
- Easy add ajax submission to any form

<a name="installation"></a>
## Installation

You can install Laraloop UI using quick installation which include everything
you need to get starting or install in a existing project using only the package.

### Installation using Quick Installation

```bash
$ cd path/to/your/webserver
$ git clone git@github.com:laraloop/ui.git laraloop-ui
$ cd laraloop-ui
$ composer install
$ mysql -uroot
$ create database laraloop_ui;
$ cp .env.example .env
```

Add database name, host, user and password.

Alternatively if you want to use SQLite database, just add in `.env`:

```bash
DB_CONNECTION=sqlite
#DB_HOST=127.0.0.1
#DB_PORT=3306
#DB_DATABASE=laraloop_ui
#DB_USERNAME=root
#DB_PASSWORD=
```

The database is under `database/database.sqlite`, if missing, you can just create that file.

Run the install command:

```bash
$ php artisan laraloop-ui:install
```

Follow the command questions such as which framework to use, if publish routes, views etc.
The command can publish views, routes, providers, models and actions.

### Installation in existing project

Create a folder in root of your Laravel installation named `packages/laraloop` and add the package
`laraloop-ui`.

Update your composer.json by adding:

```bash
"repositories": [{
    "type": "path",
    "url": "packages/*/*/",
    "options": {
        "symlink": true
    }
}],
"require": {`
    "laraloop/ui": "dev-master"
},
```

Install package using composer:

```bash
$ composer install
```

Run the install command:

```bash
$ php artisan laraloop-ui:install
```

Follow the command questions such as which framework to use, if publish routes, views etc.
The command can publish views, routes, providers, models and actions.

If you want to use [sessions driver database](https://laravel.com/docs/8.x/session#driver-prerequisites), 
then you need to create sessions table by running below command:

```bash
php artisan session:table
php artisan migrate
```

And update `.env` file:

```bash
SESSION_DRIVER=database
```

> NOTE: Sessions driver database is required if you want allow users to see their active sessions and logout
> from other browser sessions.

<a name="post-installation"></a>
## Post installation

There are several configurations which you can change after installation, below you can find how to do it.

### Socialite

Before proceed consider familiarizing yourself with the general 
terminology and features of [Socialite](https://laravel.com/docs/8.x/socialite) documentation.

In `config/services.php` for each provider you want to enable add:

```php
'github' => [
    'client_id' => env('SOCIALITE_GITHUB_CLIENT_ID'),
    'client_secret' => env('SOCIALITE_GITHUB_CLIENT_SECRET'),
    'redirect' => env('SOCIALITE_GITHUB_CALLBACK_URI'),
],
```

And then in `.env` file:

```bash
SOCIALITE_GITHUB_CLIENT_ID=YOUR_CLIENT_ID
SOCIALITE_GITHUB_CLIENT_SECRET=YOUR_SECRET_ID
SOCIALITE_GITHUB_CALLBACK_URI="${APP_URL}/social/github/callback"
```

In `config/loop-ui.php` for each provider you want to enable add an array with provider slug.
For example if you want to enable GitHub and Google add:

```php
'social_providers' => [
    'github', 'google'
],
```

> NOTE: You can publish Laraloop UI configuration file running below command:

```bash
$ php artisan vendor:publish --tag=laraloop-ui-config
```

<a name="digging-deeper"></a>
## Digging Deeper

### Compile assets

Before compile assets you need to publish it in root of your project, or you can run it directly from package folder.

### Directly from package

```bash
$ cd packages/laraloop/laraloop-ui
$ npm install
```

Once dependencies are installed you can compile assets for UIKit using:

```bash
$ npm --framework=uikit run dev
```

And for Bootstrap using:

```bash
$ npm --framework=bootstrap run dev
```

If you need to compile for production, use:

```bash
$ npm --framework=uikit run production
```

### Publish assets source in root 

For Bootstrap:

```bash
$ php artisan vendor:publish --tag=loop-ui-bootstrap-assets-src
```

For UIKit:

```bash
$ php artisan vendor:publish --tag=loop-ui-uikit-assets-src
```

> NOTE: Your package.json file will be overriden. If you want to manually update it, you can check package.json inside laraloop/ui package.

> NOTE: The same for webpack.mix.js. It will be overriden. If you want to do manually,
> be sure that your webpack.mix.js contain path to `app.js` and `app.scss` of Bootstrap or UIKit. See example below.

```js
// Bootstrap
mix.js('resources/assets/bootstrap/js/app.js',  'public/assets/bootstrap/js/app.js');
mix.sass('assets/bootstrap/sass/app.scss', 'public/assets/bootstrap/css/app.css');

// UIKit
mix.js('resources/assets/uikit/js/app.js', 'public/assets/uikit/js/app.js');
mix.sass('assets/uikit/sass/app.scss', 'public/assets/uikit/css/app.css');
```

> NOTE: You can change path of source and where files are published in public folder, in that case
> be sure to change also the path in main layout.

### Ajax Form

If you want create a custom form with ajax submission, you can use helper ajax-form.js` like below:

```js
// Import helper
import ajaxForm from "./helpers/ajax-form"

// Initialize
ajaxForm('.my-form-selector')
```

The success and error message will be shown in a toast notification if you return from server a JSON with `message`.

Laravel Fortify doesn't return any message after successfully submitting form using ajax, so you can pass as second
parameter the success message. See below example:

```js
// Import helper
import ajaxForm from "./helpers/ajax-form"

// Initialize
ajaxForm('.my-form-selector', 'Successfully processed')
```

You can also pass a callback as third paramter after successfully response. Example:

```js
// Import helper
import ajaxForm from "./helpers/ajax-form"

// Initialize
ajaxForm('.my-form-selector', 'Successfully processed', function() {
    // My callback function on success
})
```

Sometime you need to reload the page when a form is submitted via ajax, for example after user delete account,
you want to reload the page, so that user are logged out and redirect to route defined by Laravel Fortify after
account delete. Or for example after user register or login via ajax, by reloading page, the user will be 
redirected by Laravel Fortify to protected page, as normal registration and login flow.

For handle such case, just add in your form an attribute `data-ajax-form-reload`, for example in delete account
you can find the form like below example:

```html
<form action="{{ route('users.destroy') }}" method="POST" data-ajax-form="delete-account" data-ajax-form-reload>
     
</form>
```

If you want to disable single form from using ajax, just remove the attribute `data-ajax-form`.
If you want to disable all form from using ajax, then most quick solution is just remove the `form.js` 
initialization from `app.js`.

### Confirm password

Laravel Fortify require password confirmation for certain actions, such when enable or disable
two factor authentication, logout from other browser sessions or when user want to delete account.

With Laraloop UI you can handle this via ajax using below helper:

```js
// Import helper
import confirmPassword from "./helpers/confirm-password"

// Initialize
confirmPassword('.my-form-selector', function() {
    // My callback function on success
})
```

> NOTE Your form just need to have an input type password, which will be used for user to insert current password.
> And the action of form should be the route of the action that you want to perform.

You can load this form inside a modal or inline in page. The success callback is optional.

### Email verification

For enable email verification first your `User` model need to implement `Illuminate\Contracts\Auth\MustVerifyEmail`, example:

```php
class User extends Authenticatable implements MustVerifyEmail
{

}
```

Then in `config/fortify.php` you must enable feature `emailVerification`, example:

```php
    'features' => [
        Features::emailVerification()
    ],
```
