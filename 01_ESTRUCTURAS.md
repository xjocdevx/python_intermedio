### DÍA 1: ESTRUCTURAS DE DATOS AVANZADAS Y FUNCIONES
### OBJETIVO: Aplicar POO para manejar list comprehensions, slicing, sets, diccionarios, defaultdict, decoradores y funciones con argumentos variables.
### EJERCICIO 1.1: Sistema de Inventario con List Comprehensions y Slicing
```python
"""
EJERCICIO 1.1: SISTEMA DE INVENTARIO CON POO
ESENCIA: Clase que maneja productos usando list comprehensions y slicing
"""

from typing import List, Optional

class Producto:
    """Representa un producto del inventario"""
    
    def __init__(self, id: int, nombre: str, precio: float, stock: int):
        self.id = id
        self.nombre = nombre
        self.precio = precio
        self.stock = stock
    
    def valor_total(self) -> float:
        """Calcula el valor total del stock"""
        return self.precio * self.stock
    
    def __str__(self):
        return f"{self.nombre} (${self.precio}) - Stock: {self.stock}"

class Inventario:
    """Sistema de inventario usando list comprehensions y slicing"""
    
    def __init__(self):
        self.productos: List[Producto] = []
    
    def agregar(self, producto: Producto):
        """Agrega un producto al inventario"""
        self.productos.append(producto)
    
    # ============================================
    # LIST COMPREHENSIONS
    # ============================================
    def filtrar_por_precio(self, minimo: float) -> List[Producto]:
        """Filtra productos por precio mínimo usando list comprehension"""
        return [p for p in self.productos if p.precio >= minimo]
    
    def obtener_nombres(self) -> List[str]:
        """Obtiene todos los nombres usando list comprehension"""
        return [p.nombre for p in self.productos]
    
    def productos_con_descuento(self, descuento: float) -> List[Producto]:
        """Aplica descuento a productos usando list comprehension"""
        # Crear nuevos productos con descuento
        return [
            Producto(p.id, p.nombre, p.precio * (1 - descuento), p.stock)
            for p in self.productos
        ]
    
    def productos_agotados(self) -> List[Producto]:
        """Filtra productos sin stock"""
        return [p for p in self.productos if p.stock == 0]
    
    def top_productos(self, n: int = 3) -> List[Producto]:
        """Obtiene los n productos más caros usando sorted y slicing"""
        ordenados = sorted(self.productos, key=lambda p: p.precio, reverse=True)
        return ordenados[:n]
    
    # ============================================
    # DICT COMPREHENSIONS
    # ============================================
    def resumen_precios(self) -> dict:
        """Resumen de precios usando dict comprehension"""
        return {p.nombre: p.precio for p in self.productos}
    
    def productos_por_stock(self) -> dict:
        """Diccionario de productos por stock usando dict comprehension"""
        return {p.nombre: p.stock for p in self.productos}
    
    def estadisticas(self) -> dict:
        """Estadísticas del inventario"""
        return {
            "total_productos": len(self.productos),
            "precio_promedio": sum(p.precio for p in self.productos) / len(self.productos) if self.productos else 0,
            "stock_total": sum(p.stock for p in self.productos),
            "valor_total_inventario": sum(p.valor_total() for p in self.productos)
        }
    
    def __str__(self):
        return f"📦 Inventario: {len(self.productos)} productos"

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Crear inventario
    inventario = Inventario()
    
    # Agregar productos
    inventario.agregar(Producto(1, "Laptop", 1200, 10))
    inventario.agregar(Producto(2, "Mouse", 25, 50))
    inventario.agregar(Producto(3, "Teclado", 80, 30))
    inventario.agregar(Producto(4, "Monitor", 300, 15))
    inventario.agregar(Producto(5, "Impresora", 150, 0))
    
    print(inventario)
    
    # List comprehensions
    print("\n🔍 Productos > $100:")
    for p in inventario.filtrar_por_precio(100):
        print(f"  {p}")
    
    # Slicing - top productos
    print("\n🏆 Top 3 productos más caros:")
    for p in inventario.top_productos(3):
        print(f"  {p}")
    
    # Dict comprehension
    print("\n📊 Resumen de precios:")
    print(inventario.resumen_precios())
    
    # Estadísticas
    print("\n📈 Estadísticas:")
    for clave, valor in inventario.estadisticas().items():
        print(f"  {clave}: {valor}")
```
### EJERCICIO 1.2: Sistema de Ventas con Tuplas, Sets y Desempaquetado
```python
"""
EJERCICIO 1.2: SISTEMA DE VENTAS CON POO
ESENCIA: Clase que maneja ventas usando tuplas, sets y desempaquetado
"""

from typing import List, Set, Tuple
from collections import Counter
from datetime import datetime

class Venta:
    """Representa una venta usando tuplas para almacenar datos"""
    
    def __init__(self, producto: str, categoria: str, precio: float, cantidad: int, fecha: str):
        # Usar tupla para almacenar datos inmutables de la venta
        self.datos = (producto, categoria, precio, cantidad, fecha)
        self.total = precio * cantidad
    
    @property
    def producto(self) -> str:
        return self.datos[0]
    
    @property
    def categoria(self) -> str:
        return self.datos[1]
    
    @property
    def precio(self) -> float:
        return self.datos[2]
    
    @property
    def cantidad(self) -> int:
        return self.datos[3]
    
    @property
    def fecha(self) -> str:
        return self.datos[4]
    
    def __str__(self):
        return f"{self.producto} x{self.cantidad} = ${self.total}"

class SistemaVentas:
    """Sistema de ventas usando sets y desempaquetado"""
    
    def __init__(self):
        self.ventas: List[Venta] = []
    
    def registrar_venta(self, venta: Venta):
        """Registra una nueva venta"""
        self.ventas.append(venta)
    
    # ============================================
    # SETS - ELEMENTOS ÚNICOS
    # ============================================
    def productos_unicos(self) -> Set[str]:
        """Obtiene productos únicos vendidos usando set comprehension"""
        return {v.producto for v in self.ventas}
    
    def categorias_unicas(self) -> Set[str]:
        """Obtiene categorías únicas usando set comprehension"""
        return {v.categoria for v in self.ventas}
    
    def productos_frecuentes(self) -> Set[str]:
        """Productos con más de 3 ventas"""
        from collections import Counter
        contador = Counter(v.producto for v in self.ventas)
        return {producto for producto, count in contador.items() if count > 3}
    
    # ============================================
    # DESEMPAQUETADO DE TUPLAS
    # ============================================
    def ventas_resumidas(self) -> List[Tuple[str, float]]:
        """Retorna lista de tuplas (producto, total) desempaquetadas"""
        return [(v.producto, v.total) for v in self.ventas]
    
    def analisis_por_categoria(self) -> dict:
        """Análisis de ventas por categoría usando desempaquetado"""
        from collections import defaultdict
        categorias = defaultdict(list)
        
        # Desempaquetar datos de cada venta
        for venta in self.ventas:
            # Desempaquetar tupla de datos
            producto, categoria, precio, cantidad, fecha = venta.datos
            categorias[categoria].append((producto, precio * cantidad))
        
        # Procesar resultados con dict comprehension
        return {
            categoria: {
                "total_ventas": len(ventas),
                "ingreso_total": sum(total for _, total in ventas),
                "productos": [prod for prod, _ in ventas]
            }
            for categoria, ventas in categorias.items()
        }
    
    # ============================================
    # OPERACIONES CON SETS
    # ============================================
    def comparar_con_otro_sistema(self, otro_sistema: 'SistemaVentas') -> dict:
        """Compara este sistema con otro usando sets"""
        productos1 = self.productos_unicos()
        productos2 = otro_sistema.productos_unicos()
        
        return {
            "ambos": productos1 & productos2,  # Intersección
            "solo_primero": productos1 - productos2,  # Diferencia
            "solo_segundo": productos2 - productos1,
            "todos": productos1 | productos2  # Unión
        }
    
    def estadisticas(self) -> dict:
        """Estadísticas del sistema de ventas"""
        if not self.ventas:
            return {"total_ventas": 0}
        
        return {
            "total_ventas": len(self.ventas),
            "productos_unicos": len(self.productos_unicos()),
            "categorias_unicas": len(self.categorias_unicas()),
            "ingreso_total": sum(v.total for v in self.ventas),
            "promedio_venta": sum(v.total for v in self.ventas) / len(self.ventas)
        }

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Crear sistema de ventas
    sistema = SistemaVentas()
    
    # Registrar ventas
    sistema.registrar_venta(Venta("Laptop", "Electrónica", 1200, 2, "2026-08-10"))
    sistema.registrar_venta(Venta("Mouse", "Electrónica", 25, 5, "2026-08-10"))
    sistema.registrar_venta(Venta("Cuaderno", "Papelería", 3, 10, "2026-08-11"))
    sistema.registrar_venta(Venta("Monitor", "Electrónica", 300, 1, "2026-08-11"))
    sistema.registrar_venta(Venta("Laptop", "Electrónica", 1200, 1, "2026-08-12"))
    
    print("📊 SISTEMA DE VENTAS")
    print(sistema.estadisticas())
    
    # Sets - productos únicos
    print(f"\n🔍 Productos únicos: {sistema.productos_unicos()}")
    print(f"📂 Categorías únicas: {sistema.categorias_unicas()}")
    
    # Desempaquetado de tuplas
    print("\n📋 Ventas resumidas:")
    for producto, total in sistema.ventas_resumidas():
        print(f"  {producto}: ${total}")
    
    # Análisis por categoría
    print("\n📊 Análisis por categoría:")
    for categoria, datos in sistema.analisis_por_categoria().items():
        print(f"  {categoria}: {datos['total_ventas']} ventas, ${datos['ingreso_total']}")
```
### EJERCICIO 1.3: Gestor de Datos con Diccionarios y defaultdict
```python
"""
EJERCICIO 1.3: GESTOR DE DATOS CON POO
ESENCIA: Clase que maneja datos usando diccionarios y defaultdict
"""

from collections import defaultdict, Counter
from typing import Dict, List, Any, Optional

class GestorDatos:
    """Gestiona datos usando diccionarios y defaultdict"""
    
    def __init__(self):
        # defaultdict para agrupar datos automáticamente
        self.datos = defaultdict(list)
        self.contadores = defaultdict(int)
        self.agrupados = defaultdict(list)
    
    def agregar_dato(self, categoria: str, valor: Any):
        """Agrega un dato a una categoría"""
        self.datos[categoria].append(valor)
        self.contadores[categoria] += 1
    
    # ============================================
    # DEFAULTDICT CON LISTAS
    # ============================================
    def agrupar_por_tipo(self, elementos: List[Any]) -> Dict[str, List[Any]]:
        """Agrupa elementos por tipo usando defaultdict"""
        grupos = defaultdict(list)
        for elem in elementos:
            grupos[type(elem).__name__].append(elem)
        return dict(grupos)
    
    def agrupar_por_longitud(self, palabras: List[str]) -> Dict[int, List[str]]:
        """Agrupa palabras por longitud usando defaultdict"""
        grupos = defaultdict(list)
        for palabra in palabras:
            grupos[len(palabra)].append(palabra)
        return dict(grupos)
    
    # ============================================
    # DEFAULTDICT CON CONTADORES
    # ============================================
    def contar_frecuencias(self, elementos: List[Any]) -> Dict[Any, int]:
        """Cuenta frecuencias usando defaultdict(int)"""
        contador = defaultdict(int)
        for elem in elementos:
            contador[elem] += 1
        return dict(contador)
    
    def contar_por_categoria(self, elementos: List[tuple]) -> Dict[str, int]:
        """Cuenta elementos por categoría"""
        contador = defaultdict(int)
        for categoria, _ in elementos:
            contador[categoria] += 1
        return dict(contador)
    
    # ============================================
    # MÉTODOS CON DICCIONARIOS
    # ============================================
    def fusionar_diccionarios(self, *diccionarios: Dict) -> Dict:
        """Fusiona múltiples diccionarios usando update"""
        resultado = {}
        for dic in diccionarios:
            resultado.update(dic)
        return resultado
    
    def obtener_con_default(self, diccionario: Dict, clave: str, default: Any = None) -> Any:
        """Obtiene valor con default usando get"""
        return diccionario.get(clave, default)
    
    def procesar_ventas(self, ventas: List[Dict]) -> Dict:
        """Procesa ventas usando defaultdict para estadísticas"""
        # Agrupar por categoría
        por_categoria = defaultdict(list)
        por_producto = defaultdict(int)
        
        for venta in ventas:
            categoria = venta.get('categoria', 'Sin categoría')
            producto = venta.get('producto')
            cantidad = venta.get('cantidad', 0)
            
            por_categoria[categoria].append(venta)
            por_producto[producto] += cantidad
        
        return {
            "ventas_por_categoria": dict(por_categoria),
            "cantidad_por_producto": dict(por_producto),
            "total_categorias": len(por_categoria)
        }
    
    def resumen(self) -> Dict:
        """Resumen de datos gestionados"""
        return {
            "total_categorias": len(self.datos),
            "total_elementos": sum(len(v) for v in self.datos.values()),
            "por_categoria": {k: len(v) for k, v in self.datos.items()}
        }

class AnalizadorTexto:
    """Analiza texto usando defaultdict y Counter"""
    
    def __init__(self, texto: str):
        self.texto = texto
        self.palabras = texto.lower().split()
    
    def frecuencia_palabras(self) -> Dict[str, int]:
        """Frecuencia usando Counter"""
        return dict(Counter(self.palabras))
    
    def agrupar_por_longitud(self) -> Dict[int, List[str]]:
        """Agrupa palabras por longitud usando defaultdict"""
        grupos = defaultdict(list)
        for palabra in self.palabras:
            grupos[len(palabra)].append(palabra)
        return dict(grupos)
    
    def palabras_por_letra(self) -> Dict[str, List[str]]:
        """Agrupa palabras por primera letra usando defaultdict"""
        grupos = defaultdict(list)
        for palabra in self.palabras:
            grupos[palabra[0]].append(palabra)
        return dict(grupos)
    
    def estadisticas(self) -> Dict:
        """Estadísticas del texto"""
        freq = self.frecuencia_palabras()
        return {
            "total_palabras": len(self.palabras),
            "palabras_unicas": len(freq),
            "palabra_mas_frecuente": max(freq, key=freq.get) if freq else None,
            "frecuencia": freq
        }

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Gestor de datos
    gestor = GestorDatos()
    
    # Agregar datos
    gestor.agregar_dato("frutas", "manzana")
    gestor.agregar_dato("frutas", "pera")
    gestor.agregar_dato("verduras", "zanahoria")
    
    print("📊 GESTOR DE DATOS:")
    print(gestor.resumen())
    
    # Agrupar por tipo
    elementos = [1, "hola", 3.14, "mundo", 42, True]
    print(f"\n🔍 Agrupados por tipo: {gestor.agrupar_por_tipo(elementos)}")
    
    # Analizador de texto
    texto = "python es genial python es facil python es poderoso"
    analizador = AnalizadorTexto(texto)
    
    print("\n📝 ANÁLISIS DE TEXTO:")
    stats = analizador.estadisticas()
    print(f"  Total palabras: {stats['total_palabras']}")
    print(f"  Palabras únicas: {stats['palabras_unicas']}")
    print(f"  Palabra más frecuente: {stats['palabra_mas_frecuente']}")
    
    print("\n📊 Agrupado por longitud:")
    for longitud, palabras in analizador.agrupar_por_longitud().items():
        print(f"  Longitud {longitud}: {len(palabras)} palabras")
```
### **EJERCICIO 1.4: Funciones Avanzadas con POO (*args, kwargs, Type Hints)
```python
"""
EJERCICIO 1.4: FUNCIONES AVANZADAS CON POO
ESENCIA: Clase que usa *args, **kwargs y type hints
"""

from typing import Any, Dict, List, Optional, Union, Tuple

class ProcesadorDatos:
    """Procesa datos usando *args, **kwargs y type hints"""
    
    def __init__(self, nombre: str):
        self.nombre = nombre
        self.historial: List[Dict] = []
    
    # ============================================
    # *args - ARGUMENTOS POSICIONALES VARIABLES
    # ============================================
    def sumar_todos(self, *args: float) -> float:
        """Suma cualquier cantidad de números"""
        self._registrar("suma", args)
        return sum(args)
    
    def procesar_multiples(self, operacion: str, *args: float) -> Union[float, str]:
        """Procesa múltiples números con diferentes operaciones"""
        if not args:
            return "No hay datos"
        
        if operacion == "suma":
            return sum(args)
        elif operacion == "promedio":
            return sum(args) / len(args)
        elif operacion == "maximo":
            return max(args)
        elif operacion == "minimo":
            return min(args)
        else:
            return f"Operación '{operacion}' no soportada"
    
    # ============================================
    # **kwargs - ARGUMENTOS NOMBRADOS VARIABLES
    # ============================================
    def crear_usuario(self, nombre: str, **kwargs) -> Dict[str, Any]:
        """Crea un usuario con datos variables"""
        usuario = {"nombre": nombre}
        usuario.update(kwargs)
        self._registrar("crear_usuario", usuario)
        return usuario
    
    def configurar_sistema(self, **kwargs) -> Dict[str, Any]:
        """Configura el sistema con parámetros variables"""
        configuracion = {
            "modo": kwargs.get("modo", "normal"),
            "debug": kwargs.get("debug", False),
            "timeout": kwargs.get("timeout", 30),
            "formato": kwargs.get("formato", "json")
        }
        # Sobrescribir con kwargs
        configuracion.update(kwargs)
        self._registrar("configurar", configuracion)
        return configuracion
    
    # ============================================
    # COMBINAR *args Y **kwargs
    # ============================================
    def procesar_venta(self, *args, **kwargs) -> Dict:
        """Procesa una venta con datos flexibles"""
        # args: productos, kwargs: detalles de la venta
        productos = list(args)
        
        venta = {
            "productos": productos,
            "fecha": kwargs.get("fecha", "2026-08-15"),
            "cliente": kwargs.get("cliente", "Anónimo"),
            "descuento": kwargs.get("descuento", 0),
            "metodo_pago": kwargs.get("metodo_pago", "Efectivo")
        }
        
        self._registrar("procesar_venta", venta)
        return venta
    
    # ============================================
    # TYPE HINTS - MÉTODOS CON ANOTACIONES
    # ============================================
    def filtrar_numeros(self, datos: List[Union[int, float]], 
                       minimo: float, maximo: float) -> List[float]:
        """Filtra números en un rango usando type hints"""
        return [x for x in datos if minimo <= x <= maximo]
    
    def procesar_strings(self, textos: List[str], 
                        operacion: str = "mayusculas") -> List[str]:
        """Procesa strings con type hints"""
        if operacion == "mayusculas":
            return [t.upper() for t in textos]
        elif operacion == "minusculas":
            return [t.lower() for t in textos]
        elif operacion == "longitud":
            return [str(len(t)) for t in textos]
        return textos
    
    # ============================================
    # MÉTODOS PRIVADOS CON TYPE HINTS
    # ============================================
    def _registrar(self, operacion: str, datos: Any) -> None:
        """Registra una operación en el historial"""
        self.historial.append({
            "operacion": operacion,
            "datos": datos,
            "timestamp": __import__('datetime').datetime.now().isoformat()
        })
    
    def obtener_historial(self) -> List[Dict]:
        """Obtiene el historial de operaciones"""
        return self.historial
    
    def estadisticas(self) -> Dict:
        """Estadísticas del procesador"""
        return {
            "nombre": self.nombre,
            "total_operaciones": len(self.historial),
            "operaciones": [h["operacion"] for h in self.historial]
        }

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Crear procesador
    procesador = ProcesadorDatos("MiProcesador")
    
    # *args
    print("🔢 SUMAR TODOS:")
    print(f"  Suma: {procesador.sumar_todos(1, 2, 3, 4, 5)}")
    print(f"  Promedio: {procesador.procesar_multiples('promedio', 10, 20, 30, 40)}")
    print(f"  Máximo: {procesador.procesar_multiples('maximo', 5, 8, 3, 12, 6)}")
    
    # **kwargs
    print("\n👤 CREAR USUARIO:")
    usuario = procesador.crear_usuario("Ana", edad=25, ciudad="Madrid", activo=True)
    print(f"  Usuario: {usuario}")
    
    print("\n⚙️ CONFIGURAR SISTEMA:")
    config = procesador.configurar_sistema(modo="avanzado", debug=True, timeout=60)
    print(f"  Configuración: {config}")
    
    # Combinar *args y **kwargs
    print("\n🛒 PROCESAR VENTA:")
    venta = procesador.procesar_venta("Laptop", "Mouse", "Teclado", 
                                      cliente="Carlos", descuento=10, metodo_pago="Tarjeta")
    print(f"  Venta: {venta}")
    
    # Type hints
    print("\n📊 PROCESAR DATOS CON TYPE HINTS:")
    numeros = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    filtrados = procesador.filtrar_numeros(numeros, 3, 7)
    print(f"  Números entre 3 y 7: {filtrados}")
    
    textos = ["hola", "mundo", "python"]
    mayusculas = procesador.procesar_strings(textos, "mayusculas")
    print(f"  Mayúsculas: {mayusculas}")
    
    # Historial
    print("\n📋 HISTORIAL DE OPERACIONES:")
    for h in procesador.obtener_historial():
        print(f"  {h['operacion']} - {h['timestamp']}")
```
### EJERCICIO 1.5: Sistema de Análisis con Decoradores POO
```python
"""
EJERCICIO 1.5: SISTEMA DE ANÁLISIS CON DECORADORES POO
ESENCIA: Clases que usan decoradores para funcionalidad adicional
"""

import time
from functools import wraps
from typing import Callable, Any

# ============================================
# DECORADORES PARA LA CLASE
# ============================================

def medir_tiempo(func: Callable) -> Callable:
    """Decorador que mide el tiempo de ejecución"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        inicio = time.perf_counter()
        resultado = func(*args, **kwargs)
        fin = time.perf_counter()
        print(f"⏱️ {func.__name__}: {(fin-inicio)*1000:.2f}ms")
        return resultado
    return wrapper

def registrar_llamada(func: Callable) -> Callable:
    """Decorador que registra llamadas a métodos"""
    @wraps(func)
    def wrapper(self, *args, **kwargs):
        print(f"📝 Llamando a {func.__name__}")
        resultado = func(self, *args, **kwargs)
        # Registrar en el historial de la clase
        if hasattr(self, '_historial'):
            self._historial.append({
                'metodo': func.__name__,
                'args': args,
                'kwargs': kwargs,
                'resultado': resultado
            })
        return resultado
    return wrapper

def validar_argumentos(tipos: dict):
    """Decorador que valida tipos de argumentos"""
    def decorador(func: Callable) -> Callable:
        @wraps(func)
        def wrapper(self, *args, **kwargs):
            # Validar argumentos posicionales
            for i, arg in enumerate(args):
                if i in tipos:
                    tipo_esperado = tipos[i]
                    if not isinstance(arg, tipo_esperado):
                        raise TypeError(f"Arg {i} debe ser {tipo_esperado.__name__}")
            return func(self, *args, **kwargs)
        return wrapper
    return decorador

# ============================================
# CLASE CON DECORADORES
# ============================================

class AnalizadorEstadistico:
    """Analiza estadísticas usando decoradores"""
    
    def __init__(self, nombre: str):
        self.nombre = nombre
        self._historial = []
        self.datos = []
    
    @registrar_llamada
    @medir_tiempo
    def cargar_datos(self, *args: float):
        """Carga datos para análisis"""
        self.datos = list(args)
        return f"Cargados {len(self.datos)} datos"
    
    @registrar_llamada
    @medir_tiempo
    @validar_argumentos({0: list})
    def procesar_con_metodo(self, datos: list, metodo: str = "suma") -> float:
        """Procesa datos con diferentes métodos"""
        if metodo == "suma":
            return sum(datos)
        elif metodo == "promedio":
            return sum(datos) / len(datos) if datos else 0
        elif metodo == "maximo":
            return max(datos) if datos else None
        elif metodo == "minimo":
            return min(datos) if datos else None
        return 0
    
    @registrar_llamada
    def analizar_texto(self, texto: str) -> dict:
        """Analiza texto usando estructuras de datos"""
        from collections import Counter, defaultdict
        
        palabras = texto.lower().split()
        frecuencia = Counter(palabras)
        
        return {
            "total_palabras": len(palabras),
            "unicas": len(frecuencia),
            "mas_comunes": frecuencia.most_common(3),
            "frecuencia": dict(frecuencia)
        }
    
    def obtener_historial(self) -> list:
        """Obtiene el historial de operaciones"""
        return self._historial
    
    def __str__(self):
        return f"📊 Analizador: {self.nombre} ({len(self.datos)} datos)"

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    # Crear analizador
    analizador = AnalizadorEstadistico("MiAnalizador")
    
    print(analizador)
    
    # Cargar datos
    print("\n📥 CARGANDO DATOS:")
    print(analizador.cargar_datos(10, 20, 30, 40, 50))
    
    # Procesar con diferentes métodos
    print("\n📊 PROCESANDO DATOS:")
    print(f"  Suma: {analizador.procesar_con_metodo(analizador.datos, 'suma')}")
    print(f"  Promedio: {analizador.procesar_con_metodo(analizador.datos, 'promedio')}")
    print(f"  Máximo: {analizador.procesar_con_metodo(analizador.datos, 'maximo')}")
    
    # Analizar texto
    print("\n📝 ANALIZANDO TEXTO:")
    texto = "python es genial python es facil python es poderoso"
    resultado = analizador.analizar_texto(texto)
    print(f"  Total: {resultado['total_palabras']}")
    print(f"  Únicas: {resultado['unicas']}")
    print(f"  Más comunes: {resultado['mas_comunes']}")
    
    # Mostrar historial
    print("\n📋 HISTORIAL DE OPERACIONES:")
    for i, op in enumerate(analizador.obtener_historial(), 1):
        print(f"  {i}. {op['metodo']} - {op['resultado'][:50] if isinstance(op['resultado'], str) else op['resultado']}")
```
### DÍA 1: Sistema de Análisis de Texto con POO
```python
"""
MINI-PROYECTO DÍA 1: SISTEMA DE ANÁLISIS DE TEXTO CON POO
ESENCIA: Sistema completo que integra todos los conceptos de POO del día 1
"""

from collections import Counter, defaultdict
from typing import List, Dict, Set, Tuple, Optional
from datetime import datetime
import re
import time
from functools import wraps

# ============================================
# DECORADORES
# ============================================

def cronometrar(func):
    """Decorador para medir tiempo de ejecución"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        inicio = time.perf_counter()
        resultado = func(*args, **kwargs)
        print(f"⏱️ {func.__name__}: {(time.perf_counter()-inicio)*1000:.2f}ms")
        return resultado
    return wrapper

def registrar_analisis(func):
    """Decorador para registrar análisis realizados"""
    @wraps(func)
    def wrapper(self, *args, **kwargs):
        resultado = func(self, *args, **kwargs)
        if hasattr(self, 'historial'):
            self.historial.append({
                'metodo': func.__name__,
                'args': args,
                'resultado': str(resultado)[:100] if isinstance(resultado, (dict, list)) else str(resultado)
            })
        return resultado
    return wrapper

# ============================================
# CLASES DEL SISTEMA
# ============================================

class Texto:
    """Representa un texto a analizar"""
    
    def __init__(self, contenido: str, nombre: str = "Sin nombre"):
        self.contenido = contenido
        self.nombre = nombre
        self._limpiar()
        self.fecha_analisis = datetime.now()
    
    def _limpiar(self):
        """Limpia el texto (método privado)"""
        # Eliminar puntuación y convertir a minúsculas
        texto_limpio = re.sub(r'[^\w\s]', '', self.contenido.lower())
        self.palabras = texto_limpio.split()
        self.texto_limpio = texto_limpio
    
    def __len__(self):
        return len(self.palabras)
    
    def __str__(self):
        return f"📄 '{self.nombre}': {len(self.palabras)} palabras"

class AnalizadorTexto:
    """Sistema de análisis de texto con POO"""
    
    def __init__(self):
        self.textos: List[Texto] = []
        self.historial: List[Dict] = []
    
    def agregar_texto(self, texto: Texto):
        """Agrega un texto para análisis"""
        self.textos.append(texto)
        print(f"✅ Texto agregado: {texto}")
    
    # ============================================
    # MÉTODOS DE ANÁLISIS CON DECORADORES
    # ============================================
    
    @cronometrar
    @registrar_analisis
    def frecuencia_palabras(self, texto: Texto) -> Dict[str, int]:
        """Cuenta frecuencia de palabras usando Counter"""
        return dict(Counter(texto.palabras))
    
    @cronometrar
    @registrar_analisis
    def top_palabras(self, texto: Texto, n: int = 5) -> List[Tuple[str, int]]:
        """Obtiene las n palabras más comunes"""
        return Counter(texto.palabras).most_common(n)
    
    @cronometrar
    @registrar_analisis
    def palabras_unicas(self, texto: Texto) -> Set[str]:
        """Obtiene palabras únicas usando set"""
        return set(texto.palabras)
    
    @cronometrar
    @registrar_analisis
    def agrupar_por_longitud(self, texto: Texto) -> Dict[int, List[str]]:
        """Agrupa palabras por longitud usando defaultdict"""
        grupos = defaultdict(list)
        for palabra in texto.palabras:
            grupos[len(palabra)].append(palabra)
        return dict(grupos)
    
    # ============================================
    # ANÁLISIS COMPARATIVO CON SETS
    # ============================================
    
    def comparar_textos(self, texto1: Texto, texto2: Texto) -> Dict:
        """Compara dos textos usando operaciones de conjuntos"""
        palabras1 = set(texto1.palabras)
        palabras2 = set(texto2.palabras)
        
        return {
            "ambos": palabras1 & palabras2,  # Intersección
            "solo_primero": palabras1 - palabras2,  # Diferencia
            "solo_segundo": palabras2 - palabras1,
            "todos": palabras1 | palabras2,  # Unión
            "similitud": len(palabras1 & palabras2) / len(palabras1 | palabras2) if palabras1 | palabras2 else 0
        }
    
    # ============================================
    # ESTADÍSTICAS COMPLETAS
    # ============================================
    
    @cronometrar
    def estadisticas_completas(self, texto: Texto) -> Dict:
        """Genera estadísticas completas del texto"""
        freq = self.frecuencia_palabras(texto)
        
        return {
            "total_palabras": len(texto),
            "palabras_unicas": len(self.palabras_unicas(texto)),
            "longitud_promedio": sum(len(p) for p in texto.palabras) / len(texto) if texto.palabras else 0,
            "densidad_vocabulario": len(self.palabras_unicas(texto)) / len(texto) if texto.palabras else 0,
            "top_5": self.top_palabras(texto, 5),
            "palabra_mas_frecuente": max(freq, key=freq.get) if freq else None,
            "agrupacion_longitud": self.agrupar_por_longitud(texto),
            "fecha_analisis": texto.fecha_analisis.isoformat()
        }
    
    def resumen_todos_textos(self) -> Dict:
        """Resumen de todos los textos analizados"""
        if not self.textos:
            return {"total_textos": 0}
        
        return {
            "total_textos": len(self.textos),
            "textos": [{"nombre": t.nombre, "palabras": len(t)} for t in self.textos],
            "total_palabras": sum(len(t) for t in self.textos),
            "promedio_palabras": sum(len(t) for t in self.textos) / len(self.textos)
        }
    
    def __str__(self):
        return f"📊 Analizador: {len(self.textos)} textos en memoria"

# ============================================
# DEMOSTRACIÓN COMPLETA
# ============================================

if __name__ == "__main__":
    print("=" * 60)
    print("MINI-PROYECTO DÍA 1: SISTEMA DE ANÁLISIS DE TEXTO CON POO")
    print("=" * 60)
    
    # Crear sistema
    sistema = AnalizadorTexto()
    
    # Crear textos
    texto1 = Texto(
        "Python es un lenguaje de programación poderoso y versátil. "
        "Python es fácil de aprender y tiene una gran comunidad.",
        "Introducción a Python"
    )
    
    texto2 = Texto(
        "La programación orientada a objetos es un paradigma de programación "
        "que utiliza objetos y sus interacciones para diseñar aplicaciones.",
        "POO Básico"
    )
    
    # Agregar textos
    sistema.agregar_texto(texto1)
    sistema.agregar_texto(texto2)
    
    # Analizar primer texto
    print("\n" + "=" * 60)
    print("📊 ANÁLISIS DEL TEXTO 1")
    print("=" * 60)
    
    stats = sistema.estadisticas_completas(texto1)
    for clave, valor in stats.items():
        if clave in ['top_5', 'agrupacion_longitud']:
            print(f"  {clave}: {dict(list(valor.items())[:3]) if isinstance(valor, dict) else valor}")
        else:
            print(f"  {clave}: {valor}")
    
    # Comparar textos
    print("\n" + "=" * 60)
    print("🔍 COMPARACIÓN DE TEXTOS")
    print("=" * 60)
    
    comparacion = sistema.comparar_textos(texto1, texto2)
    print(f"  Similitud: {comparacion['similitud']:.2%}")
    print(f"  Palabras en común: {len(comparacion['ambos'])}")
    print(f"  Solo en texto 1: {len(comparacion['solo_primero'])}")
    print(f"  Solo en texto 2: {len(comparacion['solo_segundo'])}")
    
    # Resumen general
    print("\n" + "=" * 60)
    print("📋 RESUMEN GENERAL")
    print("=" * 60)
    
    resumen = sistema.resumen_todos_textos()
    print(f"  Total textos: {resumen['total_textos']}")
    print(f"  Total palabras: {resumen['total_palabras']}")
    print(f"  Promedio por texto: {resumen['promedio_palabras']:.1f}")
    
    # Historial
    print("\n📋 HISTORIAL DE ANÁLISIS:")
    for i, entry in enumerate(sistema.historial[-5:], 1):
        print(f"  {i}. {entry['metodo']}: {entry['resultado'][:60]}...")
```
