# python_intermedio
DÍA 4: ANÁLISIS DE DATOS Y PROYECTO FINAL CON POO
OBJETIVO: Integrar Pandas y Matplotlib con POO para análisis y visualización de datos.
EJERCICIO 4.1: Sistema de Análisis con Pandas y POO
python
"""
EJERCICIO 4.1: SISTEMA DE ANÁLISIS CON PANDAS Y POO
ESENCIA: Clase que usa Pandas para análisis de datos
"""

import pandas as pd
import numpy as np
from typing import Dict, List, Optional
from datetime import datetime, timedelta
import logging

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)

class AnalizadorDatos:
    """Sistema de análisis de datos con Pandas"""
    
    def __init__(self, nombre: str):
        self.nombre = nombre
        self.df = None
        self.historial = []
        logger.info(f"Analizador '{nombre}' creado")
    
    def cargar_desde_dict(self, datos: Dict):
        """Carga datos desde diccionario a DataFrame"""
        self.df = pd.DataFrame(datos)
        logger.info(f"Datos cargados: {self.df.shape}")
        self._registrar("cargar", self.df.shape)
        return self.df
    
    def cargar_desde_csv(self, archivo: str) -> pd.DataFrame:
        """Carga datos desde archivo CSV"""
        try:
            self.df = pd.read_csv(archivo)
            logger.info(f"CSV cargado: {self.df.shape}")
            self._registrar("cargar_csv", self.df.shape)
            return self.df
        except FileNotFoundError:
            logger.error(f"Archivo no encontrado: {archivo}")
            return None
    
    def generar_datos_prueba(self, n_registros: int = 100):
        """Genera datos de prueba"""
        fechas = pd.date_range('2026-08-01', periods=n_registros)
        datos = {
            'fecha': fechas,
            'producto': np.random.choice(['Laptop', 'Mouse', 'Teclado', 'Monitor'], n_registros),
            'categoria': np.random.choice(['Electrónica', 'Oficina'], n_registros),
            'precio': np.random.randint(50, 1500, n_registros),
            'cantidad': np.random.randint(1, 10, n_registros),
            'vendedor': np.random.choice(['Ana', 'Luis', 'Carlos', 'María'], n_registros)
        }
        self.df = pd.DataFrame(datos)
        self.df['total'] = self.df['precio'] * self.df['cantidad']
        logger.info(f"Datos de prueba generados: {n_registros} registros")
        return self.df
    
    # ============================================
    # MÉTODOS DE ANÁLISIS
    # ============================================
    def estadisticas_basicas(self) -> Dict:
        """Estadísticas básicas del DataFrame"""
        if self.df is None:
            return {"error": "No hay datos cargados"}
        
        return {
            "shape": self.df.shape,
            "columnas": list(self.df.columns),
            "tipos": self.df.dtypes.to_dict(),
            "describe": self.df.describe().to_dict(),
            "nulos": self.df.isnull().sum().to_dict()
        }
    
    def ventas_por_categoria(self) -> pd.DataFrame:
        """Agrupa ventas por categoría"""
        if self.df is None:
            return pd.DataFrame()
        
        return self.df.groupby('categoria').agg({
            'total': ['sum', 'mean', 'count'],
            'precio': 'mean'
        })
    
    def top_productos(self, n: int = 5) -> pd.DataFrame:
        """Top n productos por ventas"""
        if self.df is None:
            return pd.DataFrame()
        
        return self.df.groupby('producto')['total'].sum().sort_values(ascending=False).head(n)
    
    def analisis_por_vendedor(self) -> pd.DataFrame:
        """Análisis de ventas por vendedor"""
        if self.df is None:
            return pd.DataFrame()
        
        return self.df.groupby('vendedor').agg({
            'total': 'sum',
            'cantidad': 'sum',
            'producto': 'count'
        }).rename(columns={'producto': 'num_ventas'})
    
    def ventas_diarias(self) -> pd.Series:
        """Ventas diarias agregadas"""
        if self.df is None:
            return pd.Series()
        
        return self.df.groupby('fecha')['total'].sum()
    
    def resumen_ejecutivo(self) -> Dict:
        """Resumen ejecutivo completo"""
        if self.df is None:
            return {"error": "No hay datos"}
        
        return {
            "total_registros": len(self.df),
            "periodo": {
                "inicio": self.df['fecha'].min(),
                "fin": self.df['fecha'].max()
            },
            "ventas": {
                "total": self.df['total'].sum(),
                "promedio": self.df['total'].mean(),
                "maxima": self.df['total'].max()
            },
            "categoria_mas_vendida": self.df.groupby('categoria')['total'].sum().idxmax(),
            "producto_mas_vendido": self.df.groupby('producto')['total'].sum().idxmax(),
            "vendedor_destrecho": self.df.groupby('vendedor')['total'].sum().idxmax()
        }
    
    def _registrar(self, operacion: str, detalle: any):
        """Registra operación en el historial"""
        self.historial.append({
            "operacion": operacion,
            "detalle": str(detalle),
            "fecha": datetime.now().isoformat()
        })
    
    def __str__(self):
        if self.df is None:
            return f"📊 {self.nombre}: Sin datos"
        return f"📊 {self.nombre}: {self.df.shape[0]} filas, {self.df.shape[1]} columnas"

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    print("=" * 60)
    print("SISTEMA DE ANÁLISIS CON PANDAS Y POO")
    print("=" * 60)
    
    # Crear analizador
    analizador = AnalizadorDatos("Ventas 2026")
    
    # Generar datos
    print("\n📊 GENERANDO DATOS DE PRUEBA:")
    df = analizador.generar_datos_prueba(50)
    print(f"  Datos generados: {len(df)} registros")
    
    # Estadísticas
    print("\n📊 ESTADÍSTICAS BÁSICAS:")
    stats = analizador.estadisticas_basicas()
    print(f"  Shape: {stats['shape']}")
    print(f"  Columnas: {stats['columnas']}")
    
    # Análisis
    print("\n📊 VENTAS POR CATEGORÍA:")
    print(analizador.ventas_por_categoria())
    
    print("\n🏆 TOP 5 PRODUCTOS:")
    print(analizador.top_productos(5))
    
    print("\n📈 RESÚMEN EJECUTIVO:")
    resumen = analizador.resumen_ejecutivo()
    for clave, valor in resumen.items():
        print(f"  {clave}: {valor}")
EJERCICIO 4.2: Sistema de Visualización con Matplotlib y POO
python
"""
EJERCICIO 4.2: SISTEMA DE VISUALIZACIÓN CON MATPLOTLIB Y POO
ESENCIA: Clase que genera gráficos automáticos a partir de datos
"""

import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
from pathlib import Path
from typing import Optional, Dict, List
import logging

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)

class VisualizadorDatos:
    """Sistema de visualización de datos con Matplotlib"""
    
    def __init__(self, titulo: str = "Dashboard", estilo: str = 'seaborn-v0_8-darkgrid'):
        self.titulo = titulo
        self.estilo = estilo
        self.figuras = []
        Path("graficos").mkdir(exist_ok=True)
        plt.style.use(estilo)
        logger.info(f"Visualizador '{titulo}' creado")
    
    def grafico_lineas(self, df: pd.DataFrame, x: str, y: str, 
                       titulo: str = "", guardar: bool = True) -> plt.Figure:
        """Genera gráfico de líneas"""
        fig, ax = plt.subplots(figsize=(10, 5))
        ax.plot(df[x], df[y], marker='o', linewidth=2)
        ax.set_title(titulo or f"{y} vs {x}", fontsize=14)
        ax.set_xlabel(x)
        ax.set_ylabel(y)
        ax.grid(True, alpha=0.3)
        plt.tight_layout()
        
        if guardar:
            self._guardar(fig, f"lineas_{x}_{y}")
        
        self.figuras.append(fig)
        return fig
    
    def grafico_barras(self, df: pd.DataFrame, x: str, y: str,
                       titulo: str = "", guardar: bool = True) -> plt.Figure:
        """Genera gráfico de barras"""
        fig, ax = plt.subplots(figsize=(10, 5))
        ax.bar(df[x], df[y], color='steelblue', alpha=0.8)
        ax.set_title(titulo or f"{y} por {x}", fontsize=14)
        ax.set_xlabel(x)
        ax.set_ylabel(y)
        ax.tick_params(axis='x', rotation=45)
        ax.grid(True, alpha=0.3, axis='y')
        plt.tight_layout()
        
        if guardar:
            self._guardar(fig, f"barras_{x}_{y}")
        
        self.figuras.append(fig)
        return fig
    
    def grafico_dispersion(self, df: pd.DataFrame, x: str, y: str,
                          titulo: str = "", guardar: bool = True) -> plt.Figure:
        """Genera gráfico de dispersión"""
        fig, ax = plt.subplots(figsize=(10, 5))
        ax.scatter(df[x], df[y], alpha=0.6, s=50)
        ax.set_title(titulo or f"{y} vs {x}", fontsize=14)
        ax.set_xlabel(x)
        ax.set_ylabel(y)
        ax.grid(True, alpha=0.3)
        plt.tight_layout()
        
        if guardar:
            self._guardar(fig, f"dispersion_{x}_{y}")
        
        self.figuras.append(fig)
        return fig
    
    def grafico_histograma(self, df: pd.DataFrame, columna: str,
                          bins: int = 20, titulo: str = "", 
                          guardar: bool = True) -> plt.Figure:
        """Genera histograma"""
        fig, ax = plt.subplots(figsize=(10, 5))
        ax.hist(df[columna], bins=bins, color='green', alpha=0.7, edgecolor='black')
        ax.set_title(titulo or f"Distribución de {columna}", fontsize=14)
        ax.set_xlabel(columna)
        ax.set_ylabel("Frecuencia")
        ax.grid(True, alpha=0.3, axis='y')
        plt.tight_layout()
        
        if guardar:
            self._guardar(fig, f"histograma_{columna}")
        
        self.figuras.append(fig)
        return fig
    
    def dashboard_completo(self, df: pd.DataFrame) -> List[plt.Figure]:
        """Genera dashboard completo con múltiples gráficos"""
        if df.empty:
            return []
        
        figuras = []
        
        # 1. Líneas de tiempo
        if 'fecha' in df.columns and 'total' in df.columns:
            ventas_diarias = df.groupby('fecha')['total'].sum()
            fig = self.grafico_lineas(
                ventas_diarias.reset_index(),
                'fecha', 'total',
                "Ventas Diarias"
            )
            figuras.append(fig)
        
        # 2. Barras por categoría
        if 'categoria' in df.columns and 'total' in df.columns:
            ventas_cat = df.groupby('categoria')['total'].sum().reset_index()
            fig = self.grafico_barras(
                ventas_cat, 'categoria', 'total',
                "Ventas por Categoría"
            )
            figuras.append(fig)
        
        # 3. Top productos
        if 'producto' in df.columns and 'total' in df.columns:
            top = df.groupby('producto')['total'].sum().sort_values(ascending=False).head(5)
            fig = self.grafico_barras(
                top.reset_index(), 'producto', 'total',
                "Top 5 Productos"
            )
            figuras.append(fig)
        
        # 4. Histograma de precios
        if 'precio' in df.columns:
            fig = self.grafico_histograma(
                df, 'precio',
                titulo="Distribución de Precios"
            )
            figuras.append(fig)
        
        logger.info(f"Dashboard generado: {len(figuras)} gráficos")
        return figuras
    
    def _guardar(self, fig: plt.Figure, nombre: str):
        """Guarda la figura"""
        archivo = f"graficos/{nombre}.png"
        fig.savefig(archivo, dpi=150, bbox_inches='tight')
        logger.info(f"Gráfico guardado: {archivo}")
    
    def mostrar(self):
        """Muestra todas las figuras"""
        plt.show()
    
    def __str__(self):
        return f"📊 {self.titulo}: {len(self.figuras)} gráficos generados"

# ============================================
# DEMOSTRACIÓN
# ============================================
if __name__ == "__main__":
    print("=" * 60)
    print("SISTEMA DE VISUALIZACIÓN CON POO")
    print("=" * 60)
    
    # Crear datos de prueba
    fechas = pd.date_range('2026-08-01', periods=30)
    df = pd.DataFrame({
        'fecha': fechas,
        'total': np.random.randint(500, 2000, 30),
        'categoria': np.random.choice(['A', 'B', 'C'], 30),
        'producto': np.random.choice(['Prod1', 'Prod2', 'Prod3', 'Prod4', 'Prod5'], 30),
        'precio': np.random.randint(50, 500, 30)
    })
    
    # Crear visualizador
    visualizador = VisualizadorDatos("Análisis de Ventas")
    
    # Generar dashboard
    print("\n📊 GENERANDO DASHBOARD...")
    visualizador.dashboard_completo(df)
    
    print(f"\n✅ {visualizador}")
    print(f"  Gráficos guardados en carpeta 'graficos/'")
PROYECTO FINAL: Sistema de Dashboard de Ventas con POO
python
"""
PROYECTO FINAL: DASHBOARD DE VENTAS CON POO
ESENCIA: Sistema completo que integra POO, Pandas, Matplotlib, archivos y APIs
"""

import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import requests
import json
import csv
import logging
from pathlib import Path
from datetime import datetime
from typing import List, Dict, Optional, Any
from abc import ABC, abstractmethod
import time

# ============================================
# CONFIGURACIÓN
# ============================================
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('dashboard.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

Path("data").mkdir(exist_ok=True)
Path("graficos").mkdir(exist_ok=True)

# ============================================
# CLASES POO - DOMINIO
# ============================================

class Producto:
    """Representa un producto"""
    def __init__(self, id: int, nombre: str, categoria: str, precio: float, costo: float):
        self.id = id
        self.nombre = nombre
        self.categoria = categoria
        self.precio = precio
        self.costo = costo
    
    def margen(self) -> float:
        return self.precio - self.costo
    
    def margen_porcentual(self) -> float:
        return (self.margen() / self.precio * 100) if self.precio > 0 else 0
    
    def to_dict(self) -> Dict:
        return {
            "id": self.id,
            "nombre": self.nombre,
            "categoria": self.categoria,
            "precio": self.precio,
            "costo": self.costo,
            "margen": self.margen(),
            "margen_%": self.margen_porcentual()
        }

class Venta:
    """Representa una venta"""
    def __init__(self, producto_id: int, cantidad: int, fecha: str, vendedor: str):
        self.producto_id = producto_id
        self.cantidad = cantidad
        self.fecha = datetime.strptime(fecha, "%Y-%m-%d")
        self.vendedor = vendedor
    
    def to_dict(self) -> Dict:
        return {
            "producto_id": self.producto_id,
            "cantidad": self.cantidad,
            "fecha": self.fecha.strftime("%Y-%m-%d"),
            "vendedor": self.vendedor
        }

# ============================================
# CLASE TIENDA
# ============================================

class Tienda:
    """Sistema de gestión de tienda"""
    
    def __init__(self, nombre: str):
        self.nombre = nombre
        self.productos: List[Producto] = []
        self.ventas: List[Venta] = []
        logger.info(f"Tienda '{nombre}' creada")
    
    def agregar_producto(self, producto: Producto):
        self.productos.append(producto)
    
    def registrar_venta(self, venta: Venta):
        self.ventas.append(venta)
    
    def generar_datos_prueba(self, n_productos: int = 10, n_ventas: int = 200):
        """Genera datos de prueba"""
        categorias = ['Electrónica', 'Oficina', 'Hogar', 'Juguetes', 'Ropa']
        nombres = ['Laptop', 'Mouse', 'Teclado', 'Monitor', 'Impresora',
                   'Cuaderno', 'Bolígrafo', 'Mesa', 'Silla', 'Juguete']
        vendedores = ['Ana', 'Luis', 'Carlos', 'María', 'Pedro']
        
        # Generar productos
        for i in range(n_productos):
            nombre = np.random.choice(nombres) + f" {i+1}"
            categoria = np.random.choice(categorias)
            precio = np.random.randint(50, 1000)
            costo = precio * np.random.uniform(0.5, 0.8)
            self.agregar_producto(Producto(i+1, nombre, categoria, precio, costo))
        
        # Generar ventas
        fechas = pd.date_range('2026-08-01', periods=30)
        for _ in range(n_ventas):
            producto_id = np.random.randint(1, n_productos+1)
            cantidad = np.random.randint(1, 10)
            fecha = np.random.choice(fechas).strftime('%Y-%m-%d')
            vendedor = np.random.choice(vendedores)
            self.registrar_venta(Venta(producto_id, cantidad, fecha, vendedor))
        
        logger.info(f"Datos generados: {n_productos} productos, {n_ventas} ventas")
    
    def to_dataframes(self) -> tuple:
        """Convierte datos a DataFrames de Pandas"""
        df_productos = pd.DataFrame([p.to_dict() for p in self.productos])
        df_ventas = pd.DataFrame([v.to_dict() for v in self.ventas])
        
        # Unir datos
        df_combinado = df_ventas.merge(df_productos, left_on='producto_id', right_on='id')
        df_combinado['total'] = df_combinado['cantidad'] * df_combinado['precio']
        df_combinado['fecha'] = pd.to_datetime(df_combinado['fecha'])
        
        return df_productos, df_ventas, df_combinado
    
    def __str__(self):
        return f"🏪 {self.nombre}: {len(self.productos)} productos, {len(self.ventas)} ventas"

# ============================================
# CLASE ANALIZADOR DE VENTAS
# ============================================

class AnalizadorVentas:
    """Analiza datos de ventas"""
    
    def __init__(self, df_combinado: pd.DataFrame):
        self.df = df_combinado
    
    def metricas_generales(self) -> Dict:
        """Métricas generales de ventas"""
        return {
            "total_ventas": len(self.df),
            "ingreso_total": self.df['total'].sum(),
            "promedio_venta": self.df['total'].mean(),
            "venta_maxima": self.df['total'].max(),
            "venta_minima": self.df['total'].min()
        }
    
    def ventas_por_categoria(self) -> pd.DataFrame:
        return self.df.groupby('categoria').agg({
            'total': ['sum', 'mean', 'count'],
            'cantidad': 'sum'
        })
    
    def ventas_por_vendedor(self) -> pd.DataFrame:
        return self.df.groupby('vendedor').agg({
            'total': 'sum',
            'cantidad': 'sum',
            'producto_id': 'count'
        }).rename(columns={'producto_id': 'num_ventas'})
    
    def top_productos(self, n: int = 5) -> pd.DataFrame:
        return self.df.groupby('nombre')['total'].sum().sort_values(ascending=False).head(n)
    
    def ventas_diarias(self) -> pd.Series:
        return self.df.groupby('fecha')['total'].sum()
    
    def rentabilidad(self) -> pd.DataFrame:
        return self.df.groupby('categoria').agg({
            'margen': 'sum',
            'margen_%': 'mean'
        })
    
    def resumen_ejecutivo(self) -> Dict:
        """Resumen completo"""
        metricas = self.metricas_generales()
        
        return {
            **metricas,
            "categoria_mas_vendida": self.df.groupby('categoria')['total'].sum().idxmax(),
            "producto_mas_vendido": self.df.groupby('nombre')['total'].sum().idxmax(),
            "vendedor_destrecho": self.df.groupby('vendedor')['total'].sum().idxmax(),
            "margen_promedio": self.df['margen'].mean(),
            "margen_total": self.df['margen'].sum()
        }

# ============================================
# CLASE VISUALIZADOR
# ============================================

class VisualizadorDashboard:
    """Genera visualizaciones del dashboard"""
    
    def __init__(self, df: pd.DataFrame):
        self.df = df
        plt.style.use('seaborn-v0_8-darkgrid')
    
    def generar_dashboard(self) -> plt.Figure:
        """Genera dashboard completo"""
        fig, axes = plt.subplots(2, 2, figsize=(14, 10))
        fig.suptitle('📊 Dashboard de Ventas', fontsize=16, fontweight='bold')
        
        # 1. Ventas diarias
        ventas_diarias = self.df.groupby('fecha')['total'].sum()
        axes[0, 0].plot(ventas_diarias.index, ventas_diarias.values, marker='o', color='blue')
        axes[0, 0].set_title('Ventas Diarias', fontsize=12)
        axes[0, 0].set_xlabel('Fecha')
        axes[0, 0].set_ylabel('Ventas ($)')
        axes[0, 0].grid(True, alpha=0.3)
        axes[0, 0].tick_params(axis='x', rotation=45)
        
        # 2. Ventas por categoría
        ventas_cat = self.df.groupby('categoria')['total'].sum().sort_values()
        axes[0, 1].barh(ventas_cat.index, ventas_cat.values, color='green')
        axes[0, 1].set_title('Ventas por Categoría', fontsize=12)
        axes[0, 1].set_xlabel('Ventas ($)')
        axes[0, 1].grid(True, alpha=0.3, axis='x')
        
        # 3. Top productos
        top = self.df.groupby('nombre')['total'].sum().sort_values(ascending=False).head(5)
        axes[1, 0].bar(top.index, top.values, color='orange')
        axes[1, 0].set_title('Top 5 Productos', fontsize=12)
        axes[1, 0].set_xlabel('Producto')
        axes[1, 0].set_ylabel('Ventas ($)')
        axes[1, 0].tick_params(axis='x', rotation=45)
        axes[1, 0].grid(True, alpha=0.3, axis='y')
        
        # 4. Rentabilidad por categoría
        rentabilidad = self.df.groupby('categoria')['margen_%'].mean().sort_values()
        axes[1, 1].bar(rentabilidad.index, rentabilidad.values, color='purple')
        axes[1, 1].set_title('Margen Promedio por Categoría', fontsize=12)
        axes[1, 1].set_xlabel('Categoría')
        axes[1, 1].set_ylabel('Margen (%)')
        axes[1, 1].tick_params(axis='x', rotation=45)
        axes[1, 1].grid(True, alpha=0.3, axis='y')
        
        plt.tight_layout()
        return fig

# ============================================
# CLASE GESTOR DE ARCHIVOS
# ============================================

class GestorArchivosDashboard:
    """Guarda y carga datos del dashboard"""
    
    def __init__(self):
        self.directorio = Path("data")
        self.directorio.mkdir(exist_ok=True)
    
    def guardar_datos(self, tienda: Tienda):
        """Guarda datos de la tienda en JSON y CSV"""
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
        # Productos
        productos_dict = [p.to_dict() for p in tienda.productos]
        with open(self.directorio / f"productos_{timestamp}.json", 'w', encoding='utf-8') as f:
            json.dump(productos_dict, f, indent=2, ensure_ascii=False)
        
        # Ventas
        ventas_dict = [v.to_dict() for v in tienda.ventas]
        with open(self.directorio / f"ventas_{timestamp}.json", 'w', encoding='utf-8') as f:
            json.dump(ventas_dict, f, indent=2, ensure_ascii=False)
        
        logger.info(f"Datos guardados en {self.directorio}")
        return timestamp
    
    def guardar_analisis(self, analisis: Dict, timestamp: str):
        """Guarda resultados del análisis"""
        archivo = self.directorio / f"analisis_{timestamp}.json"
        with open(archivo, 'w', encoding='utf-8') as f:
            json.dump(analisis, f, indent=2, ensure_ascii=False, default=str)
        logger.info(f"Análisis guardado: {archivo}")

# ============================================
# FUNCIÓN PRINCIPAL - PROYECTO FINAL
# ============================================

def main():
    """Función principal del proyecto final"""
    print("=" * 60)
    print("PROYECTO FINAL: DASHBOARD DE VENTAS CON POO")
    print("=" * 60)
    
    try:
        # 1. Crear tienda y generar datos
        print("\n🏪 CREANDO TIENDA...")
        tienda = Tienda("Mi Tienda")
        tienda.generar_datos_prueba(n_productos=10, n_ventas=200)
        print(f"  {tienda}")
        
        # 2. Convertir a DataFrames
        print("\n📊 CONVIRTIENDO DATOS...")
        df_productos, df_ventas, df_combinado = tienda.to_dataframes()
        print(f"  Productos: {len(df_productos)}, Ventas: {len(df_ventas)}")
        
        # 3. Analizar datos
        print("\n📈 ANALIZANDO VENTAS...")
        analizador = AnalizadorVentas(df_combinado)
        resumen = analizador.resumen_ejecutivo()
        
        print("  Resumen:")
        for clave, valor in resumen.items():
            if isinstance(valor, float):
                print(f"    {clave}: ${valor:,.2f}" if 'total' in clave or 'margen' in clave else f"    {clave}: {valor:,.2f}")
            else:
                print(f"    {clave}: {valor}")
        
        # 4. Generar visualizaciones
        print("\n📊 GENERANDO DASHBOARD...")
        visualizador = VisualizadorDashboard(df_combinado)
        fig = visualizador.generar_dashboard()
        fig.savefig('graficos/dashboard_final.png', dpi=150, bbox_inches='tight')
        print("  Dashboard guardado: graficos/dashboard_final.png")
        
        # 5. Guardar datos
        print("\n💾 GUARDANDO DATOS...")
        gestor = GestorArchivosDashboard()
        timestamp = gestor.guardar_datos(tienda)
        gestor.guardar_analisis(resumen, timestamp)
        
        print("\n" + "=" * 60)
        print("✅ PROYECTO FINAL COMPLETADO EXITOSAMENTE")
        print("=" * 60)
        print(f"📁 Datos guardados en: data/")
        print(f"📊 Gráficos guardados en: graficos/")
        print(f"📋 Log: dashboard.log")
        
    except Exception as e:
        logger.critical(f"Error en el proyecto: {e}")
        raise

if __name__ == "__main__":
    main()
    
