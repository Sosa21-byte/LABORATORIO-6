

# ✋📡 **Detector de Manos con FastAPI + Mediapipe + Streaming MJPEG**

Este proyecto crea un **servidor FastAPI** que procesa la cámara en **un hilo independiente**, detecta manos con **Mediapipe**, dibuja los landmarks y sirve:

* `/video` → Streaming de cámara en vivo (MJPEG)
* `/gesture` → Último gesto detectado
* `/client` → Interfaz web en HTML

Es ideal para:

✔ Interfaces controladas por gestos
✔ Proyectos de visión artificial
✔ Juegos
✔ IoT / domótica activada por la mano

---

---

# 🧩 **1. Requisitos**

Asegúrate de tener **Python 3.8+**:

```bash
python --version
```

---

---

# 📁 **2. Crear carpeta del proyecto**

```bash
mkdir hand-detector
cd hand-detector
```

---

---

# 🧱 **3. Crear entorno virtual (opcional pero recomendado)**

```bash
python -m venv venv
```

Activar:

**Windows**

```bash
venv\Scripts\activate
```

---

---

# 📦 **4. Instalar dependencias necesarias**

```bash
pip install fastapi uvicorn opencv-python mediapipe
```

Todas son necesarias:

* **fastapi** → servidor backend
* **uvicorn** → servidor ASGI
* **opencv-python** → usar cámara + dibujar
* **mediapipe** → modelo de manos

---

---

# 📂 **5. Estructura del proyecto**

Crea esta estructura:

```
hand-detector/
 ├── app.py
 ├── detector_thread.py
 └── static/
      └── client.html
```

---

---

# 📜 **6. Código REAL DE PRODUCCIÓN**

## ✅ `app.py`

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse, FileResponse
from fastapi.staticfiles import StaticFiles
from detector_thread import detector

app = FastAPI()

# Servir carpeta static (donde está el client.html)
app.mount("/static", StaticFiles(directory="static"), name="static")


@app.get("/")
def home():
    return {"status": "ok", "msg": "Servidor funcionando"}


def stream_camera():
    while True:
        frame = detector.get_jpeg_frame()
        if frame:
            yield (
                b"--frame\r\n"
                b"Content-Type: image/jpeg\r\n\r\n" + frame + b"\r\n"
            )


@app.get("/video")
def video_feed():
    return StreamingResponse(
        stream_camera(),
        media_type="multipart/x-mixed-replace; boundary=frame"
    )


@app.get("/gesture")
def gesture():
    return {"gesture": detector.current_gesture}


@app.get("/client")
def serve_client():
    return FileResponse("static/client.html")
```

---

## ✅ `static/client.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Detector de Manos</title>
    <style>
        body { font-family: Arial; text-align: center; background: #111; color: white; }
        #video { width: 60%; border: 4px solid #444; border-radius: 12px; margin-top: 20px; }
        #gestureBox {
            margin-top: 20px;
            font-size: 30px;
            padding: 10px;
            background: #222;
            display: inline-block;
            border-radius: 8px;
        }
    </style>
</head>
<body>

    <h1>Detector de Gestos</h1>
    <img id="video" src="/video">

    <div id="gestureBox">Cargando gesto...</div>

    <script>
        async function updateGesture() {
            const res = await fetch("/gesture");
            const data = await res.json();
            document.getElementById("gestureBox").innerText = "Gesto: " + data.gesture;
        }

        setInterval(updateGesture, 300);
    </script>

</body>
</html>
```

---

## ✅ `detector_thread.py`

```python
import cv2
import threading
import mediapipe as mp


class HandDetectorThread(threading.Thread):
    def __init__(self):
        super().__init__(daemon=True)

        # Cámara
        self.cap = cv2.VideoCapture(0, cv2.CAP_DSHOW)

        # Mediapipe manos
        self.mp_hands = mp.solutions.hands
        self.hands = self.mp_hands.Hands(
            max_num_hands=1,
            min_detection_confidence=0.6,
            min_tracking_confidence=0.6
        )
        self.mp_draw = mp.solutions.drawing_utils

        # Estado del detector
        self.current_gesture = "none"
        self.last_frame = None
        self.running = True

        self.start()

    def run(self):
        while self.running:
            ret, frame = self.cap.read()
            if not ret:
                continue

            # Mediapipe requiere RGB
            rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
            results = self.hands.process(rgb)

            if results.multi_hand_landmarks:
                self.current_gesture = "hand_detected"

                for hand in results.multi_hand_landmarks:
                    self.mp_draw.draw_landmarks(
                        frame,
                        hand,
                        self.mp_hands.HAND_CONNECTIONS
                    )
            else:
                self.current_gesture = "none"

            # Guardar frame en JPEG para streaming
            _, jpeg = cv2.imencode(".jpg", frame)
            self.last_frame = jpeg.tobytes()

    def get_jpeg_frame(self):
        return self.last_frame

    def stop(self):
        self.running = False
        self.cap.release()


# Instancia global del detector
detector = HandDetectorThread()
```

---

---

# ▶️ **7. Ejecutar el servidor**

Desde la raíz del proyecto:

```bash
uvicorn app:app --reload
```

Deberías ver:

```
INFO:     Uvicorn running on http://127.0.0.1:8000
```

---

---

# 🌐 **8. Abrir el cliente web**

Ve a:

```
http://127.0.0.1:8000/client
```

✔ Verás la cámara
✔ El modelo de manos con landmarks
✔ Un cuadro que muestra:
`Gesto: hand_detected`
o
`Gesto: none`

---

---

# 🧠 **9. ¿Quieres añadir más gestos?**

Te puedo generar:

* ✊ **Puño**
* ✋ **Mano abierta**
* 👉 **Apuntar**
* 🤘 **Rock**
* 🤙 **Hang loose**
* ✌️ **Paz**
* 👌 **OK**
* 👍 **Like**

# Resultados 
![Imagen de WhatsApp 2025-11-13 a las 23 16 20_7d8947ca](https://github.com/user-attachments/assets/911e2d54-7654-489b-915f-288da2862f43)
![Imagen de WhatsApp 2025-11-13 a las 23 16 34_7cae8464](https://github.com/user-attachments/assets/b485efd4-d066-4f3a-be5a-12f4f78e23e3)
![Imagen de WhatsApp 2025-11-13 a las 23 16 50_7f229fe9](https://github.com/user-attachments/assets/83dcc39c-80d0-4259-bb37-56c3a05d0d52)
![Imagen de WhatsApp 2025-11-13 a las 23 17 14_44077c7b](https://github.com/user-attachments/assets/acfc17ef-76f1-4152-b8f0-08cfd6403a4f)
