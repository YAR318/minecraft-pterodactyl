# 🎮 Pterodactyl & Minecraft Infrastructure

Repositorio de infraestructura para el despliegue de un panel **Pterodactyl** y servidores de **Minecraft** sobre Docker en un Droplet de DigitalOcean.

---

## 🏗️ Arquitectura

| Componente         | Tecnología              | Descripción                                   |
|--------------------|-------------------------|-----------------------------------------------|
| **Servidor Host**  | DigitalOcean Droplet    | Ubuntu 24.04 LTS                              |
| **Panel Web**      | Pterodactyl Panel v1.12 | Interfaz de administración de servidores      |
| **Motor**          | Pterodactyl Wings v1.12 | Backend que gestiona los contenedores Docker  |
| **Contenedores**   | Docker                  | Aislamiento de cada servidor de Minecraft     |
| **SSL**            | Let's Encrypt (Certbot) | Certificados HTTPS automáticos                |
| **Proxy Reverso**  | Nginx                   | Enruta el tráfico web al panel                |
| **Base de Datos**  | MariaDB                 | Almacena configuración del panel              |

---

## 🌐 Acceso al Panel

- **URL:** `https://panel.yeremi.work`
- **Usuario admin:** `Maru`

---

## 📂 Rutas Clave del Sistema

```
/var/www/pterodactyl/          → Archivos del panel web
/var/log/pterodactyl/          → Logs del sistema
/var/lib/pterodactyl/volumes/  → Mundos de Minecraft (Docker Volumes)
/etc/pterodactyl/              → Configuración de Wings
```

---

## 🌿 Flujo de Ramas (Git Flow)

```
main          ← Producción (solo merges desde staging aprobados)
  └── staging ← Integración y pruebas finales
        ├── Dev-Yeremi    ← Desarrollo individual Yeremi
        └── Dev-Guillermo ← Desarrollo individual Guillermo
```

### Reglas del flujo:
1. Cada desarrollador trabaja en su rama `Dev-*`.
2. Cuando la feature está lista, se hace PR hacia `staging`.
3. Se prueba en `staging`. Si todo está correcto, se hace merge a `main`.

---

## 🔥 Puertos Abiertos (Firewall UFW)

| Puerto    | Protocolo | Uso                         |
|-----------|-----------|-----------------------------|
| `80`      | TCP       | HTTP (redirección a HTTPS)  |
| `443`     | TCP       | HTTPS (Panel web)           |
| `8080`    | TCP       | Wings API                   |
| `2022`    | TCP       | Wings SFTP                  |
| `25565`   | TCP/UDP   | Minecraft (por defecto)     |

---

## 👥 Equipo

| Desarrollador | Rama Git        | Rol            |
|---------------|-----------------|----------------|
| Yeremi Gomez  | `Dev-Yeremi`    | Lead / DevOps  |
| Guillermo     | `Dev-Guillermo` | Developer      |
