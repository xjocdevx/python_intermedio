# 📁 Backend API con PostgreSQL
📋 Índice

    Instalación de Dependencias

    Estructura del Proyecto

    Configuración Inicial

    Archivos de Configuración

    Ejemplos (1-20)

Instalación de Dependencias
```bash
api_exercises

# Instalar todas las dependencias del archivo
pip install -r requirements.txt

# En sistemas Linux/Mac a veces necesitas pip3
pip3 install -r requirements.txt

# En Windows (PowerShell)
python -m pip install -r requirements.txt
```
### 📁 Estructura del Backend - API REST
```text

fastapi_postgresql_examples/
│
├── config.py                 # Configuración de BD
├── models.py                 # Modelos Pydantic
├── database.py               # Conexión y operaciones DB
├── main.py                   # Aplicación principal
│
├── ejemplos/
│   ├── 01_hola_mundo.py
│   ├── 02_crud_basico.py
│   ├── 03_con_db.py
│   ├── 04_modelos_pydantic.py
│   ├── 05_path_params.py
│   ├── 06_query_params.py
│   ├── 07_body_request.py
│   ├── 08_validaciones.py
│   ├── 09_relaciones_join.py
│   ├── 10_paginacion.py
│   ├── 11_filtros_avanzados.py
│   ├── 12_operaciones_masivas.py
│   ├── 13_autenticacion_jwt.py
│   ├── 14_dependencias.py
│   ├── 15_middleware.py
│   ├── 16_excepciones.py
│   ├── 17_upload_files.py
│   ├── 18_background_tasks.py
│   ├── 19_websocket.py
│   └── 20_completo_ventas.py
│
├── requirements.txt
└── .env
```
### 🔧 Configuración Inicial
```text
requirements.txt
```bash

fastapi==0.104.1
uvicorn==0.24.0
psycopg2-binary==2.9.9
pydantic==2.4.2
python-dotenv==1.0.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6
websockets==12.0
```
### .env
```bash

DB_HOST=localhost
DB_USER=postgres
DB_PASSWORD=tu_password
DB_NAME=api_exercises
DB_PORT=5432
SECRET_KEY=mi_secreto_super_seguro
```
### 📄 Archivos de Configuración
config.py
```python

import os
from dotenv import load_dotenv

load_dotenv()

DB_CONFIG = {
    'host': os.getenv('DB_HOST', 'localhost'),
    'user': os.getenv('DB_USER', 'postgres'),
    'password': os.getenv('DB_PASSWORD', ''),
    'dbname': os.getenv('DB_NAME', 'api_exercises'),
    'port': os.getenv('DB_PORT', '5432')
}

SECRET_KEY = os.getenv('SECRET_KEY', 'mi_secreto_dev')
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30
```
### database.py
```python

import psycopg2
from psycopg2 import Error
from psycopg2.extras import RealDictCursor
from config import DB_CONFIG

def get_db_connection():
    """Retorna una conexión a PostgreSQL"""
    try:
        conn = psycopg2.connect(**DB_CONFIG)
        return conn
    except Error as e:
        print(f"Error de conexión: {e}")
        return None

def init_database():
    """Inicializa tablas necesarias en PostgreSQL"""
    conn = get_db_connection()
    cursor = conn.cursor()
    
    # Tabla de usuarios
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios (
            id SERIAL PRIMARY KEY,
            name VARCHAR(100) NOT NULL,
            email VARCHAR(100) UNIQUE NOT NULL,
            password_hash VARCHAR(255),
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    # Tabla de productos
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS productos (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100) NOT NULL,
            descripcion TEXT,
            precio DECIMAL(10,2) NOT NULL,
            stock INTEGER DEFAULT 0,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    # Tabla de ventas
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS ventas (
            id SERIAL PRIMARY KEY,
            usuario_id INTEGER,
            producto_id INTEGER,
            cantidad INTEGER,
            total DECIMAL(10,2),
            fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
            FOREIGN KEY (producto_id) REFERENCES productos(id)
        )
    """)
    
    conn.commit()
    cursor.close()
    conn.close()
    print("✅ Base de datos PostgreSQL inicializada")
```
### models.py
```python

from pydantic import BaseModel, Field, validator
from typing import Optional
from datetime import datetime

# Modelos para Usuario
class UsuarioBase(BaseModel):
    name: str = Field(..., min_length=2, max_length=100)
    email: str = Field(..., pattern=r'^[\w\.-]+@[\w\.-]+\.\w+$')

class UsuarioCreate(UsuarioBase):
    password: str = Field(..., min_length=6)

class UsuarioResponse(UsuarioBase):
    id: int
    created_at: datetime
    
    class Config:
        from_attributes = True

# Modelos para Producto
class ProductoBase(BaseModel):
    nombre: str = Field(..., min_length=1, max_length=100)
    descripcion: Optional[str] = None
    precio: float = Field(..., gt=0)
    stock: int = Field(0, ge=0)

class ProductoCreate(ProductoBase):
    pass

class ProductoResponse(ProductoBase):
    id: int
    created_at: datetime
    
    class Config:
        from_attributes = True

# Modelos para Venta
class VentaCreate(BaseModel):
    usuario_id: int
    producto_id: int
    cantidad: int = Field(..., gt=0)
    
    @validator('producto_id')
    def validate_producto(cls, v):
        if v <= 0:
            raise ValueError('producto_id debe ser positivo')
        return v

class VentaResponse(BaseModel):
    id: int
    usuario_id: int
    producto_id: int
    cantidad: int
    total: float
    fecha: datetime
    usuario_nombre: Optional[str] = None
    producto_nombre: Optional[str] = None
    
    class Config:
        from_attributes = True

# Token y Login
class Token(BaseModel):
    access_token: str
    token_type: str

class LoginRequest(BaseModel):
    email: str
    password: str
```
### 📝 Ejemplos (1-20)
### ejemplos/01_hola_mundo.py
```python

"""
Ejemplo 1: Hola Mundo con FastAPI
FastAPI - Endpoint básico
"""

from fastapi import FastAPI

app = FastAPI(title="Mi API", description="Ejemplo básico", version="1.0.0")

@app.get("/")
def root():
    return {"message": "Hola Mundo desde FastAPI!"}

@app.get("/health")
def health_check():
    return {"status": "ok", "service": "FastAPI"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```
ejemplos/02_crud_basico.py
```python

"""
Ejemplo 2: CRUD básico con PostgreSQL
Endpoints: GET, POST, PUT, DELETE
Lee configuración de .env y consume la tabla productos
"""

from fastapi import FastAPI, HTTPException
from typing import List
import psycopg2
from psycopg2.extras import RealDictCursor
from config import DB_CONFIG

app = FastAPI()

def get_db():
    conn = psycopg2.connect(**DB_CONFIG)
    return conn

@app.on_event("startup")
def init_database_and_seed():
    conn = get_db()
    cursor = conn.cursor()
    
    # Crear tabla si no existe
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS productos (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100) NOT NULL,
            precio DECIMAL(10,2) NOT NULL,
            stock INTEGER DEFAULT 0,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    # Verificar si hay datos, si no, insertar datos de prueba
    cursor.execute("SELECT COUNT(*) FROM productos")
    count = cursor.fetchone()[0]
    
    if count == 0:
        datos_prueba = [
            ("Laptop Gaming", 1200.00, 10),
            ("Mouse Inalámbrico", 25.99, 50),
            ("Teclado Mecánico", 89.99, 30),
            ("Monitor 24''", 199.99, 15),
            ("Disco SSD 1TB", 129.99, 25)
        ]
        cursor.executemany(
            "INSERT INTO productos (nombre, precio, stock) VALUES (%s, %s, %s)",
            datos_prueba
        )
        conn.commit()
        print("✅ Datos de prueba insertados en productos")
    
    cursor.close()
    conn.close()

@app.get("/productos")
def listar_productos():
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT id, nombre, precio, stock FROM productos")
    productos = cursor.fetchall()
    cursor.close()
    conn.close()
    return productos

@app.get("/productos/{id}")
def obtener_producto(id: int):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute(
        "SELECT id, nombre, precio, stock FROM productos WHERE id = %s", (id,)
    )
    producto = cursor.fetchone()
    cursor.close()
    conn.close()
    
    if not producto:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    return producto

@app.post("/productos", status_code=201)
def crear_producto(nombre: str, precio: float, stock: int = 0):
    conn = get_db()
    cursor = conn.cursor()
    cursor.execute(
        "INSERT INTO productos (nombre, precio, stock) VALUES (%s, %s, %s) RETURNING id",
        (nombre, precio, stock),
    )
    producto_id = cursor.fetchone()[0]
    conn.commit()
    cursor.close()
    conn.close()
    return {"id": producto_id, "nombre": nombre, "precio": precio, "stock": stock}

@app.put("/productos/{id}")
def actualizar_producto(id: int, nombre: str, precio: float, stock: int = 0):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute(
        "SELECT id FROM productos WHERE id = %s", (id,)
    )
    producto = cursor.fetchone()
    
    if not producto:
        cursor.close()
        conn.close()
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    
    cursor.execute(
        "UPDATE productos SET nombre = %s, precio = %s, stock = %s WHERE id = %s",
        (nombre, precio, stock, id),
    )
    conn.commit()
    cursor.close()
    conn.close()
    return {"id": id, "nombre": nombre, "precio": precio, "stock": stock}

@app.delete("/productos/{id}")
def eliminar_producto(id: int):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute(
        "SELECT id FROM productos WHERE id = %s", (id,)
    )
    producto = cursor.fetchone()
    
    if not producto:
        cursor.close()
        conn.close()
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    
    cursor.execute("DELETE FROM productos WHERE id = %s", (id,))
    conn.commit()
    cursor.close()
    conn.close()
    return {"message": "Producto eliminado"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)
```
ejemplos/03_con_db.py
```python

"""
Ejemplo 3: Conexión a PostgreSQL
Endpoints que interactúan con base de datos
"""

from fastapi import FastAPI, HTTPException
from typing import List
import psycopg2
from psycopg2.extras import RealDictCursor
from config import DB_CONFIG

app = FastAPI()

def get_db():
    conn = psycopg2.connect(**DB_CONFIG)
    return conn

@app.on_event("startup")
def init_db_and_seed():
    conn = get_db()
    cursor = conn.cursor()
    
    # Crear tabla
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios2 (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100),
            email VARCHAR(100)
        )
    """)
    
    # Insertar datos demo
    cursor.execute("SELECT COUNT(*) FROM usuarios2")
    if cursor.fetchone()[0] == 0:
        usuarios_demo = [
            ("Juan Pérez", "juan@email.com"),
            ("María García", "maria@email.com"),
            ("Carlos López", "carlos@email.com"),
            ("Ana Martínez", "ana@email.com")
        ]
        cursor.executemany(
            "INSERT INTO usuarios2 (nombre, email) VALUES (%s, %s)",
            usuarios_demo
        )
        conn.commit()
        print("✅ Usuarios demo insertados")
    
    cursor.close()
    conn.close()

@app.get("/usuarios")
def get_usuarios():
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT * FROM usuarios2")
    usuarios = cursor.fetchall()
    cursor.close()
    conn.close()
    return {"usuarios2": usuarios}

@app.post("/usuarios")
def create_usuario(nombre: str, email: str):
    conn = get_db()
    cursor = conn.cursor()
    cursor.execute(
        "INSERT INTO usuarios2 (nombre, email) VALUES (%s, %s) RETURNING id",
        (nombre, email)
    )
    usuario_id = cursor.fetchone()[0]
    conn.commit()
    cursor.close()
    conn.close()
    return {"id": usuario_id, "nombre": nombre, "email": email}

@app.get("/usuarios/{id}")
def get_usuario(id: int):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT * FROM usuarios2 WHERE id = %s", (id,))
    usuario = cursor.fetchone()
    cursor.close()
    conn.close()
    
    if not usuario:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    return usuario

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)
```
ejemplos/04_modelos_pydantic.py
```python

"""
Ejemplo 4: Uso de modelos Pydantic
Validación automática de datos
"""

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field, validator, EmailStr
from typing import Optional
from datetime import datetime

app = FastAPI()

# Modelos Pydantic
class UsuarioCreate(BaseModel):
    nombre: str = Field(..., min_length=2, max_length=50, example="Juan Perez")
    email: EmailStr = Field(..., example="juan@email.com")
    edad: int = Field(..., ge=18, le=120, description="Edad entre 18 y 120")
    telefono: Optional[str] = Field(None, pattern=r'^\+?[\d\s-]{8,}$')
    
    @validator('nombre')
    def nombre_no_vacio(cls, v):
        if not v.strip():
            raise ValueError('El nombre no puede estar vacío')
        return v.title()
    
    class Config:
        json_schema_extra = {
            "example": {
                "nombre": "Maria Gomez",
                "email": "maria@email.com",
                "edad": 25,
                "telefono": "+5491123456789"
            }
        }

class UsuarioResponse(BaseModel):
    id: int
    nombre: str
    email: str
    edad: int
    registrado: datetime
    
    class Config:
        from_attributes = True

# Base de datos simulada
usuarios_db = []
contador = 1

@app.post("/usuarios", response_model=UsuarioResponse, status_code=201)
def crear_usuario(usuario: UsuarioCreate):
    global contador
    nuevo = {
        "id": contador,
        "nombre": usuario.nombre,
        "email": usuario.email,
        "edad": usuario.edad,
        "registrado": datetime.now()
    }
    usuarios_db.append(nuevo)
    contador += 1
    return nuevo

@app.get("/usuarios/{id}", response_model=UsuarioResponse)
def obtener_usuario(id: int):
    for u in usuarios_db:
        if u["id"] == id:
            return u
    raise HTTPException(status_code=404, detail="Usuario no encontrado")

@app.get("/validar-email/{email}")
def validar_email(email: EmailStr):
    return {"email_valido": email, "es_correcto": True}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)
```
ejemplos/05_path_params.py
python

"""
Ejemplo 5: Parámetros de ruta (Path Parameters)
Diferentes formas de capturar valores en la URL
"""

from fastapi import FastAPI, Path
from typing import Optional

app = FastAPI()

# Parámetros básicos
@app.get("/usuarios/{usuario_id}")
def get_usuario(usuario_id: int):
    return {"usuario_id": usuario_id, "tipo": "entero"}

@app.get("/productos/{slug}")
def get_producto_slug(slug: str):
    return {"slug": slug, "producto": f"Producto: {slug}"}

# Validación con Path
@app.get("/items/{item_id}")
def read_item(
    item_id: int = Path(..., title="ID del item", ge=1, le=1000),
    q: Optional[str] = None
):
    return {"item_id": item_id, "q": q}

# Múltiples parámetros
@app.get("/categorias/{categoria_id}/productos/{producto_id}")
def get_producto_en_categoria(
    categoria_id: int = Path(..., ge=1),
    producto_id: int = Path(..., ge=1)
):
    return {
        "categoria_id": categoria_id,
        "producto_id": producto_id,
        "mensaje": f"Producto {producto_id} de categoría {categoria_id}"
    }

# Parámetros con formato específico
@app.get("/fecha/{anio}/{mes}/{dia}")
def get_fecha(
    anio: int = Path(..., ge=2000, le=2100),
    mes: int = Path(..., ge=1, le=12),
    dia: int = Path(..., ge=1, le=31)
):
    return {"fecha": f"{anio}-{mes:02d}-{dia:02d}"}

# Path con regex
@app.get("/codigos/{codigo}")
def validar_codigo(
    codigo: str = Path(..., regex=r'^[A-Z]{2}-\d{4}$')
):
    return {"codigo": codigo, "valido": True}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/06_query_params.py
python

"""
Ejemplo 6: Parámetros de consulta (Query Parameters)
Filtros, paginación y opciones en la URL
"""

from fastapi import FastAPI, Query
from typing import Optional, List

app = FastAPI()

# Parámetros básicos
@app.get("/buscar")
def buscar(
    q: str,                    # Requerido
    limit: int = 10,           # Opcional con default
    offset: int = 0            # Opcional con default
):
    return {
        "query": q,
        "limit": limit,
        "offset": offset,
        "resultados": [f"Resultado {i+1}" for i in range(limit)]
    }

# Parámetros opcionales
@app.get("/productos")
def listar_productos(
    categoria: Optional[str] = None,
    precio_min: Optional[float] = None,
    precio_max: Optional[float] = None,
    orden: str = "nombre"
):
    return {
        "filtros": {
            "categoria": categoria,
            "precio_min": precio_min,
            "precio_max": precio_max
        },
        "orden": orden,
        "mensaje": "Filtros aplicados correctamente"
    }

# Validación avanzada con Query
@app.get("/usuarios")
def get_usuarios(
    page: int = Query(1, ge=1, description="Número de página"),
    per_page: int = Query(10, ge=1, le=100, description="Items por página"),
    search: Optional[str] = Query(None, min_length=2, max_length=50),
    active: bool = Query(True, description="Usuarios activos")
):
    return {
        "page": page,
        "per_page": per_page,
        "search": search,
        "active": active,
        "skip": (page - 1) * per_page,
        "limit": per_page
    }

# Lista de valores
@app.get("/filtros-multiples")
def filtros_multiples(
    tags: List[str] = Query([], description="Múltiples tags"),
    precios: List[float] = Query([], description="Rangos de precio")
):
    return {"tags": tags, "precios": precios}

# Parámetros booleanos
@app.get("/config")
def get_config(
    verbose: bool = Query(False, description="Modo verbose"),
    debug: bool = Query(False, description="Modo debug")
):
    return {"verbose": verbose, "debug": debug}

# Deprecated
@app.get("/old-endpoint")
def old_endpoint(
    param: str = Query(..., deprecated=True)
):
    return {"message": "Este parámetro está deprecado"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/07_body_request.py
python

"""
Ejemplo 7: Request Body (JSON)
Envío de datos complejos en POST/PUT
"""

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Optional
from datetime import datetime

app = FastAPI()

# Modelos anidados
class Direccion(BaseModel):
    calle: str
    numero: int
    ciudad: str
    codigo_postal: str

class UsuarioCreate(BaseModel):
    nombre: str
    email: str
    direccion: Direccion
    intereses: List[str]
    fecha_nacimiento: Optional[datetime] = None

class VentaItem(BaseModel):
    producto_id: int
    cantidad: int
    precio_unitario: float

class VentaCreate(BaseModel):
    usuario_id: int
    items: List[VentaItem]
    metodo_pago: str

# Endpoint con body simple
@app.post("/usuarios")
def crear_usuario(usuario: UsuarioCreate):
    return {
        "message": "Usuario creado",
        "usuario": usuario.dict(),
        "total_intereses": len(usuario.intereses)
    }

# Endpoint con body anidado
@app.post("/ventas")
def crear_venta(venta: VentaCreate):
    total = sum(item.cantidad * item.precio_unitario for item in venta.items)
    return {
        "venta_id": 123,
        "total": total,
        "items_count": len(venta.items),
        "metodo_pago": venta.metodo_pago
    }

# Múltiples cuerpos
@app.post("/procesar")
def procesar_datos(
    usuario: UsuarioCreate,
    config: dict,
    items: List[str]
):
    return {
        "usuario": usuario.nombre,
        "config": config,
        "items": items,
        "items_count": len(items)
    }

# Body + Path + Query
@app.put("/usuarios/{id}")
def actualizar_usuario(
    id: int,
    usuario: UsuarioCreate,
    confirmar: bool = True
):
    return {
        "id": id,
        "usuario": usuario.dict(),
        "confirmar": confirmar,
        "actualizado": datetime.now()
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/08_validaciones.py
python

"""
Ejemplo 8: Validaciones avanzadas
Validaciones personalizadas y constraints
"""

from fastapi import FastAPI, HTTPException, Body
from pydantic import BaseModel, Field, validator, root_validator
from typing import Optional
from datetime import date, datetime

app = FastAPI()

class RegistroUsuario(BaseModel):
    username: str = Field(..., min_length=3, max_length=20, pattern=r'^[a-zA-Z0-9_]+$')
    email: str = Field(..., pattern=r'^[\w\.-]+@[\w\.-]+\.\w+$')
    password: str = Field(..., min_length=8)
    password_confirm: str
    edad: int = Field(..., ge=18, le=99)
    fecha_registro: datetime = Field(default_factory=datetime.now)
    
    # Validación individual
    @validator('username')
    def username_no_reservado(cls, v):
        reservados = ['admin', 'root', 'system']
        if v.lower() in reservados:
            raise ValueError('Username reservado')
        return v
    
    @validator('password')
    def password_fuerte(cls, v):
        if not any(c.isupper() for c in v):
            raise ValueError('La contraseña debe tener al menos una mayúscula')
        if not any(c.isdigit() for c in v):
            raise ValueError('La contraseña debe tener al menos un número')
        return v
    
    # Validación a nivel de todo el modelo
    @root_validator
    def validate_passwords_match(cls, values):
        if values.get('password') != values.get('password_confirm'):
            raise ValueError('Las contraseñas no coinciden')
        return values

class RangoPrecio(BaseModel):
    min: float = Field(..., ge=0)
    max: float = Field(..., ge=0)
    
    @root_validator
    def validate_range(cls, values):
        if values.get('min') > values.get('max'):
            raise ValueError('El precio mínimo no puede ser mayor al máximo')
        return values

@app.post("/registro")
def registrar(usuario: RegistroUsuario):
    return {
        "message": "Registro exitoso",
        "username": usuario.username,
        "email": usuario.email,
        "edad": usuario.edad
    }

@app.post("/validar-rango")
def validar_rango(rango: RangoPrecio):
    return {"rango_valido": True, "min": rango.min, "max": rango.max}

@app.post("/crear-con-body")
def crear_con_body(
    data: dict = Body(..., description="Datos a procesar"),
    version: str = Body("1.0", description="Versión de la API")
):
    return {"data": data, "version": version}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/09_relaciones_join.py
python

"""
Ejemplo 9: Relaciones y JOINs en PostgreSQL
Obtener datos relacionados de múltiples tablas
"""

from fastapi import FastAPI, HTTPException
from typing import List, Optional
from pydantic import BaseModel
from datetime import datetime
import psycopg2
from psycopg2.extras import RealDictCursor

app = FastAPI()

DB_CONFIG = {
    'host': 'localhost',
    'user': 'postgres',
    'password': 'tu_password',
    'dbname': 'api_exercises',
    'port': '5432'
}

# Modelos de respuesta
class UsuarioVentas(BaseModel):
    id: int
    nombre: str
    email: str
    total_gastado: float
    cantidad_ventas: int
    ultima_compra: Optional[datetime]

class VentaDetalle(BaseModel):
    id: int
    fecha: datetime
    producto: str
    cantidad: int
    precio_unitario: float
    total: float

class UsuarioConVentas(BaseModel):
    id: int
    nombre: str
    email: str
    ventas: List[VentaDetalle]
    total_gastado: float

def get_db():
    return psycopg2.connect(**DB_CONFIG)

@app.on_event("startup")
def init_relations_and_seed():
    conn = get_db()
    cursor = conn.cursor()
    
    # Crear tablas
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100),
            email VARCHAR(100)
        )
    """)
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS ventas (
            id SERIAL PRIMARY KEY,
            usuario_id INTEGER,
            producto VARCHAR(100),
            cantidad INTEGER,
            precio_unitario DECIMAL(10,2),
            total DECIMAL(10,2),
            fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
        )
    """)
    
    # Insertar usuarios demo
    cursor.execute("SELECT COUNT(*) FROM usuarios")
    if cursor.fetchone()[0] == 0:
        usuarios = [
            ("Ana Torres", "ana@email.com"),
            ("Luis Ramírez", "luis@email.com"),
            ("Carmen Vega", "carmen@email.com")
        ]
        cursor.executemany(
            "INSERT INTO usuarios (nombre, email) VALUES (%s, %s)",
            usuarios
        )
        conn.commit()
        
        # Obtener IDs de usuarios insertados
        cursor.execute("SELECT id FROM usuarios ORDER BY id")
        usuarios_ids = [row[0] for row in cursor.fetchall()]
        
        # Insertar ventas relacionadas
        ventas_demo = [
            (usuarios_ids[0], "Laptop", 1, 800.00, 800.00),
            (usuarios_ids[0], "Mouse", 2, 25.00, 50.00),
            (usuarios_ids[1], "Teclado", 1, 60.00, 60.00),
            (usuarios_ids[2], "Monitor", 1, 300.00, 300.00),
            (usuarios_ids[0], "Disco SSD", 1, 150.00, 150.00),
            (usuarios_ids[2], "Memoria RAM", 2, 45.00, 90.00)
        ]
        cursor.executemany("""
            INSERT INTO ventas (usuario_id, producto, cantidad, precio_unitario, total)
            VALUES (%s, %s, %s, %s, %s)
        """, ventas_demo)
        conn.commit()
        print("✅ Datos relacionales insertados")
    
    cursor.close()
    conn.close()

@app.get("/usuarios/{id}/resumen", response_model=UsuarioVentas)
def resumen_usuario(id: int):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    query = """
        SELECT 
            u.id,
            u.nombre,
            u.email,
            COALESCE(SUM(v.total), 0) as total_gastado,
            COUNT(v.id) as cantidad_ventas,
            MAX(v.fecha) as ultima_compra
        FROM usuarios u
        LEFT JOIN ventas v ON u.id = v.usuario_id
        WHERE u.id = %s
        GROUP BY u.id
    """
    cursor.execute(query, (id,))
    resultado = cursor.fetchone()
    
    cursor.close()
    conn.close()
    
    if not resultado:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    
    return resultado

@app.get("/usuarios/{id}/ventas", response_model=UsuarioConVentas)
def ventas_usuario(id: int):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    # Datos del usuario
    cursor.execute("SELECT id, nombre, email FROM usuarios WHERE id = %s", (id,))
    usuario = cursor.fetchone()
    
    if not usuario:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    
    # Ventas del usuario
    cursor.execute("""
        SELECT id, fecha, producto, cantidad, precio_unitario, total
        FROM ventas
        WHERE usuario_id = %s
        ORDER BY fecha DESC
    """, (id,))
    
    ventas = cursor.fetchall()
    
    # Calcular total
    total_gastado = sum(v['total'] for v in ventas) if ventas else 0
    
    cursor.close()
    conn.close()
    
    return {
        **usuario,
        "ventas": ventas,
        "total_gastado": total_gastado
    }

@app.get("/reportes/top-usuarios")
def top_usuarios(limit: int = 10):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    query = """
        SELECT 
            u.id,
            u.nombre,
            u.email,
            COUNT(v.id) as total_compras,
            COALESCE(SUM(v.total), 0) as monto_total,
            AVG(v.total) as ticket_promedio
        FROM usuarios u
        LEFT JOIN ventas v ON u.id = v.usuario_id
        GROUP BY u.id
        ORDER BY monto_total DESC
        LIMIT %s
    """
    cursor.execute(query, (limit,))
    resultados = cursor.fetchall()
    
    cursor.close()
    conn.close()
    
    return {
        "top_usuarios": resultados,
        "limit": limit
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/10_paginacion.py
python

"""
Ejemplo 10: Paginación profesional
Manejo de grandes volúmenes de datos
"""

from fastapi import FastAPI, Query, HTTPException
from typing import Optional, List, Dict, Any
from pydantic import BaseModel, Field
from math import ceil
import psycopg2
from psycopg2.extras import RealDictCursor

app = FastAPI()

DB_CONFIG = {
    'host': 'localhost',
    'user': 'postgres',
    'password': 'tu_password',
    'dbname': 'api_exercises',
    'port': '5432'
}

class PaginationParams(BaseModel):
    page: int = Field(1, ge=1, description="Número de página")
    per_page: int = Field(20, ge=1, le=100, description="Items por página")
    sort_by: Optional[str] = Field(None, description="Campo para ordenar")
    sort_order: str = Field("DESC", pattern="^(ASC|DESC)$")

class PaginatedResponse(BaseModel):
    data: List[Any]
    total: int
    page: int
    per_page: int
    total_pages: int
    has_next: bool
    has_prev: bool
    next_page: Optional[int]
    prev_page: Optional[int]

@app.on_event("startup")
def init_pagination_data():
    conn = get_db()
    cursor = conn.cursor()
    
    # Crear tabla productos si no existe
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS productos (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100),
            descripcion TEXT,
            precio DECIMAL(10,2),
            stock INTEGER
        )
    """)
    
    # Generar 100 productos de prueba si está vacío
    cursor.execute("SELECT COUNT(*) FROM productos")
    if cursor.fetchone()[0] == 0:
        productos_demo = []
        for i in range(1, 101):
            productos_demo.append((
                f"Producto {i}",
                f"Descripción del producto número {i}",
                10.00 + (i * 1.5),
                i * 10
            ))
        
        cursor.executemany(
            "INSERT INTO productos (nombre, descripcion, precio, stock) VALUES (%s, %s, %s, %s)",
            productos_demo
        )
        conn.commit()
        print("✅ 100 productos generados para pruebas de paginación")
    
    cursor.close()
    conn.close()

def paginate_query(cursor, base_query: str, count_query: str, params: List, page: int, per_page: int):
    """Helper para paginar queries SQL"""
    offset = (page - 1) * per_page
    
    # Obtener total
    cursor.execute(count_query, params)
    total = cursor.fetchone()['total']
    
    # Obtener datos paginados
    paginated_query = f"{base_query} LIMIT %s OFFSET %s"
    cursor.execute(paginated_query, params + [per_page, offset])
    data = cursor.fetchall()
    
    total_pages = ceil(total / per_page) if per_page > 0 else 0
    
    return {
        'data': data,
        'total': total,
        'page': page,
        'per_page': per_page,
        'total_pages': total_pages,
        'has_next': page < total_pages,
        'has_prev': page > 1,
        'next_page': page + 1 if page < total_pages else None,
        'prev_page': page - 1 if page > 1 else None
    }

def get_db():
    return psycopg2.connect(**DB_CONFIG)

@app.get("/ventas/paginadas", response_model=PaginatedResponse)
def get_ventas_paginadas(
    page: int = Query(1, ge=1),
    per_page: int = Query(20, ge=1, le=100),
    sort_by: Optional[str] = Query(None, regex="^(id|fecha|total)$"),
    sort_order: str = Query("DESC", regex="^(ASC|DESC)$"),
    usuario_id: Optional[int] = None,
    producto: Optional[str] = None
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    # Construir queries dinámicamente
    base_query = """
        SELECT id, fecha, producto, cantidad, total, usuario_id
        FROM ventas
        WHERE 1=1
    """
    count_query = "SELECT COUNT(*) as total FROM ventas WHERE 1=1"
    params = []
    
    if usuario_id:
        base_query += " AND usuario_id = %s"
        count_query += " AND usuario_id = %s"
        params.append(usuario_id)
    
    if producto:
        base_query += " AND producto LIKE %s"
        count_query += " AND producto LIKE %s"
        params.append(f"%{producto}%")
    
    if sort_by:
        base_query += f" ORDER BY {sort_by} {sort_order}"
    else:
        base_query += " ORDER BY fecha DESC"
    
    result = paginate_query(cursor, base_query, count_query, params, page, per_page)
    
    cursor.close()
    conn.close()
    
    return result

@app.get("/productos/paginados")
def get_productos_paginados(
    page: int = Query(1, ge=1),
    per_page: int = Query(10, ge=1, le=50),
    min_precio: Optional[float] = None,
    max_precio: Optional[float] = None,
    search: Optional[str] = None
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    base_query = "SELECT * FROM productos WHERE 1=1"
    count_query = "SELECT COUNT(*) as total FROM productos WHERE 1=1"
    params = []
    
    if min_precio:
        base_query += " AND precio >= %s"
        count_query += " AND precio >= %s"
        params.append(min_precio)
    
    if max_precio:
        base_query += " AND precio <= %s"
        count_query += " AND precio <= %s"
        params.append(max_precio)
    
    if search:
        base_query += " AND (nombre LIKE %s OR descripcion LIKE %s)"
        count_query += " AND (nombre LIKE %s OR descripcion LIKE %s)"
        search_term = f"%{search}%"
        params.extend([search_term, search_term])
    
    base_query += " ORDER BY id DESC"
    
    result = paginate_query(cursor, base_query, count_query, params, page, per_page)
    
    cursor.close()
    conn.close()
    
    # Agregar URLs de navegación
    base_url = "/productos/paginados"
    result['first_page_url'] = f"{base_url}?page=1&per_page={per_page}"
    result['last_page_url'] = f"{base_url}?page={result['total_pages']}&per_page={per_page}"
    
    return result

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/11_filtros_avanzados.py
python

"""
Ejemplo 11: Filtros avanzados y búsqueda
Múltiples criterios de búsqueda
"""

from fastapi import FastAPI, Query, HTTPException
from typing import Optional, List
from pydantic import BaseModel
from datetime import date, datetime
import psycopg2
from psycopg2.extras import RealDictCursor

app = FastAPI()

DB_CONFIG = {
    'host': 'localhost',
    'user': 'postgres',
    'password': 'tu_password',
    'dbname': 'api_exercises',
    'port': '5432'
}

class FiltrosVenta(BaseModel):
    usuario_id: Optional[int] = None
    producto: Optional[str] = None
    fecha_desde: Optional[date] = None
    fecha_hasta: Optional[date] = None
    total_min: Optional[float] = None
    total_max: Optional[float] = None
    cantidad_min: Optional[int] = None

@app.on_event("startup")
def init_filters_data():
    conn = get_db()
    cursor = conn.cursor()
    
    # Crear tablas necesarias
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS ventas (
            id SERIAL PRIMARY KEY,
            usuario_id INTEGER,
            producto VARCHAR(100),
            cantidad INTEGER,
            total DECIMAL(10,2),
            fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100)
        )
    """)
    
    # Insertar datos variados para pruebas de filtros
    cursor.execute("SELECT COUNT(*) FROM ventas")
    if cursor.fetchone()[0] == 0:
        # Usuarios
        usuarios = [(f"Usuario_{i}",) for i in range(1, 6)]
        cursor.executemany("INSERT INTO usuarios (nombre) VALUES (%s)", usuarios)
        conn.commit()
        
        # Ventas con diferentes fechas, cantidades y totales
        from datetime import datetime, timedelta
        ventas = []
        base_date = datetime.now()
        
        for i in range(1, 51):
            usuario_id = (i % 5) + 1
            cantidad = (i % 10) + 1
            precio_unitario = 50 + (i * 10)
            total = cantidad * precio_unitario
            
            fecha = base_date - timedelta(days=i % 30)
            
            ventas.append((
                usuario_id,
                f"Producto_Filtro_{i}",
                cantidad,
                total,
                fecha
            ))
        
        cursor.executemany("""
            INSERT INTO ventas (usuario_id, producto, cantidad, total, fecha)
            VALUES (%s, %s, %s, %s, %s)
        """, ventas)
        conn.commit()
        print("✅ 50 ventas generadas para pruebas de filtros")
    
    cursor.close()
    conn.close()

def get_db():
    return psycopg2.connect(**DB_CONFIG)

@app.get("/ventas/buscar")
def buscar_ventas(
    usuario_id: Optional[int] = Query(None, description="ID del usuario"),
    producto: Optional[str] = Query(None, description="Nombre del producto"),
    fecha_desde: Optional[date] = Query(None, description="Fecha inicio"),
    fecha_hasta: Optional[date] = Query(None, description="Fecha fin"),
    total_min: Optional[float] = Query(None, ge=0, description="Total mínimo"),
    total_max: Optional[float] = Query(None, ge=0, description="Total máximo"),
    cantidad_min: Optional[int] = Query(None, ge=1, description="Cantidad mínima"),
    limit: int = Query(50, ge=1, le=200, description="Límite de resultados")
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    query = """
        SELECT v.*, u.nombre as usuario_nombre
        FROM ventas v
        LEFT JOIN usuarios u ON v.usuario_id = u.id
        WHERE 1=1
    """
    params = []
    
    if usuario_id:
        query += " AND v.usuario_id = %s"
        params.append(usuario_id)
    
    if producto:
        query += " AND v.producto LIKE %s"
        params.append(f"%{producto}%")
    
    if fecha_desde:
        query += " AND v.fecha >= %s"
        params.append(fecha_desde)
    
    if fecha_hasta:
        query += " AND v.fecha <= %s"
        params.append(fecha_hasta)
    
    if total_min:
        query += " AND v.total >= %s"
        params.append(total_min)
    
    if total_max:
        query += " AND v.total <= %s"
        params.append(total_max)
    
    if cantidad_min:
        query += " AND v.cantidad >= %s"
        params.append(cantidad_min)
    
    query += " ORDER BY v.fecha DESC LIMIT %s"
    params.append(limit)
    
    cursor.execute(query, params)
    resultados = cursor.fetchall()
    
    cursor.close()
    conn.close()
    
    return {
        "total_resultados": len(resultados),
        "limite_aplicado": limit,
        "filtros_aplicados": {
            k: v for k, v in locals().items() 
            if k in ['usuario_id', 'producto', 'fecha_desde', 'fecha_hasta', 
                     'total_min', 'total_max', 'cantidad_min'] and v is not None
        },
        "datos": resultados
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/12_operaciones_masivas.py
python

"""
Ejemplo 12: Operaciones masivas con PostgreSQL
Inserción, actualización y eliminación en lote
"""

from fastapi import FastAPI, HTTPException, BackgroundTasks
from typing import List
from pydantic import BaseModel, Field
import psycopg2
from psycopg2 import Error

app = FastAPI()

DB_CONFIG = {
    'host': 'localhost',
    'user': 'postgres',
    'password': 'tu_password',
    'dbname': 'api_exercises',
    'port': '5432'
}

class ProductoMasivo(BaseModel):
    nombre: str = Field(..., min_length=1)
    precio: float = Field(..., gt=0)
    stock: int = Field(0, ge=0)

def get_db():
    return psycopg2.connect(**DB_CONFIG)

@app.post("/productos/masivo", status_code=202)
async def crear_productos_masivo(
    productos: List[ProductoMasivo],
    background_tasks: BackgroundTasks
):
    """Inserta múltiples productos en lote"""
    
    def bulk_insert():
        conn = get_db()
        cursor = conn.cursor()
        
        query = "INSERT INTO productos (nombre, precio, stock) VALUES (%s, %s, %s)"
        values = [(p.nombre, p.precio, p.stock) for p in productos]
        
        try:
            cursor.executemany(query, values)
            conn.commit()
            insertados = cursor.rowcount
            print(f"✅ {insertados} productos insertados masivamente")
        except Error as e:
            print(f"❌ Error en inserción masiva: {e}")
            conn.rollback()
        finally:
            cursor.close()
            conn.close()
    
    background_tasks.add_task(bulk_insert)
    
    return {
        "message": f"Procesando {len(productos)} productos en segundo plano",
        "cantidad": len(productos),
        "status": "processing"
    }

@app.put("/productos/precios/masivo")
def actualizar_precios_masivo(
    incremento_porcentaje: float = Field(..., gt=0, le=100),
    categoria: str = None
):
    """Actualiza precios masivamente"""
    conn = get_db()
    cursor = conn.cursor()
    
    query = """
        UPDATE productos 
        SET precio = precio * (1 + %s / 100)
    """
    params = [incremento_porcentaje]
    
    if categoria:
        query += " WHERE categoria = %s"
        params.append(categoria)
    
    try:
        cursor.execute(query, params)
        conn.commit()
        actualizados = cursor.rowcount
    except Error as e:
        print(f"❌ Error en actualización masiva: {e}")
        conn.rollback()
        raise HTTPException(status_code=500, detail="Error en actualización masiva")
    finally:
        cursor.close()
        conn.close()
    
    return {
        "message": f"Precios actualizados exitosamente",
        "productos_afectados": actualizados,
        "incremento_aplicado": f"{incremento_porcentaje}%"
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/13_autenticacion_jwt.py
python

"""
Ejemplo 13: Autenticación JWT
Registro, login y protección de rutas
"""

from fastapi import FastAPI, HTTPException, Depends, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel
from typing import Optional
from datetime import datetime, timedelta
import psycopg2
from psycopg2.extras import RealDictCursor
from passlib.context import CryptContext
from jose import JWTError, jwt
from config import DB_CONFIG, SECRET_KEY, ALGORITHM, ACCESS_TOKEN_EXPIRE_MINUTES

app = FastAPI()

# Configuración de seguridad
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Modelos
class UsuarioCreate(BaseModel):
    name: str
    email: str
    password: str

class UsuarioLogin(BaseModel):
    email: str
    password: str

class Token(BaseModel):
    access_token: str
    token_type: str

class TokenData(BaseModel):
    email: Optional[str] = None

def get_db():
    return psycopg2.connect(**DB_CONFIG)

@app.on_event("startup")
def init_auth_tables():
    conn = get_db()
    cursor = conn.cursor()
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios (
            id SERIAL PRIMARY KEY,
            name VARCHAR(100) NOT NULL,
            email VARCHAR(100) UNIQUE NOT NULL,
            password_hash VARCHAR(255) NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    conn.commit()
    cursor.close()
    conn.close()
    print("✅ Tabla de usuarios inicializada")

# Funciones de autenticación
def verify_password(plain_password, hashed_password):
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password):
    return pwd_context.hash(password)

def authenticate_user(email: str, password: str):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute("SELECT * FROM usuarios WHERE email = %s", (email,))
    user = cursor.fetchone()
    
    cursor.close()
    conn.close()
    
    if not user:
        return False
    if not verify_password(password, user['password_hash']):
        return False
    return user

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

# Endpoints
@app.post("/register", status_code=201)
def register(usuario: UsuarioCreate):
    conn = get_db()
    cursor = conn.cursor()
    
    hashed_password = get_password_hash(usuario.password)
    
    try:
        cursor.execute(
            "INSERT INTO usuarios (name, email, password_hash) VALUES (%s, %s, %s) RETURNING id",
            (usuario.name, usuario.email, hashed_password)
        )
        user_id = cursor.fetchone()[0]
        conn.commit()
    except psycopg2.IntegrityError:
        conn.rollback()
        raise HTTPException(status_code=400, detail="Email ya registrado")
    finally:
        cursor.close()
        conn.close()
    
    return {"message": "Usuario creado exitosamente", "id": user_id}

@app.post("/token", response_model=Token)
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Credenciales incorrectas",
            headers={"WWW-Authenticate": "Bearer"},
        )
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user['email']}, expires_delta=access_token_expires
    )
    return {"access_token": access_token, "token_type": "bearer"}

@app.get("/protected")
async def protected_route(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        email: str = payload.get("sub")
        if email is None:
            raise HTTPException(status_code=401, detail="Token inválido")
    except JWTError:
        raise HTTPException(status_code=401, detail="Token inválido")
    
    return {"message": f"Acceso autorizado para {email}"}

@app.get("/usuarios/me")
async def read_users_me(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        email: str = payload.get("sub")
        if email is None:
            raise HTTPException(status_code=401, detail="Token inválido")
    except JWTError:
        raise HTTPException(status_code=401, detail="Token inválido")
    
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT id, name, email, created_at FROM usuarios WHERE email = %s", (email,))
    user = cursor.fetchone()
    cursor.close()
    conn.close()
    
    if user is None:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    return user

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/14_dependencias.py
python

"""
Ejemplo 14: Dependencias en FastAPI
Reutilización de lógica compartida
"""

from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from typing import Optional
import psycopg2
from psycopg2.extras import RealDictCursor
from config import DB_CONFIG

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Dependencia para conexión a BD
def get_db():
    conn = psycopg2.connect(**DB_CONFIG)
    try:
        yield conn
    finally:
        conn.close()

# Dependencia para verificar token
async def get_current_user(token: str = Depends(oauth2_scheme)):
    # Simulación de verificación
    if token == "invalid":
        raise HTTPException(status_code=401, detail="Token inválido")
    return {"id": 1, "email": "usuario@email.com"}

# Dependencia para verificar admin
def get_admin_user(current_user: dict = Depends(get_current_user)):
    if current_user.get("email") != "admin@email.com":
        raise HTTPException(status_code=403, detail="Acceso denegado")
    return current_user

# Dependencia para paginación
def pagination_params(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}

# Endpoints usando dependencias
@app.get("/items")
def get_items(
    db: psycopg2.extensions.connection = Depends(get_db),
    pagination: dict = Depends(pagination_params)
):
    cursor = db.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT * FROM productos LIMIT %s OFFSET %s", 
                   (pagination["limit"], pagination["skip"]))
    items = cursor.fetchall()
    cursor.close()
    return items

@app.get("/protected-data")
def protected_data(current_user: dict = Depends(get_current_user)):
    return {"message": f"Datos para {current_user['email']}"}

@app.get("/admin-only")
def admin_only(admin: dict = Depends(get_admin_user)):
    return {"message": "Acceso de administrador"}

# Dependencia con clase
class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit

@app.get("/search")
def search(commons: CommonQueryParams = Depends()):
    return {
        "q": commons.q,
        "skip": commons.skip,
        "limit": commons.limit
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/15_middleware.py
python

"""
Ejemplo 15: Middleware en FastAPI
Logging, tiempo de respuesta, CORS
"""

from fastapi import FastAPI, Request
from fastapi.middleware.cors import CORSMiddleware
import time
import logging

app = FastAPI()

# Configurar logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Middleware de logging
@app.middleware("http")
async def log_requests(request: Request, call_next):
    start_time = time.time()
    
    # Procesar request
    response = await call_next(request)
    
    # Calcular tiempo de procesamiento
    process_time = time.time() - start_time
    
    # Loggear
    logger.info(
        f"{request.method} {request.url.path} - "
        f"Status: {response.status_code} - "
        f"Time: {process_time:.3f}s"
    )
    
    # Agregar header con tiempo de respuesta
    response.headers["X-Process-Time"] = str(process_time)
    return response

# Middleware de autenticación simple
@app.middleware("http")
async def auth_middleware(request: Request, call_next):
    # Rutas que no requieren autenticación
    public_paths = ["/", "/docs", "/redoc", "/openapi.json", "/health"]
    
    if request.url.path in public_paths:
        return await call_next(request)
    
    # Verificar token en header
    auth_header = request.headers.get("Authorization")
    if not auth_header or not auth_header.startswith("Bearer "):
        from fastapi.responses import JSONResponse
        return JSONResponse(
            status_code=401,
            content={"detail": "No autorizado"}
        )
    
    return await call_next(request)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # En producción, especificar dominios
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Endpoints
@app.get("/")
def root():
    return {"message": "Middleware funcionando"}

@app.get("/health")
def health():
    return {"status": "ok"}

@app.get("/protected")
def protected():
    return {"message": "Acceso autorizado"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/16_excepciones.py
python

"""
Ejemplo 16: Manejo de excepciones
Manejo de errores personalizados
"""

from fastapi import FastAPI, HTTPException, Request
from fastapi.responses import JSONResponse
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

# Excepción personalizada
class BusinessException(Exception):
    def __init__(self, code: str, message: str, status_code: int = 400):
        self.code = code
        self.message = message
        self.status_code = status_code

# Manejador global de excepciones
@app.exception_handler(BusinessException)
async def business_exception_handler(request: Request, exc: BusinessException):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": {
                "code": exc.code,
                "message": exc.message
            }
        }
    )

@app.exception_handler(HTTPException)
async def http_exception_handler(request: Request, exc: HTTPException):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": {
                "code": "HTTP_ERROR",
                "message": exc.detail
            }
        }
    )

@app.exception_handler(Exception)
async def global_exception_handler(request: Request, exc: Exception):
    return JSONResponse(
        status_code=500,
        content={
            "error": {
                "code": "INTERNAL_ERROR",
                "message": "Error interno del servidor"
            }
        }
    )

# Modelos
class ProductCreate(BaseModel):
    nombre: str
    precio: float
    stock: Optional[int] = 0

# Endpoints con manejo de excepciones
@app.post("/productos")
def crear_producto(producto: ProductCreate):
    if producto.precio <= 0:
        raise BusinessException(
            code="PRICE_INVALID",
            message="El precio debe ser mayor a 0",
            status_code=400
        )
    
    if producto.stock < 0:
        raise BusinessException(
            code="STOCK_INVALID",
            message="El stock no puede ser negativo",
            status_code=400
        )
    
    # Simular creación
    return {"id": 1, **producto.dict()}

@app.get("/productos/{id}")
def obtener_producto(id: int):
    if id < 0:
        raise BusinessException(
            code="ID_INVALID",
            message="El ID debe ser positivo",
            status_code=400
        )
    
    if id > 1000:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    
    return {"id": id, "nombre": "Producto de prueba", "precio": 99.99}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/17_upload_files.py
python

"""
Ejemplo 17: Subida de archivos
Upload de imágenes y documentos
"""

from fastapi import FastAPI, File, UploadFile, HTTPException
from fastapi.responses import FileResponse
from typing import List
import os
import shutil
from datetime import datetime

app = FastAPI()

# Configuración
UPLOAD_DIR = "uploads"
os.makedirs(UPLOAD_DIR, exist_ok=True)

# Validación de archivos
ALLOWED_EXTENSIONS = {'.jpg', '.jpeg', '.png', '.gif', '.pdf', '.txt'}
MAX_FILE_SIZE = 5 * 1024 * 1024  # 5MB

def validate_file(filename: str, content_length: int):
    """Valida el archivo subido"""
    # Validar extensión
    extension = os.path.splitext(filename)[1].lower()
    if extension not in ALLOWED_EXTENSIONS:
        raise HTTPException(
            status_code=400,
            detail=f"Extensión no permitida. Permitidas: {', '.join(ALLOWED_EXTENSIONS)}"
        )
    
    # Validar tamaño
    if content_length > MAX_FILE_SIZE:
        raise HTTPException(
            status_code=400,
            detail=f"Archivo demasiado grande. Máximo: {MAX_FILE_SIZE // (1024*1024)}MB"
        )
    
    return True

@app.post("/upload/single")
async def upload_single(file: UploadFile = File(...)):
    """Subir un archivo"""
    # Validar archivo
    content_length = file.size or 0
    validate_file(file.filename, content_length)
    
    # Generar nombre único
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    safe_filename = f"{timestamp}_{file.filename.replace(' ', '_')}"
    file_path = os.path.join(UPLOAD_DIR, safe_filename)
    
    # Guardar archivo
    try:
        with open(file_path, "wb") as buffer:
            shutil.copyfileobj(file.file, buffer)
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Error al guardar: {str(e)}")
    
    return {
        "filename": safe_filename,
        "original_name": file.filename,
        "content_type": file.content_type,
        "size": file.size,
        "path": file_path
    }

@app.post("/upload/multiple")
async def upload_multiple(files: List[UploadFile] = File(...)):
    """Subir múltiples archivos"""
    results = []
    
    for file in files:
        try:
            content_length = file.size or 0
            validate_file(file.filename, content_length)
            
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            safe_filename = f"{timestamp}_{file.filename.replace(' ', '_')}"
            file_path = os.path.join(UPLOAD_DIR, safe_filename)
            
            with open(file_path, "wb") as buffer:
                shutil.copyfileobj(file.file, buffer)
            
            results.append({
                "filename": safe_filename,
                "original_name": file.filename,
                "status": "success"
            })
        except HTTPException as e:
            results.append({
                "filename": file.filename,
                "status": "error",
                "error": e.detail
            })
    
    return {"results": results, "total": len(files)}

@app.get("/files/{filename}")
async def download_file(filename: str):
    """Descargar un archivo"""
    file_path = os.path.join(UPLOAD_DIR, filename)
    
    if not os.path.exists(file_path):
        raise HTTPException(status_code=404, detail="Archivo no encontrado")
    
    return FileResponse(file_path, media_type="application/octet-stream", filename=filename)

@app.get("/files")
async def list_files():
    """Listar archivos subidos"""
    files = []
    for filename in os.listdir(UPLOAD_DIR):
        file_path = os.path.join(UPLOAD_DIR, filename)
        if os.path.isfile(file_path):
            files.append({
                "filename": filename,
                "size": os.path.getsize(file_path),
                "modified": os.path.getmtime(file_path)
            })
    return {"files": files, "count": len(files)}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/18_background_tasks.py
python

"""
Ejemplo 18: Tareas en segundo plano
Procesamiento asíncrono
"""

from fastapi import FastAPI, BackgroundTasks
from typing import List
import time
import logging

app = FastAPI()
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Funciones de ejemplo para tareas en segundo plano
def send_email(email: str, subject: str, body: str):
    """Simula envío de email"""
    time.sleep(2)  # Simular proceso lento
    logger.info(f"Email enviado a {email}")
    logger.info(f"Asunto: {subject}")
    logger.info(f"Cuerpo: {body[:50]}...")

def process_data(data: List[dict]):
    """Procesa datos en segundo plano"""
    logger.info(f"Iniciando procesamiento de {len(data)} registros")
    
    for i, item in enumerate(data):
        time.sleep(0.5)  # Simular procesamiento
        logger.info(f"Procesando item {i+1}: {item}")
    
    logger.info("Procesamiento completado")

def generate_report():
    """Genera un reporte"""
    time.sleep(3)  # Simular generación de reporte
    logger.info("Reporte generado exitosamente")

# Endpoints con background tasks
@app.post("/send-email")
async def send_email_task(
    email: str,
    subject: str,
    body: str,
    background_tasks: BackgroundTasks
):
    """Envía un email en segundo plano"""
    background_tasks.add_task(send_email, email, subject, body)
    
    return {
        "message": "Email programado para envío",
        "email": email,
        "status": "pending"
    }

@app.post("/process-data")
async def process_data_task(
    data: List[dict],
    background_tasks: BackgroundTasks
):
    """Procesa datos en segundo plano"""
    background_tasks.add_task(process_data, data)
    
    return {
        "message": "Procesamiento de datos iniciado",
        "records": len(data),
        "status": "processing"
    }

@app.post("/generate-report")
async def generate_report_task(background_tasks: BackgroundTasks):
    """Genera un reporte en segundo plano"""
    background_tasks.add_task(generate_report)
    
    return {
        "message": "Generación de reporte iniciada",
        "status": "processing"
    }

# Múltiples tareas en segundo plano
@app.post("/multiple-tasks")
async def multiple_tasks(background_tasks: BackgroundTasks):
    """Ejecuta múltiples tareas en segundo plano"""
    background_tasks.add_task(send_email, "user@email.com", "Bienvenida", "Gracias por registrarte")
    background_tasks.add_task(generate_report)
    background_tasks.add_task(process_data, [{"id": 1}, {"id": 2}, {"id": 3}])
    
    return {
        "message": "Tareas programadas",
        "tasks": ["email", "reporte", "procesamiento"]
    }

# Dependencia con background tasks
@app.get("/health")
async def health_check(background_tasks: BackgroundTasks):
    """Verifica el estado del servicio"""
    background_tasks.add_task(logger.info, "Health check realizado")
    return {"status": "ok"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

ejemplos/19_websocket.py
python

"""
Ejemplo 19: WebSocket
Comunicación bidireccional en tiempo real
"""

from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from typing import List, Dict
import json

app = FastAPI()

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []
        self.rooms: Dict[str, List[WebSocket]] = {}
    
    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)
    
    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)
        # Remover de todas las salas
        for room in self.rooms.values():
            if websocket in room:
                room.remove(websocket)
    
    async def send_personal_message(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)
    
    async def broadcast(self, message: str):
        for connection in self.active_connections:
            await connection.send_text(message)
    
    async def broadcast_to_room(self, room: str, message: str):
        if room in self.rooms:
            for connection in self.rooms[room]:
                await connection.send_text(message)
    
    def add_to_room(self, websocket: WebSocket, room: str):
        if room not in self.rooms:
            self.rooms[room] = []
        if websocket not in self.rooms[room]:
            self.rooms[room].append(websocket)

manager = ConnectionManager()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await manager.connect(websocket)
    
    try:
        while True:
            data = await websocket.receive_text()
            
            try:
                # Parsear JSON
                message_data = json.loads(data)
                
                # Mensaje para sala específica
                if message_data.get("type") == "room" and message_data.get("room"):
                    room = message_data["room"]
                    manager.add_to_room(websocket, room)
                    await manager.broadcast_to_room(
                        room,
                        json.dumps({
                            "type": "system",
                            "message": f"Usuario conectado a la sala {room}",
                            "room": room
                        })
                    )
                    continue
                
                # Mensaje privado
                if message_data.get("type") == "private" and message_data.get("target"):
                    # En esta implementación, no se pueden enviar mensajes privados
                    # porque no tenemos identificadores de usuarios
                    await manager.send_personal_message(
                        json.dumps({
                            "type": "error",
                            "message": "Mensajes privados no implementados"
                        }),
                        websocket
                    )
                    continue
                
                # Broadcast general
                await manager.broadcast(
                    json.dumps({
                        "type": "broadcast",
                        "message": message_data.get("message", data),
                        "sender": "anonymous"
                    })
                )
                
            except json.JSONDecodeError:
                # Si no es JSON, enviar como texto plano
                await manager.broadcast(
                    json.dumps({
                        "type": "text",
                        "message": data,
                        "sender": "anonymous"
                    })
                )
                
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(
            json.dumps({
                "type": "system",
                "message": "Usuario desconectado"
            })
        )

@app.websocket("/ws/{room}")
async def websocket_room_endpoint(websocket: WebSocket, room: str):
    await manager.connect(websocket)
    manager.add_to_room(websocket, room)
    
    # Enviar mensaje de bienvenida a la sala
    await manager.broadcast_to_room(
        room,
        json.dumps({
            "type": "system",
            "message": f"Usuario conectado a la sala {room}"
        })
    )
    
    try:
        while True:
            data = await websocket.receive_text()
            
            # Enviar mensaje a la sala
            await manager.broadcast_to_room(
                room,
                json.dumps({
                    "type": "room_message",
                    "room": room,
                    "message": data,
                    "timestamp": str(datetime.now())
                })
            )
            
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast_to_room(
            room,
            json.dumps({
                "type": "system",
                "message": "Usuario desconectado de la sala"
            })
        )

@app.get("/rooms")
async def list_rooms():
    """Lista las salas activas"""
    return {
        "rooms": list(manager.rooms.keys()),
        "total_users": len(manager.active_connections)
    }

if __name__ == "__main__":
    import uvicorn
    from datetime import datetime
    uvicorn.run(app, port=8000)

ejemplos/20_completo_ventas.py
python

"""
Ejemplo 20: API completa de ventas
Integración de todos los conceptos
"""

from fastapi import FastAPI, HTTPException, Depends, status, BackgroundTasks
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
from typing import List, Optional
from datetime import datetime, timedelta
import psycopg2
from psycopg2.extras import RealDictCursor
from passlib.context import CryptContext
from jose import JWTError, jwt
from config import DB_CONFIG, SECRET_KEY, ALGORITHM, ACCESS_TOKEN_EXPIRE_MINUTES

app = FastAPI(title="API de Ventas", version="1.0.0")

# Configuración de seguridad
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="login")

# Modelos
class ProductoCreate(BaseModel):
    nombre: str
    descripcion: Optional[str] = None
    precio: float
    stock: int

class ProductoResponse(BaseModel):
    id: int
    nombre: str
    descripcion: Optional[str]
    precio: float
    stock: int
    created_at: datetime

class VentaCreate(BaseModel):
    producto_id: int
    cantidad: int

class VentaResponse(BaseModel):
    id: int
    usuario_id: int
    producto_id: int
    cantidad: int
    total: float
    fecha: datetime
    producto_nombre: str
    usuario_nombre: str

class UsuarioCreate(BaseModel):
    nombre: str
    email: str
    password: str

class UsuarioResponse(BaseModel):
    id: int
    nombre: str
    email: str
    created_at: datetime

class Token(BaseModel):
    access_token: str
    token_type: str

# Funciones de base de datos
def get_db():
    conn = psycopg2.connect(**DB_CONFIG)
    return conn

def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        email: str = payload.get("sub")
        if email is None:
            raise HTTPException(status_code=401, detail="Token inválido")
    except JWTError:
        raise HTTPException(status_code=401, detail="Token inválido")
    
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    cursor.execute("SELECT * FROM usuarios WHERE email = %s", (email,))
    user = cursor.fetchone()
    cursor.close()
    conn.close()
    
    if user is None:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    return user

# Inicialización
@app.on_event("startup")
def init_database():
    conn = get_db()
    cursor = conn.cursor()
    
    # Tabla de usuarios
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS usuarios (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100) NOT NULL,
            email VARCHAR(100) UNIQUE NOT NULL,
            password_hash VARCHAR(255) NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    # Tabla de productos
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS productos (
            id SERIAL PRIMARY KEY,
            nombre VARCHAR(100) NOT NULL,
            descripcion TEXT,
            precio DECIMAL(10,2) NOT NULL,
            stock INTEGER DEFAULT 0,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    """)
    
    # Tabla de ventas
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS ventas (
            id SERIAL PRIMARY KEY,
            usuario_id INTEGER NOT NULL,
            producto_id INTEGER NOT NULL,
            cantidad INTEGER NOT NULL,
            total DECIMAL(10,2) NOT NULL,
            fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
            FOREIGN KEY (producto_id) REFERENCES productos(id)
        )
    """)
    
    conn.commit()
    cursor.close()
    conn.close()
    print("✅ Base de datos inicializada")

# Endpoints de autenticación
@app.post("/register", response_model=UsuarioResponse)
def register(usuario: UsuarioCreate):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    # Verificar si el email ya existe
    cursor.execute("SELECT id FROM usuarios WHERE email = %s", (usuario.email,))
    if cursor.fetchone():
        raise HTTPException(status_code=400, detail="Email ya registrado")
    
    # Crear usuario
    hashed_password = pwd_context.hash(usuario.password)
    cursor.execute(
        "INSERT INTO usuarios (nombre, email, password_hash) VALUES (%s, %s, %s) RETURNING *",
        (usuario.nombre, usuario.email, hashed_password)
    )
    new_user = cursor.fetchone()
    conn.commit()
    
    cursor.close()
    conn.close()
    
    return new_user

@app.post("/login", response_model=Token)
def login(email: str, password: str):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute("SELECT * FROM usuarios WHERE email = %s", (email,))
    user = cursor.fetchone()
    
    cursor.close()
    conn.close()
    
    if not user or not pwd_context.verify(password, user['password_hash']):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Credenciales incorrectas"
        )
    
    access_token = jwt.encode(
        {"sub": user['email'], "exp": datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)},
        SECRET_KEY,
        algorithm=ALGORITHM
    )
    
    return {"access_token": access_token, "token_type": "bearer"}

# Endpoints de productos
@app.get("/productos", response_model=List[ProductoResponse])
def listar_productos(
    skip: int = 0,
    limit: int = 100,
    current_user: dict = Depends(get_current_user)
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute(
        "SELECT * FROM productos ORDER BY id LIMIT %s OFFSET %s",
        (limit, skip)
    )
    productos = cursor.fetchall()
    
    cursor.close()
    conn.close()
    
    return productos

@app.get("/productos/{id}", response_model=ProductoResponse)
def obtener_producto(id: int, current_user: dict = Depends(get_current_user)):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute("SELECT * FROM productos WHERE id = %s", (id,))
    producto = cursor.fetchone()
    
    cursor.close()
    conn.close()
    
    if not producto:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    return producto

@app.post("/productos", response_model=ProductoResponse)
def crear_producto(
    producto: ProductoCreate,
    current_user: dict = Depends(get_current_user)
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute(
        "INSERT INTO productos (nombre, descripcion, precio, stock) VALUES (%s, %s, %s, %s) RETURNING *",
        (producto.nombre, producto.descripcion, producto.precio, producto.stock)
    )
    new_producto = cursor.fetchone()
    conn.commit()
    
    cursor.close()
    conn.close()
    
    return new_producto

# Endpoints de ventas
@app.post("/ventas", response_model=VentaResponse)
def crear_venta(
    venta: VentaCreate,
    background_tasks: BackgroundTasks,
    current_user: dict = Depends(get_current_user)
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    # Verificar producto
    cursor.execute("SELECT * FROM productos WHERE id = %s", (venta.producto_id,))
    producto = cursor.fetchone()
    
    if not producto:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    
    if producto['stock'] < venta.cantidad:
        raise HTTPException(status_code=400, detail="Stock insuficiente")
    
    # Calcular total
    total = producto['precio'] * venta.cantidad
    
    # Crear venta
    cursor.execute(
        """
        INSERT INTO ventas (usuario_id, producto_id, cantidad, total)
        VALUES (%s, %s, %s, %s) RETURNING *
        """,
        (current_user['id'], venta.producto_id, venta.cantidad, total)
    )
    nueva_venta = cursor.fetchone()
    
    # Actualizar stock
    cursor.execute(
        "UPDATE productos SET stock = stock - %s WHERE id = %s",
        (venta.cantidad, venta.producto_id)
    )
    
    conn.commit()
    cursor.close()
    conn.close()
    
    # Tarea en segundo plano para enviar notificación
    background_tasks.add_task(
        send_notification,
        current_user['email'],
        f"Venta realizada por {total}"
    )
    
    return nueva_venta

def send_notification(email: str, message: str):
    """Simula envío de notificación"""
    import time
    time.sleep(1)
    print(f"Notificación enviada a {email}: {message}")

@app.get("/ventas/usuario", response_model=List[VentaResponse])
def ventas_usuario(current_user: dict = Depends(get_current_user)):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    cursor.execute("""
        SELECT v.*, p.nombre as producto_nombre, u.nombre as usuario_nombre
        FROM ventas v
        JOIN productos p ON v.producto_id = p.id
        JOIN usuarios u ON v.usuario_id = u.id
        WHERE v.usuario_id = %s
        ORDER BY v.fecha DESC
    """, (current_user['id'],))
    
    ventas = cursor.fetchall()
    
    cursor.close()
    conn.close()
    
    return ventas

# Endpoints de reportes
@app.get("/reportes/ventas")
def reporte_ventas(
    fecha_desde: Optional[datetime] = None,
    fecha_hasta: Optional[datetime] = None,
    current_user: dict = Depends(get_current_user)
):
    conn = get_db()
    cursor = conn.cursor(cursor_factory=RealDictCursor)
    
    query = """
        SELECT 
            DATE(v.fecha) as fecha,
            COUNT(v.id) as total_ventas,
            SUM(v.total) as monto_total,
            AVG(v.total) as promedio_venta,
            COUNT(DISTINCT v.usuario_id) as usuarios_unicos
        FROM ventas v
        WHERE 1=1
    """
    params = []
    
    if fecha_desde:
        query += " AND v.fecha >= %s"
        params.append(fecha_desde)
    
    if fecha_hasta:
        query += " AND v.fecha <= %s"
        params.append(fecha_hasta)
    
    query += " GROUP BY DATE(v.fecha) ORDER BY fecha DESC"
    
    cursor.execute(query, params)
    reporte = cursor.fetchall()
    
    cursor.close()
    conn.close()
    
    return reporte

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, port=8000)

🚀 Cómo ejecutar

    Instalar PostgreSQL en tu sistema

    Crear la base de datos:
    sql

    CREATE DATABASE api_exercises;

    Instalar dependencias:
    bash

    pip install -r requirements.txt

    Configurar .env con tus credenciales

    Ejecutar cualquier ejemplo:
    bash

    python ejemplos/02_crud_basico.py

    Acceder a la documentación: http://localhost:8000/docs
