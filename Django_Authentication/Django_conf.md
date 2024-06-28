## Settings.py
For Single User System:
```
AUTH_USER_MODEL = 'AuthApp.User'

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```
For Multi User System:
```
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'authApp.authenticator.UnifiedTokenAuthentication',
    ],
}
```
Define Custom Authentication:
```
import jwt
from rest_framework.authentication import BaseAuthentication
from rest_framework.exceptions import AuthenticationFailed
from utility.auth_utils import jwt_decode_handler
from .models import Broker, Learner

class UnifiedTokenAuthentication(BaseAuthentication):
    def authenticate(self, request):
        token = self.get_token_from_request(request)
        if not token:
            return None  # No token provided, so don't authenticate

        try:
            decoded_token = jwt_decode_handler(token)
            user = self.get_user(decoded_token['user_id'])
            if user:
                return (user, None)
        except (jwt.DecodeError, KeyError, Broker.DoesNotExist, Learner.DoesNotExist):
            pass  # Invalid token or user not found

        return None  # Invalid token or user not found, so don't authenticate

    def get_token_from_request(self, request):
        auth_header = request.headers.get('Authorization')
        if auth_header and auth_header.startswith('Bearer '):
            parts = auth_header.split(' ')
            if len(parts) == 2:
                return parts[1]
        return None  # No valid token found

    def get_user(self, user_id):
        try:
            return Broker.objects.get(id=user_id)
        except Broker.DoesNotExist:
            try:
                return Learner.objects.get(id=user_id)
            except Learner.DoesNotExist:
                return None  # User not found
```

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
## Actual Usage in view.py
```
from rest_framework.permissions import IsAuthenticated

class AuthenticatedModelViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated]

class RawMaterialsViewSet(AuthenticatedModelViewSet):
    queryset = models.RawMaterials.objects.all()
    serializer_class = serializers.RawMaterialsSerializer
```
## If Need to Unauthenticate Specific Class
```
class GoogleLoginApi(viewsets.ViewSet):
    authentication_classes = []
```
## If Need to Unauthenticate Specific Function
```
@action(detail=False, methods=['POST'], permission_classes = [])
```
## If Need to Authenticate Specific
```
from rest_framework.permissions import IsAuthenticated
= [IsAuthenticated]
```
## Note
```
Authentication Classes: Determine who the user is by validating their credentials.
Permission Classes: Determine what the authenticated user can do, i.e., whether they have the right to access or perform actions on the requested resource.
```
