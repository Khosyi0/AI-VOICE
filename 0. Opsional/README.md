## Download Audio dari Youtube
Dalam proses training kita akan menggunakan audio dengan format .wav sebagai media training. Salah satu cara untuk mendapatkan audio adalah dari Youtube, dengan bantuan library yt_dlp untuk melakukan download dan ffmpeg untuk melakukan konversi menjadi file .wav
```
!pip install yt_dlp
!pip install ffmpeg
!mkdir youtubeaudio
```
Sedangkan kode untuk mendownload file adalah sebagai berikut:
```
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
      url = "https://www.youtube.com/watch?v=cQGfLDnmWS8&pp=ygULYXNtYWxpYnJhc2k%3D" #@param {type:"string"}
      download_from_url(url)
```