# Mudu Bot: Geliştirme Ortamı Kurulum Kılavuzu

## 1. Giriş

Bu kılavuz, Mudu Bot'un geliştirme ortamının hazırlanması için gerekli adımları ve bileşenleri içermektedir. Yazılım geliştiriciler, robotik mühendisleri ve yapay zeka araştırmacıları için hazırlanmış olup, sistem gereksinimleri, kurulum adımları ve temel test prosedürlerini kapsamaktadır. Geliştirme ortamı, **Docker, ROS 2, Gazebo, Llama.cpp, Prolog, Whisper, OpenCV ve YOLO** gibi temel bileşenleri içermektedir.

Bu belge, farklı kullanım senaryoları için uygun olan sistem gereksinimlerini, kurulum adımlarını ve temel test prosedürlerini ayrıntılı olarak sunmaktadır.

## 2. Sistem Gereksinimleri

Mudu Bot geliştirme ortamı için önerilen sistem gereksinimleri aşağıda belirtilmiştir. Gereksinimler, basit geliştirme ortamından tam simülasyon çalıştırılmasına kadar farklı seviyelerde belirlenmiştir.

### Donanım Gereksinimleri

- **İşlemci:** Intel i7 / AMD Ryzen 7 veya Apple M1/M2/M3
- **Bellek:** Minimum 16GB (LLM ve simülasyon için önerilen 32GB)
- **Grafik İşlemci:** NVIDIA RTX 3060+ veya Apple Metal destekli GPU
- **Depolama:** Minimum 50GB boş alan

### Yazılım Gereksinimleri

| Bileşen           | macOS                                                               | Windows                                  |
| ----------------- | ------------------------------------------------------------------- | ---------------------------------------- |
| **Docker**        | Homebrew ile yüklenmeli                                             | Docker Desktop ile yüklenmeli            |
| **ROS 2**         | Docker ile çalıştırılmalı                                           | WSL 2 + Docker ile çalıştırılmalı        |
| **Gazebo**        | Docker üzerinden çalıştırılmalı                                     | WSL 2 + Docker ile çalıştırılmalı        |
| **Llama.cpp**     | Terminal üzerinden derlenmeli                                       | Git Bash veya WSL üzerinden derlenmeli   |
| **Prolog**        | SWI-Prolog veya Yapay Zeka için özel Prolog kütüphaneleri kurulmalı | SWI-Prolog veya WSL üzerinden yüklenmeli |
| **Whisper**       | Python üzerinden çalıştırılmalı                                     | Python ile çalıştırılmalı                |
| **OpenCV + YOLO** | Homebrew ile yüklenmeli                                             | Conda veya pip ile yüklenmeli            |

---

## 3. Gerekli Kurulumlar

### Docker Kurulumu

#### macOS

```sh
brew install --cask docker
open -a Docker
```

#### Windows

1. [Docker Desktop](https://www.docker.com/products/docker-desktop/) indirilip yüklenmelidir.
2. **WSL 2 desteği etkinleştirilmelidir:**

```sh
wsl --install
```

3. Docker başlatıldıktan sonra doğrulamak için:

```sh
docker --version
```

### ROS 2 ve Gazebo Kurulumu (Docker ile)

```sh
docker run -it --rm --net=host osrf/ros:iron-desktop
```

Alternatif olarak kalıcı bir geliştirme ortamı için Docker Compose kullanılabilir. Aşağıdaki içerik ile `compose.yml` dosyası oluşturulmalıdır:

### Docker Compose Dosyası

```yml
version: "3.8"
services:
  ros2:
    image: osrf/ros:iron-desktop
    container_name: ros2
    network_mode: host
    tty: true
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
      - $HOME/.Xauthority:/root/.Xauthority
      - $HOME:/root
    environment:
      - DISPLAY=$DISPLAY
      - XAUTHORITY=$HOME/.Xauthority
  gazebo:
    image: osrf/ros:iron-desktop
    container_name: gazebo
    network_mode: host
    tty: true
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
      - $HOME/.Xauthority:/root/.Xauthority
      - $HOME:/root
    environment:
      - DISPLAY=$DISPLAY
      - XAUTHORITY=$HOME/.Xauthority
```

### Docker Compose Kullanımı

Başlatmak için:

```sh
docker compose up -d
```

Sonlandırmak için:

```sh
docker compose down
```

### Llama.cpp Kurulumu

#### macOS

```sh
brew install cmake
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
git pull
make LLAMA_METAL=1
```

#### Windows (WSL 2 ile)

```sh
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make
```

Model indirme ve test işlemi:

```sh
curl -o model.gguf https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.1/resolve/main/mistral-7b-instruct-v0.1.Q4_K_M.gguf
./main -m model.gguf -p "Merhaba, nasılsın?"
```

### Prolog Kurulumu

#### macOS

```sh
brew install swi-prolog
```

#### Windows (WSL veya Native)

##### WSL Üzerinden

```sh
sudo apt install swi-prolog
```

##### Windows Native

1. [SWI-Prolog](https://www.swi-prolog.org/download/stable) resmi web sitesinden indirilmelidir.
2. Kurulum tamamlandıktan sonra doğrulamak için:

```sh
swipl --version
```

### Whisper Kurulumu

```sh
pip install openai-whisper ffmpeg
```

Ses kaydı transkripti oluşturma:

```sh
whisper kayit.mp3 --model small
```

### OpenCV ve YOLO Kurulumu

#### macOS

```sh
brew install opencv
pip install ultralytics
```

#### Windows

```sh
pip install opencv-python ultralytics
```

YOLO modelini test etmek için:

```python
from ultralytics import YOLO
model = YOLO("yolov8n.pt")
results = model("test.jpg")
results.show()
```

---

## 4. Sonuç ve Sonraki Adımlar

Mudu Bot geliştirme ortamı başarıyla hazırlanmıştır. Aşağıdaki bileşenler çalışır durumda olmalıdır:

| Bileşen           | macOS                      | Windows                  |
| ----------------- | -------------------------- | ------------------------ |
| **Docker**        | Çalışıyor                  | Çalışıyor                |
| **ROS 2**         | Docker içinde              | Docker + WSL 2 ile       |
| **Gazebo**        | Docker ile                 | WSL 2 içinde Docker ile  |
| **Llama.cpp**     | Çalışıyor (Metal destekli) | WSL 2 üzerinde çalışıyor |
| **Prolog**        | Çalışıyor                  | Çalışıyor                |
| **Whisper**       | Çalışıyor                  | Çalışıyor                |
| **OpenCV + YOLO** | Çalışıyor                  | Çalışıyor                |
