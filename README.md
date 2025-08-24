# Parcial I — Patrones Arquitectónicos Avanzados

**Proyecto:** Sistema de Gestión de Pedidos  
**Componentes:** Base de datos (PostgreSQL) · Backend (Spring Boot) · Frontend (React/Angular/Vue)  
**Entrega Continua:** Helm + ArgoCD

---

## 🚀 Instalación manual con Helm

### 1. Agregar repositorio de Bitnami (para PostgreSQL)
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 2. Instalar en entorno de desarrollo
```bash
kubectl create namespace pedido-dev

helm upgrade --install pedido-app-dev charts/pedido-app   -n pedido-dev   -f charts/pedido-app/values-dev.yaml
```

### 3. Instalar en entorno de producción
```bash
kubectl create namespace pedido-prod

helm upgrade --install pedido-app-prod charts/pedido-app   -n pedido-prod   -f charts/pedido-app/values-prod.yaml
```

---

## 🔄 Integración con ArgoCD

1. ArgoCD gestiona **dos entornos**:
   - `pedido-app-dev`
   - `pedido-app-prod`

2. Cada entorno está definido en:
   - `environments/dev/application.yaml`
   - `environments/prod/application.yaml`

3. En estas definiciones, el **Application** de ArgoCD **apunta a un repositorio Helm Chart** (ejemplo: `https://usuario.github.io/helm-chart/`) en lugar de un repo de manifests plano.  
   - Allí se encuentra publicado el paquete `.tgz` generado a partir del chart `pedido-app`.  
   - El campo `chart` y `targetRevision` permiten a ArgoCD instalar la versión correspondiente.

4. Flujo GitOps:
   - Modificar `values-dev.yaml` o `values-prod.yaml` en el repositorio de origen.
   - Hacer commit y push.
   - El pipeline empaqueta el chart y lo publica en el repo de Helm Charts.
   - ArgoCD, al estar apuntando a ese repo de Helm, detecta la nueva versión y sincroniza automáticamente el clúster (sin comandos manuales).

---

## 🌍 Endpoints de acceso

### Entorno Dev
- **URL base:** `http://dev.167.172.14.0.nip.io`

### Entorno Prod
- **URL base:** `http://prod.167.172.14.0.nip.io`


##### 1. Health check
- **Método:** `GET`  
- **Ruta:** `/health`  
- **URL:** `http://dev.167.172.14.0.nip.io/health`
- **Descripción:** Verifica que la aplicación y la conexión a la base de datos estén disponibles.  
- **Ejemplo de respuesta exitosa:**
```json
{ "status": "ok", "db": "up" }
```

---

##### 2. Crear un pedido
- **Método:** `POST`  
- **Ruta:** `/orders`  
- **URL:** `http://dev.167.172.14.0.nip.io/orders`
- **Body esperado (JSON):**
```json
{
  "nombre": "Juan",
  "pedido": "Pizza Margarita",
  "cantidad": 2
}
```
- **Respuesta (201 Created):** Pedido recién insertado.

---

##### 3. Listar pedidos
- **Método:** `GET`  
- **Ruta:** `/orders`  
- **URL:** `http://dev.167.172.14.0.nip.io/orders`
- **Descripción:** Devuelve todos los pedidos registrados en orden descendente por `id`.  
- **Ejemplo de respuesta:**
```json
[
  {
    "id": 1,
    "nombre": "Juan",
    "pedido": "Pizza Margarita",
    "cantidad": 2,
    "creado_en": "2025-08-24T16:34:21.123Z"
  }
]
```

---

## 📺 Demostración en vivo

- **URL:** `https://youtu.be/OwuagonhORo?si=v11_qrsdJK4WnAqd`

---

## Otros Repositorios Usados
- **URL:** `https://github.com/Ritatrcr/delivery-app.git`
- **URL:** `https://github.com/Ritatrcr/manifiestos-delivery-app.git`
- **URL:** `https://github.com/Ritatrcr/helm-chart.git`


## Repositorio Helm - GitHub Pages
- **URL:** `https://ritatrcr.github.io/helm-chart/`



