# MÓDULO 1 — Programación Orientada a Objetos (POO)
Ejercicio 1 — Clase Persona
```python
class Persona:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.edad = edad

persona = Persona("Carlos", 25)
print(persona.nombre)
print(persona.edad)
```
Ejercicio 2 — Clase Animal
```python
class Animal:
    def hacer_sonido(self):
        print("Sonido del animal")

animal = Animal()
animal.hacer_sonido()
```
Ejercicio 3 — Clase Auto
```python
class Auto:
    def __init__(self, marca, modelo):
        self.marca = marca
        self.modelo = modelo

    def mostrar(self):
        print(self.marca, self.modelo)

auto = Auto("Toyota", "Corolla")
auto.mostrar()
```
Ejercicio 4 — Clase Libro
```python
class Libro:
    def __init__(self, titulo):
        self.titulo = titulo

libro = Libro("Python Básico")
print(libro.titulo)
```
Ejercicio 5 — Clase Rectangulo
```python
class Rectangulo:
    def __init__(self, base, altura):
        self.base = base
        self.altura = altura

    def area(self):
        return self.base * self.altura

r = Rectangulo(5, 3)
print(r.area())
```
Ejercicio 6 — Clase Cuenta
```python
class Cuenta:
    def __init__(self, saldo):
        self.saldo = saldo

    def depositar(self, monto):
        self.saldo += monto

cuenta = Cuenta(100)
cuenta.depositar(50)
print(cuenta.saldo)
```
Ejercicio 7 — Clase Producto
```python
class Producto:
    def __init__(self, nombre, precio):
        self.nombre = nombre
        self.precio = precio

producto = Producto("Mouse", 80)
print(producto.nombre)
print(producto.precio)
```
Ejercicio 8 — Clase Estudiante
```python
class Estudiante:
    def __init__(self, nombre, nota):
        self.nombre = nombre
        self.nota = nota

    def aprobar(self):
        return self.nota >= 51

est = Estudiante("Ana", 70)
print(est.aprobar())
```
Ejercicio 9 — Herencia Vehículo
```python
class Vehiculo:
    def mover(self):
        print("El vehículo se mueve")

class Moto(Vehiculo):
    pass

m = Moto()
m.mover()
```
Ejercicio 10 — Clase Empleado
```python
class Empleado:
    def __init__(self, nombre, salario):
        self.nombre = nombre
        self.salario = salario

    def bono(self):
        return self.salario * 0.10

emp = Empleado("Luis", 3000)
print(emp.bono())
```
Ejercicio 11 — Clase Calculadora
```python
class Calculadora:
    def sumar(self, a, b):
        return a + b

calc = Calculadora()
print(calc.sumar(5, 3))
```
Ejercicio 12 — Clase Celular
```python
class Celular:
    def llamar(self):
        print("Llamando...")

c = Celular()
c.llamar()
```
Ejercicio 13 — Clase Película
```python
class Pelicula:
    def __init__(self, nombre, duracion):
        self.nombre = nombre
        self.duracion = duracion

p = Pelicula("Matrix", 120)
print(p.nombre)
```
Ejercicio 14 — Clase Profesor
```python
class Profesor:
    def __init__(self, nombre, materia):
        self.nombre = nombre
        self.materia = materia

prof = Profesor("Mario", "Matemática")
print(prof.materia)
```
Ejercicio 15 — Clase Tienda
```python
class Tienda:
    def __init__(self):
        self.productos = []

    def agregar(self, producto):
        self.productos.append(producto)

store = Tienda()
store.agregar("Teclado")
print(store.productos)
```
Ejercicio 16 — Clase Factura
```python
class Factura:
    def __init__(self, monto):
        self.monto = monto

    def iva(self):
        return self.monto * 0.13

f = Factura(100)
print(f.iva())
```
Ejercicio 17 — Clase Banco
```python
class Banco:
    def __init__(self):
        self.clientes = []

    def agregar_cliente(self, nombre):
        self.clientes.append(nombre)

banco = Banco()
banco.agregar_cliente("Carlos")
print(banco.clientes)
```
Ejercicio 18 — Encapsulamiento
```python
class Usuario:
    def __init__(self):
        self.__password = "1234"

u = Usuario()
print("Password protegida")
```
Ejercicio 19 — Clase Computadora
```python
class Computadora:
    def __init__(self, ram):
        self.ram = ram

pc = Computadora("8GB")
print(pc.ram)
```
Ejercicio 20 — Mini Inventario
```python
class Inventario:
    def __init__(self):
        self.items = []

    def agregar(self, item):
        self.items.append(item)

inv = Inventario()
inv.agregar("Monitor")
print(inv.items)
```
