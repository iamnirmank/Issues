## Installation
```
pip install pyjwt djangorestframework-simplejwt
```
# Auth_utility.py
```
from django.core.mail import EmailMessage
import random
import string
from rest_framework_simplejwt.tokens import RefreshToken
import string
import jwt
from rest_framework.response import Response
from rest_framework import status

from capitech import settings

def create_response(success, message, body=None, status_code=status.HTTP_200_OK):
        response_data = {'success': success, 'message': message}
        if body is not None:
            response_data['body'] = body
        return Response(response_data, status=status_code)

def jwt_encode_handler(user):
    """Generates a JWT access token with a set expiration time."""
    refresh = RefreshToken.for_user(user)
    return {
        'access': str(refresh.access_token),
        'refresh': str(refresh)
        }

def jwt_decode_handler(token):
    """Decodes a JWT token and returns the payload."""
    return jwt.decode(token, settings.SECRET_KEY, algorithms=['HS256'])

def send_verification_code(user, email):
    verification_code = ''.join(random.choices(string.digits, k=6))
    user.verification_code = verification_code
    user.save()
    subject = "User Verification"
    message = f"Your verification code is: {verification_code}"
    email = EmailMessage(subject, message, to=[email])
    email.send()

def send_verification_url(user):
    token = jwt_encode_handler(user)
    access_token = token['access']
    verification_url = f"{settings.BASE_URL}/api/authApp/api/learner/verify_email/?token={access_token}"
    subject = "User Verification"
    message = f"Click the following link to verify your account: {verification_url}"
    email = EmailMessage(subject, message, to=[user.email])
    email.send()

def send_password_reset_email(user):
    token = jwt_encode_handler(user)
    access_token = token['access']
    verification_url = f"{settings.BASE_URL}/api/authApp/api/learner/verify_email_for_reset_password/?token={access_token}"
    subject = "User Verification"
    message = f"Click the following link to verify your account: {verification_url}"
    email = EmailMessage(subject, message, to=[user.email])
    email.send()

def generate_code():
    return ''.join(random.choices(string.digits, k=6))

def send_reset_email(email, reset_token):
    subject = "Password Reset"
    message = f"Use the following token to reset your password: {reset_token}"
    email = EmailMessage(subject, message, to=[email])
    email.send()

def upload_path(instance, filename):
    # Adjust the upload path to include content_id and filename
    random_string = ''.join(random.choices(string.ascii_lowercase + string.digits, k=16))
    return f'upload/{instance.__class__.__name__.lower()}/{random_string}+{filename}'
```
