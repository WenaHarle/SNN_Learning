# Pengertian 
Spiking Neural Network (SNN) adalah jenis jaringan saraf tiruan yang meniru cara kerja neuron biologis dengan mengirimkan informasi melalui **lonjakan elektrik (spike)** pada waktu tertentu. Berbeda dengan jaringan saraf tradisional yang menggunakan aktivasi kontinu, SNN memproses informasi secara temporal dan asinkron, membuatnya lebih efisien dan mirip dengan sistem saraf alami.

### Cara Kerja SNN

1. **Mekanisme Lonjakan**: Neuron dalam SNN hanya mengirim sinyal (lonjakan) ketika potensial membran mencapai ambang batas tertentu. Ini mirip dengan neuron biologis yang mengirim impuls listrik[^1][^11].
2. **Pemrosesan Temporal**: Informasi tidak hanya ditentukan oleh frekuensi lonjakan, tetapi juga oleh **waktu pasti terjadinya lonjakan** (spike timing)[^11].
3. **Model Neuron**:
    - **Leaky Integrate-and-Fire (LIF)**: Model sederhana di mana neuron mengintegrasikan input dan "melepaskan" lonjakan saat mencapai ambang, lalu mereset potensial[^9][^11].
    - **Spike-Timing-Dependent Plasticity (STDP)**: Aturan pembelajaran yang menyesuaikan kekuatan koneksi antar neuron berdasarkan waktu lonjakan[^9].

### Keunggulan

- **Efisiensi Energi**: Hanya mengonsumsi daya saat terjadi lonjakan, cocok untuk perangkat hemat energi[^9].
- **Pemrosesan Waktu Nyata**: Mampu memproses data temporal seperti sinyal sensor atau video secara efektif[^6][^9].
- **Kompatibilitas dengan Neuromorphic Hardware**: Dirancang untuk kerja optimal di perangkat keras yang meniru otak manusia[^7][^9].


### Tantangan

- **Kompleksitas Pelatihan**: Sulit menerapkan backpropagation tradisional karena sifat diskret lonjakan[^6][^11].
- **Kebutuhan Hardware Khusus**: Membutuhkan prosesor neuromorfik seperti SpiNNaker untuk implementasi optimal[^2][^9].


### Aplikasi

- **Robotika**: Pemrosesan sensorik real-time[^9].
- **Antarmuka Otak-Komputer**: Decoding sinyal saraf biologis[^9].
- **Pengenalan Pola Temporal**: Seperti pengenalan ucapan atau gerakan[^7][^11].

SNN merepresentasikan generasi ketiga jaringan saraf tiruan, menggabungkan efisiensi energi dengan kemampuan pemrosesan temporal yang unggul[^4][^11]. Perkembangannya terus didorong oleh riset dalam algoritma pembelajaran (seperti STDP) dan hardware neuromorfik[^2][^9].

[^1]: https://goong.com/id/word/spiking-neural-network-dalam-bahasa-indonesia/

[^2]: http://repo.darmajaya.ac.id/4874/1/Time-Space, Spiking Neural Networks and Brain-Inspired Artificial Intelligence ( PDFDrive ).pdf

[^3]: http://arxiv.org/pdf/1804.08150.pdf

[^4]: https://pure.ulster.ac.uk/files/86005452/Clarence_snn_paper.pdf

[^5]: https://klu.ai/glossary/spiking-neural-network

[^6]: https://serpapi.com/blog/spiking-neural-networks/

[^7]: https://paperswithcode.com/method/snn

[^8]: https://aws.amazon.com/id/what-is/neural-network/

[^9]: https://www.youtube.com/watch?v=-HIiOXE7Mz8

[^10]: https://socs.binus.ac.id/2012/07/26/konsep-neural-network/

[^11]: https://en.wikipedia.org/wiki/Spiking_neural_network

[^12]: https://revou.co/kosakata/neural-network

[^13]: https://www.ijcai.org/proceedings/2024/0596.pdf

[^14]: https://codingstudio.id/blog/apa-itu-neural-network/

[^15]: https://ojs.aaai.org/index.php/AAAI/article/view/29635/31078

[^16]: https://codepolitan.com/blog/apa-itu-neural-network-revolusi-deep-learning-penting-banget-nih

[^17]: https://www.youtube.com/watch?v=KL09IQOvQg4

[^18]: https://www.youtube.com/watch?v=Xsj-XcbE7cU

[^19]: https://www.youtube.com/watch?v=9dYZXQl4ozk

[^20]: https://www.youtube.com/watch?v=AQp9UgQXbIY
