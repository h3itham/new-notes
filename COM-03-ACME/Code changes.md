1. New log channel 
	```php

        // NEW LOGGING CHANALL FOR KUBERNETS (EFK-STACK)
        'stdout' => [
            'driver' => 'monolog',
            'level' => env('LOG_LEVEL', 'debug'),
            'handler' => StreamHandler::class,
            'formatter' => env('LOG_STDOUT_FORMATTER'),
            'with' => [
                'stream' => 'php://stdout',
            ],
        ],
	```
2. Change `log_channle` in `.env` file
	```txt
	LOG_CHANNEL=stdout
	```
3. Health chekc EndPoint 
	```php
	Route::get('/health', function () {
	    return response()->json(['status' => 'OK'], 200);
	});
	```

4. Force HTTPS in `app/Providers/AppServiceProvider.php`
	```php
	public function boot()
	{
	    if ($this->app->environment('production')) {
	        URL::forceScheme('https');
	    }
	}
	```
	