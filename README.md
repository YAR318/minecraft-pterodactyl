# 🎮 Pterodactyl & Minecraft Infrastructure

Infraestructura **100% Dockerizada** para el despliegue de un panel Pterodactyl y servidores de Minecraft sobre Docker en DigitalOcean (o cualquier VPS con Docker).

---

## 🏗️ Arquitectura

```
docker-compose.yml
├── 🐋 nginx      → Reverse proxy + SSL (puerto 80/443)
├── 🐋 panel      → Laravel/PHP-FPM (Pterodactyl Panel)
├── 🐋 database   → MariaDB 10.11
├── 🐋 cache      → Redis 7
└── 🐋 wings      → Pterodactyl Wings (monta Docker socket)
                        ↓
                 🎮 Servidor Minecraft 1   (contenedor Docker)
                 🎮 Servidor Minecraft 2   (contenedor Docker)
                 🎮 Servidor Minecraft N
```

| Componente       | Tecnología                       | Descripción                          |
|------------------|----------------------------------|--------------------------------------|
| **Servidor Host**| Cualquier VPS (Ubuntu 24.04)     | DigitalOcean, Hetzner, AWS, etc.     |
| **Panel Web**    | Pterodactyl Panel v1.12          | Interfaz de administración           |
| **Motor**        | Pterodactyl Wings v1.12          | Backend que gestiona Docker          |
| **Contenedores** | Docker                           | Un contenedor por servidor Minecraft |
| **SSL**          | Let's Encrypt                    | Certificados HTTPS automáticos       |
| **Proxy**        | Nginx                            | Enruta tráfico web al panel          |

---

## 🚀 Despliegue Rápido (nuevo servidor)

### Requisitos
- Ubuntu 22.04 / 24.04
- Docker + Docker Compose instalados
- Dominio apuntando a la IP del servidor
- Certificado SSL en `/etc/letsencrypt/live/<dominio>/`

### Pasos

```bash
# 1. Clonar el repositorio
git clone https://github.com/YAR318/minecraft-pterodactyl.git
cd minecraft-pterodactyl

# 2. Configurar variables de entorno
cp .env.example .env
nano .env   # ← Editar passwords y dominio

# 3. Levantar todos los servicios
docker compose -f docker-compose.yml up -d

# 4. Ver estado de los servicios
docker compose ps
docker compose logs -f panel
```

### Post-despliegue: Conectar Wings al Panel

1. Ir a `https://panel.yeremi.work/admin/nodes`
2. Crear un nuevo nodo
3. En la pestaña **Configuration** → copiar el comando **Auto-Deploy**
4. Ejecutarlo en el servidor (escribe el config en `/etc/pterodactyl/config.yml`)
5. `docker compose restart wings`

---

## 💻 Desarrollo Local

```bash
# Levantar entorno local (usa docker-compose.override.yml automáticamente)
docker compose up -d

# Panel disponible en: http://localhost:8000
```

---

## 🌐 Acceso al Panel

- **URL Producción:** `https://panel.yeremi.work`
- **Usuario admin:** `Maru`

---

## 📂 Estructura del Repositorio

```
minecraft-pterodactyl/
├── docker-compose.yml           # Producción
├── docker-compose.override.yml  # Desarrollo local
├── .env.example                 # Template de variables
├── config/
│   ├── nginx/
│   │   ├── panel.conf           # Nginx producción (SSL)
│   │   └── panel.local.conf     # Nginx desarrollo (sin SSL)
│   └── wings/
│       └── config.yml.example   # Template config Wings
└── README.md
```

---

## 🔥 Puertos del Servidor

| Puerto      | Protocolo | Uso                          |
|-------------|-----------|------------------------------|
| `80`        | TCP       | HTTP → redirige a HTTPS      |
| `443`       | TCP       | HTTPS (Panel web)            |
| `8080`      | TCP       | Wings API                    |
| `2022`      | TCP       | Wings SFTP                   |
| `25565`     | TCP/UDP   | Minecraft (por defecto)      |
| `25566-25575`| TCP/UDP  | Minecraft (servidores extra) |

---

## 🌿 Flujo de Trabajo Git

```
main          ← Producción (solo merges desde staging aprobados)
  └── staging ← Integración y pruebas
        ├── Dev-Yeremi    ← Desarrollo Yeremi
        └── Dev-Guillermo ← Desarrollo Guillermo
```

### Reglas:
1. Trabajar siempre en tu rama `Dev-*`
2. PR de `Dev-*` → `staging` cuando esté listo
3. De `staging` → `main` cuando esté probado y aprobado

---

## 🔄 Migrar de Host

```bash
# En el servidor ACTUAL — exportar la base de datos
docker compose exec database mysqldump -u pterodactyl -p panel > backup_panel.sql

# En el NUEVO servidor — restaurar
docker compose up -d database
docker compose exec -T database mysql -u pterodactyl -p panel < backup_panel.sql
docker compose up -d
```

---

## 👥 Equipo

| Desarrollador | Rama Git        | Rol            |
|---------------|-----------------|----------------|
| Yeremi Gomez  | `Dev-Yeremi`    | Lead / DevOps  |
| Guillermo     | `Dev-Guillermo` | Developer      |
