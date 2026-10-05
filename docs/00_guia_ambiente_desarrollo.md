# SG-Prod · Guía de configuración del ambiente de desarrollo

**Proyecto:** SG-Prod — Tablero de mando gerencial para piso de manufactura
**Stack:** Python 3.12/3.13 · Django 5.2 LTS · PostgreSQL (ya desplegado) · Docker solo para la BD (fuera de nuestro alcance) · virtualenv + pip
**Equipo:** 7 personas (Rocío, Martha, Eduardo, Ángela, Karen, Mariana, Iván) — perfiles junior
**Tiempo disponible:** 10 semanas / 5 sprints (ver sección C del documento de ideas)
**Sprint responsable de esta guía:** Sprint 1 (Sem 1–2) — *Bases & Autenticación*
**Tiempo estimado de ejecución completa:** 60–90 min por persona (hacerlo en parejas, ver §11)

> **Objetivo de esta guía:** que cada integrante tenga el mismo ambiente local funcionando, conectado a la PostgreSQL que ya está arriba, con Django corriendo, migraciones aplicadas y el Admin accesible. Al terminar el §10 todas y todos deben ver la misma pantalla.

---

## 0. Resumen del flujo (para imprimir o pegar en el pizarrón)

```
1. Requisitos previos (Python 3.12/3.13, git, cliente psql)
2. Clonar el repositorio
3. Crear el virtualenv                 -> python -m venv .venv
4. Activar el virtualenv               -> source .venv/bin/activate
5. Actualizar pip
6. Instalar dependencias               -> pip install -r requirements/dev.txt
7. Crear y llenar el archivo .env      -> credenciales de PostgreSQL ya existente
8. Verificar la conexión a la BD       -> python manage.py check --database default
9. Migrar y crear superusuario         -> migrate / createsuperuser
10. Levantar el servidor y validar     -> runserver + /admin/
11. Reparto de responsabilidades y acuerdos de equipo
12. Problemas comunes y how to solve them
```

**Regla de oro:** nunca instalar paquetes con `pip install` *fuera* del virtualenv. Si el prompt no muestra `(.venv)`, el virtualenv no está activo.

---

## 1. Requisitos previos

| Herramienta | Versión requerida | Cómo verificar | Notas |
| :---- | :---- | :---- | :---- |
| Python | **3.12 o 3.13** | `python3 --version` | Ver advertencia abajo ⚠️ |
| pip | el que trae Python | `python3 -m pip --version` | Se actualiza en el paso 5 |
| virtualenv/venv | incluido en Python | `python3 -m venv --help` | No hay que instalar nada extra |
| git | 2.30+ | `git --version` | Para clonar y trabajar por ramas |
| Cliente PostgreSQL | 15+ | `psql --version` | Solo el cliente, para probar la conexión |
| Editor | VS Code (recomendado) | — | Extensiones: Python, Pylance, Ruff |

> ⚠️ **Advertencia importante sobre la versión de Python.**
> Django 5.2 LTS soporta oficialmente Python **3.10 a 3.13**. Si en la máquina hay Python 3.14 (o superior), Django y `psycopg` pueden fallar al instalar o al arrancar. **Todo el equipo debe usar la misma versión menor**: se acuerda **Python 3.12** como versión oficial del proyecto.
> Si no tienen 3.12 en el sistema, instálenlo con el gestor de su distribución o con `pyenv` (ver §12.1). No sigan adelante con 3.14.

**Datos que deben pedir a Rocío (Gestión de Información) antes de empezar:**
las credenciales y el host de la PostgreSQL ya existente:

- `DB_NAME` (nombre de la base para desarrollo)
- `DB_USER` / `DB_PASSWORD`
- `DB_HOST` / `DB_PORT`
- Confirmación de que cada persona tendrá **su propia base de datos** (recomendado: `sgprod_dev_<nombre>`, ver §11).

---

## 2. Clonar el repositorio

```bash
# Sustituyan <url-del-repo> por la URL real que les comparta Rocío
git clone <url-del-repo> sg-prod
cd sg-prod
```

**Convención de ramas (acuerdo de equipo, no negociable):**

- `main` → solo código que ya pasó revisión y funciona. Nadie hace push directo.
- `dev` → rama de integración del sprint; aquí se juntan las tareas terminadas.
- `feat/rf-1-2-skill-matrix`, `fix/login-qr-pin`, `docs/guia-ambiente` → ramas de trabajo, **una por historia de usuario**.
- Flujo: `git switch dev` → `git pull` → `git switch -c feat/mi-historia` → trabajar → `git push` → Pull Request hacia `dev` → revisión de otra persona → merge.

---

## 3. Crear el virtualenv

Un virtualenv es una carpeta con una copia aislada de Python y sus paquetes. **No se sube a git.**

```bash
# Desde la raíz del proyecto (donde está manage.py o donde lo crearán)
python3 -m venv .venv
```

- Se crea la carpeta `.venv/` en la raíz del repositorio.
- Un solo virtualenv por proyecto. Nunca dentro de otra carpeta como `src/`.

**Alternativa (solo si `venv` falla):** `python3 -m virtualenv .venv` (requiere `pip install virtualenv` a nivel de usuario). Con Python 3.12 `venv` es suficiente.

---

## 4. Activar el virtualenv

El comando depende del sistema operativo. **Debe repetirse cada vez que abran una terminal nueva.**

| Sistema | Comando |
| :---- | :---- |
| Linux / macOS (bash, zsh) | `source .venv/bin/activate` |
| Windows PowerShell | `.venv\Scripts\Activate.ps1` |
| Windows CMD | `.venv\Scripts\activate.bat` |
| Git Bash en Windows | `source .venv/Scripts/activate` |

**¿Cómo sé que funcionó?** El prompt debe mostrar el nombre del ambiente entre paréntesis:

```bash
(.venv) usuario@equipo:~/sg-prod$
```

Verificación explícita:

```bash
which python          # Linux/macOS -> .../sg-prod/.venv/bin/python
# where python        # Windows
python -c "import sys; print(sys.prefix)"
```

Si `sys.prefix` apunta a `/usr` o al sistema, el virtualenv **no** está activo.

Para salir: `deactivate`.

---

## 5. Actualizar pip y herramientas base

Dentro del virtualenv activo:

```bash
python -m pip install --upgrade pip setuptools wheel
python -m pip --version   # debe responder desde la ruta .venv
```

---

## 6. Estructura de dependencias e instalación

### 6.1 Estructura de carpetas propuesta

```text
sg-prod/
├── .venv/                     # NO se sube a git
├── .env                       # NO se sube a git (credenciales)
├── .env.example               # SÍ se sube (plantilla sin secretos)
├── .gitignore
├── manage.py
├── requirements/
│   ├── base.txt               # Django, psycopg, etc.
│   ├── dev.txt                # base + herramientas de desarrollo
│   └── prod.txt               # base + gunicorn/whitenoise (Sprint 5)
├── config/                    # proyecto Django (settings, urls, wsgi)
│   ├── settings/
│   │   ├── base.py
│   │   ├── dev.py
│   │   └── prod.py
│   ├── urls.py
│   └── wsgi.py
├── apps/
│   ├── core/                  # autenticación QR/PIN (RNF-1)
│   ├── personal/              # empleados, estaciones, skill matrix (Módulo 1)
│   ├── calidad/               # captura horaria, defectos (Módulo 2)
│   └── dashboard/             # KPIs, bonos, capacitación (Módulo 3)
├── templates/                 # base.html y pantallas (paquete de Martha)
├── static/                    # css/theme.css (paquete de Martha)
└── docs/                      # esta guía, historias de usuario, MER
```

### 6.2 Contenido de los archivos de requirements

`requirements/base.txt`

```txt
Django==5.2.*
psycopg[binary]==3.2.*
django-environ==0.11.*
qrcode[pil]==8.*
Pillow==11.*
```

`requirements/dev.txt`

```txt
-r base.txt
pytest==8.*
pytest-django==4.*
ruff==0.9.*
django-extensions==3.*
```

> **Nota:** no se usa `psycopg2-binary`; el driver oficial actual es **psycopg 3**, que es el que Django 5.x recomienda.

### 6.3 Instalar

```bash
python -m pip install -r requirements/dev.txt
python -m pip list        # verificar Django, psycopg, pytest, ruff
python -m django --version
```

**Regla de equipo:** si alguien necesita una librería nueva, la agrega a `requirements/`, la instala, y avisa por el canal del equipo. Así nadie se queda con un ambiente incompleto.

---

## 7. Variables de entorno (`.env`)

**Nunca** se escriben credenciales en `settings.py` ni se suben al repositorio.

### 7.1 Crear `.env.example` (sí se sube a git)

```ini
# Django
DJANGO_SETTINGS_MODULE=config.settings.dev
DJANGO_SECRET_KEY=cambien-esta-llave-en-su-.env-local
DJANGO_DEBUG=True
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1

# PostgreSQL YA EXISTENTE (la llena cada persona con sus datos)
DB_NAME=sgprod_dev_nombre
DB_USER=sgprod_user
DB_PASSWORD=pon-tu-password-aqui
DB_HOST=localhost
DB_PORT=5432
DB_CONN_MAX_AGE=60

# Zona horaria de planta
TZ=America/Tijuana
```

### 7.2 Crear el `.env` local

```bash
cp .env.example .env
# Editar .env con los datos reales de PostgreSQL que dio Rocío
```

Generar una `SECRET_KEY` distinta por persona:

```bash
python -c "from django.core.management.utils import get_random_secret_key as k; print(k())"
```

### 7.3 `.gitignore` (crear si no existe)

```gitignore
.venv/
.env
*.pyc
__pycache__/
*.sqlite3
staticfiles/
.pytest_cache/
.ruff_cache/
media/
```

Verificar que el `.env` esté realmente ignorado:

```bash
git check-ignore -v .env      # debe imprimir una regla de .gitignore
```

---

## 8. Configurar la conexión a PostgreSQL (ya desplegada)

> Aquí **no** se levanta ningún contenedor: la base ya está arriba. Solo se conecta Django.
> Si Docker fuera necesario en el futuro, ese sería otro documento; hoy queda explícitamente fuera del alcance.

### 8.1 Crear el proyecto y las apps

```bash
# Proyecto Django llamado config, con la configuración separada por ambientes
django-admin startproject config .

# Las apps viven dentro de apps/, así que primero se crea la carpeta contenedora
mkdir -p apps && touch apps/__init__.py

# startapp crea la carpeta destino: el segundo argumento ES la ruta de la app
python manage.py startapp core apps/core
python manage.py startapp personal apps/personal
python manage.py startapp calidad apps/calidad
python manage.py startapp dashboard apps/dashboard
```

Al terminar, en el `apps.py` de cada app ajusten el nombre para que coincida con la ruta:

```python
class PersonalConfig(AppConfig):
    name = "apps.personal"      # no solo "personal"
```

> Esto se hace **una sola vez**, y quien lo haga avisa al equipo antes de tocar la estructura: si alguien crea las apps en otra ruta, los `INSTALLED_APPS` y las migraciones se rompen para todos.

### 8.2 Ajustes mínimos en `config/settings/base.py`

```python
from pathlib import Path
import environ

BASE_DIR = Path(__file__).resolve().parent.parent.parent

env = environ.Env()
environ.Env.read_env(BASE_DIR / ".env")

SECRET_KEY = env("DJANGO_SECRET_KEY")
DEBUG = env.bool("DJANGO_DEBUG", default=False)
ALLOWED_HOSTS = env.list("DJANGO_ALLOWED_HOSTS", default=["localhost", "127.0.0.1"])

INSTALLED_APPS = [
    # ... apps de Django ...
    "django_extensions",
    "apps.core",
    "apps.personal",
    "apps.calidad",
    "apps.dashboard",
]

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": env("DB_NAME"),
        "USER": env("DB_USER"),
        "PASSWORD": env("DB_PASSWORD"),
        "HOST": env("DB_HOST"),
        "PORT": env("DB_PORT", default="5432"),
        "CONN_MAX_AGE": env.int("DB_CONN_MAX_AGE", default=60),
    }
}

LANGUAGE_CODE = "es-mx"
TIME_ZONE = env("TZ", default="America/Tijuana")
USE_TZ = True

TEMPLATES[0]["DIRS"] = [BASE_DIR / "templates"]      # paquete de diseño de Martha
STATICFILES_DIRS = [BASE_DIR / "static"]
STATIC_URL = "static/"
```

### 8.3 Probar la conexión antes de migrar

```bash
# 1) Prueba directa del cliente (opcional, pero ayuda a aislar el problema)
psql -h "$DB_HOST" -U "$DB_USER" -d "$DB_NAME" -c "SELECT version();"

# 2) Prueba desde Django
python manage.py check --database default
```

Si (1) falla, el problema es de red/credenciales, no de Django. Si (1) funciona y (2) falla, revisen el `.env` y `DJANGO_SETTINGS_MODULE`.

---

## 9. Migraciones, superusuario y arranque

```bash
python manage.py makemigrations
python manage.py migrate

python manage.py createsuperuser      # con su correo y una contraseña conocida
python manage.py runserver
```

Validación final: abrir <http://127.0.0.1:8000/admin/> e iniciar sesión.

**Sugerencia de higiene (no obligatoria en Sprint 1):** para no mezclar cambios de modelos entre 7 personas, antes de `makemigrations` hagan `git pull` de `dev`, y suban las migraciones **en el mismo commit** que el cambio del modelo.

---

## 10. Checklist de "ambiente listo" (cada persona lo llena y lo reporta)

- [ ] `python3 --version` muestra 3.12.x (o 3.13.x)
- [ ] `git clone` hecho y rama `dev` actualizada
- [ ] `.venv/` creado y **activo** (el prompt muestra `(.venv)`)
- [ ] `python -m pip list` muestra Django, psycopg, pytest, ruff
- [ ] `.env` creado con datos reales y **no** aparece en `git status`
- [ ] `psql` conecta a la base de datos de desarrollo
- [ ] `python manage.py check` sin errores
- [ ] `python manage.py migrate` aplica sin errores
- [ ] `runserver` levanta y `/admin/` carga con el superusuario
- [ ] `templates/base.html` y `static/css/theme.css` del paquete de diseño copiados
- [ ] `pytest` corre (aunque no haya pruebas todavía: 0 tests, 0 fallos)
- [ ] `ruff check .` corre sin errores nuevos

Cuando todo esté marcado, se avisa en el canal del equipo y **se cierra la tarea de Sprint 1 "Entorno Django/PostgreSQL"**.

---

## 11. Reparto de responsabilidades (7 personas, tiempo limitado)

El cronograma es de 10 semanas: **no hay tiempo para que cada quien descubra sus errores solo**. Se trabaja en parejas y con *copiloto*.

| Persona | Rol en la configuración del ambiente | Entregable de esta guía |
| :---- | :---- | :---- |
| **Rocío** | Gestión de información y alineación | Consigue y distribuye credenciales de PostgreSQL; crea la base de datos por persona; guarda el `.env.example` en el repo; responde dudas de reglas de negocio |
| **Ángela** | Base de datos (líder) | Diseña el MER; define `models.py`; **dueña de las migraciones**; valida que `migrate` corra limpio en una base vacía |
| **Karen** | Base de datos (acompañante) | Apoya el MER; escribe *seeding* de datos de prueba; verifica índices y restricciones |
| **Martha** | UI/UX (líder) | Integra `base.html`, `theme.css` y `templates/`; deja `STATICFILES_DIRS` y `TEMPLATES` funcionando |
| **Eduardo** | UI/UX + Módulo 1 | Verifica que la plantilla base se extienda correctamente; apoya el arranque de las vistas |
| **Mariana** | Módulo 1 (líder) | Configura el Admin de Django; crea el primer modelo de prueba y hace la migración *piloto* del equipo |
| **Iván** | Módulo 2 (líder) | Valida la conexión a BD y el rendimiento; propone la estructura de `requirements/` y el `.env` |

**Reglas de convivencia técnica:**

1. **Una persona dueña de las migraciones por sprint** (Sprint 1: Ángela). Evita colisiones de `000X_*.py`.
2. **Pull Request obligatorio** hacia `dev`, con al menos 1 revisión.
3. **En parejas para instalar el ambiente**: quien ya lo tiene, se sienta con quien no. Meta: **nadie termina la Semana 1 sin ambiente**.
4. **Bloqueo de más de 30 minutos → preguntar al equipo**, no seguir peleando con el error.
5. Todo acuerdo técnico se anota en `docs/`.

---

## 12. Problemas comunes (how to solve them)

### 12.1 No tengo Python 3.12

```bash
# Opción A: gestor de paquetes de la distribución (Fedora/RHEL)
sudo dnf install python3.12 python3.12-devel

# Opción B: pyenv (no requiere sudo y permite varias versiones)
curl https://pyenv.run | bash
pyenv install 3.12.7
cd ~/sg-prod && pyenv local 3.12.7
python --version
```

Después, **borren y recreeen el virtualenv** con la versión correcta.

### 12.2 "No module named django" aunque instalé Django

El virtualenv no está activo. Ejecuten `source .venv/bin/activate` en esa terminal y verifiquen con `which python`.

### 12.3 Error al instalar `psycopg` (falta `pg_config`)

Usen la rueda binaria `psycopg[binary]` (ya está en `base.txt`). Si aun así falla:

```bash
# Fedora/RHEL
sudo dnf install postgresql-devel gcc
# Debian/Ubuntu
sudo apt install libpq-dev gcc
```

Luego `python -m pip install --upgrade --force-reinstall "psycopg[binary]"`.

### 12.4 `django.db.utils.OperationalError: connection refused`

1. Verifiquen host y puerto en `.env` (¿la BD está en `localhost` o en otro servidor?).
2. Prueben con `psql` directo.
3. Si la BD está en otra máquina, confirmen que su IP esté permitida en `pg_hba.conf` (esto lo pide Rocío al responsable del servidor).

### 12.5 `FATAL: password authentication failed`

Contraseña mal escapada en `.env` (comillas, `#`, espacios). Envuélvanla entre comillas dobles: `DB_PASSWORD="mi#pass"`. Reinicien el `runserver` porque el `.env` se lee al arrancar.

### 12.6 `You have N unapplied migration(s)`

```bash
python manage.py migrate
```

Si persiste, probablemente dos personas generaron migraciones con el mismo número: hagan `git pull`, revisen `git status`, y **consulten a Ángela** antes de borrar nada.

### 12.7 Quiero empezar de cero mi base de datos de desarrollo

```bash
python manage.py migrate --run-syncdb   # no recomendado en producción
# O bien, con autorización de Ángela:
psql -h "$DB_HOST" -U "$DB_USER" -d "$DB_NAME" -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"
python manage.py migrate
```

> Solo en su **base de desarrollo personal**. Nunca sobre una base compartida.

### 12.8 Windows: "la ejecución de scripts está deshabilitada"

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

---

## 13. Referencia rápida de comandos del día a día

```bash
# Activar el ambiente (cada terminal nueva)
source .venv/bin/activate

# Levantar el servidor
python manage.py runserver

# Cambios en modelos
python manage.py makemigrations
python manage.py migrate

# Shell de Django con modelos cargados
python manage.py shell_plus        # viene de django-extensions

# Calidad de código
ruff check .
ruff format .

# Pruebas
pytest
pytest apps/personal/tests/ -v

# Actualizar dependencias del equipo
git pull
python -m pip install -r requirements/dev.txt
python manage.py migrate

# Fin de la jornada
deactivate
```

---

## 14. Siguientes pasos (de esta guía a las historias de usuario)

1. **Ejecutar esta guía** (Sprint 1, Semana 1) y cerrar el checklist del §10.
2. **Redactar las historias de usuario** del MVP por módulo, con formato `Como <rol> quiero <acción> para <beneficio>` + criterios de aceptación dados-cuando-entonces, trazadas a los RF-1.x, RF-2.x y RF-3.x del documento de ideas.
3. **Estimar y priorizar** en el backlog de los 5 sprints (Sem 1–2, 3–4, 5–6, 7–8, 9–10).
4. **Congelar el MER** (Ángela y Karen) antes de que los módulos empiecen a escribir modelos.
5. **Definir el formato de las historias** y el tablero (por ejemplo: `docs/01_historias_usuario.md`) para que las 7 personas trabajen en paralelo sin pisarse.
