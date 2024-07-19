## Package Innstallation 
```
pip install djangorestframework, drf-yasg
```

## Settings.py
```
INSTALLED_APPS = [
    "rest_framework",
    "drf_yasg",
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    "django.middleware.csrf.CsrfViewMiddleware",
]

# Static files
STATIC_URL = "static/"
STATICFILES_DIRS = []
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# Rest Framework settings
REST_FRAMEWORK = {
    'DEFAULT_INFO': {
        'title': 'RestonovaAPIs',
        'version': '1.0.0',
    },
    'DEFAULT_RENDERER_CLASSES': (
        'rest_framework.renderers.JSONRenderer',
    ),
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
    ),
}

# Swagger settings
SWAGGER_SETTINGS = {
    'DEFAULT_AUTO_SCHEMA_CLASS': 'drf_yasg.inspectors.SwaggerAutoSchema',
}
```
## projectName.urls.py
```
from django.contrib import admin
from django.urls import path, include
from rest_framework import permissions
from drf_yasg.views import get_schema_view
from drf_yasg import openapi
from django.conf import settings
from django.conf.urls.static import static

# Create a custom namespace for your API URLs to avoid conflicts with Django's authentication URLs.
api_urlpatterns = [
    # path('InventoryApp/', include('InventoryApp.urls')), 
    path('AuthApp/', include('AuthApp.urls')),
    path('InventoryApp/', include('InventoryApp.urls')),
]

schema_view = get_schema_view(
    openapi.Info(
        title="RestronovaRMS API",
        default_version="v1",
        description="No any description for now",
        contact=openapi.Contact(email="work.restronova@gmail.com"),
        license=openapi.License(name="sikify"),
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(api_urlpatterns)),
    path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
]

if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

```
## Commands
```
python3 manage.py generateschema --file openapi-schema.yml
python3 manage.py collectstatic
```
