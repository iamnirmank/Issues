## Installation
```
pip install apscheduler
```
## tasks.py
```
from asgiref.sync import async_to_sync
from channels.layers import get_channel_layer
from datetime import datetime
from apscheduler.schedulers.background import BackgroundScheduler

def check_inventory_levele():
    pass

def start():
    try:
        scheduler = BackgroundScheduler()
        # scheduler.add_job(check_inventory_level, CronTrigger(day_of_week='mon-thu', hour='11-15', second='15'))
        scheduler.add_job(check_inventory_level, 'interval', seconds=30)
        # scheduler.add_job(check_inventory_level, 'cron', hour='11,14', minute=0)
        scheduler.start()
    except Exception as e:
        print(f"An error occurred in start: {str(e)}")

def myTask():
    print("scheduler task run at: ", datetime.now().strftime("%H:%M:%S"))
```
## apps.py
```
from django.apps import AppConfig
from . import tasks


class InventoryAppConfig(AppConfig):
    default_auto_field = "django.db.models.BigAutoField"
    name = "InventoryApp"

    def ready(self):
        tasks.start()
``