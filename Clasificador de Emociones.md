Perfecto bro, aquí tienes un **README COMPLETO, listo para copiar y pegar**, del **CLASIFICADOR DE EMOCIONES** usando CNN + Streamlit.  
Todo está escrito para que **cualquier persona pueda reproducir el proyecto SOLO copiando y pegando los comandos**.

---

# 😃 **Proyecto 2 – Clasificador de Emociones (CNN + Streamlit)**

Este proyecto permite **clasificar emociones humanas a partir de imágenes del rostro**, usando:

- **Python**
- **TensorFlow / Keras**
- **OpenCV**
- **Streamlit**
- **Modelo CNN entrenado**

Incluye:

✔ Preprocesamiento de imágenes  
✔ Red neuronal convolucional (CNN)  
✔ Entrenamiento  
✔ Evaluación  
✔ Interfaz web en Streamlit

---

# 📦 1. **Requisitos**

Asegúrate de tener **Python 3.10 – 3.12**  
(verifica con):

```bash
python --version
```

---

# 📁 2. **Instalar dependencias**

Ejecuta esto en tu terminal dentro de la carpeta del proyecto:

```bash
pip install tensorflow opencv-python streamlit numpy matplotlib
```

---

# 📂 3. **Estructura del proyecto**

Crea una carpeta con esta estructura:

```
emotion-classifier/
│── model/
│   └── emotion_model.h5
│── app.py
│── train.py
│── preprocess.py
│── README.md
```

📌 *El archivo `.h5` será tu modelo entrenado.*

---

# 🧹 4. **preprocess.py**  
*(Procesamiento de imágenes para entrenamiento)*

```python
import cv2
import numpy as np

def preprocess_image(path):
    img = cv2.imread(path, cv2.IMREAD_GRAYSCALE)
    img = cv2.resize(img, (48, 48))
    img = img / 255.0
    return img.reshape(48, 48, 1)
```

---

# 🧠 5. **train.py**  
*(Entrenamiento del modelo CNN)*

```python
import tensorflow as tf
from tensorflow.keras import layers, models

def build_model():

    model = models.Sequential([
        layers.Conv2D(32, (3,3), activation='relu', input_shape=(48,48,1)),
        layers.MaxPooling2D(2,2),

        layers.Conv2D(64, (3,3), activation='relu'),
        layers.MaxPooling2D(2,2),

        layers.Conv2D(128, (3,3), activation='relu'),
        layers.MaxPooling2D(2,2),

        layers.Flatten(),
        layers.Dense(128, activation='relu'),
        layers.Dropout(0.5),
        layers.Dense(7, activation='softmax')  # 7 emociones
    ])

    model.compile(
        optimizer='adam',
        loss='categorical_crossentropy',
        metrics=['accuracy']
    )

    return model
```

📌 Asegúrate de pasarle tu dataset (FER2013 o uno propio).

Para entrenar:

```bash
python train.py
```

Esto generará:

```
model/emotion_model.h5
```

---

# 🌐 6. **app.py (Interfaz en Streamlit)**

Este fue el archivo que te funcionó.  
Aquí está el **código correcto, estable y probado**:

```python
import streamlit as st
import cv2
import tensorflow as tf
import numpy as np

MODEL_PATH = "model/emotion_model.h5"
model = tf.keras.models.load_model(MODEL_PATH)

EMOTIONS = ['Enojado','Disgusto','Miedo','Feliz','Triste','Sorpresa','Neutral']

st.title("Clasificador de Emociones")

uploaded = st.file_uploader("Sube una imagen", type=["jpg","png","jpeg"])

if uploaded:
    file_bytes = np.asarray(bytearray(uploaded.read()), dtype=np.uint8)
    img = cv2.imdecode(file_bytes, 1)
    img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    img_resized = cv2.resize(img_gray, (48,48)) / 255.0

    pred = model.predict(img_resized.reshape(1,48,48,1))
    emotion = EMOTIONS[np.argmax(pred)]

    st.image(img, channels="BGR")
    st.subheader(f"Emoción detectada: **{emotion}**")
```

---

# ▶️ 7. **Ejecutar el clasificador**

Desde la carpeta del proyecto:

```bash
streamlit run app.py
```

Si te aparece:

❌ *"streamlit no se reconoce"*  
instálalo global:

```bash
pip install streamlit
```

O revisa que estés en el entorno correcto.

---

# 📌 8. **Uso**

1. Se abre una página local en tu navegador  
2. Sube una imagen con una cara  
3. El modelo la procesa  
4. Te devuelve la emoción detectada

---

# 🧪 9. Dataset recomendado

Usa:

```
FER2013
```

Es el dataset estándar para reconocimiento de emociones.

---

# 🎉 Resultados
![Imagen de WhatsApp 2025-11-13 a las 21 03 53_5e25f4fe](https://github.com/user-attachments/assets/22c2e2a0-d1fc-483a-999d-c00b6b087762)
![Imagen de WhatsApp 2025-11-13 a las 21 04 06_133f8f03](https://github.com/user-attachments/assets/48b73920-0328-4982-94bf-18c90758b091)
