# Google Drive Checkpoint Callback for Keras
This callback is intended to replace [ModelCheckpoint](https://keras.io/callbacks/). It's functionality is super similar, with the addition of needing a PyDrive connection. I made this callback for use with Google Colab, but you can easily do it locally or on another cloud provider. 

## What does it do?
On epoch completition, if the defined metric improves, the model will be saved and uploaded to Google Drive, with the name given (must end with .h5) + the best monitored metric value. This is super useful for if you leave something running on Colab, but you're scared of it disconnecting from the runtime and you not having a backup of your last best weights. This way, you can load them back in afterwards.

## Usage
First make sure you have PyDrive installed and then authenticate with the Google account you'd like to backup in. The following snippet it from Colab, but for local use you'll have to athenticate a different way.


```python
# Install the PyDrive wrapper & import libraries.
# This only needs to be done once in a notebook.
!pip install -U -q PyDrive
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive
from google.colab import auth
from oauth2client.client import GoogleCredentials

# Authenticate and create the PyDrive client.
# This only needs to be done once in a notebook.
auth.authenticate_user()
gauth = GoogleAuth()
gauth.credentials = GoogleCredentials.get_application_default()
drive = GoogleDrive(gauth)
```

Then download it locally in your notebook.
```python
!curl  -O
```

Then you can create the checkpointer, and use it as a callback!
```python
from google_drive_checkpoint import GoogleDriveCheckpoint

checkpoint = GoogleDriveCheckpoint('unet.h5', drive, save_best_only=True, save_weights_only=True,
                                       verbose=1)

model.fit(X, y, batch_size=32, callbacks=[checkpoint])
```

## Output
```
Epoch 1/400
100/100 [==============================] - 45s 451ms/step - loss: 20.2932 - out_seg_dice_hard: 0.2529 - val_loss: 18.6297 - val_out_seg_dice_hard: 0.2521

Epoch 00001: val_out_seg_dice_hard improved from -inf to 0.25209, saving model to unet.h5
Uploaded file unet-0.252.h5 to drive with ID **************sGqhHn-W8WaL

Epoch 2/400
100/100 [==============================] - 45s 450ms/step - loss: 15.8151 - out_seg_dice_hard: 0.2310 - val_loss: 13.7357 - val_out_seg_dice_hard: 0.2641

Epoch 00002: val_out_seg_dice_hard improved from 0.25209 to 0.26411, saving model to unet.h5
Uploaded file unet-0.264.h5 to drive with ID **************sjtlHn-W8naL

```

## Todo
- Delete previously saved weights so it doesn't bloat up your drive.