## **Materi Lengkap Spiking Neural Network (SNN)**

---

### **1. Pengantar Spiking Neural Network**

Spiking Neural Network (SNN) adalah generasi ketiga dari jaringan saraf tiruan (Artificial Neural Network, ANN) yang lebih mendekati cara kerja otak biologis. Tidak seperti ANN biasa yang bekerja dengan sinyal kontinu (real-valued), SNN bekerja dengan **impuls listrik (spike)** dalam waktu diskrit — mirip dengan impuls yang digunakan oleh neuron biologis.

---

### **2. Karakteristik Kunci SNN**

* **Pemrosesan Berdasarkan Waktu (Temporal Coding)**: Informasi tidak hanya dibawa oleh besar sinyal, tetapi juga oleh **waktu terjadinya spike**.
* **Event-Driven**: Hanya neuron yang menerima input akan melakukan komputasi, sehingga lebih hemat energi.
* **Threshold Dynamics**: Neuron mengumpulkan potensial membran dan hanya akan “menembak” (spike) jika melewati ambang batas.

---

### **3. Model Neuron dalam SNN**

Beberapa model neuron dalam SNN antara lain:

#### a. **Leaky Integrate-and-Fire (LIF)**

Model paling populer. Potensial membran diakumulasikan dan bocor seiring waktu:



Jika $V(t) \geq V_{th}$, maka neuron akan menghasilkan spike dan $V(t)$ di-reset.

#### b. **Hodgkin-Huxley Model**

Lebih kompleks dan biologis akurat, menggunakan persamaan diferensial non-linear.

#### c. **Izhikevich Model**

Kombinasi akurat & efisien dari aspek biologis dan kecepatan komputasi.

---

### **4. Encoding Informasi ke Spike**

Beberapa teknik pengkodean sinyal ke spike:

* **Rate Coding**: Informasi diwakili oleh frekuensi spike.
* **Temporal Coding**: Informasi diwakili oleh waktu terjadinya spike.
* **Phase Coding**: Informasi dikodekan berdasarkan pergeseran fase relatif terhadap osilasi.

---

### **5. Arsitektur Jaringan SNN**

SNN dapat memiliki topologi seperti:

* **Feedforward SNN**: Sama seperti MLP (Multilayer Perceptron).
* **Recurrent SNN**: Memiliki umpan balik.
* **Convolutional SNN**: Versi konvolusi dari SNN, digunakan dalam pengolahan citra.

---

### **6. Pembelajaran dalam SNN**

Pembelajaran dalam SNN dapat dilakukan melalui dua pendekatan:

#### a. **Unsupervised Learning (STDP)**

* **Spike-Timing Dependent Plasticity (STDP)**: Bobot sinaps berubah tergantung urutan spike:

  * Pre-spike sebelum post → penguatan (potensiasi)
  * Post-spike sebelum pre → pelemahan (depresi)

#### b. **Supervised Learning**

* **Backpropagation berbasis spike**: Sulit karena fungsi spike tidak terdiferensiasi.
* Solusi:
  * **Surrogate Gradient**: Gunakan fungsi aktivasi aproksimasi saat training.
  * **ANN-to-SNN Conversion**: Latih ANN biasa, lalu konversi ke SNN.
---

### **7. Perbandingan ANN vs SNN**

| Aspek            | ANN              | SNN                                   |                                                         |
| ---------------- | ---------------- | ------------------------------------- | ------------------------------------------------------- |
| Jenis sinyal     | Nilai kontinu    | Spike diskrit                         |                                                         |
| Pemrosesan waktu | Statik           | Dinamis                               |                                                         |
| Biologis         | Kurang realistis | Lebih mendekati otak                  |                                                         |
| Energi           | Lebih boros      | Efisien (event-driven)                |                                                         |
| Aplikasi         | CV, NLP, dll     | Robotics, IoT, Neuromorphic Computing | ([arxiv.org][1], [en.wikipedia.org][2], [arxiv.org][3]) |

---

### **8. Aplikasi SNN**

* **Edge AI & IoT**: Efisiensi energi sangat cocok untuk perangkat edge.
* **Neuroprostetik**: Interface otak-komputer (BCI).
* **Robotika**: Reaksi cepat & efisiensi energi.
* **Pemrosesan sinyal waktu nyata**: Deteksi suara, penciuman, dan sentuhan tiruan.([arxiv.org][1])

---

### **9. Implementasi SNN**

#### a. **Perangkat Lunak**

* **Brian2** – Python library fleksibel.
* **NEST** – Untuk simulasi skala besar.
* **BindsNET** – Framework berbasis PyTorch untuk SNN.
* **Nengo** – Komputasi kognitif berbasis SNN.([GeeksforGeeks][4])

#### b. **Perangkat Keras (Neuromorphic Hardware)**

* **Intel Loihi**
* **IBM TrueNorth**
* **SpiNNaker**
* Menggunakan arsitektur event-driven untuk efisiensi energi.

---

### **10. Tantangan dan Riset Masa Depan**
* **Backpropagation pada spike**: Belum efisien atau stabil.
* **Konversi ANN ke SNN**: Masih ada kehilangan akurasi.
* **Hardware support**: Belum umum dan mahal.
* **Standarisasi model & format data spike**

---
