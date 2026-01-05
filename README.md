# 🧠 Review Insight – Sistema Completo de Análisis de Sentimiento

**Plataforma integral para clasificación de reseñas hoteleras**
_(Frontend React + Backends Node/Java + FastAPI ML + Persistencia Dual)_

---

## 🎯 **Visión General del Sistema**

Review Insight es una solución completa para el análisis de sentimiento de reseñas hoteleras en español, que combina **inteligencia artificial**, **persistencia redundante** y **arquitectura resiliente** para ofrecer un sistema robusto y escalable.

---

## 🏗️ **Arquitectura del Sistema**

## ⚙️ Arquitectura del Sistema (Resiliencia Total)

```

┌─────────────────┐       (1) HTTP POST      ┌─────────────────┐       (2) Proxy      ┌─────────────────┐
│                 │ ───────────────────────► │                 │ ──────────────────►  │                 │
│     Frontend    │    ERR_CONN_REFUSED      │  Node.js (Main) │                      │  FastAPI (ML)   │
│     (React)     │ ◄─────────────────────── │  (Port: 7860)   │ ◄──────────────────  │  (Inferencia)   │
└────────┬────────┘          (3) Fallback    └────────┬────────┘       JSON           └────────┬────────┘
         │                                            │                                        │
         │          (4) Re-intento Exitoso            │ 💾 Persistencia Dual                   │ (2.1) Proxy Java
         │    ┌───────────────────────────────────────┤                                        │
         │    │                                       │                                        │
         ▼    ▼                                       ▼                                        │
┌─────────────────┐                          ┌──────────────────────────────────┐              │
│                 │      HTTP POST (5)       │         Capa de Datos            │              │
│  Java / Spring  │ ───────────────────────► ├──────────────────────────────────┤              │
│    (Backup)     │ ◄─────────────────────── │ 🔥 Firestore  <──🔄──>  🍃 Mongo  │              │
└─────────────────┘      (6) JSON Resp       └──────────────────────────────────┘              │
         │                                                                                     │
         └─────────────────────────────────────────────────────────────────────────────────────┘
                (5.1) Comunicación FastAPI
```

### **Componentes del Sistema**

| Componente             | Tecnologías             | Propósito                       | Puerto |
| ---------------------- | ----------------------- | ------------------------------- | ------ |
| **Frontend**           | React + Vite + Tailwind | Interfaz de usuario interactiva | 5173   |
| **Backend Primario**   | Node.js + Express       | Orquestación principal          | 7860   |
| **Backend Secundario** | Java + Spring Boot      | Backup de alta disponibilidad   | 8080   |
| **Motor de ML**        | FastAPI + Scikit-learn  | Inferencia del modelo           | 8000   |
| **Base de Datos 1**    | Firebase Firestore      | Persistencia principal          | Cloud  |
| **Base de Datos 2**    | MongoDB Atlas           | Persistencia de respaldo        | Cloud  |

---

## 📊 **Características Principales**

### **🔄 Resiliencia y Alta Disponibilidad**

- **Failover automático** entre backends Node.js y Java
- **Persistencia dual** en Firebase y MongoDB
- **Sincronización automática** entre bases de datos
- **Exportación con redundancia** (intenta Firebase, fallback a MongoDB)

### **🎯 Clasificación Multiclase**

- **Modelo entrenado**: Logistic Regression con TF-IDF
- **Clasificación ternaria**: Negativo (0), Positivo (1), Neutro (3)
- **Dataset balanceado**: 1,455 muestras por clase (total: 4,365)
- **Métricas**: Accuracy 73.88%, F1-Score promedio 0.73

### **📱 Experiencia de Usuario**

- **Modo individual**: Análisis de una reseña a la vez
- **Modo batch**: Procesamiento de hasta 100 reseñas simultáneas
- **Visualización rica**: Tarjetas con emojis, barras de progreso, porcentajes
- **Exportación CSV**: Historial completo con timestamps

---

## 🚀 **Endpoints Disponibles (API REST)**

### **GET** `/health`

Verifica el estado del servicio y conexiones a bases de datos.

### **POST** `/sentiment`

Analiza sentimiento individual. Guarda en ambas bases automáticamente.

**Request:**

```json
{
  "text": "El hotel estaba impecable y el personal muy atento"
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "text": "El hotel estaba impecable...",
    "sentiment": "Positivo",
    "label": 1,
    "confidence": 0.93
  },
  "storage": {
    "firebase": "saved",
    "mongodb": "saved"
  }
}
```

### **POST** `/sentiment/batch`

Analiza múltiples reseñas simultáneamente (límite: 100).

### **GET** `/sentiment/export`

Exporta historial completo con redundancia automática.

### **GET** `/admin/sync` o `/sentiment/sync`

Sincroniza registros entre Firebase y MongoDB.

---

## 📁 **Estructuras de Proyectos**

### **1. Frontend (React + Vite)**

```
ReviewInsight/
├── src/
│   ├── components/     # Componentes UI (analyzer/, ui/)
│   ├── hooks/         # Custom hooks (useSentiment, useToast)
│   ├── lib/           # Utilidades y constantes
│   ├── pages/         # Páginas de la aplicación
│   └── App.tsx        # Componente raíz
```

### **2. Backend Node.js (Express)**

```
node-backend/
├── config/            # Configuraciones (firebaseConfig.js, mongoConfig.js)
├── server.js          # Servidor principal
├── serviceAccountKey.json
└── Dockerfile
```

### **3. Backend Java (Spring Boot)**

```
java-backend/
├── src/main/java/com.reviewinsight/
│   ├── config/        # FirebaseConfig, MongoConfig
│   ├── controller/    # SentimentController
│   ├── model/         # Entidades (ReviewEntity, ReviewRecord)
│   ├── repository/    # ReviewRepository (MongoDB)
│   ├── service/       # Servicios de negocio
│   └── application.properties
```

### **4. Data Science (FastAPI + ML)**

```
data-science/
├── models/            # Modelo serializado (.pkl)
├── data/              # Dataset de entrenamiento
├── notebooks/         # Experimentación y análisis
├── main.py           # Servidor FastAPI
└── Dockerfile
```

---

## ⚙️ **Configuración y Despliegue**

### **Requisitos Mínimos**

- **Node.js**: v18+ (para backend Node.js y frontend)
- **Java**: 17+ (para backend Spring Boot)
- **Python**: 3.9+ (para microservicio FastAPI)
- **Docker**: Para despliegue en contenedores

### **Variables de Entorno Clave**

**Frontend (.env):**

```env
VITE_API_URL=http://localhost:7860
VITE_FALLBACK_API_URL=http://localhost:8080
```

**Backend Node.js:**

```javascript
// mongoConfig.js
const uri = "mongodb+srv://usuario:contraseña@cluster.mongodb.net/";
// server.js
const FASTAPI_URL = "http://127.0.0.1:8000";
```

**Backend Java (application.properties):**

```properties
fastapi.url=http://localhost:8000
spring.data.mongodb.uri=mongodb+srv://usuario:contraseña@cluster.mongodb.net/review_insight
```

### **Ejecución Local**

```bash
# 1. Iniciar FastAPI (ML)
uvicorn main:app --port 8000

# 2. Iniciar Backend Node.js (primario)
node server.js

# 3. Iniciar Backend Java (secundario)
.\mvnw spring-boot:run

# 4. Iniciar Frontend
npm run dev
```

---

## 🧠 **Modelo de Machine Learning**

### **Pipeline de Entrenamiento**

1. **Dataset**: Reseñas hoteleras en español (hotel_reviews_andalucia.csv)
2. **Balanceo**: Submuestreo a 1,455 muestras por clase
3. **Vectorización**: TF-IDF con unigramas y bigramas
4. **Clasificador**: Logistic Regression multiclase
5. **Serialización**: Pipeline completo en `.pkl`

### **Métricas de Rendimiento**

| Métrica   | Negativo (0) | Positivo (1) | Neutro (3) | Global |
| --------- | ------------ | ------------ | ---------- | ------ |
| Precision | 0.76         | 0.77         | 0.68       | -      |
| Recall    | 0.82         | 0.82         | 0.57       | -      |
| F1-Score  | 0.79         | 0.79         | 0.62       | 0.73   |
| Accuracy  | -            | -            | -          | 73.88% |

---

## 🔐 **Consideraciones de Seguridad**

### **Protección de Datos**

- Credenciales en archivos ignorados por Git
- Validación de entrada en todos los endpoints
- Límites de rate limiting para endpoints batch
- CORS configurado para dominios específicos

### **Redundancia Operativa**

- **Persistencia dual**: Datos en dos proveedores cloud diferentes
- **Failover backend**: Frontend conmuta automáticamente entre Node.js y Java
- **Exportación resiliente**: Fallback de Firebase a MongoDB
- **Sincronización automática**: Endpoint para reconciliar discrepancias

---

## 📈 **Ventajas Arquitectónicas**

### **🔥 Firebase Firestore**

- Tiempo real y escalabilidad automática
- Ideal para operaciones frecuentes de escritura
- Integración nativa con ecosistema Google Cloud

### **🍃 MongoDB Atlas**

- Flexibilidad en consultas complejas
- Rendimiento optimizado para operaciones de lectura
- Control granular sobre índices y particionamiento

### **🔄 Sistema Combinado**

1. **Alta disponibilidad**: Tolerancia a fallos de un proveedor cloud
2. **Respaldo automático**: Datos duplicados sin intervención manual
3. **Migración facilitada**: Transición gradual entre tecnologías
4. **Rendimiento optimizado**: Cada base para su caso de uso ideal

---

## 🧪 **Casos de Uso**

### **Hotelería y Hospitality**

- Monitoreo de reputación en tiempo real
- Análisis de feedback post-estadía
- Identificación de áreas de mejora

### **Gestión de Calidad**

- Clasificación automática de quejas y elogios
- Seguimiento de tendencias temporales
- Benchmarking competitivo

### **Investigación de Mercado**

- Análisis de sentimiento a escala
- Identificación de patrones en reseñas
- Validación de hipótesis de mejora

---

## 🚢 **Despliegue en Producción**

El sistema utiliza una infraestructura **Multi-Cloud** distribuida para garantizar la máxima disponibilidad y resiliencia de los servicios.

### **🌍 Servicios y Hosting**

- **Frontend (React)**: Desplegado en **Vercel** con integración de CI/CD para actualizaciones automáticas.
- **Backend Primario (Node.js)**: Alojado en **Hugging Face**, funcionando como el punto de entrada principal del sistema.
- **Motor de ML (FastAPI)**: Desplegado en **Hugging Face**, optimizado para la ejecución del pipeline de Scikit-learn.
- **Backend de Backup (Java/Spring)**: Implementado en **AWS** mediante una instancia dedicada y gestión de artefactos `.jar` a través de **AWS S3 Buckets**.

### **💾 Persistencia en la Nube**

- **Firebase Firestore**: Base de datos NoSQL principal para sincronización en tiempo real.
- **MongoDB Atlas**: Persistencia redundante y escalable utilizada para respaldo y consultas analíticas.

---

### **Uso Académico**

Ideal para:

- Cursos de arquitectura de software
- Clases de machine learning aplicado
- Proyectos de integración de sistemas
- Casos de estudio de microservicios

---

## ✨ **Conclusión**

Review Insight demuestra una **arquitectura moderna y resiliente** que combina lo mejor de múltiples tecnologías:

- **🎯 Frontend reactivo** con experiencia de usuario intuitiva
- **🔄 Backends redundantes** para máxima disponibilidad
- **🧠 Modelo ML balanceado** con métricas sólidas
- **🗄️ Persistencia dual** que garantiza integridad de datos
- **🐳 Infraestructura containerizada** para fácil despliegue

Este sistema está listo para producción y puede escalar desde startups hasta grandes cadenas hoteleras, adaptándose a diferentes volúmenes y requerimientos de negocio.

**✨ Review Insight – Inteligencia de sentimiento al servicio de la hospitalidad moderna**
