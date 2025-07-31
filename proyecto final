"""
Proyecto Final - Sistema de Gestión de Productos
Nombre: SuscripTech

Este programa permite gestionar productos relacionados con suscripciones tecnológicas:
planes de suscripción y dispositivos adicionales. Permite agregar, visualizar, buscar y eliminar productos.

Desarrollado por: Daniela Flores
"""

import sqlite3
from colorama import init, Fore

init(autoreset=True)

# Conexión a la base de datos
conn = sqlite3.connect("inventario.db")
cursor = conn.cursor()

# Crear tabla si no existe
cursor.execute("""
CREATE TABLE IF NOT EXISTS productos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre TEXT NOT NULL,
    precio REAL NOT NULL,
    cantidad INTEGER NOT NULL
)
""")
conn.commit()

# Planes predefinidos
planes = [
    ("Plan Básico", 3200, 1000),
    ("Plan Estándar", 4300, 800),
    ("Plan Premium", 5500, 500),
    ("Dispositivo adicional", 2000, 100)
]

# Insertar planes por primera vez si no existen
for nombre, precio, cantidad in planes:
    cursor.execute("SELECT * FROM productos WHERE nombre = ?", (nombre,))
    if not cursor.fetchone():
        cursor.execute("INSERT INTO productos (nombre, precio, cantidad) VALUES (?, ?, ?)", (nombre, precio, cantidad))
conn.commit()

def mostrar_bienvenida():
    print(Fore.CYAN + "=" * 50)
    print(Fore.GREEN + "📱 Bienvenido a SuscripTech - Sistema de Gestión de Productos")
    print("Plataforma para gestión de suscripciones y dispositivos adicionales")
    print("=" * 50)

def mostrar_menu():
    print(Fore.CYAN + "\n--- Menú Principal ---")
    print("1. Agregar producto")
    print("2. Mostrar productos")
    print("3. Buscar producto")
    print("4. Eliminar producto")
    print("5. Seleccionar plan")
    print("6. Salir")

def agregar_producto():
    print(Fore.YELLOW + "\nAgregar nuevo producto")
    print("1. Agregar plan de suscripción")
    print("2. Agregar dispositivo adicional")
    tipo = input("Seleccione el tipo de producto (1 o 2): ")

    if tipo == "1":
        print("\nPlanes disponibles:")
        for i, (nombre, precio, _) in enumerate(planes[:3], start=1):
            print(f"{i}. {nombre} - ${precio} ARS")

        try:
            opcion = int(input("Seleccione un plan por número: "))
            nombre, precio, _ = planes[opcion - 1]
        except (IndexError, ValueError):
            print(Fore.RED + "Opción inválida.")
            return

        confirmar = input("¿Desea confirmar el agregado? (s/n): ").lower()
        if confirmar != 's':
            print(Fore.RED + "Operación cancelada.")
            return

        # Sumar 1 unidad al stock existente
        cursor.execute("UPDATE productos SET cantidad = cantidad + 1 WHERE nombre = ?", (nombre,))
        conn.commit()
        print(Fore.GREEN + f"{nombre} agregado exitosamente.")

    elif tipo == "2":
        try:
            cantidad = int(input("¿Cuántos dispositivos adicionales desea agregar?: "))
            if cantidad <= 0:
                raise ValueError
        except ValueError:
            print(Fore.RED + "Cantidad inválida.")
            return

        cursor.execute("SELECT cantidad, precio FROM productos WHERE nombre = 'Dispositivo adicional'")
        fila = cursor.fetchone()
        if fila:
            stock_actual, precio = fila
            confirmar = input(f"Precio: ${precio} ARS | ¿Confirmar agregar {cantidad} dispositivos? (s/n): ").lower()
            if confirmar != "s":
                print(Fore.RED + "Operación cancelada.")
                return
            cursor.execute("UPDATE productos SET cantidad = cantidad + ? WHERE nombre = 'Dispositivo adicional'", (cantidad,))
            conn.commit()
            print(Fore.GREEN + "Dispositivos adicionales agregados correctamente.")
        else:
            print(Fore.RED + "Producto 'Dispositivo adicional' no encontrado.")
    else:
        print(Fore.RED + "Opción inválida.")

def mostrar_productos():
    cursor.execute("SELECT * FROM productos")
    productos = cursor.fetchall()
    if productos:
        print(Fore.CYAN + "\n--- Lista de productos ---")
        for prod in productos:
            print(f"ID: {prod[0]} | Nombre: {prod[1]} | Precio: ${int(prod[2])} | Cantidad: {prod[3]}")
    else:
        print(Fore.YELLOW + "No hay productos registrados.")

def buscar_producto():
    termino = input("Ingrese el nombre o número del plan/producto: ").strip().lower()
    if termino.isdigit():
        cursor.execute("SELECT * FROM productos WHERE id = ?", (int(termino),))
    else:
        cursor.execute("SELECT * FROM productos WHERE LOWER(nombre) LIKE ?", (f"%{termino}%",))
    resultado = cursor.fetchall()
    if resultado:
        for prod in resultado:
            print(Fore.CYAN + f"\nID: {prod[0]} | Nombre: {prod[1]} | Precio: ${int(prod[2])} | Cantidad: {prod[3]}")
    else:
        print(Fore.RED + "Producto no encontrado.")

def eliminar_producto():
    id_input = input("Ingrese el ID del producto a eliminar: ")
    try:
        id_prod = int(id_input)
    except ValueError:
        print(Fore.RED + "ID inválido. Solo se aceptan números.")
        return

    cursor.execute("SELECT * FROM productos WHERE id = ?", (id_prod,))
    prod = cursor.fetchone()
    if not prod:
        print(Fore.RED + "Producto no encontrado.")
        return
    confirmar = input(f"¿Está seguro de eliminar '{prod[1]}'? (s/n): ").lower()
    if confirmar == "s":
        cursor.execute("DELETE FROM productos WHERE id = ?", (id_prod,))
        conn.commit()
        print(Fore.GREEN + "Producto eliminado.")
    else:
        print("Operación cancelada.")

def seleccionar_plan():
    print(Fore.BLUE + "\n--- Selección de plan ---")
    for i, (nombre, precio, _) in enumerate(planes[:3], start=1):
        print(f"{i}. {nombre} - ${precio} ARS")
    try:
        opcion = int(input("Seleccione un plan (1-3): "))
        nombre, precio, _ = planes[opcion - 1]
        print(Fore.GREEN + f"Has seleccionado el plan {nombre} con un precio de ${precio} ARS.")
    except (IndexError, ValueError):
        print(Fore.RED + "Selección inválida.")

# Ejecución del programa
if __name__ == "__main__":
    mostrar_bienvenida()
    while True:
        mostrar_menu()
        try:
            opcion = int(input("Seleccione una opción (1-6): "))
            if opcion == 1:
                agregar_producto()
            elif opcion == 2:
                mostrar_productos()
            elif opcion == 3:
                buscar_producto()
            elif opcion == 4:
                eliminar_producto()
            elif opcion == 5:
                seleccionar_plan()
            elif opcion == 6:
                print("Gracias por usar SuscripTech. ¡Hasta luego!")
                break
            else:
                print(Fore.RED + "Opción inválida.")
        except ValueError:
            print(Fore.RED + "Entrada inválida. Debe ingresar un número.")
