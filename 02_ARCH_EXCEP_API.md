### MODULO 3: MANEJO DE ARCHIVOS, EXCEPCIONES Y APIS CON POO
### OBJETIVO: Implementar clases para manejar archivos, excepciones, logging y consumo de APIs.
### EJERCICIO 2.1: Gestor de Archivos con POO
```python


"""
EJERCICIO 2.1: GESTOR DE ARCHIVOS CON POO
ESENCIA: Clase que maneja archivos TXT, CSV y JSON con manejo de errores
"""

# ============================================
# EJERCICIO 2.1: GESTOR DE ARCHIVOS CON POO
# ESENCIA: Clase que maneja archivos TXT, CSV y JSON con manejo de errores
# ============================================

# LIBRERÍAS UTILIZADAS:
# - json: Para leer/escribir archivos JSON
# - csv: Para leer/escribir archivos CSV usando diccionarios
# - pathlib.Path: Para manejar rutas de archivos de forma orientada a objetos
# - typing: Para anotaciones de tipo (List, Dict, Optional, Any)
# - logging: Para registrar eventos y errores del sistema
import json
import csv
from pathlib import Path
from typing import Any, List, Dict, Optional
import logging

# ============================================
# CONFIGURACIÓN DE LOGGING
# ============================================
# Configura el sistema de logging para mostrar en consola
# Formato: fecha/hora - nivel - mensaje
logging.basicConfig(
    level=logging.INFO,  # Nivel de detalle: INFO, DEBUG, ERROR, etc.
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)  # Crea un logger específico para este módulo

# ============================================
# EXCEPCIONES PERSONALIZADAS
# ============================================
# Se crean clases de excepción para manejar errores específicos de archivos
class ErrorArchivo(Exception):
    """Excepción base para errores de archivos"""
    pass  # pass: instrucción que no hace nada, permite definir una clase vacía

class ErrorFormatoArchivo(ErrorArchivo):
    """Excepción para errores de formato (ej. JSON inválido)"""
    pass

# ============================================
# CLASE GESTOR DE ARCHIVOS
# ============================================
class GestorArchivos:
    """Clase para manejar archivos en múltiples formatos (TXT, CSV, JSON)"""

    def __init__(self, directorio: str = "data"):
        """
        Constructor de la clase
        Args:
            directorio: Ruta donde se almacenarán los archivos
        """
        # Path(): Convierte string en objeto Path para manejo avanzado de rutas
        self.directorio = Path(directorio)
        # mkdir(): Crea el directorio si no existe
        # exist_ok=True: No lanza error si ya existe
        self.directorio.mkdir(exist_ok=True)
        logger.info(f"Directorio creado: {self.directorio}")

    # ============================================
    # ARCHIVOS TXT
    # ============================================
    def leer_txt(self, nombre: str) -> Optional[str]:
        """
        Lee un archivo TXT y retorna su contenido como string
        Args:
            nombre: Nombre del archivo
        Returns:
            Contenido del archivo o None si hay error
        Raises:
            ErrorArchivo: Si el archivo no existe o hay error de lectura
        """
        ruta = self.directorio / nombre  # /: Une rutas con Path
        try:
            # open(): Abre el archivo en modo lectura ('r')
            # with: Maneja el contexto, cierra automáticamente el archivo
            # encoding='utf-8': Especifica codificación para caracteres especiales
            with open(ruta, 'r', encoding='utf-8') as f:
                contenido = f.read()  # read(): Lee todo el contenido
            logger.info(f"TXT leído: {nombre}")
            return contenido
        except FileNotFoundError:
            # raise: Lanza una excepción personalizada
            logger.error(f"Archivo no encontrado: {nombre}")
            raise ErrorArchivo(f"El archivo {nombre} no existe")
        except Exception as e:
            # Captura cualquier otra excepción
            logger.error(f"Error leyendo {nombre}: {e}")
            raise ErrorArchivo(f"Error al leer {nombre}")

    def escribir_txt(self, nombre: str, contenido: str) -> bool:
        """
        Escribe contenido en un archivo TXT
        Args:
            nombre: Nombre del archivo
            contenido: Texto a escribir
        Returns:
            True si se escribió correctamente, False si hay error
        """
        ruta = self.directorio / nombre
        try:
            # 'w': Modo escritura (sobrescribe si existe)
            with open(ruta, 'w', encoding='utf-8') as f:
                f.write(contenido)  # write(): Escribe el contenido
            logger.info(f"TXT escrito: {nombre}")
            return True
        except Exception as e:
            logger.error(f"Error escribiendo {nombre}: {e}")
            return False

    # ============================================
    # ARCHIVOS CSV
    # ============================================
    def leer_csv(self, nombre: str) -> List[Dict]:
        """
        Lee un archivo CSV y lo convierte en lista de diccionarios
        Args:
            nombre: Nombre del archivo
        Returns:
            Lista de diccionarios con los datos, o lista vacía si hay error
        """
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'r', encoding='utf-8') as f:
                # csv.DictReader(): Lee CSV usando la primera fila como claves
                reader = csv.DictReader(f)
                # list(): Convierte el objeto reader en lista de diccionarios
                datos = list(reader)
            logger.info(f"CSV leído: {len(datos)} registros")
            return datos
        except FileNotFoundError:
            logger.error(f"CSV no encontrado: {nombre}")
            return []  # Retorna lista vacía en caso de error
        except Exception as e:
            logger.error(f"Error leyendo CSV {nombre}: {e}")
            return []

    def escribir_csv(self, nombre: str, datos: List[Dict]) -> bool:
        """
        Escribe una lista de diccionarios en un archivo CSV
        Args:
            nombre: Nombre del archivo
            datos: Lista de diccionarios con los datos
        Returns:
            True si se escribió correctamente, False si hay error
        """
        if not datos:
            # warning(): Nivel de logging para advertencias
            logger.warning("No hay datos para guardar")
            return False

        ruta = self.directorio / nombre
        try:
            # newline='': Evita líneas en blanco extra en Windows
            with open(ruta, 'w', encoding='utf-8', newline='') as f:
                # fieldnames: Toma las claves del primer diccionario como encabezados
                writer = csv.DictWriter(f, fieldnames=datos[0].keys())
                writer.writeheader()  # Escribe la primera fila (encabezados)
                writer.writerows(datos)  # Escribe todas las filas
            logger.info(f"CSV guardado: {len(datos)} registros")
            return True
        except Exception as e:
            logger.error(f"Error guardando CSV {nombre}: {e}")
            return False

    # ============================================
    # ARCHIVOS JSON
    # ============================================
    def leer_json(self, nombre: str) -> Optional[Dict]:
        """
        Lee un archivo JSON y lo convierte en diccionario
        Args:
            nombre: Nombre del archivo
        Returns:
            Diccionario con los datos
        Raises:
            ErrorArchivo: Si el archivo no existe
            ErrorFormatoArchivo: Si el JSON tiene formato inválido
        """
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'r', encoding='utf-8') as f:
                # json.load(): Convierte JSON a objeto Python (dict/list)
                datos = json.load(f)
            logger.info(f"JSON leído: {nombre}")
            return datos
        except FileNotFoundError:
            logger.error(f"JSON no encontrado: {nombre}")
            raise ErrorArchivo(f"El archivo {nombre} no existe")
        except json.JSONDecodeError as e:
            # Excepción específica de la librería json
            logger.error(f"Error en formato JSON: {e}")
            raise ErrorFormatoArchivo(f"Formato JSON inválido en {nombre}")

    def escribir_json(self, nombre: str, datos: Any) -> bool:
        """
        Escribe datos en un archivo JSON
        Args:
            nombre: Nombre del archivo
            datos: Datos a guardar (dict, list, etc.)
        Returns:
            True si se escribió correctamente, False si hay error
        """
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'w', encoding='utf-8') as f:
                # json.dump(): Convierte objeto Python a JSON y lo escribe
                # indent=2: Formatea con sangría para legibilidad
                # ensure_ascii=False: Permite caracteres Unicode (ñ, acentos)
                json.dump(datos, f, indent=2, ensure_ascii=False)
            logger.info(f"JSON guardado: {nombre}")
            return True
        except Exception as e:
            logger.error(f"Error guardando JSON {nombre}: {e}")
            return False

    # ============================================
    # MÉTODOS UTILITARIOS
    # ============================================
    def listar_archivos(self, patron: str = "*") -> List[str]:
        """
        Lista los archivos en el directorio que coinciden con un patrón
        Args:
            patron: Patrón de búsqueda (ej. "*.txt", "*.csv")
        Returns:
            Lista de nombres de archivos
        """
        # glob(): Retorna un generador con todos los archivos que coinciden
        # List comprehension: [f.name for f in ...] crea lista de nombres
        return [f.name for f in self.directorio.glob(patron)]

    def existe(self, nombre: str) -> bool:
        """
        Verifica si un archivo existe en el directorio
        Args:
            nombre: Nombre del archivo
        Returns:
            True si existe, False si no
        """
        # exists(): Método de Path que verifica existencia
        return (self.directorio / nombre).exists()

    def __str__(self):
        """
        Método especial que define cómo se representa el objeto como string
        Se invoca con print() o str()
        """
        return f"📁 Gestor: {self.directorio} ({len(self.listar_archivos())} archivos)"

# ============================================
# DEMOSTRACIÓN
# ============================================
# __name__ == "__main__": Esta condición ejecuta el código solo si el script
# se ejecuta directamente, no si se importa como módulo
if __name__ == "__main__":
    # Crear instancia del gestor
    gestor = GestorArchivos()

    # Probar TXT
    print("📄 ARCHIVOS TXT:")
    gestor.escribir_txt("ejemplo.txt", "Contenido de prueba")
    contenido = gestor.leer_txt("ejemplo.txt")
    print(f"  Contenido: {contenido}")

    # Probar CSV
    print("\n📊 ARCHIVOS CSV:")
    datos = [
        {"nombre": "Ana", "edad": 25},
        {"nombre": "Luis", "edad": 30}
    ]
    gestor.escribir_csv("personas.csv", datos)
    leidos = gestor.leer_csv("personas.csv")
    print(f"  Datos: {leidos}")

    # Probar JSON
    print("\n🔷 ARCHIVOS JSON:")
    gestor.escribir_json("persona.json", {"nombre": "Ana", "edad": 25})
    datos_json = gestor.leer_json("persona.json")
    print(f"  Datos: {datos_json}")

    # Listar archivos
    print(f"\n📁 Archivos en {gestor}:")
    for archivo in gestor.listar_archivos():
        print(f"  - {archivo}")
```
### EJERCICIO 2.2: Sistema con Excepciones Personalizadas
```python
"""
EJERCICIO 2.2: SISTEMA CON EXCEPCIONES PERSONALIZADAS
ESENCIA: Clases con manejo de errores y excepciones personalizadas
"""

import logging
from datetime import datetime
from typing import Optional, List

# ============================================
# CONFIGURACIÓN DE LOGGING
# ============================================
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('sistema.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

# ============================================
# EXCEPCIONES PERSONALIZADAS
# ============================================
class ErrorSistema(Exception):
    """Excepción base del sistema"""
    pass

class ErrorUsuario(ErrorSistema):
    """Excepción para errores de usuario"""
    pass

class ErrorValidacion(ErrorSistema):
    """Excepción para errores de validación"""
    pass

class ErrorTransaccion(ErrorSistema):
    """Excepción para errores de transacción"""
    pass

# ============================================
# CLASE SISTEMA CON EXCEPCIONES
# ============================================
class SistemaTransacciones:
    """Sistema con manejo de excepciones y transacciones"""
    
    def __init__(self):
        self.transacciones = []
        self.usuarios = {}
    
    def validar_usuario(self, nombre: str) -> bool:
        """Valida un usuario"""
        try:
            if not nombre or len(nombre) < 3:
                raise ErrorValidacion("Nombre debe tener al menos 3 caracteres")
            
            if nombre in self.usuarios:
                raise ErrorUsuario(f"Usuario '{nombre}' ya existe")
            
            return True
        except ErrorValidacion as e:
            logger.error(f"Error de validación: {e}")
            return False
        except ErrorUsuario as e:
            logger.error(f"Error de usuario: {e}")
            return False
    
    def registrar_usuario(self, nombre: str) -> bool:
        """Registra un nuevo usuario"""
        try:
            if not self.validar_usuario(nombre):
                return False
            
            self.usuarios[nombre] = {
                "nombre": nombre,
                "fecha_registro": datetime.now(),
                "transacciones": 0
            }
            logger.info(f"Usuario registrado: {nombre}")
            return True
        
        except Exception as e:
            logger.critical(f"Error inesperado: {e}")
            return False
    
    def realizar_transaccion(self, usuario: str, monto: float) -> bool:
        """Realiza una transacción"""
        try:
            # Validaciones
            if usuario not in self.usuarios:
                raise ErrorUsuario(f"Usuario '{usuario}' no existe")
            
            if monto <= 0:
                raise ErrorValidacion(f"Monto inválido: {monto}")
            
            if monto > 10000:
                raise ErrorTransaccion(f"Monto excede límite: {monto}")
            
            # Registrar transacción
            transaccion = {
                "usuario": usuario,
                "monto": monto,
                "fecha": datetime.now(),
                "estado": "completada"
            }
            self.transacciones.append(transaccion)
            self.usuarios[usuario]["transacciones"] += 1
            
            logger.info(f"Transacción completada: {usuario} - ${monto}")
            return True
        
        except ErrorUsuario as e:
            logger.error(f"Error de usuario: {e}")
            return False
        except ErrorValidacion as e:
            logger.error(f"Error de validación: {e}")
            return False
        except ErrorTransaccion as e:
            logger.error(f"Error de transacción: {e}")
            return False
        except Exception as e:
            logger.critical(f"Error inesperado: {e}")
            return False
    
    def obtener_transacciones_usuario(self, usuario: str) -> List[Dict]:
        """Obtiene transacciones de un usuario"""
        try:
            if usuario not in self.usuarios:
                raise ErrorUsuario(f"Usuario '{usuario}' no existe")
            
            return [t for t in self.transacciones if t["usuario"] == usuario]
        except ErrorUsuario as e:
            logger.error(f"Error: {e}")
            return []
    
    def estadisticas(self) -> Dict:
        """Estadísticas del sistema"""
        return {
            "total_usuarios": len(self.usuarios),
            "total_transacciones": len(self.transacciones),
            "monto_total": sum(t["monto"] for t in self.transacciones),
            "transacciones_por_usuario": {
                u: self.usuarios[u]["transacciones"] 
                for u in self.usuarios
            }
        }

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    print("=" * 60)
    print("SISTEMA DE TRANSACCIONES CON EXCEPCIONES")
    print("=" * 60)
    
    sistema = SistemaTransacciones()
    
    # Registrar usuarios
    print("\n📝 REGISTRANDO USUARIOS:")
    sistema.registrar_usuario("Ana")
    sistema.registrar_usuario("Luis")
    sistema.registrar_usuario("An")  # Error - nombre corto
    
    # Realizar transacciones
    print("\n💰 REALIZANDO TRANSACCIONES:")
    sistema.realizar_transaccion("Ana", 1000)
    sistema.realizar_transaccion("Luis", 500)
    sistema.realizar_transaccion("Ana", 15000)  # Error - excede límite
    sistema.realizar_transaccion("Carlos", 100)  # Error - usuario no existe
    
    # Estadísticas
    print("\n📊 ESTADÍSTICAS:")
    stats = sistema.estadisticas()
    for clave, valor in stats.items():
        print(f"  {clave}: {valor}")
    
    # Transacciones de usuario
    print("\n📋 TRANSACCIONES DE ANA:")
    transacciones = sistema.obtener_transacciones_usuario("Ana")
    for t in transacciones:
        print(f"  ${t['monto']} - {t['fecha'].strftime('%H:%M:%S')}")
```
### EJERCICIO 2.3: Cliente API con POO y Manejo de Errores
```python
"""
EJERCICIO 2.3: CLIENTE API CON POO Y MANEJO DE ERRORES
ESENCIA: Clase para consumir APIs con manejo de excepciones y logging
"""

import requests
import json
import logging
from typing import Optional, Dict, List, Any
from datetime import datetime

# ============================================
# CONFIGURACIÓN DE LOGGING
# ============================================
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# ============================================
# EXCEPCIONES PERSONALIZADAS
# ============================================
class ErrorAPI(Exception):
    """Excepción para errores de API"""
    pass

class ErrorConexion(ErrorAPI):
    """Excepción para errores de conexión"""
    pass

class ErrorRespuesta(ErrorAPI):
    """Excepción para errores en la respuesta"""
    pass

# ============================================
# CLASE CLIENTE API
# ============================================
class ClienteAPI:
    """Cliente para consumir APIs REST"""
    
    def __init__(self, base_url: str, timeout: int = 10):
        self.base_url = base_url
        self.timeout = timeout
        self.sesion = requests.Session()
        logger.info(f"Cliente API inicializado: {base_url}")
    
    def _peticion(self, metodo: str, endpoint: str = "", **kwargs) -> Optional[Dict]:
        """Método interno para hacer peticiones HTTP"""
        url = f"{self.base_url}/{endpoint}" if endpoint else self.base_url
        
        try:
            response = self.sesion.request(
                metodo, url,
                timeout=self.timeout,
                **kwargs
            )
            response.raise_for_status()
            
            try:
                return response.json()
            except json.JSONDecodeError:
                return {"texto": response.text}
                
        except requests.exceptions.ConnectionError:
            logger.error(f"Error de conexión a {url}")
            raise ErrorConexion("No se pudo conectar al servidor")
        except requests.exceptions.Timeout:
            logger.error(f"Timeout en {url}")
            raise ErrorConexion("Tiempo de espera agotado")
        except requests.exceptions.HTTPError as e:
            logger.error(f"Error HTTP {e.response.status_code}")
            raise ErrorRespuesta(f"Error HTTP: {e.response.status_code}")
        except Exception as e:
            logger.error(f"Error inesperado: {e}")
            raise ErrorAPI(f"Error: {e}")
    
    def get(self, endpoint: str = "", params: Dict = None) -> Optional[Dict]:
        """Petición GET"""
        return self._peticion("GET", endpoint, params=params)
    
    def post(self, endpoint: str = "", data: Dict = None) -> Optional[Dict]:
        """Petición POST"""
        return self._peticion("POST", endpoint, json=data)
    
    def put(self, endpoint: str = "", data: Dict = None) -> Optional[Dict]:
        """Petición PUT"""
        return self._peticion("PUT", endpoint, json=data)
    
    def delete(self, endpoint: str = "") -> Optional[Dict]:
        """Petición DELETE"""
        return self._peticion("DELETE", endpoint)

class ProcesadorAPI(ClienteAPI):
    """Procesador de datos de API con POO"""
    
    def __init__(self, base_url: str):
        super().__init__(base_url)
        self.datos_procesados = []
        self.historial = []
    
    def obtener_usuarios(self) -> List[Dict]:
        """Obtiene y procesa usuarios"""
        try:
            datos = self.get("users")
            if not datos:
                return []
            
            procesados = []
            for u in datos:
                procesados.append({
                    "id": u.get("id"),
                    "nombre": u.get("name"),
                    "email": u.get("email"),
                    "ciudad": u.get("address", {}).get("city", "Desconocida"),
                    "empresa": u.get("company", {}).get("name", "Desconocida")
                })
            
            self.datos_procesados = procesados
            logger.info(f"Procesados {len(procesados)} usuarios")
            self._registrar("usuarios", len(procesados))
            return procesados
            
        except ErrorAPI as e:
            logger.error(f"Error obteniendo usuarios: {e}")
            return []
    
    def obtener_posts(self, usuario_id: Optional[int] = None) -> List[Dict]:
        """Obtiene posts (opcionalmente de un usuario)"""
        try:
            params = {"userId": usuario_id} if usuario_id else None
            datos = self.get("posts", params)
            
            if not datos:
                return []
            
            logger.info(f"Obtenidos {len(datos)} posts")
            self._registrar("posts", len(datos))
            return datos
            
        except ErrorAPI as e:
            logger.error(f"Error obteniendo posts: {e}")
            return []
    
    def obtener_comentarios(self, post_id: int) -> List[Dict]:
        """Obtiene comentarios de un post"""
        try:
            datos = self.get(f"posts/{post_id}/comments")
            logger.info(f"Obtenidos {len(datos)} comentarios")
            return datos
        except ErrorAPI as e:
            logger.error(f"Error obteniendo comentarios: {e}")
            return []
    
    def _registrar(self, operacion: str, cantidad: int):
        """Registra operación en el historial"""
        self.historial.append({
            "operacion": operacion,
            "cantidad": cantidad,
            "fecha": datetime.now().isoformat()
        })
    
    def estadisticas(self) -> Dict:
        """Estadísticas del procesador"""
        return {
            "total_operaciones": len(self.historial),
            "datos_procesados": len(self.datos_procesados) if self.datos_procesados else 0,
            "historial": self.historial[-5:]  # Últimas 5 operaciones
        }

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    print("=" * 60)
    print("CLIENTE API CON POO")
    print("=" * 60)
    
    # Crear cliente
    api = ProcesadorAPI("https://jsonplaceholder.typicode.com")
    
    try:
        # Obtener usuarios
        print("\n📥 OBTENIENDO USUARIOS:")
        usuarios = api.obtener_usuarios()
        if usuarios:
            print(f"  Total: {len(usuarios)}")
            for u in usuarios[:3]:
                print(f"  - {u['nombre']} ({u['ciudad']})")
        
        # Obtener posts
        print("\n📥 OBTENIENDO POSTS:")
        posts = api.obtener_posts(1)
        if posts:
            print(f"  Total: {len(posts)}")
            for p in posts[:2]:
                print(f"  - {p['title'][:50]}...")
        
        # Estadísticas
        print("\n📊 ESTADÍSTICAS:")
        stats = api.estadisticas()
        for clave, valor in stats.items():
            print(f"  {clave}: {valor}")
            
    except ErrorAPI as e:
        print(f"❌ Error de API: {e}")
```
### MODULO 3: Sistema de Procesamiento de Datos con POO
```python
"""
MINI-PROYECTO DÍA 2: SISTEMA DE PROCESAMIENTO DE DATOS CON POO
ESENCIA: Sistema completo que integra archivos, excepciones y APIs
"""

import requests
import json
import csv
import logging
from pathlib import Path
from datetime import datetime
from typing import List, Dict, Optional, Any
from abc import ABC, abstractmethod

# ============================================
# CONFIGURACIÓN DE LOGGING
# ============================================
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('procesador.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

# ============================================
# EXCEPCIONES PERSONALIZADAS
# ============================================
class ErrorProcesamiento(Exception):
    pass

class ErrorExtraccion(ErrorProcesamiento):
    pass

class ErrorAlmacenamiento(ErrorProcesamiento):
    pass

# ============================================
# CLASE ABSTRACTA
# ============================================
class ProcesadorDatos(ABC):
    """Clase abstracta para procesadores de datos"""
    
    @abstractmethod
    def extraer(self) -> List[Dict]:
        pass
    
    @abstractmethod
    def transformar(self, datos: List[Dict]) -> List[Dict]:
        pass
    
    @abstractmethod
    def cargar(self, datos: List[Dict]) -> bool:
        pass

# ============================================
# CLASE CONCRETA
# ============================================
class ProcesadorAPI(ProcesadorDatos):
    """Procesador de datos desde API"""
    
    def __init__(self, base_url: str, directorio: str = "output"):
        self.base_url = base_url
        self.directorio = Path(directorio)
        self.directorio.mkdir(exist_ok=True)
        logger.info(f"Procesador API inicializado")
    
    def extraer(self, limite: int = 10) -> List[Dict]:
        """Extrae datos desde API"""
        try:
            url = f"{self.base_url}/posts"
            response = requests.get(url, timeout=10)
            response.raise_for_status()
            datos = response.json()[:limite]
            logger.info(f"Extraídos {len(datos)} registros")
            return datos
        except Exception as e:
            logger.error(f"Error extrayendo: {e}")
            raise ErrorExtraccion(f"No se pudieron extraer datos: {e}")
    
    def transformar(self, datos: List[Dict]) -> List[Dict]:
        """Transforma datos agregando metadata"""
        if not datos:
            return []
        
        transformados = []
        for d in datos:
            transformados.append({
                "id": d.get("id"),
                "usuario": d.get("userId"),
                "titulo": d.get("title", ""),
                "resumen": d.get("body", "")[:50] + "...",
                "longitud": len(d.get("body", "")),
                "fecha_analisis": datetime.now().isoformat()
            })
        logger.info(f"Transformados {len(transformados)} registros")
        return transformados
    
    def cargar(self, datos: List[Dict]) -> bool:
        """Carga datos en múltiples formatos"""
        try:
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            
            # Guardar JSON
            archivo_json = self.directorio / f"datos_{timestamp}.json"
            with open(archivo_json, 'w', encoding='utf-8') as f:
                json.dump(datos, f, indent=2, ensure_ascii=False)
            logger.info(f"JSON guardado: {archivo_json}")
            
            # Guardar CSV
            if datos:
                archivo_csv = self.directorio / f"datos_{timestamp}.csv"
                with open(archivo_csv, 'w', encoding='utf-8', newline='') as f:
                    writer = csv.DictWriter(f, fieldnames=datos[0].keys())
                    writer.writeheader()
                    writer.writerows(datos)
                logger.info(f"CSV guardado: {archivo_csv}")
            
            return True
        except Exception as e:
            logger.error(f"Error cargando datos: {e}")
            raise ErrorAlmacenamiento(f"No se pudieron cargar datos: {e}")
    
    def ejecutar(self, limite: int = 10) -> Dict:
        """Ejecuta el flujo completo"""
        try:
            # Extraer
            datos = self.extraer(limite)
            if not datos:
                return {"error": "No se extrajeron datos"}
            
            # Transformar
            transformados = self.transformar(datos)
            
            # Cargar
            self.cargar(transformados)
            
            return {
                "extraidos": len(datos),
                "transformados": len(transformados),
                "estado": "completado"
            }
        except ErrorProcesamiento as e:
            return {"error": str(e)}
        except Exception as e:
            logger.critical(f"Error inesperado: {e}")
            return {"error": f"Error inesperado: {e}"}

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    print("=" * 60)
    print("MINI-PROYECTO DÍA 2: PROCESADOR DE DATOS CON POO")
    print("=" * 60)
    
    # Crear procesador
    procesador = ProcesadorAPI("https://jsonplaceholder.typicode.com")
    
    # Ejecutar
    print("\n🚀 INICIANDO PROCESAMIENTO...")
    resultado = procesador.ejecutar(5)
    
    print("\n📊 RESULTADO:")
    for clave, valor in resultado.items():
        print(f"  {clave}: {valor}")
```
