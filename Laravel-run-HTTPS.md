To configure Laravel to run HTTPS in production and HTTP in development, you need to handle this at multiple levels. Here's how to set it up properly:

## 1. Environment Configuration

In your `.env` files:

**Production (.env.production or main .env):**
```env
APP_URL=https://yourdomain.com
FORCE_HTTPS=true
```

**Development (.env.local or development .env):**
```env
APP_URL=http://localhost:8000
FORCE_HTTPS=false
```

## 2. AppServiceProvider Configuration

In `app/Providers/AppServiceProvider.php`:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\URL;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Force HTTPS in production
        if (config('app.env') === 'production' || config('app.force_https', false)) {
            URL::forceScheme('https');
            $this->app['request']->server->set('HTTPS', true);
        }
    }
}
```

## 3. Middleware for HTTPS Redirect

Create a middleware to force HTTPS in production:

```bash
php artisan make:middleware ForceHttps
```

In `app/Http/Middleware/ForceHttps.php`:

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ForceHttps
{
    public function handle(Request $request, Closure $next)
    {
        if (!$request->secure() && config('app.env') === 'production') {
            return redirect()->secure($request->getRequestUri());
        }

        return $next($request);
    }
}
```

Register it in `app/Http/Kernel.php`:

```php
protected $middleware = [
    // ... other middleware
    \App\Http\Middleware\ForceHttps::class,
];
```

## 4. Configuration File

Add to `config/app.php`:

```php
'force_https' => env('FORCE_HTTPS', false),
```

## 5. Trusted Proxies (if using load balancer/proxy)

If you're behind a proxy or load balancer, configure `app/Http/Middleware/TrustProxies.php`:

```php
<?php

namespace App\Http\Middleware;

use Illuminate\Http\Middleware\TrustProxies as Middleware;
use Illuminate\Http\Request;

class TrustProxies extends Middleware
{
    protected $proxies = '*'; // or specific proxy IPs
    
    protected $headers = Request::HEADER_X_FORWARDED_FOR |
                         Request::HEADER_X_FORWARDED_HOST |
                         Request::HEADER_X_FORWARDED_PORT |
                         Request::HEADER_X_FORWARDED_PROTO |
                         Request::HEADER_X_FORWARDED_AWS_ELB;
}
```

## 6. Web Server Configuration

**For Apache (.htaccess):**
```apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteCond %{HTTP_HOST} !^localhost
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

**For Nginx:**
```nginx
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com;
    
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    # Your Laravel configuration
}
```

## 7. Asset URLs

To ensure assets use the correct protocol, you can also add this to your AppServiceProvider:

```php
public function boot()
{
    if (config('app.env') === 'production') {
        URL::forceScheme('https');
        URL::forceRootUrl(config('app.url'));
    }
}
```

## Testing

- **Development**: `php artisan serve` will run on HTTP
- **Production**: Deploy with proper SSL certificate and the above configuration

This setup ensures that:
- Development runs on HTTP without redirects
- Production forces HTTPS for all requests
- Asset URLs are generated with the correct protocol
- The application handles proxy headers correctly

Make sure to obtain and install proper SSL certificates for your production domain using services like Let's Encrypt, Cloudflare, or commercial SSL providers.