## AuthUser.model.py Example
```
from django.db import models
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager, PermissionsMixin, Group, Permission
from django.utils.translation import gettext as _

class Restaurant(models.Model):
    restaurant_code = models.CharField(max_length=255, primary_key=True)
    name = models.CharField(max_length=255)
    contact_no = models.CharField(max_length=20, blank=True, null=True)
    email = models.EmailField(unique=True)
    subscription_token = models.CharField(max_length=32, blank=True, null=True)

    def __str__(self):
        return self.restaurant_code

class Outlet(models.Model):
    outlet_code = models.CharField(max_length=255, primary_key=True)
    addresss = models.CharField(max_length=255, blank=True, null=True)
    contact_no = models.CharField(max_length=20, blank=True, null=True)
    email = models.EmailField(unique=True)
    restaurant = models.ForeignKey(Restaurant, on_delete=models.CASCADE, to_field='restaurant_code', related_name='restaurant', blank=True, null=True)

    def __str__(self):
        return self.outlet_code

class UserManager(BaseUserManager):
    def create_user(self, email, name, contact_no, address, role, password=None):
        if not email:
            raise ValueError('The Email field must be set')
        email = self.normalize_email(email)
        user = self.model(
            email=email,
            name=name,
            contact_no=contact_no,
            address=address,
            role=role
        )
        user.set_password(password)
        user.save(using=self._db)
        return user
    
    def create_superuser(self, email, password=None):
        user = self.create_user(
            email,
            name="Restronova",
            contact_no="9810937611",
            address="Bhaktapur, Nepal",
            role="Super Admin",
            password=password
        )
        user.is_verified = True
        user.is_superuser = True
        user.save(using=self._db)
        return user

class User(AbstractBaseUser, PermissionsMixin):
    name = models.CharField(max_length=255)
    contact_no = models.CharField(max_length=20, blank=True, null=True)
    email = models.EmailField(unique=True)
    address = models.CharField(max_length=255, blank=True, null=True)
    role = models.CharField(max_length=255, blank=True, null=True)
    verification_code = models.CharField(max_length=6, blank=True, null=True)
    is_verified = models.BooleanField(default=False)
    is_staff = models.BooleanField(default=True)
    is_superuser = models.BooleanField(default=False)
    password_reset_token = models.CharField(max_length=32, blank=True, null=True)
    password_reset_token_created_at = models.DateTimeField(blank=True, null=True)
    outlet = models.ForeignKey(Outlet, on_delete=models.CASCADE, to_field='outlet_code', related_name='outlet', blank=True, null=True)

    objects = UserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []

    groups = models.ManyToManyField(
        Group,
        verbose_name=_('groups'),
        blank=True,
        help_text=_('The groups this user belongs to. A user will get all permissions granted to each of their groups.'),
        related_name='user_users'
    )

    user_permissions = models.ManyToManyField(
        Permission,
        verbose_name=_('user permissions'),
        blank=True,
        help_text=_('Specific permissions for this user.'),
        related_name='user_users'
    )

    def __str__(self):
        return self.name
```
## serializers.py
```
from rest_framework import serializers
from . import models

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.User
        fields = '__all__'

class OutletSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Outlet
        fields = '__all__'
```
## urls.py
```
from rest_framework import routers
from django.urls import path, include
from . import views

router = routers.DefaultRouter()
router.register(r'user', views.UserViewSet)
router.register(r'outlet', views.OutletViewSet)

urlpatterns = [
    path('api/', include(router.urls)),  
    path('accounts/', include('allauth.urls')),
]
```
## view.py Example
```
from rest_framework import viewsets, status
from rest_framework.decorators import action
from django.utils import timezone
from django.contrib.auth.hashers import make_password
from .models import User, Outlet
from .serializers import UserSerializer, OutletSerializer
from .utils import get_user, jwt_encode_handler, generate_code, create_response, send_reset_email, send_verification_email


class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=False, methods=['POST'])
    def register(self, request):
        serializer = UserSerializer(data=request.data)

        if serializer.is_valid():
            password = request.data.get('password')
            serializer.validated_data['password'] = make_password(password)
            user = serializer.save()
            send_verification_email(user)

            # Serialize the user instance before returning the response
            serialized_user = UserSerializer(user).data

            return create_response(
                True,
                'User registered successfully. Please check your email for verification instructions.',
                serialized_user,
                status.HTTP_201_CREATED
            )

        return create_response(False, serializer.errors, status_code=status.HTTP_400_BAD_REQUEST)
    

    @action(detail=False, methods=['POST'])
    def login(self, request):
        email, password = request.data.get('email'), request.data.get('password')
        user = get_user(email)

        if not user:
            return create_response(False, 'User not found.', status_code=status.HTTP_404_NOT_FOUND)

        if user.check_password(password):
            if not user.is_verified:
                return create_response(False, 'Email not verified. Please check your email for verification instructions.', status_code=status.HTTP_403_FORBIDDEN)

            token = jwt_encode_handler(user)
            response = {
                'user': UserSerializer(user).data,  
                'token': token  
            }
            return create_response(True, 'User logged in successfully.', response)

        return create_response(False, 'Invalid email or password.', status_code=status.HTTP_401_UNAUTHORIZED)

    @action(detail=False, methods=['POST'])
    def verify_email(self, request):
        email, verification_code = request.data.get('email'), request.data.get('verification_code')
        user = get_user(email)

        if not user:
            return create_response(False, 'User not found.', status_code=status.HTTP_404_NOT_FOUND)

        if user.verification_code == verification_code:
            user.is_verified = True
            user.save()
            return create_response(True, 'Email verified successfully.')

        return create_response(False, 'Invalid verification code.', status_code=status.HTTP_400_BAD_REQUEST)

    @action(detail=False, methods=['POST'])
    def forget_password(self, request):
        email = request.data.get('email')
        user = get_user(email)

        if not user:
            return create_response(False, 'User not found.', status_code=status.HTTP_404_NOT_FOUND)

        reset_token = generate_code()
        user.password_reset_token = reset_token
        user.password_reset_token_created_at = timezone.now()
        user.save()

        send_reset_email(user.email, reset_token)

        return create_response(True, 'Password reset email sent. Please check your email for instructions.')

    @action(detail=False, methods=['POST'])
    def reset_password(self, request):
        email, reset_token, new_password = request.data.get('email'), request.data.get('reset_token'), request.data.get('new_password')

        user = get_user(email)

        if not user:
            return create_response(False, 'User not found.', status_code=status.HTTP_404_NOT_FOUND)

        if user.password_reset_token != reset_token:
            return create_response(False, 'Invalid reset token.', status_code=status.HTTP_400_BAD_REQUEST)

        reset_token_created_at = user.password_reset_token_created_at
        if reset_token_created_at and (timezone.now() - reset_token_created_at).days > 1:
            return create_response(False, 'Reset token has expired. Please request a new one.', status_code=status.HTTP_400_BAD_REQUEST)

        user.set_password(new_password)
        user.password_reset_token = None
        user.password_reset_token_created_at = None
        user.save()

        return create_response(True, 'Password reset successfully.')

class OutletViewSet(viewsets.ModelViewSet):
    queryset = Outlet.objects.all()
    serializer_class = OutletSerializer
```