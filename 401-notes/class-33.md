# Authentication & Production Server
## [JSON Web Tokens](https://jwt.io/introduction/)
JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA or ECDSA.
### When should you use JSON Web Tokens?
Here are some scenarios where JSON Web Tokens are useful:

- Authorization: This is the most common scenario for using JWT. Once the user is logged in, each subsequent request will include the JWT, allowing the user to access routes, services, and resources that are permitted with that token. Single Sign On is a feature that widely uses JWT nowadays, because of its small overhead and its ability to be easily used across different domains.

- Information Exchange: JSON Web Tokens are a good way of securely transmitting information between parties. Because JWTs can be signed—for example, using public/private key pairs—you can be sure the senders are who they say they are. Additionally, as the signature is calculated using the header and the payload, you can also verify that the content hasn't been tampered with.
### What is the JSON Web Token structure?
In its compact form, JSON Web Tokens consist of three parts separated by dots (.), which are:

Header
Payload
Signature
Therefore, a JWT typically looks like the following.

xxxxx.yyyyy.zzzzz

Header

The header typically consists of two parts: the type of the token, which is JWT, and the signing algorithm being used, such as HMAC SHA256 or RSA.
For example:\
{\
  "alg": "HS256",\
  "typ": "JWT"\
}

### Payload
The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: registered, public, and private claims.

- Registered claims: These are a set of predefined claims which are not mandatory but recommended, to provide a set of useful, interoperable claims. Some of them are: iss (issuer), exp (expiration time), sub (subject), aud (audience), and others.

Notice that the claim names are only three characters long as JWT is meant to be compact.

- Public claims: These can be defined at will by those using JWTs. But to avoid collisions they should be defined in the IANA JSON Web Token Registry or be defined as a URI that contains a collision resistant namespace.

- Private claims: These are the custom claims created to share information between parties that agree on using them and are neither registered or public claims.

An example payload could be:

{\
  "sub": "1234567890",\
  "name": "John Doe",\
  "admin": true\
}
### Signature
To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.

For example if you want to use the HMAC SHA256 algorithm, the signature will be created in the following way:

HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)

  ## [Installation & Setup](https://simpleisbetterthancomplex.com/tutorial/2018/12/19/how-to-use-jwt-authentication-with-django-rest-framework.html)
For this tutorial we are going to use the djangorestframework_simplejwt library, recommended by the DRF developers.

pip install djangorestframework_simplejwt

settings.py

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

urls.py

from django.urls import path
from rest_framework_simplejwt import views as jwt_views

urlpatterns = [
    # Your URLs...
    path('api/token/', jwt_views.TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', jwt_views.TokenRefreshView.as_view(), name='token_refresh'),
]

views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated


class HelloView(APIView):
    permission_classes = (IsAuthenticated,)

    def get(self, request):
        content = {'message': 'Hello, World!'}
        return Response(content)

urls.py

from django.urls import path\
from myapi.core import views

urlpatterns = [
    path('hello/', views.HelloView.as_view(), name='hello'),
]

## [Django Migrations: A Primer](https://realpython.com/django-migrations-a-primer/)
You can take a peek at the database with the dbshell management command. In SQLite, the command to list all tables is simply .tables:

$ python manage.py dbshell\
SQLite version 3.19.3 2017-06-27 16:48:08\
Enter ".help" for usage hints.\
sqlite> .tables\
sqlite>

### Listing Out Migrations
If you want to know what migrations exist in a Django project, you don’t have to dig trough the migrations directories of your installed apps. You can use the showmigrations command:

$ ./manage.py showmigrations

## Unapplying Migrations
If you want to revert the migration 0002_auto_20181112_1950 in your historical_data app, you have to pass 0001_initial as an argument to the migrate command:

$ python manage.py migrate historical_data 0001_initial\
Operations to perform:\
  Target specific migration: 0001_initial, from historical_data\
Running migrations:\
  Rendering model states... DONE\
  Unapplying historical_data.0002_auto_20181112_1950... OK