# AI-VOICE
Untuk mempermudah penggunaannya, kita bisa meminjam komputer google dengan cara menggunakan google colab yang dimana kita bisa menggunakan lebih banyak library dan juga kita bisa menggunakan framwork TensorFlow dengan mudah di sana.
## **Mount Google Drive**
Langkah pertama yang harus kita lakukan adalah nge-mount google drive kita, mengapa kita harus nge-mount google drive kita, hal ini bertujuan agar ketika kita akan melakukan training, dapat langsung di save di google drive dan ketika ingin mengupload sesuatu yang diinginkan dapat melalui google drive, karena ketika mengupload langsung ke google colab cukup memakan waktu yang lebih lama.
Berikut command untuk nge-mount google drive
```
from google.colab import drive
drive.mount('/content/drive')
```