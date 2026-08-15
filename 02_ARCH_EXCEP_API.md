### DÍA 2: MANEJO DE ARCHIVOS, EXCEPCIONES Y APIS CON POO
### OBJETIVO: Implementar clases para manejar archivos, excepciones, logging y consumo de APIs.
### EJERCICIO 2.1: Gestor de Archivos con POO
```python


"""
EJERCICIO 2.1: GESTOR DE ARCHIVOS CON POO
ESENCIA: Clase que maneja archivos TXT, CSV y JSON con manejo de errores
"""

import json
import csv
from pathlib import Path
from typing import Any, List, Dict, Optional
import logging

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
class ErrorArchivo(Exception):
    """Excepción base para errores de archivos"""
    pass

class ErrorFormatoArchivo(ErrorArchivo):
    """Excepción para errores de formato"""
    pass

# ============================================
# CLASE GESTOR DE ARCHIVOS
# ============================================
class GestorArchivos:
    """Clase para manejar archivos en múltiples formatos"""
    
    def __init__(self, directorio: str = "data"):
        self.directorio = Path(directorio)
        self.directorio.mkdir(exist_ok=True)
        logger.info(f"Directorio creado: {self.directorio}")
    
    # ============================================
    # ARCHIVOS TXT
    # ============================================
    def leer_txt(self, nombre: str) -> Optional[str]:
        """Lee archivo TXT con manejo de errores"""
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'r', encoding='utf-8') as f:
                contenido = f.read()
            logger.info(f"TXT leído: {nombre}")
            return contenido
        except FileNotFoundError:
            logger.error(f"Archivo no encontrado: {nombre}")
            raise ErrorArchivo(f"El archivo {nombre} no existe")
        except Exception as e:
            logger.error(f"Error leyendo {nombre}: {e}")
            raise ErrorArchivo(f"Error al leer {nombre}")
    
    def escribir_txt(self, nombre: str, contenido: str) -> bool:
        """Escribe archivo TXT"""
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'w', encoding='utf-8') as f:
                f.write(contenido)
            logger.info(f"TXT escrito: {nombre}")
            return True
        except Exception as e:
            logger.error(f"Error escribiendo {nombre}: {e}")
            return False
    
    # ============================================
    # ARCHIVOS CSV
    # ============================================
    def leer_csv(self, nombre: str) -> List[Dict]:
        """Lee archivo CSV como lista de diccionarios"""
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'r', encoding='utf-8') as f:
                reader = csv.DictReader(f)
                datos = list(reader)
            logger.info(f"CSV leído: {len(datos)} registros")
            return datos
        except FileNotFoundError:
            logger.error(f"CSV no encontrado: {nombre}")
            return []
        except Exception as e:
            logger.error(f"Error leyendo CSV {nombre}: {e}")
            return []
    
    def escribir_csv(self, nombre: str, datos: List[Dict]) -> bool:
        """Escribe lista de diccionarios a CSV"""
        if not datos:
            logger.warning("No hay datos para guardar")
            return False
        
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'w', encoding='utf-8', newline='') as f:
                writer = csv.DictWriter(f, fieldnames=datos[0].keys())
                writer.writeheader()
                writer.writerows(datos)
            logger.info(f"CSV guardado: {len(datos)} registros")
            return True
        except Exception as e:
            logger.error(f"Error guardando CSV {nombre}: {e}")
            return False
    
    # ============================================
    # ARCHIVOS JSON
    # ============================================
    def leer_json(self, nombre: str) -> Optional[Dict]:
        """Lee archivo JSON"""
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'r', encoding='utf-8') as f:
                datos = json.load(f)
            logger.info(f"JSON leído: {nombre}")
            return datos
        except FileNotFoundError:
            logger.error(f"JSON no encontrado: {nombre}")
            raise ErrorArchivo(f"El archivo {nombre} no existe")
        except json.JSONDecodeError as e:
            logger.error(f"Error en formato JSON: {e}")
            raise ErrorFormatoArchivo(f"Formato JSON inválido en {nombre}")
    
    def escribir_json(self, nombre: str, datos: Any) -> bool:
        """Escribe archivo JSON"""
        ruta = self.directorio / nombre
        try:
            with open(ruta, 'w', encoding='utf-8') as f:
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
        """Lista archivos en el directorio"""
        return [f.name for f in self.directorio.glob(patron)]
    
    def existe(self, nombre: str) -> bool:
        """Verifica si un archivo existe"""
        return (self.directorio / nombre).exists()
    
    def __str__(self):
        return f"📁 Gestor: {self.directorio} ({len(self.listar_archivos())} archivos)"

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Crear gestor
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
