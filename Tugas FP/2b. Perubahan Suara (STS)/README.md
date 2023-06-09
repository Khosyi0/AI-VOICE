## Proses Perubahan Suara Melalui Media STS(Speech to Speech)
Setelah melakukan training dan mendapatkan output filenya, kita bisa melakukan perekaman suara kita sendiri dan merubah suara kita menjadi suara hasil training tadi.
## Menginstall kebutuhan library
Sebelum menginstall kebutuhan library, anda bisa melakukan mount google drive terlebih dahulu.
```py
!python -m pip install -U pip wheel
!pip install -U ipython
!pip install -U so-vits-svc-fork
!pip install ffmpeg-python
```
## Merekam Suara Sendiri
Terlebih dahulu coba run program berikut
```py
from IPython.display import HTML, Audio
from google.colab.output import eval_js
from base64 import b64decode
import numpy as np
from scipy.io.wavfile import read as wav_read
import io
import ffmpeg

AUDIO_HTML = """
<script>
var my_div = document.createElement("DIV");
var my_p = document.createElement("P");
var my_btn = document.createElement("BUTTON");
var t = document.createTextNode("Press to start recording");

my_btn.appendChild(t);
//my_p.appendChild(my_btn);
my_div.appendChild(my_btn);
document.body.appendChild(my_div);

var base64data = 0;
var reader;
var recorder, gumStream;
var recordButton = my_btn;

var handleSuccess = function(stream) {
  gumStream = stream;
  var options = {
    //bitsPerSecond: 8000, //chrome seems to ignore, always 48k
    mimeType : 'audio/webm;codecs=opus'
    //mimeType : 'audio/webm;codecs=pcm'
  };            
  //recorder = new MediaRecorder(stream, options);
  recorder = new MediaRecorder(stream);
  recorder.ondataavailable = function(e) {            
    var url = URL.createObjectURL(e.data);
    var preview = document.createElement('audio');
    preview.controls = true;
    preview.src = url;
    document.body.appendChild(preview);

    reader = new FileReader();
    reader.readAsDataURL(e.data); 
    reader.onloadend = function() {
      base64data = reader.result;
      //console.log("Inside FileReader:" + base64data);
    }
  };
  recorder.start();
  };

recordButton.innerText = "Recording... press to stop";

navigator.mediaDevices.getUserMedia({audio: true}).then(handleSuccess);


function toggleRecording() {
  if (recorder && recorder.state == "recording") {
      recorder.stop();
      gumStream.getAudioTracks()[0].stop();
      recordButton.innerText = "Saving the recording... pls wait!"
  }
}

// https://stackoverflow.com/a/951057
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

var data = new Promise(resolve=>{
//recordButton.addEventListener("click", toggleRecording);
recordButton.onclick = ()=>{
toggleRecording()

sleep(2000).then(() => {
  // wait 2000ms for the data to be available...
  // ideally this should use something like await...
  //console.log("Inside data:" + base64data)
  resolve(base64data.toString())

});

}
});
      
</script>
"""

def get_audio():
  display(HTML(AUDIO_HTML))
  data = eval_js("data")
  binary = b64decode(data.split(',')[1])
  
  process = (ffmpeg
    .input('pipe:0')
    .output('pipe:1', format='wav')
    .run_async(pipe_stdin=True, pipe_stdout=True, pipe_stderr=True, quiet=True, overwrite_output=True)
  )
  output, err = process.communicate(input=binary)
  
  riff_chunk_size = len(output) - 8
  # Break up the chunk size into four bytes, held in b.
  q = riff_chunk_size
  b = []
  for i in range(4):
      q, r = divmod(q, 256)
      b.append(r)

  # Replace bytes 4:8 in proc.stdout with the actual size of the RIFF chunk.
  riff = output[:4] + bytes(b) + output[8:]

  sr, audio = wav_read(io.BytesIO(riff))

  return audio, sr
```
Setelah itu, run program dibawah untuk merekam suara anda. Tekan tombol stop untk berhenti merekam.
```py
audio, sr = get_audio()
```
Jika suara rekaman anda sudah cocok, maka run program dibawah untuk menyimpan hasil rekaman anda.
```py
from scipy.io import wavfile
wavfile.write("speech.wav", sr, audio)
```

## Perubahan Suara
Jalankan program berikut untuk memulai proses perubahan suara. Jangan lupa untuk mengubah path file untuk `MODEL` dan `CONFIG`nya.
```py
from IPython.display import Audio

AUDIO = "/content/speech"
MODEL = "masukkan file .pth" #@param {type:"string"}
CONFIG = "masukkan file config.json" #@param {type:"string"}
PITCH = 0 #@param {type:"integer"}

!svc infer {AUDIO}.wav -c {CONFIG} -m {MODEL} -na -t {PITCH}

# Try comment this line below if you got Runtime Error
try:
  display(Audio(f"{AUDIO}.out.wav", autoplay=True))
except Exception as e:  print("Error:", str(e))
```

## Cara Penggunaan dan Algoritma
User akan diminta untuk melakukan perekaman suara sendiri. User juga dapat melakukan retake suara sebelum menyimpan hasil suara yang akan dirubah. Setelah user sudah puas dengan suaranya, user akan diminta untuk menyimpan dan memiliki output file `speech.wav`. Setelah menyimpan file tersebut, user akan diminta untuk menjalankan program untuk merubah suara. Setelah melakukan perubahan suara, outputnya adalah file `speech.out.wav`.

### Google Colab
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Khosyi0/AI-VOICE/blob/main/2b.%20Perubahan%20Suara%20(STS)/2b_STS.ipynb)
