# Zero-Trust Network Sandbox

Implementación de arquitectura Zero Trust usando Docker Compose y Kubernetes con NetworkPolicies para demostrar micro-segmentación de red y prevención de movimiento lateral.

## Información del Equipo

**Integrantes:**

- Pineda García Diego Ronaldo
- Torres Fuero Mateo Lorenzo
- Trujillo Serva Luis Andre

**Videos de Presentación:**

- Sprint 1: https://www.youtube.com/watch?v=pCTwkz9yQgE
- Sprint 2: https://www.youtube.com/watch?v=5JV09eLksvY

## Descripción del Proyecto

Este proyecto simula un escenario de seguridad donde un atacante interno intenta moverse lateralmente en una red para acceder a servicios que no debería. El objetivo es demostrar cómo las NetworkPolicies de Kubernetes implementan el principio de Zero Trust, reduciendo la superficie de ataque.

### Componentes

El proyecto consta de tres servicios Python:

- **Frontend**: Aplicación web Flask que se comunica con el backend
- **Backend**: API de negocio que debe ser protegida
- **Attacker**: Scanner de red que simula una amenaza interna

### Comparación de Escenarios

**Docker Compose (sin NetworkPolicies):**

- El attacker puede conectarse a servicios internos
- Movimiento lateral es posible
- Superficie de ataque: 100%

**Kubernetes (con NetworkPolicies):**

- Solo se permiten comunicaciones explícitamente autorizadas
- El attacker está completamente aislado
- Superficie de ataque: 0%

## Requisitos Previos

- Docker Desktop instalado y corriendo
- kubectl instalado (para la parte de Kubernetes)
- Minikube o un cluster de Kubernetes local
- Python 3.12+ (opcional, para desarrollo local)
- make (opcional, facilita la ejecución)

## Instalación de Requisitos

### Docker Desktop

**Linux:**

```bash
# Opción 1: Usando el script oficial (recomendado)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Opción 2: Desde el gestor de paquetes de tu distribución
# Consultar: https://docs.docker.com/engine/install/

# Agregar tu usuario al grupo docker (para no usar sudo)
sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar para aplicar cambios
```

**macOS:**

```bash
# Descargar Docker Desktop desde:
# https://www.docker.com/products/docker-desktop

# Alternativamente, usando Homebrew:
brew install --cask docker
```

**Windows:**

```powershell
# Descargar Docker Desktop desde:
# https://www.docker.com/products/docker-desktop

# Requiere WSL 2. Para habilitar WSL 2:
wsl --install
```

### kubectl

**Linux:**

```bash
# Opción 1: Descarga directa
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Opción 2: Usando gestor de paquetes (ejemplo con apt)
sudo apt-get update
sudo apt-get install -y kubectl
```

**macOS:**

```bash
# Opción 1: Usando Homebrew (recomendado)
brew install kubectl

# Opción 2: Descarga directa
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

**Windows:**

```powershell
# Opción 1: Usando Chocolatey
choco install kubernetes-cli

# Opción 2: Descarga manual desde:
# https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

# Opción 3: Si tienes Docker Desktop, kubectl ya está incluido
```

### Minikube

**Linux:**

```bash
# Descarga e instalación
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**macOS:**

```bash
# Usando Homebrew (recomendado)
brew install minikube

# Alternativamente, descarga directa
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

**Windows:**

```powershell
# Opción 1: Usando Chocolatey
choco install minikube

# Opción 2: Descarga manual del instalador desde:
# https://minikube.sigs.k8s.io/docs/start/

# Opción 3: Usando winget
winget install Kubernetes.minikube
```

**Iniciar Minikube (todos los sistemas):**

```bash
minikube start
```

### Python 3.12+

**Linux:**

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install python3.12 python3.12-venv python3-pip

# Fedora
sudo dnf install python3.12

# Queda a elección del usuario usar pyenv para gestionar versiones
```

**macOS:**

```bash
# Usando Homebrew
brew install python@3.12

# Alternativamente, descargar desde python.org
# https://www.python.org/downloads/
```

**Windows:**

```powershell
# Opción 1: Descarga desde python.org (recomendado)
# https://www.python.org/downloads/

# Opción 2: Usando Chocolatey
choco install python --version=3.12.0

# Opción 3: Usando Microsoft Store
# Buscar "Python 3.12" en Microsoft Store
```

### Make

**Linux:**

```bash
# Ubuntu/Debian
sudo apt-get install build-essential

# Fedora
sudo dnf install make

# Arch
sudo pacman -S make
```

**macOS:**

```bash
# make viene incluido con Xcode Command Line Tools
xcode-select --install

# Alternativamente
brew install make
```

**Windows:**

```powershell
# Opción 1: Usando Chocolatey
choco install make

# Opción 2: Instalar Git Bash (incluye make)
# Descargar desde: https://git-scm.com/downloads

# Opción 3: Usar WSL y seguir instrucciones de Linux
```

**Nota:** Si no deseas instalar make, puedes ejecutar los scripts directamente desde el directorio `scripts/`.

### Verificación de Instalación

Después de instalar, verifica que todo está correctamente instalado:

```bash
docker --version
kubectl version --client
minikube version
python3 --version
make --version
```

## Inicio Rápido

### Opción 1: Usando Makefile

```bash
# Preparar entorno (primera vez)
make setup

# Levantar servicios con Docker Compose
make compose-up

# Ejecutar scanner para validar conectividad
make compose-scan

# Ver resultados
cat reports/compose-connectivity.json

# Bajar servicios
make compose-down
```

### Opción 2: Usando Scripts Directamente

```bash
# Levantar stack de Docker Compose
./scripts/compose-up.sh

# Ejecutar scanner
./scripts/compose-scan.sh

# Bajar stack
./scripts/compose-down.sh
```

## Ejecución con Kubernetes

```bash
# Aplicar manifiestos de Kubernetes
./scripts/k8s-apply.sh

# Ejecutar scanner en K8s
./scripts/k8s-scan.sh

# Ver resultados
cat reports/k8s-connectivity.json

# Limpiar recursos
./scripts/k8s-clean.sh
```

## Estructura del Proyecto

```
PC04/
├── services/           # Código de las aplicaciones
│   ├── frontend/       # Aplicación web Flask
│   ├── backend/        # API de negocio
│   └── attacker/       # Scanner de red
├── compose/            # Configuración de Docker Compose
│   ├── docker-compose.yml
│   └── .env.example
├── k8s/                # Manifiestos de Kubernetes
│   ├── namespace.yaml
│   ├── *-deployment.yaml
│   ├── *-service.yaml
│   └── networkpolicy-*.yaml
├── scripts/            # Scripts de automatización
│   ├── compose-*.sh
│   └── k8s-*.sh
├── docs/               # Documentación del proyecto
│   ├── vision.md
│   ├── metricas.md
│   ├── risk-register.md
│   └── definition-of-done.md
├── reports/            # Reportes generados por los scanners
└── Makefile            # Comandos de automatización
```

## Comandos Disponibles

Para ver todos los comandos disponibles:

```bash
make help
```

Comandos principales:

- `make setup`: Preparar entorno de desarrollo
- `make dev`: Levantar entorno local (alias de compose-up)
- `make compose-up`: Levantar stack de Docker Compose
- `make compose-scan`: Ejecutar scanner en Compose
- `make compose-down`: Bajar stack de Compose
- `make k8s-apply`: Aplicar manifiestos de Kubernetes
- `make k8s-scan`: Ejecutar scanner en K8s
- `make k8s-clean`: Limpiar recursos de K8s
- `make clean`: Limpiar archivos temporales

## Verificación de Resultados

Después de ejecutar los scanners, puedes comparar los resultados:

```bash
# Ver reporte de Docker Compose
cat reports/compose-connectivity.json

# Ver reporte de Kubernetes
cat reports/k8s-connectivity.json

# Comparar ambos entornos
python scripts/compare-reports.py
```

Los reportes muestran qué puertos están abiertos desde el contenedor attacker, evidenciando la diferencia de seguridad entre ambas arquitecturas.

## Arquitectura de Red

### Docker Compose

- **frontend-net**: Red donde está el frontend
- **backend-net**: Red compartida entre frontend y backend
- **isolated-net**: Red aislada para el attacker

El attacker se conecta manualmente a backend-net durante el escaneo para simular una brecha de seguridad.

### Kubernetes

Todas las comunicaciones están bloqueadas por defecto mediante NetworkPolicies:

- **default-deny**: Bloquea todo tráfico ingress
- **allow-frontend-to-backend**: Permite solo frontend -> backend:5000
- **allow-frontend-egress**: Permite egress del frontend
- **isolate-attacker**: Bloquea completamente al attacker
- **allow-dns**: Permite resolución DNS para todos los pods

## Documentación Adicional

- `docs/vision.md`: Contexto y objetivos del proyecto
- `docs/metricas.md`: Métricas de los sprints
- `docs/risk-register.md`: Registro de riesgos identificados
- `docs/definition-of-done.md`: Criterios de completitud

## Solución de Problemas

### Docker Compose no levanta servicios

```bash
# Verificar que Docker está corriendo
docker info

# Ver logs de servicios
cd compose && docker compose logs
```

### Kubernetes: Pods no inician

```bash
# Ver estado de pods
kubectl get pods -n zero-trust-lab

# Ver logs de un pod específico
kubectl logs -n zero-trust-lab <pod-name>

# Describir un pod para ver eventos
kubectl describe pod -n zero-trust-lab <pod-name>
```

### Scanner no genera reporte

Verificar que:

- El stack está corriendo (`docker compose ps` o `kubectl get pods -n zero-trust-lab`)
- El contenedor attacker existe y está activo
- El directorio `reports/` existe

## Tecnologías Utilizadas

- Python 3.12
- Flask 3.0.0
- Docker y Docker Compose
- Kubernetes
- Bash scripting
- Make

## Desarrollo

Para trabajar en el código localmente:

```bash
# Crear entorno virtual
python3 -m venv venv
source venv/bin/activate  # En Linux/Mac

# Instalar dependencias
pip install -r services/frontend/requirements.txt
pip install -r services/backend/requirements.txt
pip install -r services/attacker/requirements.txt

# Ejecutar un servicio localmente
cd services/frontend
python app.py
```

## Referencias

- Documento del examen: `Practica_calificada4-CC3S2 - Seccion B.pdf`
- Zero Trust Architecture: NIST SP 800-207
- Kubernetes NetworkPolicies: https://kubernetes.io/docs/concepts/services-networking/network-policies/
