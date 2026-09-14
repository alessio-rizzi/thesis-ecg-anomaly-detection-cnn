# ECG Anomaly Detection & Transfer Learning tramite CNN

Questo repository contiene il codice sorgente per un progetto di tesi incentrato sull'analisi e la classificazione di segnali elettrocardiografici (ECG) utilizzando Reti Neurali Convoluzionali (CNN) in TensorFlow/Keras. Il progetto si divide in due fasi principali: un pre-training su un dataset clinico su larga scala e un fine-tuning su un dataset specifico per il riconoscimento dei cambi di postura (supin/stand-up).

## 🎯 Obiettivi della Tesi

*   **Pre-Training:** Addestramento di una CNN leggera (meno di 100.000 parametri) con 8 layer convoluzionali 1D per classificare i tracciati ECG in due macro-categorie: Normali (NORM) e Anormali (ABNORM). Il segnale viene campionato a 100Hz, normalizzato tramite Z-score e segmentato in finestre sovrapposte da 344 campioni[cite: 3].
*   **Fine-Tuning & Cross-Validation:** Il modello pre-addestrato viene riadattato per classificare due transizioni posturali specifiche: "Supin" (etichetta 0) e "Stand-up" (etichetta 1). Il processo utilizza una Cross-Validation basata sui pazienti, sbloccando gradualmente i pesi della rete e applicando un decadimento lineare del learning rate.
*   **Data Augmentation:** Entrambe le fasi applicano tecniche di data augmentation on-the-fly, tra cui l'aggiunta di rumore Gaussiano e il Random Amplitude Scaling[cite: 2, 3].

## 💾 Download dei Dataset

Per ragioni di spazio, i file dei dataset non sono inclusi in questo repository. Segui queste istruzioni per scaricarli e posizionarli correttamente:

1.  **Dataset PTB-XL (Pre-Training):**
    *   Scarica il dataset ufficiale (versione 1.0.3) dal portale PhysioNet (https://physionet.org/content/ptb-xl/get-zip/1.0.3/)
    *   Estrai i file e posiziona l'intera cartella esattamente in questo percorso: `./dataset/ptb-xl-a-large-publicly-available-electrocardiography-dataset-1.0.3/`All'interno devono essere presenti i file annotati come `ptbxl_database.csv` e `scp_statements.csv`.
2.  **Dataset Transizioni Posturali (Fine-Tuning):**
    *   Scarica il dataset contenente i file fisici (es. `.dat`, `.hea`) e il file `RECORDS.txt`(https://physionet.org/content/prcp/get-zip/1.0.0/).
    *   Posiziona tutti i record dei pazienti all'interno della cartella: `./dataset/supin_stand/`.

## 🛠️ Installazione e Struttura delle Cartelle

Clona questo repository e reinstalla l'ambiente virtuale utilizzando il file delle dipendenze:

```bash
git clone https://github.com/alessio-rizzi/thesis-ecg-anomaly-detection-cnn.git
cd thesis-ecg-anomaly-detection-cnn
pip install -r requirements.txt

## 🚀 Come eseguire il codice

L'esecuzione deve seguire un ordine rigoroso affinché il Transfer Learning vada a buon fine:

1. **Avvia `pre_training.ipynb`:** Questo notebook elabora il dataset PTB-XL, applica l'estrazione della singola derivazione D1 e salva il modello migliore nella directory `./model/best_ptbxl_d1_model_NORM_vs_ALL_344.keras`. I grafici delle curve di apprendimento verranno generati in `./img/training_curves_344.png`.
2. **Avvia `fine_tuning.ipynb`:** Questo notebook necessita del modello generato nel passaggio precedente. Leggerà il dataset `supin_stand`, estraendo segmenti da 344 campioni per generare le immagini visuali in `img/tot_ecg` e caricando poi i segnali puliti da `img/filtrati_ecg`.
3. **Risultati:** Alla fine del processo di fine-tuning, le matrici di confusione e i grafici delle metriche (Loss e Accuracy combinate per le due fasi) verranno automaticamente salvati rispettivamente in `./img/confusion_matrix/test_2` e `./img/metriche/test_2`.

### Assicurati che la struttura delle cartelle sia la seguente prima di lanciare i notebook:

├── dataset/
│   ├── ptb-xl-a-large-publicly-available-electrocardiography-dataset-1.0.3/
│   └── supin_stand/
├── img/
│   ├── confusion_matrix/
│   ├── metriche/
│   ├── filtrati_ecg/
│   └── tot_ecg/
├── model/
├── weights/
├── pre_training.ipynb
└── fine_tuning.ipynb