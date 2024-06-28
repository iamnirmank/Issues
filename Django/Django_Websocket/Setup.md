## Installation
```
pip install daphne
```
## Settings.py
```
INSTALLED_APPS = [
    'daphne',
]
# ASGI application
ASGI_APPLICATION = "RestronovaRMS.asgi.application"

# Channels layers
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels.layers.InMemoryChannelLayer",
    }
}

MIDDLEWARE = [
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]


```
## project_name.asgi.py
```
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import InventoryApp.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'RestronovaRMS.settings')

# Define your WebSocket routing
websocket_application = ProtocolTypeRouter({
    "websocket": AuthMiddlewareStack(
        URLRouter(
            InventoryApp.routing.websocket_urlpatterns
        )
    ),
})

# Combine the HTTP and WebSocket applications
application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": websocket_application,
})
```
## routing.py
```
from django.urls import path
from .consumers import Consumer

websocket_urlpatterns = [
    path('ws/websocket/', Consumer.as_asgi()),
    ]
```
## Consumer.py
```
# consumers.py
from channels.generic.websocket import WebsocketConsumer
from asgiref.sync import async_to_sync
import json

class Consumer(WebsocketConsumer):
    def connect(self):
        try:
            self.room_group_name = 'InventoryApp'
            self.room_name = 'InventoryAlerts'
            async_to_sync(self.channel_layer.group_add)(
                self.room_group_name,
                self.channel_name
            )
            self.accept()
            self.send(text_data=json.dumps({'type': 'connection', 'message': 'Connected'}))
        except Exception as e:
            print(f"WebSocket connection failed: {str(e)}")

    def send_inventory_alerts(self, event):
        alert_data = event['value']
        event_type = event['type']
        self.send(text_data=json.dumps({'type': event_type, 'value': alert_data}))
    
    def disconnect(self, close_code):
        async_to_sync(self.channel_layer.group_discard)(
            self.room_group_name,
            self.channel_name
        )
        self.send(text_data=json.dumps({'type': 'connection', 'message': 'Disconnected'}))
```
## Run Project
```
daphne -b 0.0.0.0 -p 8001 django_project.asgi:application
```