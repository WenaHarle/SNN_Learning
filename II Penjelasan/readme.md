## 🔑 Konsep Utama dalam SNN

### 1. **Neuron** dan **Spike**

Dalam SNN, **neuron** berperan sebagai unit pemroses sinyal yang berperilaku mirip neuron biologis. Setiap neuron memiliki nilai **membrane potential (V<sub>m</sub>)**, yaitu tegangan internal yang berubah akibat spike yang diterima dari **presynaptic neurons**.

* Ketika sebuah spike diterima, nilai V<sub>m</sub> bertambah sesuai dengan **synaptic weight (w)** dan **jenis sinaps** (eksitatorik atau inhibitorik).
* Jika V<sub>m</sub> > threshold, neuron akan **firing** dan menghasilkan **spike output**.
* Setelah firing, neuron masuk ke **refractory period** (periode tidak aktif sementara) dan V<sub>m</sub> di-reset.

Ini jauh lebih realistis dibanding Artificial Neural Networks (ANNs) yang hanya memproses nilai numerik secara langsung.

---

### 2. **Temporal Coding**

**Temporal coding** adalah prinsip utama SNN dalam mengkodekan informasi berdasarkan **waktu kemunculan spike**.

* Dalam **rate coding** (seperti di ANN), informasi dikodekan dalam jumlah spike dalam interval waktu tertentu.
* Dalam **temporal coding**, informasi dikodekan dalam waktu **spike pertama**, **interval antar spike**, atau **pola urutan spike**.

Contoh:

* Dua neuron menerima input suara. Neuron A spike lebih dulu → menandakan sumber suara lebih dekat.
* Pola spike dari berbagai neuron dapat membentuk **spatio-temporal pattern** → mewakili fitur kompleks seperti bentuk, arah gerak, dsb.

> ⏳ Keunggulan utama: Representasi lebih kaya dan efisien dari sinyal dunia nyata (audio, visual, sensorik).

---

### 3. **Synaptic Weights** dan **Plasticity**

**Synaptic weight (w<sub>ij</sub>)** adalah bobot dari sinaps antara neuron i (presynaptic) dan j (postsynaptic). Nilai ini menentukan seberapa besar pengaruh spike terhadap perubahan membrane potential.

#### **Plasticity** adalah kemampuan sinaps untuk berubah berdasarkan pengalaman (input spike history).

### a. **Spike-Timing-Dependent Plasticity (STDP)**

STDP adalah **aturan pembelajaran lokal dan temporal**:

* Jika neuron presynaptic spike **lebih dulu** dari postsynaptic dalam interval t, maka **LTP (Long-Term Potentiation)** terjadi → sinaps diperkuat.
* Jika spike presynaptic **datang setelah** postsynaptic, maka **LTD (Long-Term Depression)** terjadi → sinaps dilemahkan.

Fungsi pembaruan bobot STDP:

```math
\Delta w = 
\begin{cases}
A_+ e^{-\Delta t / \tau_+}, & \text{if } \Delta t > 0 \\
-A_- e^{\Delta t / \tau_-}, & \text{if } \Delta t < 0
\end{cases}
```

Dimana:

* Δt = t<sub>post</sub> - t<sub>pre</sub>
* A<sub>+</sub>, A<sub>-</sub>: amplitudo maksimum
* τ<sub>+</sub>, τ<sub>-</sub>: konstanta waktu

> 🔁 STDP memungkinkan pembelajaran tanpa supervisi, sangat cocok untuk deteksi pola spatio-temporal.

---

## ⚙️ Mekanisme Kerja SNN

### 1. **Membrane Potential** dan **Firing Threshold**

* Setiap neuron memiliki nilai **V<sub>m</sub>(t)** yang mengikuti dinamika seperti:

```math
\tau_m \frac{dV_m}{dt} = -V_m(t) + R \cdot I(t)
```

Dimana:

* τ<sub>m</sub>: konstanta waktu membran

* R: resistansi membran

* I(t): arus input dari sinaps

* Jika V<sub>m</sub>(t) ≥ threshold → neuron **firing**, lalu di-reset ke baseline (misalnya 0) dan masuk fase refraktori (biasanya 2–5 ms).

---

### 2. **Synaptic Integration**

* Spike yang masuk menyebabkan perubahan pada V<sub>m</sub> sesuai dengan bobot sinaps dan jenis sinaps:

  * **Excitatory synapse**: spike menambah V<sub>m</sub> (depolarisasi).
  * **Inhibitory synapse**: spike mengurangi V<sub>m</sub> (hiperpolarisasi).

* Total perubahan didapat dari penjumlahan spike input:

```math
V_m(t) = \sum_{i} w_i \cdot \epsilon(t - t_i)
```

Dimana:

* w<sub>i</sub>: bobot sinaps dari neuron ke-i
* t<sub>i</sub>: waktu spike dari neuron ke-i
* ε(t): fungsi respons sinaps (exponential decay)

---

### 3. **Neuron Models**

#### a. **Leaky Integrate-and-Fire (LIF)**

Model ini menangkap esensi neuron biologis:

* Potensial naik karena spike masuk.
* Potensial **bocor** seiring waktu tanpa input.
* Jika melewati threshold, neuron firing.

Kelebihan:

* Sederhana & efisien
* Ideal untuk simulasi besar (jutaan neuron)

#### b. **Hodgkin-Huxley Model**

Model biologi rinci yang merepresentasikan arus ion (Na⁺, K⁺) melalui kanal membran.

* Memodelkan **aksi potensial** dengan sangat akurat.
* Digunakan dalam penelitian neurobiologi.
* Lebih mahal secara komputasi.
