# Readings: Permissions & Postgresql

## [How permissions are determined](https://www.django-rest-framework.org/api-guide/permissions/#how-permissions-are-determined)
Permissions in REST framework are always defined as a list of permission classes.

Before running the main body of the view each permission in the list is checked. If any permission check fails an exceptions.PermissionDenied or exceptions.NotAuthenticated exception will be raised, and the main body of the view will not run.

When the permissions checks fail either a "403 Forbidden" or a "401 Unauthorized" response will be returned, according to the following rules:

The request was successfully authenticated, but permission was denied. — An HTTP 403 Forbidden response will be returned.
The request was not successfully authenticated, and the highest priority authentication class does not use WWW-Authenticate headers. — An HTTP 403 Forbidden response will be returned.
The request was not successfully authenticated, and the highest priority authentication class does use WWW-Authenticate headers. — An HTTP 401 Unauthorized response, with an appropriate WWW-Authenticate header will be returned.

## Object level permissions
If you're writing your own views and want to enforce object level permissions, or if you override the get_object method on a generic view, then you'll need to explicitly call the .check_object_permissions(request, obj) method on the view at the point at which you've retrieved the object.

This will either raise a PermissionDenied or NotAuthenticated exception, or simply return if the view has the appropriate permissions.

For example:

def get_object(self):
    obj = get_object_or_404(self.get_queryset(), pk=self.kwargs["pk"])
    self.check_object_permissions(self.request, obj)
    return obj
Note: With the exception of DjangoObjectPermissions, the provided permission classes in rest_framework.permissions do not implement the methods necessary to check object permissions.

If you wish to use the provided permission classes in order to check object permissions, you must subclass them and implement the has_object_permission() method described in the Custom permissions section (below).

## Setting the permission policy
The default permission policy may be set globally, using the DEFAULT_PERMISSION_CLASSES setting. For example.

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
If not specified, this setting defaults to allowing unrestricted access:

'DEFAULT_PERMISSION_CLASSES': [
   'rest_framework.permissions.AllowAny',
]

You can also set the authentication policy on a per-view, or per-viewset basis, using the APIView class-based views.

from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)

Or, if you're using the @api_view decorator with function based views.

from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response

@api_view(['GET'])
@permission_classes([IsAuthenticated])
def example_view(request, format=None):
    content = {
        'status': 'request was permitted'
    }
    return Response(content)

Provided they inherit from rest_framework.permissions.BasePermission, permissions can be composed using standard Python bitwise operators. For example, IsAuthenticatedOrReadOnly could be written:

from rest_framework.permissions import BasePermission, IsAuthenticated, SAFE_METHODS
from rest_framework.response import Response
from rest_framework.views import APIView

class ReadOnly(BasePermission):
    def has_permission(self, request, view):
        return request.method in SAFE_METHODS

class ExampleView(APIView):
    permission_classes = [IsAuthenticated|ReadOnly]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)

## Custom permissions

### Examples
The following is an example of a permission class that checks the incoming request's IP address against a blacklist, and denies the request if the IP has been blacklisted.

from rest_framework import permissions

class BlacklistPermission(permissions.BasePermission):\
    """
    Global permission check for blacklisted IPs.
    """

    def has_permission(self, request, view):
        ip_addr = request.META['REMOTE_ADDR']
        blacklisted = Blacklist.objects.filter(ip_addr=ip_addr).exists()
        return not blacklisted

# [Generic views](https://www.django-rest-framework.org/api-guide/generic-views/)
## Examples
Typically when using the generic views, you'll override the view, and set several class attributes.

from django.contrib.auth.models import User
from myapp.serializers import UserSerializer
from rest_framework import generics
from rest_framework.permissions import IsAdminUser

class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAdminUser]

For very simple cases you might want to pass through any class attributes using the .as_view() method. For example, your URLconf might include something like the following entry:

url(r'^/users/', ListCreateAPIView.as_view(queryset=User.objects.all(), serializer_class=UserSerializer), name='user-list')

## Attributes
### Basic settings:

The following attributes control the basic view behavior.

* queryset - The queryset that should be used for returning objects from this view. Typically, you must either set this attribute, or override the get_queryset() method. If you are overriding a view method, it is important that you call get_queryset() instead of accessing this property directly, as queryset will get evaluated once, and those results will be cached for all subsequent requests.
* serializer_class - The serializer class that should be used for validating and deserializing input, and for serializing output. Typically, you must either set this attribute, or override the get_serializer_class() method.
* lookup_field - The model field that should be used to for performing object lookup of individual model instances. Defaults to 'pk'. Note that when using hyperlinked APIs you'll need to ensure that both the API views and the serializer classes set the lookup fields if you need to use a custom value.
* lookup_url_kwarg - The URL keyword argument that should be used for object lookup. The URL conf should include a keyword argument corresponding to this value. If unset this defaults to using the same value as lookup_field.

### Pagination:

The following attributes are used to control pagination when used with list views.

* pagination_class - The pagination class that should be used when paginating list results. Defaults to the same value as the DEFAULT_PAGINATION_CLASS setting, which is 'rest_framework.pagination.PageNumberPagination'. Setting pagination_class=None will disable pagination on this view.

### Filtering:

* filter_backends - A list of filter backend classes that should be used for filtering the queryset. Defaults to the same value as the DEFAULT_FILTER_BACKENDS setting.

## Mixins
The mixin classes provide the actions that are used to provide the basic view behavior. Note that the mixin classes provide action methods rather than defining the handler methods, such as .get() and .post(), directly. This allows for more flexible composition of behavior.

The mixin classes can be imported from rest_framework.mixins.

## ListModelMixin
Provides a .list(request, *args, **kwargs) method, that implements listing a queryset.

If the queryset is populated, this returns a 200 OK response, with a serialized representation of the queryset as the body of the response. The response data may optionally be paginated.

## CreateModelMixin
Provides a .create(request, *args, **kwargs) method, that implements creating and saving a new model instance.

If an object is created this returns a 201 Created response, with a serialized representation of the object as the body of the response. If the representation contains a key named url, then the Location header of the response will be populated with that value.

If the request data provided for creating the object was invalid, a 400 Bad Request response will be returned, with the error details as the body of the response.

## RetrieveModelMixin
Provides a .retrieve(request, *args, **kwargs) method, that implements returning an existing model instance in a response.

If an object can be retrieved this returns a 200 OK response, with a serialized representation of the object as the body of the response. Otherwise it will return a 404 Not Found.

## UpdateModelMixin
Provides a .update(request, *args, **kwargs) method, that implements updating and saving an existing model instance.

Also provides a .partial_update(request, *args, **kwargs) method, which is similar to the update method, except that all fields for the update will be optional. This allows support for HTTP PATCH requests.

If an object is updated this returns a 200 OK response, with a serialized representation of the object as the body of the response.

If the request data provided for updating the object was invalid, a 400 Bad Request response will be returned, with the error details as the body of the response.

## DestroyModelMixin
Provides a .destroy(request, *args, **kwargs) method, that implements deletion of an existing model instance.

If an object is deleted this returns a 204 No Content response, otherwise it will return a 404 Not Found.

## Concrete View Classes
The following classes are the concrete generic views. If you're using generic views this is normally the level you'll be working at unless you need heavily customized behavior.

The view classes can be imported from rest_framework.generics.

### CreateAPIView
Used for create-only endpoints.

Provides a post method handler.

Extends: GenericAPIView, CreateModelMixin

### ListAPIView
Used for read-only endpoints to represent a collection of model instances.

Provides a get method handler.

Extends: GenericAPIView, ListModelMixin

### RetrieveAPIView
Used for read-only endpoints to represent a single model instance.

Provides a get method handler.

Extends: GenericAPIView, RetrieveModelMixin

### DestroyAPIView
Used for delete-only endpoints for a single model instance.

Provides a delete method handler.

Extends: GenericAPIView, DestroyModelMixin

### UpdateAPIView
Used for update-only endpoints for a single model instance.

Provides put and patch method handlers.

Extends: GenericAPIView, UpdateModelMixin

### ListCreateAPIView
Used for read-write endpoints to represent a collection of model instances.

Provides get and post method handlers.

Extends: GenericAPIView, ListModelMixin, CreateModelMixin

### RetrieveUpdateAPIView
Used for read or update endpoints to represent a single model instance.

Provides get, put and patch method handlers.

Extends: GenericAPIView, RetrieveModelMixin, UpdateModelMixin

### RetrieveDestroyAPIView
Used for read or delete endpoints to represent a single model instance.

Provides get and delete method handlers.

Extends: GenericAPIView, RetrieveModelMixin, DestroyModelMixin

### RetrieveUpdateDestroyAPIView
Used for read-write-delete endpoints to represent a single model instance.

Provides get, put, patch and delete method handlers.

Extends: GenericAPIView, RetrieveModelMixin, UpdateModelMixin, DestroyModelMixin

