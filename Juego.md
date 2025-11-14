

# 🎮 Plataforma de Juego 2D 

Este proyecto es un **juego de plataformas en 2D** creado con **Python + Pygame**.
Incluye:

✔ Movimiento del jugador
✔ Saltos
✔ Plataformas
✔ Obstáculos
✔ Ítems
✔ Enemigos
✔ Colisiones

Este README permite que **cualquier persona**, incluso sin experiencia, pueda copiar los comandos y ejecutar el juego correctamente.

---

---

# 🧩 **1. Requisitos**

Asegúrate de tener Python 3.9+ instalado.
Puedes verificarlo ejecutando:

```bash
python --version
```

---

---

# 🏗️ **2. Crear Carpeta del Proyecto**

```bash
mkdir platform-game
cd platform-game
```

---

---

# 🧪 **3. Crear Entorno Virtual (opcional pero recomendado)**

```bash
python -m venv venv
```

Activar entorno virtual:

### En Windows:

```bash
venv\Scripts\activate
```

---

---

# 📦 **4. Instalar Dependencias**

El juego usa solo **pygame**, que instalamos con:

```bash
pip install pygame
```

---

---

# 📁 **5. Estructura del Proyecto**

El usuario debe crear estos archivos:

```
platform-game/
 ├── game.py
 ├── player.py
 ├── platform.py
 ├── item.py
 ├── obstacle.py
 └── enemy.py
```

---

---

# 📜 **6. Códigos de Cada Archivo**

Aquí está cada archivo listo para **copiar y pegar**.

---

## ✅ **player.py**

```python
import pygame

class Player:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, 40, 60)
        self.color = (0, 0, 255)
        self.vel_y = 0
        self.jump_speed = -15
        self.gravity = 1

    def move(self, keys):
        if keys[pygame.K_LEFT]:
            self.rect.x -= 5
        if keys[pygame.K_RIGHT]:
            self.rect.x += 5

    def jump(self):
        if self.vel_y == 0:
            self.vel_y = self.jump_speed

    def apply_gravity(self):
        self.vel_y += self.gravity
        self.rect.y += self.vel_y
        if self.rect.y > 540:
            self.rect.y = 540
            self.vel_y = 0

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
```

---

## ✅ **platform.py**

```python
import pygame

class Platform:
    def __init__(self, x, y, width, height):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = (0, 255, 0)

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
```

---

## ✅ **item.py**

```python
import pygame

class Item:
    def __init__(self, x, y):
        self.rect = pygame.Rect(x, y, 20, 20)
        self.color = (255, 255, 0)

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
```

---

## ✅ **enemy.py**

```python
import pygame

class Enemy:
    def __init__(self, x, y, width, height, speed=2):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = (255, 100, 0)
        self.speed = speed

    def update(self):
        self.rect.x += self.speed
        if self.rect.x < 0 or self.rect.x > 760:
            self.speed *= -1

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
```

---

##  **obstacle.py **

```python
import pygame

class Obstacle:
    def __init__(self, x, y, width, height):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = (255, 0, 0)  # rojo fuerte

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
```

---

## 🎮 **game.py (archivo principal)**

```python
import pygame
from player import Player
from platform import Platform
from item import Item
from obstacle import Obstacle
from enemy import Enemy

pygame.init()
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Juego de Plataformas")
clock = pygame.time.Clock()

player = Player(100, 540)

platforms = [
    Platform(0, 580, 800, 20),
    Platform(200, 450, 200, 20),
    Platform(500, 350, 200, 20),
]

items = [Item(250, 420), Item(550, 320)]

obstacles = [Obstacle(400, 560, 40, 20)]

enemies = [Enemy(300, 560, 40, 40, speed=3)]

running = True
while running:
    screen.fill((30, 30, 30))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                player.jump()

    keys = pygame.key.get_pressed()
    player.move(keys)
    player.apply_gravity()

    for platform in platforms:
        if player.rect.colliderect(platform.rect) and player.vel_y > 0:
            player.rect.bottom = platform.rect.top
            player.vel_y = 0

    player.draw(screen)

    for p in platforms:
        p.draw(screen)

    for item in items:
        if player.rect.colliderect(item.rect):
            items.remove(item)
        else:
            item.draw(screen)

    for ob in obstacles:
        ob.draw(screen)

    for e in enemies:
        e.update()
        e.draw(screen)

    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

---

---

# ▶️ **7. Ejecutar el juego**

```bash
python game.py
```

Esto abrirá la ventana del juego.

---

---

# 🎯 **8. Controles**

| Tecla | Acción          |
| ----- | --------------- |
| ⬅     | Mover izquierda |
| ➡     | Mover derecha   |
| SPACE | Saltar          |

---

---

# 🎉 Resultados

![Imagen de WhatsApp 2025-11-13 a las 21 11 21_bb829241](https://github.com/user-attachments/assets/2d69574c-7ab5-4c3c-9ebf-4859f36895af)
![Imagen de WhatsApp 2025-11-13 a las 21 28 12_e84233e6](https://github.com/user-attachments/assets/d29df654-06d6-4f84-a2a0-d9b3c72b6ee4)
![Imagen de WhatsApp 2025-11-13 a las 21 30 20_9f6066c1](https://github.com/user-attachments/assets/3f17f5f8-e8cc-4aff-bb54-ebf5a33355dd)
![Imagen de WhatsApp 2025-11-13 a las 21 58 35_4fd2d278](https://github.com/user-attachments/assets/ed06426d-cb09-4519-8bd2-feeff73cbb2a)
![Imagen de WhatsApp 2025-11-13 a las 22 01 23_8032d5ed](https://github.com/user-attachments/assets/a14217a2-d124-42b5-8788-9a88b9f97680)
