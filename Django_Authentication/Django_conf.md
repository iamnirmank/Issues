## Settings.py
```
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
]

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': datetime.timedelta(days=1),
    'REFRESH_TOKEN_LIFETIME': datetime.timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
}

INSTALLED_APPS = [
    "django.contrib.auth",
    "django.contrib.sessions",
    "django.contrib.messages",
    'allauth',
    'allauth.account',
]
```
### Actual Usage in view.py
```
from rest_framework.permissions import IsAuthenticated

class AuthenticatedModelViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated]

class RawMaterialsViewSet(AuthenticatedModelViewSet):
    queryset = models.RawMaterials.objects.all()
    serializer_class = serializers.RawMaterialsSerializer
```