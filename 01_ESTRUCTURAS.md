
### DÍA 1: ESTRUCTURAS DE DATOS AVANZADAS Y FUNCIONES
### OBJETIVO: Dominar list comprehensions, slicing, sets, diccionarios, defaultdict, decoradores y funciones con argumentos variables.
EJERCICIO 1.1: List Comprehensions y Slicing
python
"""
EJERCICIO 1.1: LIST COMPREHENSIONS Y SLICING
ESENCIA: Crear listas de forma eficiente y extraer subconjuntos
"""

### ============================================
### 1. LIST COMPREHENSION BÁSICA
## ============================================
# Sintaxis: [expresion for elemento in iterable]
numeros = [1, 2, 3, 4, 5]
cuadrados = [x**2 for x in numeros]
print(f"Cuadrados: {cuadrados}")  # [1, 4, 9, 16, 25]

# ============================================
# 2. LIST COMPREHENSION CON CONDICIÓN
# ============================================
# Sintaxis: [expresion for elemento in iterable if condición]
pares = [x for x in numeros if x % 2 == 0]
print(f"Números pares: {pares}")  # [2, 4]

# Con operación y condición
pares_cuadrado = [x**2 for x in numeros if x % 2 == 0]
print(f"Cuadrado de pares: {pares_cuadrado}")  # [4, 16]

# ============================================
# 3. LIST COMPREHENSION ANIDADA
# ============================================
matriz = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
# Aplanar matriz
aplanada = [num for fila in matriz for num in fila]
print(f"Matriz aplanada: {aplanada}")  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# ============================================
# 4. SLICING (REBANADO)
# ============================================
# Sintaxis: lista[inicio:fin:paso]
lista = [10, 20, 30, 40, 50, 60, 70, 80, 90]

print(f"Original: {lista}")
print(f"Primeros 3: {lista[:3]}")      # [10, 20, 30]
print(f"Últimos 3: {lista[-3:]}")      # [70, 80, 90]
print(f"Posiciones 2-5: {lista[2:6]}") # [30, 40, 50, 60]
print(f"Cada 2 elementos: {lista[::2]}") # [10, 30, 50, 70, 90]
print(f"Invertir lista: {lista[::-1]}")  # [90, 80, 70, 60, 50, 40, 30, 20, 10]

# ============================================
# 5. SLICING EN STRINGS
# ============================================
texto = "Python Intermedio"
print(f"Primeros 6: {texto[:6]}")      # Python
print(f"Últimos 10: {texto[-10:]}")     # Intermedio
print(f"Texto invertido: {texto[::-1]}") # oidemretnI nohtyP

# ============================================
# 6. EJERCICIO PRÁCTICO: PROCESAMIENTO DE DATOS
# ============================================
ventas = [1200, 1500, 800, 2000, 950, 1800, 600]

# Filtrar ventas mayores a 1000
ventas_altas = [v for v in ventas if v > 1000]
print(f"Ventas > 1000: {ventas_altas}")  # [1200, 1500, 2000, 1800]

# Aplicar descuento del 10% a ventas altas
ventas_con_descuento = [v * 0.9 if v > 1000 else v for v in ventas]
print(f"Con descuento: {ventas_con_descuento}")

# Obtener top 3 ventas
top_3 = sorted(ventas, reverse=True)[:3]
print(f"Top 3 ventas: {top_3}")  # [2000, 1800, 1500]
EJERCICIO 1.2: Tuplas, Desempaquetado y Sets
python
"""
EJERCICIO 1.2: TUPLAS, DESEMPAQUETADO Y SETS
ESENCIA: Estructuras inmutables y operaciones de conjuntos
"""

# ============================================
# 1. TUPLAS Y DESEMPAQUETADO
# ============================================
# Crear tupla
coordenadas = (10, 20, 30)
print(f"Tupla: {coordenadas}")

# Desempaquetado básico
x, y, z = coordenadas
print(f"x={x}, y={y}, z={z}")

# Desempaquetado con * (resto de elementos)
numeros = (1, 2, 3, 4, 5)
primero, *resto = numeros
print(f"Primero: {primero}, Resto: {resto}")  # 1, [2, 3, 4, 5]

*inicio, ultimo = numeros
print(f"Inicio: {inicio}, Último: {ultimo}")  # [1, 2, 3, 4], 5

# Intercambio de variables usando tuplas
a, b = 5, 10
print(f"Antes: a={a}, b={b}")
a, b = b, a  # Intercambio mágico
print(f"Después: a={a}, b={b}")

# ============================================
# 2. SETS - CONJUNTOS
# ============================================
# Crear sets
set_a = {1, 2, 3, 4, 5}
set_b = {4, 5, 6, 7, 8}
print(f"Set A: {set_a}")
print(f"Set B: {set_b}")

# OPERACIONES DE CONJUNTOS
# Unión: elementos en A o B
union = set_a | set_b
print(f"Unión: {union}")  # {1, 2, 3, 4, 5, 6, 7, 8}

# Intersección: elementos en A y B
interseccion = set_a & set_b
print(f"Intersección: {interseccion}")  # {4, 5}

# Diferencia: elementos en A pero no en B
diferencia = set_a - set_b
print(f"Diferencia (A - B): {diferencia}")  # {1, 2, 3}

# Diferencia simétrica: en A o B pero no en ambos
dif_simetrica = set_a ^ set_b
print(f"Diferencia simétrica: {dif_simetrica}")  # {1, 2, 3, 6, 7, 8}

# ============================================
# 3. ELIMINAR DUPLICADOS CON SET
# ============================================
lista_duplicados = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unicos = list(set(lista_duplicados))
print(f"Original: {lista_duplicados}")
print(f"Sin duplicados: {unicos}")  # [1, 2, 3, 4]

# ============================================
# 4. EJERCICIO PRÁCTICO: ANÁLISIS DE TEXTOS
# ============================================
texto = "python es genial python es fácil python es poderoso"
palabras = texto.split()
palabras_unicas = set(palabras)

print(f"Total palabras: {len(palabras)}")
print(f"Palabras únicas: {len(palabras_unicas)}")
print(f"Palabras únicas: {palabras_unicas}")

# Encontrar palabras que aparecen en ambos textos
texto2 = "python es divertido y fácil"
palabras2 = set(texto2.split())
comunes = palabras_unicas & palabras2
print(f"Palabras comunes: {comunes}")  # {'python', 'es', 'fácil'}
EJERCICIO 1.3: Diccionarios y defaultdict
python
"""
EJERCICIO 1.3: DICCIONARIOS Y DEFAULTDICT
ESENCIA: Estructuras clave-valor con valores predeterminados
"""

from collections import defaultdict

# ============================================
# 1. DICCIONARIO BÁSICO
# ============================================
persona = {
    "nombre": "Ana",
    "edad": 25,
    "ciudad": "Madrid",
    "hobbies": ["leer", "programar"]
}
print(f"Persona: {persona}")

# Acceso seguro con get()
print(f"Nombre: {persona.get('nombre')}")  # Ana
print(f"Apellido: {persona.get('apellido', 'No existe')}")  # No existe

# ============================================
# 2. MÉTODOS ÚTILES DE DICCIONARIO
# ============================================
# update() - Actualizar múltiples valores
persona.update({"edad": 26, "profesion": "Ingeniera"})
print(f"Actualizado: {persona}")

# setdefault() - Obtener o establecer valor
persona.setdefault("pais", "España")  # Si no existe, lo crea
persona.setdefault("nombre", "María")  # Si existe, no lo cambia
print(f"Con setdefault: {persona}")

# keys(), values(), items()
print(f"Claves: {list(persona.keys())}")
print(f"Valores: {list(persona.values())}")
print(f"Items: {list(persona.items())}")

# ============================================
# 3. DEFAULTDICT
# ============================================
# Sin defaultdict (requiere verificación)
diccionario_normal = {}
for letra in "python":
    if letra not in diccionario_normal:
        diccionario_normal[letra] = 0
    diccionario_normal[letra] += 1
print(f"Normal: {diccionario_normal}")

# Con defaultdict (automático)
diccionario_default = defaultdict(int)  # int() devuelve 0
for letra in "python":
    diccionario_default[letra] += 1
print(f"Defaultdict: {dict(diccionario_default)}")

# defaultdict con listas
agrupados = defaultdict(list)
nombres = ["Ana", "Luis", "Ana", "Carlos", "Luis", "Ana"]
for nombre in nombres:
    agrupados[nombre].append(1)  # La lista se crea automáticamente
print(f"Agrupados: {dict(agrupados)}")

# ============================================
# 4. EJERCICIO PRÁCTICO: CONTADOR DE PALABRAS
# ============================================
def contar_palabras(texto):
    """Cuenta palabras usando defaultdict"""
    contador = defaultdict(int)
    for palabra in texto.lower().split():
        contador[palabra] += 1
    return dict(contador)

texto_ejemplo = "python es genial python es fácil y python es poderoso"
frecuencia = contar_palabras(texto_ejemplo)
print(f"Frecuencia: {frecuencia}")
# {'python': 3, 'es': 3, 'genial': 1, 'fácil': 1, 'y': 1, 'poderoso': 1}
**EJERCICIO 1.4: Funciones con *args y kwargs
python
"""
EJERCICIO 1.4: FUNCIONES CON *args Y **kwargs
ESENCIA: Funciones flexibles con número variable de argumentos
"""

# ============================================
# 1. *args - ARGUMENTOS POSICIONALES VARIABLES
# ============================================
def sumar_todos(*args):
    """Suma cualquier cantidad de números"""
    print(f"Args recibidos: {args}")
    return sum(args)

print(f"Suma: {sumar_todos(1, 2, 3)}")        # 6
print(f"Suma: {sumar_todos(10, 20, 30, 40)}") # 100
print(f"Suma: {sumar_todos()}")               # 0

# *args con otros parámetros
def calcular(operacion, *args):
    if operacion == "suma":
        return sum(args)
    elif operacion == "multiplicar":
        resultado = 1
        for num in args:
            resultado *= num
        return resultado
    elif operacion == "promedio":
        return sum(args) / len(args) if args else 0

print(f"Suma: {calcular('suma', 1, 2, 3, 4)}")        # 10
print(f"Multiplicar: {calcular('multiplicar', 2, 3, 4)}") # 24
print(f"Promedio: {calcular('promedio', 10, 20, 30)}") # 20.0

# ============================================
# 2. **kwargs - ARGUMENTOS NOMBRADOS VARIABLES
# ============================================
def mostrar_info(**kwargs):
    """Muestra información en formato clave-valor"""
    print(f"Kwargs recibidos: {kwargs}")
    for clave, valor in kwargs.items():
        print(f"  {clave}: {valor}")

mostrar_info(nombre="Ana", edad=25, ciudad="Madrid")
# nombre: Ana
# edad: 25
# ciudad: Madrid

# ============================================
# 3. COMBINAR *args Y **kwargs
# ============================================
def procesar(*args, **kwargs):
    print(f"Args posicionales: {args}")
    print(f"Args nombrados: {kwargs}")
    print(f"Suma args: {sum(args)}")
    print(f"Operación: {kwargs.get('operacion', 'No especificada')}")
    print(f"Formato: {kwargs.get('formato', 'No especificado')}")

procesar(1, 2, 3, 4, 5, operacion="suma", formato="json")

# ============================================
# 4. EJERCICIO PRÁCTICO: FUNCIÓN FLEXIBLE
# ============================================
def crear_usuario(nombre, **kwargs):
    """Crea un usuario con datos variables"""
    usuario = {"nombre": nombre}
    usuario.update(kwargs)
    return usuario

usuario1 = crear_usuario("Ana", edad=25, ciudad="Madrid")
usuario2 = crear_usuario("Luis", edad=30, profesion="Ingeniero", activo=True)

print(f"Usuario 1: {usuario1}")
print(f"Usuario 2: {usuario2}")
EJERCICIO 1.5: Decoradores
python
"""
EJERCICIO 1.5: DECORADORES
ESENCIA: Añadir funcionalidad a funciones sin modificar su código
"""

import time
from functools import wraps

# ============================================
# 1. DECORADOR BÁSICO - MEDIR TIEMPO
# ============================================
def medir_tiempo(func):
    """Decorador que mide el tiempo de ejecución"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        inicio = time.perf_counter()
        resultado = func(*args, **kwargs)
        fin = time.perf_counter()
        print(f"⏱️ {func.__name__} tardó: {(fin - inicio)*1000:.2f} ms")
        return resultado
    return wrapper

@medir_tiempo
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(f"Fibonacci(30): {fibonacci(30)}")

# ============================================
# 2. DECORADOR - REGISTRAR LLAMADAS
# ============================================
def registrar(func):
    """Decorador que registra cada llamada"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"📝 Llamando a {func.__name__}")
        print(f"   Args: {args}, Kwargs: {kwargs}")
        return func(*args, **kwargs)
    return wrapper

@registrar
def saludar(nombre, saludo="Hola"):
    return f"{saludo} {nombre}"

print(saludar("Ana", saludo="Buenos días"))

# ============================================
# 3. DECORADOR CON PARÁMETROS
# ============================================
def repetir(veces=2):
    """Decorador que repite la ejecución n veces"""
    def decorador(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            resultados = []
            for i in range(veces):
                print(f"Ejecución {i+1}/{veces}")
                resultados.append(func(*args, **kwargs))
            return resultados
        return wrapper
    return decorador

@repetir(3)
def multiplicar(a, b):
    return a * b

print(f"Resultados: {multiplicar(5, 3)}")  # [15, 15, 15]

# ============================================
# 4. EJERCICIO PRÁCTICO: DECORADOR DE VALIDACIÓN
# ============================================
def validar_tipos(func):
    """Decorador que valida tipos de argumentos"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        # Obtener anotaciones de tipo
        anotaciones = func.__annotations__
        
        # Validar argumentos posicionales
        for i, arg in enumerate(args):
            if i in anotaciones:
                tipo = anotaciones[list(anotaciones.keys())[i]]
                if not isinstance(arg, tipo):
                    raise TypeError(f"Arg {i} debe ser {tipo.__name__}")
        
        # Validar argumentos nombrados
        for nombre, valor in kwargs.items():
            if nombre in anotaciones:
                tipo = anotaciones[nombre]
                if not isinstance(valor, tipo):
                    raise TypeError(f"{nombre} debe ser {tipo.__name__}")
        
        return func(*args, **kwargs)
    return wrapper

@validar_tipos
def procesar_numeros(a: int, b: int) -> int:
    return a + b

print(procesar_numeros(5, 3))  # 8
# procesar_numeros("5", 3)  # TypeError
