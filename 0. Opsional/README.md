## Download Audio dari Youtube
Dalam proses training kita akan menggunakan audio dengan format .wav sebagai media training. Salah satu cara untuk mendapatkan audio adalah dari Youtube, dengan bantuan library yt_dlp untuk melakukan download dan ffmpeg untuk melakukan konversi menjadi file .wav
```py
!pip install yt_dlp
!pip install ffmpeg
!mkdir youtubeaudio
```
Sedangkan kode untuk mendownload file adalah sebagai berikut:
```py
from __future__ import unicode_literals
import yt_dlp
import ffmpeg
import sys

ydl_opts = {
    'format': 'bestaudio/best',
#    'outtmpl': 'output.%(ext)s',
    'postprocessors': [{
        'key': 'FFmpegExtractAudio',
        'preferredcodec': 'wav',
    }],
    "outtmpl": 'youtubeaudio/audio',  # this is where you can edit how you'd like the filenames to be formatted
}
def download_from_url(url):
    ydl.download([url])
    # stream = ffmpeg.input('output.m4a')
    # stream = ffmpeg.output(stream, 'output.wav')


with yt_dlp.YoutubeDL(ydl_opts) as ydl:
      url = "https://www.youtube.com/" #@param {type:"string"}
      download_from_url(url)
```

## Memisahkan Vocal dan Non-vocal (BGM) dari Audio
Jika pada audio yang telah didownload memiliki suara 'noise' atau 'background-music' yang mengganggu maka diperlukan pemisahan audio vocal agar proses train dapat dijalankan dengan benar. Karena audio yang dimasukkan pada proses train harus hanya berisi audio vocal yang ingin dijadikan model suara nantinya. Namun, jika audio vocal sudah bersih maka proses ini dapat diabaikan atau dilewati.

**Install Dependencies**
```py
!python3 -m pip install -U demucs
```
```py
import subprocess
AUDIO_INPUT = "/path/to/youtubeaudio/audio.wav" #@param {type:"string"}

command = f"demucs --two-stems=vocals {AUDIO_INPUT}"
result = subprocess.run(command.split(), stdout=subprocess.PIPE)
print(result.stdout.decode())
```