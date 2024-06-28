## Settings.py
```
# Media files (uploaded by users)
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```
## project_name.urls.py
```
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```