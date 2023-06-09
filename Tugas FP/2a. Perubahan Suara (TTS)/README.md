## Proses Perubahan Suara Melalui Media TTS(Text to Speech)
Setelah melakukan training dan mendapatkan output filenya, kita bisa menggenerate TTS dari apa yang kita tuliskan dan merubah suaranya menjadi suara hasil training tadi.
## Menginstall kebutuhan library
Sebelum menginstall kebutuhan library, anda bisa melakukan mount google drive terlebih dahulu.
```py
!python -m pip install -U pip wheel
!pip install -U ipython
!pip install -U so-vits-svc-fork
!pip install edge-tts
!pip install audiosegment
```
## Generate TTS Sekaligus Merubah Suara
```py
import subprocess
import audiosegment
from IPython.display import Audio, display

gender = "Female" #@param ["Male", "Female"]
text = "isi text" #@param {type:"string"}

if gender == "Male":
  command = ['edge-tts', '--voice', 'id-ID-ArdiNeural', '--text', text, '--write-media', 'tts.mp3', '--write-subtitles', 'isi_tts.vtt']
  result = subprocess.run(command, stdout=subprocess.PIPE, text=True)
  print(result.stdout)
elif gender == "Female":
  command = ['edge-tts', '--voice', 'id-ID-GadisNeural', '--text', text, '--write-media', 'tts.mp3', '--write-subtitles', 'isi_tts.vtt']
  result = subprocess.run(command, stdout=subprocess.PIPE, text=True)
  print(result.stdout)

audio = audiosegment.from_file("tts.mp3")

# Set the output format to WAV
audio = audio.set_sample_width(2)
audio = audio.set_frame_rate(44100)
audio = audio.set_channels(1)

# Export the audio to WAV format
audio.export("tts.wav", format='wav')

AUDIO = "/content/tts" 
MODEL = "isi path file pth" #@param {type:"string"}
CONFIG = "isi path file config.json" #@param {type:"string"}
METHOD = "harvest" #@param ["harvest", "dio", "crepe", "crepe-tiny", "parselmouth"]
PITCH = 0 #@param {type:"slider", min:-12, max:12, step:1}

# Auto Pitch Mode
!svc infer {AUDIO}.wav -c {CONFIG} -m {MODEL} -fm {METHOD}

# Manual Pitch Mode
#!svc infer {AUDIO}.wav -c {CONFIG} -m {MODEL} -fm {METHOD} -na -t {PITCH}

# Try comment this line below if you got Runtime Error
try:
  display(Audio(f"{AUDIO}.out.wav", autoplay=True))
except Exception as e:  print("Error:", str(e))
```
### Cara menggunakan
Terdapat pilihan `gender`, `text`, `MODEL`, `CONFIG`, `METHOD`, dan `PITCH`.
|    Pilihan   |    Keterangan    |
| :----------: | :--------: |
|    gender    | memilih suara awal tts yang akan digenerate oleh google |
|     text     | isi dari TTS yang akan digenerate |
|    MODEL     | masukkan file .pth |
|    CONFIG    | masukkan file config.json |
|    METHOD    | memilih metode apa yang digunakan untuk merubah suara |
|    PITCH     | mengatur pitch dari suara tts awal yang digenerate |

- Perlu diketahui jika tts yang digenerate oleh google merupakan TTS Bahasa Indonesia. Jika ingin selain Bahasa Indonesia, anda dapat merubah sendiri kodingan diatas pada bagian `'id-ID-ArdiNeural'` untuk suara laki-laki dan `'id-ID-GadisNeural'` untuk suara perempuan dengan suara lain yang ada pada list `edge-TTS-list.txt`.
- Pengaturan pitch tidak akan memiliki efek ketika pada kode bagian ini belum dicommand.
```py
# Auto Pitch Mode
!svc infer {AUDIO}.wav -c {CONFIG} -m {MODEL} -fm {METHOD}
```
- dan pada bagian ini commandnya belum dihilangkan.
```py
# Manual Pitch Mode
#!svc infer {AUDIO}.wav -c {CONFIG} -m {MODEL} -fm {METHOD} -na -t {PITCH}
```

## Algoritma
Ketika di run, program akan menggenerate TTS Bahasa Indonesia dari Google dalam bentuk file .mp3. Setelah file `tts.mp3` didapatkan, program akan merubah format file tersebut menjadi `tts.wav`. Lalu, file `tts.wav` tersebut akan dirubah isi suaranya menjadi suara yang kita inginkan dan akan memiliki output file bernama `tts.out.wav`. Ada juga file tambahan bernama `isi_tts.vtt` yang merupakan log dari program tersebut.

### Google Colab
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Khosyi0/AI-VOICE/blob/main/2a.%20Perubahan%20Suara%20(TTS)/2a_TTS.ipynb)
