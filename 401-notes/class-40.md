# Web application security
## [Security in Django](https://docs.djangoproject.com/en/3.0/topics/security/)

## Cross site scripting (XSS) protection
Django templates escape specific characters which are particularly dangerous to HTML. While this protects users from most malicious input, it is not entirely foolproof. For example, it will not protect the following:

<style class={{ var }}>...</style>
If var is set to 'class1 onmouseover=javascript:func()', this can result in unauthorized JavaScript execution, depending on how the browser renders imperfect HTML. (Quoting the attribute value would fix this case.)

It is also important to be particularly careful when using is_safe with custom template tags, the safe template tag, mark_safe, and when autoescape is turned off.

In addition, if you are using the template system to output something other than HTML, there may be entirely separate characters and words which require escaping.

You should also be very careful when storing HTML in the database, especially when that HTML is retrieved and displayed.

## Cross site request forgery (CSRF) protection
CSRF protection works by checking for a secret in each POST request. This ensures that a malicious user cannot “replay” a form POST to your website and have another logged in user unwittingly submit that form. The malicious user would have to know the secret, which is user specific (using a cookie).

When deployed with HTTPS, CsrfViewMiddleware will check that the HTTP referer header is set to a URL on the same origin (including subdomain and port). Because HTTPS provides additional security, it is imperative to ensure connections use HTTPS where it is available by forwarding insecure connection requests and using HSTS for supported browsers.

Be very careful with marking views with the csrf_exempt decorator unless it is absolutely necessary.

## SQL injection protection
SQL injection is a type of attack where a malicious user is able to execute arbitrary SQL code on a database. This can result in records being deleted or data leakage.

Django’s querysets are protected from SQL injection since their queries are constructed using query parameterization. A query’s SQL code is defined separately from the query’s parameters. Since parameters may be user-provided and therefore unsafe, they are escaped by the underlying database driver.

Django also gives developers power to write raw queries or execute custom sql. These capabilities should be used sparingly and you should always be careful to properly escape any parameters that the user can control. In addition, you should exercise caution when using extra() and RawSQL.

## Clickjacking protection
Clickjacking is a type of attack where a malicious site wraps another site in a frame. This attack can result in an unsuspecting user being tricked into performing unintended actions on the target site.

Django contains clickjacking protection in the form of the X-Frame-Options middleware which in a supporting browser can prevent a site from being rendered inside a frame. It is possible to disable the protection on a per view basis or to configure the exact header value sent.

The middleware is strongly recommended for any site that does not need to have its pages wrapped in a frame by third party sites, or only needs to allow that for a small section of the site.

## SSL/HTTPS¶
It is always better for security to deploy your site behind HTTPS. Without this, it is possible for malicious network users to sniff authentication credentials or any other information transferred between client and server, and in some cases – active network attackers – to alter data that is sent in either direction.

If you want the protection that HTTPS provides, and have enabled it on your server, there are some additional steps you may need:

If necessary, set SECURE_PROXY_SSL_HEADER, ensuring that you have understood the warnings there thoroughly. Failure to do this can result in CSRF vulnerabilities, and failure to do it correctly can also be dangerous!

Set SECURE_SSL_REDIRECT to True, so that requests over HTTP are redirected to HTTPS.

Please note the caveats under SECURE_PROXY_SSL_HEADER. For the case of a reverse proxy, it may be easier or more secure to configure the main Web server to do the redirect to HTTPS.

Use ‘secure’ cookies.

If a browser connects initially via HTTP, which is the default for most browsers, it is possible for existing cookies to be leaked. For this reason, you should set your SESSION_COOKIE_SECURE and CSRF_COOKIE_SECURE settings to True. This instructs the browser to only send these cookies over HTTPS connections. Note that this will mean that sessions will not work over HTTP, and the CSRF protection will prevent any POST data being accepted over HTTP (which will be fine if you are redirecting all HTTP traffic to HTTPS).

Use HTTP Strict Transport Security (HSTS)

HSTS is an HTTP header that informs a browser that all future connections to a particular site should always use HTTPS. Combined with redirecting requests over HTTP to HTTPS, this will ensure that connections always enjoy the added security of SSL provided one successful connection has occurred. HSTS may either be configured with SECURE_HSTS_SECONDS, SECURE_HSTS_INCLUDE_SUBDOMAINS, and SECURE_HSTS_PRELOAD, or on the Web server.

## Host header validation
Django uses the Host header provided by the client to construct URLs in certain cases. While these values are sanitized to prevent Cross Site Scripting attacks, a fake Host value can be used for Cross-Site Request Forgery, cache poisoning attacks, and poisoning links in emails.

Because even seemingly-secure web server configurations are susceptible to fake Host headers, Django validates Host headers against the ALLOWED_HOSTS setting in the django.http.HttpRequest.get_host() method.

This validation only applies via get_host(); if your code accesses the Host header directly from request.META you are bypassing this security protection.

For more details see the full ALLOWED_HOSTS documentation.

## Additional security topics
While Django provides good security protection out of the box, it is still important to properly deploy your application and take advantage of the security protection of the Web server, operating system and other components.

Make sure that your Python code is outside of the Web server’s root. This will ensure that your Python code is not accidentally served as plain text (or accidentally executed).
Take care with any user uploaded files.
Django does not throttle requests to authenticate users. To protect against brute-force attacks against the authentication system, you may consider deploying a Django plugin or Web server module to throttle these requests.
Keep your SECRET_KEY a secret.
It is a good idea to limit the accessibility of your caching system and database using a firewall.
Take a look at the Open Web Application Security Project (OWASP) Top 10 list which identifies some common vulnerabilities in web applications. While Django has tools to address some of the issues, other issues must be accounted for in the design of your project.
Mozilla discusses various topics regarding web security. Their pages also include security principles that apply to any system.

* ## [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/Glossary.html)

* ## [Hacksplaining: Lessons](https://www.hacksplaining.com/lessons)
* ## [Owasp Top Ten](https://owasp.org/www-project-top-ten/)
* 