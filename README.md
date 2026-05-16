# 🗣️ Jacky — Voz TTS en Español

<p align="center">
  <strong>Voz de personaje en español entrenada con Piper TTS</strong><br>
  Una voz estilo ratoncito de caricatura 🐭 — ideal para asistentes virtuales, mascotas digitales y proyectos creativos.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/idioma-español_(es--419)-blue" alt="Idioma">
  <img src="https://img.shields.io/badge/calidad-medium-green" alt="Calidad">
  <img src="https://img.shields.io/badge/sample_rate-22050_Hz-orange" alt="Sample Rate">
  <img src="https://img.shields.io/badge/formato-ONNX-purple" alt="Formato">
</p>

---

## 🔊 Muestra de Audio

Escuchá cómo suena la voz de Jacky con la siguiente frase:

> *"Hola soy Jacky, ¿qué hacemos hoy? Esta es una prueba para ver cómo se escucha mi voz."*

<audio src="./test.wav" controls></audio>

📥 **[Descargar audio de muestra (test.wav)](./test.wav)**

> Descargá el archivo y reproducilo en tu reproductor de audio favorito para escuchar la calidad de la voz.

---

## 📖 Descripción

**Jacky** es una voz TTS (Text-to-Speech) neuronal entrenada para español latinoamericano (`es-419`).

> ⚠️ **Nota:** Esta **no es una voz humana convencional**. Jacky tiene un timbre estilo **ratoncito de caricatura** 🐭 — agudo, expresivo y con personalidad. Está pensada para personajes, mascotas virtuales, asistentes con carácter, o cualquier proyecto donde quieras una voz divertida y distintiva en español.

Fue entrenada utilizando [piper1-gpl](https://github.com/OHF-Voice/piper1-gpl), el framework de entrenamiento de voces de [Piper TTS](https://github.com/rhasspy/piper), partiendo de un checkpoint pre-entrenado de español mexicano (`es_MX`).

Este repositorio contiene los archivos del modelo ya exportado, listo para usar en dos motores de síntesis de voz:

| Motor | Archivos necesarios |
|---|---|
| **Piper** | `jacky.onnx` + `jacky.onnx.json` |
| **Sherpa-ONNX** | `jacky.onnx` + `tokens.txt` + `espeak-ng-data/` |

---

## 📋 Especificaciones del Modelo

| Propiedad | Valor |
|---|---|
| **Nombre** | Jacky |
| **Idioma** | Español Latinoamericano (`es-419`) |
| **Hablantes** | 1 |
| **Calidad** | Medium |
| **Sample Rate** | 22,050 Hz |
| **Tipo de fonemas** | eSpeak-NG |
| **Formato** | ONNX |
| **Tamaño del modelo** | ~60 MB |
| **Versión de Piper** | 1.3.0 |

---

## 📂 Estructura del Repositorio

```
jacky-voice/
├── jacky.onnx           # Modelo ONNX (archivo principal)
├── jacky.onnx.json      # Configuración del modelo para Piper
├── jacky.json           # Configuración del modelo (copia)
├── tokens.txt           # Tabla de tokens para Sherpa-ONNX
├── espeak-ng-data/      # Datos de eSpeak-NG para Sherpa-ONNX
├── test.wav             # Audio de muestra
├── MODEL_CARD           # Ficha del modelo
└── README.md
```

---

## 🚀 Uso

### Opción 1 — Con Piper TTS

[Piper](https://github.com/rhasspy/piper) es el motor original para el que fue entrenada esta voz.

#### Instalación

```bash
pip install piper-tts
```

#### Sintetizar texto a audio

```bash
echo "Hola, soy Jacky. ¿Qué hacemos hoy?" | python -m piper \
  -m ./jacky.onnx \
  --output_file salida.wav
```

#### Sintetizar y reproducir directamente (Linux)

```bash
echo "Esta es una prueba para ver cómo se escucha mi voz." | python -m piper \
  -m ./jacky.onnx \
  --output-raw | aplay -r 22050 -f S16_LE -t raw -
```

#### Desde un archivo de texto

```bash
cat mi_texto.txt | python -m piper \
  -m ./jacky.onnx \
  --output_file salida.wav
```

---

### Opción 2 — Con Sherpa-ONNX

[Sherpa-ONNX](https://github.com/k2-fsa/sherpa-onnx) es un motor de inferencia multiplataforma optimizado para dispositivos embebidos y aplicaciones de escritorio.

#### Instalación

```bash
pip install sherpa-onnx
```

#### Uso con Python

```python
import sherpa_onnx
import soundfile as sf

tts_config = sherpa_onnx.OfflineTtsConfig(
    model=sherpa_onnx.OfflineTtsModelConfig(
        vits=sherpa_onnx.OfflineTtsVitsModelConfig(
            model="./jacky.onnx",
            tokens="./tokens.txt",
            data_dir="./espeak-ng-data",
        ),
        num_threads=2,
    ),
)

tts = sherpa_onnx.OfflineTts(tts_config)

audio = tts.generate("Hola, soy Jacky. Esta es una prueba con Sherpa-ONNX.")

sf.write("salida.wav", audio.samples, audio.sample_rate)
print("Audio guardado en salida.wav")
```

#### Uso desde la línea de comandos

```bash
python -c "
import sherpa_onnx, soundfile as sf
tts = sherpa_onnx.OfflineTts(sherpa_onnx.OfflineTtsConfig(
    model=sherpa_onnx.OfflineTtsModelConfig(
        vits=sherpa_onnx.OfflineTtsVitsModelConfig(
            model='./jacky.onnx',
            tokens='./tokens.txt',
            data_dir='./espeak-ng-data'),
        num_threads=2)))
audio = tts.generate('Hola, soy Jacky. ¿Cómo estás?')
sf.write('salida.wav', audio.samples, audio.sample_rate)
print('Listo!')
"
```

---

## ⚙️ Parámetros de Inferencia

La configuración del modelo incluye parámetros ajustables para controlar la voz generada:

| Parámetro | Valor por defecto | Descripción |
|---|---|---|
| `noise_scale` | `0.667` | Controla la variación en la voz. Valores más altos = más expresividad. |
| `length_scale` | `1.0` | Controla la velocidad del habla. `< 1.0` = más rápido, `> 1.0` = más lento. |
| `noise_w` | `0.8` | Controla la variación en la duración de los fonemas. |

### Ejemplo: ajustar velocidad en Piper

```bash
echo "Esto es más lento." | python -m piper \
  -m ./jacky.onnx \
  --length-scale 1.3 \
  --output_file lento.wav
```

```bash
echo "Esto es más rápido." | python -m piper \
  -m ./jacky.onnx \
  --length-scale 0.8 \
  --output_file rapido.wav
```

---

## 🏋️ Entrenamiento

La voz fue entrenada mediante **fine-tuning** usando [piper1-gpl](https://github.com/OHF-Voice/piper1-gpl), partiendo de un modelo pre-entrenado de español mexicano.

### Modelo base (pre-entrenado)

```
pretrained/es/es_MX/ald/medium/epoch=9999-step=1753600.ckpt
```

Este checkpoint proviene de las voces pre-entrenadas de Piper para `es_MX` y sirvió como punto de partida para transferir el conocimiento fonético del español al timbre particular de Jacky.

### Comando de entrenamiento

```bash
python -m piper.train fit \
  --data.voice_name "mi_voz" \
  --data.csv_path "corpus/metadata.csv" \
  --data.audio_dir "corpus/wavs" \
  --model.sample_rate 22050 \
  --data.espeak_voice "es-419" \
  --data.cache_dir "./cache" \
  --data.config_path "./mi_voz.json" \
  --data.batch_size 32 \
  --trainer.precision "bf16-mixed" \
  --ckpt_path "./lightning_logs/version_11/checkpoints/epoch=10687-step=22878.ckpt"
```

> El fine-tuning se realizó durante **~687 épocas adicionales** sobre el checkpoint pre-entrenado, con precisión mixta `bf16` para optimizar el uso de memoria y la velocidad de entrenamiento.

---

## 📄 Licencia

Este modelo fue entrenado con herramientas licenciadas bajo GPL. Consultá la licencia del repositorio [piper1-gpl](https://github.com/OHF-Voice/piper1-gpl) para más detalles sobre las condiciones de uso y distribución.

---

## 🙏 Créditos

- **[Piper TTS](https://github.com/rhasspy/piper)** — Motor de síntesis de voz neuronal.
- **[piper1-gpl](https://github.com/OHF-Voice/piper1-gpl)** — Framework de entrenamiento utilizado.
- **[Sherpa-ONNX](https://github.com/k2-fsa/sherpa-onnx)** — Motor de inferencia multiplataforma.
- **[eSpeak-NG](https://github.com/espeak-ng/espeak-ng)** — Conversor de grafemas a fonemas.
