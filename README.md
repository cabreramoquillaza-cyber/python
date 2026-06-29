     # ==============================================================================
    # SISTEMA DE INVENTARIO Y VENTAS - NEW MODEL (ORIENTADO A TIENDA)
    # ==============================================================================

    from datetime import date
    
    menu = {
        1: "Registrar o Añadir Tipo de Prenda",
        2: "Listar Todo el Inventario",
        3: "Consulta Detallada de Stock y Precios",
        4: "Eliminar Prenda o Tipo Específico",
        5: "Modificar Datos de Prenda y Stock",
        6: "Aumentar Stock (Ingreso de Mercadería)",
        7: "Reducir Stock (Registrar Venta)",
        8: "Ventas",
        9: "Comprobante de Pago",
        10: "Salir"
    }
    
    # Base de datos de Clientes y Empleados
    cliente = {
        45892104: "Mateo Alejandro Silva Torres",
        12432678: "Sofía Valentina Mendoza Castro",
        70419352: "Lucas Gabriel Espinosa Ruiz",
        28105693: "Camila Isabella Navarro Vega",
        53921476: "Diego Fernando Ortiz Romero",
        36784120: "Martina Alejandra Benítez Vega",
        61258943: "Valeria Nicole Miranda Flores",
        41258943: "Mías Salomón Reyes Vargas",
        22468895: "Isadora Beatriz Muñoz Delgado"
    }
    
    empleado = {
        100: "Sebastián Enrique Ortega Marín",
        200: "Gabriela Fernanda Lazcano Ríos",
        300: "Nicolás Alberto Duarte Pardo",
        400: "Mariana Elizabeth Cisneros Cruz",
        500: "Samuel David Quintana Pinto",
        600: "Florencia Agustina Santibáñez Leyva",
        700: "Benjamín Omar Palacios Meza",
        800: "Constanza Abigail Farías Sandoval"
    }
    
    # Base de datos de fidelización: DNI -> {"prendas_acumuladas": int, "puntos": int}
    puntos_clientes = {}
    
    carrito = {}
    
    producto = {
        1: "Casaca",
        2: "Blusa",
        3: "Polo",
        4: "Short"
    }
    
    stock = {
        1: {  # CASACAS
            "Deportiva":   {"S": 20, "M": 20, "L": 20, "precio": 120.00},
            "Jean":        {"S": 20, "M": 20, "L": 20, "precio": 140.00},
            "Impermeable": {"S": 20, "M": 20, "L": 20, "precio": 135.00},
            "Bomber":      {"S": 20, "M": 20, "L": 20, "precio": 125.00}
        },
        2: {  # BLUSAS
            "Manga corta": {"S": 20, "M": 20, "L": 20, "precio": 55.00},
            "Manga larga": {"S": 20, "M": 20, "L": 20, "precio": 65.00},
            "Casual":      {"S": 20, "M": 20, "L": 20, "precio": 60.00},
            "Elegante":    {"S": 20, "M": 20, "L": 20, "precio": 75.00}
        },
        3: {  # POLOS
            "Cuello redondo":{"S": 20, "M": 20, "L": 20, "precio": 35.00},
            "Cuello V":     {"S": 20, "M": 20, "L": 20, "precio": 38.00},
            "Deportivo":    {"S": 20, "M": 20, "L": 20, "precio": 40.00},
            "Piqué":        {"S": 20, "M": 20, "L": 20, "precio": 45.00}
        },
        4: {  # SHORTS
            "Verano":      {"S": 15, "M": 14, "L": 25, "precio": 50.00},
            "Invierno":    {"S": 25, "M": 30, "L": 24, "precio": 60.00}
        }
    }
    
    # ==============================================================================
    # FUNCIONES DE LECTURA Y VALIDACIÓN
    # ==============================================================================
    
    def leerProducto():
        return input("Ingrese la categoría de la prenda (ej: Polo, Casaca): ").strip().capitalize()
    
    def leerTipo():
        return input("Ingrese el modelo o estilo (ej: Deportivo, Jean, Verano): ").strip().capitalize()
    
    def leerPrecio():
        while True:
            try:
                val_precio = float(input("Ingrese el precio de venta al público: S/. "))
                if val_precio <= 0:
                    print("Error: El precio debe ser mayor a cero.")
                else:
                    return val_precio
            except ValueError:
                print("Error: Debe ingresar un monto válido.")
    
    def leerStockTallas():
        print("Ingrese las cantidades de stock inicial por talla:")
        while True:
            try:
                s = int(input("  Talla S: "))
                m = int(input("  Talla M: "))
                l = int(input("  Talla L: "))
                if s < 0 or m < 0 or l < 0:
                    print("Error: El stock no puede ser negativo.")
                else:
                    return s, m, l
            except ValueError:
                print("Error: Debe ingresar números enteros.")
    
    def seleccionarTalla():
        while True:
            talla = input("Ingrese la talla seleccionada (S, M, L): ").strip().upper()
            if talla in ["S", "M", "L"]:
                return talla
            print("Error: Talla no válida. Intente de nuevo.")
    
    def leerStock():
        while True:
            try:
                return int(input("Ingrese Stock: "))
            except ValueError:
                print("\nError: Stock inválido, vuelve a intentar..\n")
    
    # ==============================================================================
    # PROCESOS PRINCIPALES DEL SISTEMA
    # ==============================================================================
    
    def mostrar_menu():
        print("\n" + "=" * 70)
        print(" " * 22 + "TIENDA NEW MODEL - VENTAS")
        print("=" * 70)
        for clave, valor in menu.items():
            print(f" {clave}: {valor}")
        print("=" * 70)
    
    def agregar():
        print("\n" + "-" * 50)
        print("REGISTRAR PRENDAS / MODELOS NUEVOS")
        print("-" * 50)
        print("1: Agregar un nuevo modelo a una prenda existente")
        print("2: Registrar una categoría de prenda totalmente nueva")
        
        try:
            opc = int(input("Seleccione una opción: "))
        except ValueError:
            print("Error: Debe ingresar un número entero.")
            return
            
        if opc == 1:
            print("\nCategorías disponibles:")
            for k, v in producto.items():
                print(f"  Código {k}: {v}")
                
            try:
                clave = int(input("Ingrese el código de la prenda a la que añadirá el modelo: "))
            except ValueError:
                print("Error: El código debe ser numérico.")
                return
    
            if clave in producto:
                nuevo_tipo = leerTipo()
                if clave in stock and nuevo_tipo in stock[clave]:
                    print("Error: Ese modelo ya se encuentra registrado.")
                else:
                    if clave not in stock:
                        stock[clave] = {}
                    
                    s, m, l = leerStockTallas()
                    v_precio = leerPrecio()
                    
                    stock[clave][nuevo_tipo] = {
                        "S": s, "M": m, "L": l,
                        "precio": v_precio
                    }
                    print(f"\n¡Modelo '{nuevo_tipo}' añadido correctamente a {producto[clave]}!")
            else:
                print("Error: El código de prenda no existe.")
                
        elif opc == 2:
            clave = max(producto.keys()) + 1 if producto else 1
            producto[clave] = leerProducto()
            nuevo_tipo = leerTipo()
            
            s, m, l = leerStockTallas()
            v_precio = leerPrecio()
            
            stock[clave] = {
                nuevo_tipo: {
                    "S": s, "M": m, "L": l,
                    "precio": v_precio
                }
            }
            print(f"\n¡Nueva categoría '{producto[clave]}' [{nuevo_tipo}] guardada con éxito!")
        else:
            print("Opción incorrecta.")
    
    def listar():
        print("\n" + "=" * 80)
        print(" " * 24 + "CATÁLOGO DE PRODUCTOS - NEW MODEL")
        print("=" * 80)
        print(f"{'Cod':<5}{'Prenda':<12}{'Modelo / Estilo':<20}{'Precio':<15}{'Stock (S / M / L)':<20}")
        print("-" * 80)
        for clave in producto:
            if clave in stock:
                for tipo in stock[clave]:
                    tallas_info = f"S:{stock[clave][tipo]['S']}  M:{stock[clave][tipo]['M']}  L:{stock[clave][tipo]['L']}"
                    v_precio = stock[clave][tipo]['precio']
                    print(f"{clave:<5}{producto[clave]:<12}{tipo:<20}S/. {v_precio:<10.2f}{tallas_info:<20}")
        print("=" * 80)
    
    def consulta():
        print("\n" + "-" * 50)
        print("CONSULTA DETALLADA DE PRODUCTO")
        print("-" * 50)
        try:
            clave = int(input("Ingrese el código del producto: "))
        except ValueError:
            print("Error: Debe ingresar un código numérico.")
            return
    
        if clave in producto:
            print(f"\nPrenda de Vestir: {producto[clave]}")
            print("Modelos y Disponibilidad en Tienda:")
            print("-" * 60)
            for tipo in stock[clave]:
                v_precio = stock[clave][tipo]['precio']
                print(f" > Modelo: {tipo}")
                print(f"   - Precio al Público  : S/. {v_precio:.2f}")
                print(f"   - Stock Disponible   : Talla S: {stock[clave][tipo]['S']} und | Talla M: {stock[clave][tipo]['M']} und | Talla L: {stock[clave][tipo]['L']} und")
                total_und = stock[clave][tipo]['S'] + stock[clave][tipo]['M'] + stock[clave][tipo]['L']
                print(f"   - Total Unidades     : {total_und} prendas en vitrina")
                print("   . . . . . . . . . . . . . . . . . . . . . . . . . . . . .")
        else:
            print("Error: El producto no existe en el catálogo.")
    
    def eliminar():
        print("\n" + "-" * 50)
        print("ELIMINAR PRENDA O MODELO DE LA TIENDA")
        print("-" * 50)
        try:
            clave = int(input("Ingrese código de producto: "))
        except ValueError:
            print("Error: Debe ingresar un código numérico válido.")
            return
    
        if clave in producto:
            print(f"Seleccionó la línea de: {producto[clave]}")
            print("1: Quitar COMPLETAMENTE la prenda del sistema")
            print("2: Quitar únicamente un modelo/estilo específico")
            try:
                opc = int(input("Seleccione opción: "))
            except ValueError:
                print("Opción inválida.")
                return
            
            if opc == 1:
                del producto[clave]
                if clave in stock: del stock[clave]
                print("Prenda dada de baja del catálogo correctamente.")
            elif opc == 2:
                tipo_eliminar = leerTipo()
                if tipo_eliminar in stock[clave]:
                    del stock[clave][tipo_eliminar]
                    print(f"Modelo '{tipo_eliminar}' eliminado de la tienda.")
                    if not stock[clave]:
                        del producto[clave]
                        del stock[clave]
                else:
                    print("Error: Ese modelo no se encuentra registrado.")
            else:
                print("Opción fuera de rango.")
        else:
            print("Error: El producto no existe.")
    
    def modificar():
        print("\n" + "-" * 50)
        print("MODIFICAR PRECIOS O STOCK DE PRENDA")
        print("-" * 50)
        try:
            clave = int(input("Ingrese código del producto: "))
        except ValueError:
            print("Error: Debe ingresar un código numérico.")
            return
        
        if clave in producto:
            print(f"Modificando: {producto[clave]}")
            producto[clave] = leerProducto()
            
            print("\nModelos actuales en tienda:", ", ".join(stock[clave].keys()))
            tipo = leerTipo()
            
            if tipo in stock[clave]:
                print(f"\nActualizando datos para el modelo: {tipo}")
                s, m, l = leerStockTallas()
                v_precio = leerPrecio()
                
                stock[clave][tipo]["S"] = s
                stock[clave][tipo]["M"] = m
                stock[clave][tipo]["L"] = l
                stock[clave][tipo]["precio"] = v_precio
                
                print("\n¡Precio y stock actualizados con éxito para el público!")
            else:
                print("Error: El modelo no coincide con los registros de la tienda.")
        else:
            print("Error: Producto no localizado.")
    
    def aumentarStock():
        print("\n" + "=" * 50)
        print("INGRESO DE NUEVA MERCADERÍA (AUMENTAR STOCK)")
        print("=" * 50)
        try:
            clave = int(input("Ingrese código del producto: "))
        except ValueError:
            print("Error: Código numérico inválido.")
            return
        
        if clave in producto:
            tipo = leerTipo()
            if tipo in stock[clave]:
                talla = seleccionarTalla()
                try:
                    nuevo = int(input(f"Cantidad de unidades a ingresar [{tipo} - Talla {talla}]: "))
                    if nuevo <= 0:
                        print("Error: La cantidad debe ser positiva.")
                        return
                except ValueError:
                    print("Error: Ingrese solo números.")
                    return
                
                stock[clave][tipo][talla] += nuevo
                print(f"\n¡Stock actualizado! Inventario actual de {tipo} ({talla}): {stock[clave][tipo][talla]} unidades.")
            else:
                print("Error: Ese modelo de prenda no existe.")
        else:
            print("Error: Producto no registrado.")
    
    def reducirStock():
        print("\n" + "-" * 50)
        print("REGISTRAR NUEVA VENTA (REDUCIR STOCK)")
        print("-" * 50)
        try:
            clave = int(input("Ingrese código del producto vendido: "))
        except ValueError:
            print("Error: Código numérico inválido.")
            return
        
        if clave in producto:
            tipo = leerTipo()
            if tipo in stock[clave]:
                talla = seleccionarTalla()
                try:
                    cantidad = int(input(f"Cantidad de unidades vendidas [{tipo} - Talla {talla}]: "))
                    if cantidad <= 0:
                        print("Error: Ingrese un valor válido.")
                        return
                except ValueError:
                    print("Error: Ingrese solo números enteros.")
                    return
                
                if cantidad <= stock[clave][tipo][talla]:
                    # CORREGIDO: Aquí decía 'quantity', cámbialo por 'cantidad'
                    stock[clave][tipo][talla] -= cantidad 
                    print(f"\n¡Venta registrada con éxito! Quedan {stock[clave][tipo][talla]} unidades disponibles en Talla {talla}.")
                else:
                    print(f"\nError: Stock insuficiente para la venta. Solo cuentas con {stock[clave][tipo][talla]} unidades.")
            else:
                print("Error: Ese modelo de prenda no existe.")
        else:
            print("Error: Producto no registrado.")
    
    def ventas():
        global carrito
        print("\n--- REGISTRAR VENTA ---")
        try:
            dni = int(input("DNI del cliente: "))
            
            # --- NUEVO: REGISTRO AUTOMÁTICO SI EL DNI NO EXISTE ---
            if dni not in cliente:
                print("\n[SISTEMA] El DNI ingresado no está registrado.")
                nombre_nuevo = input("-> Ingrese el Nombre Completo del nuevo cliente para registrarlo: ").strip().title()
                
                # Guardamos en la base de datos de clientes oficiales
                cliente[dni] = nombre_nuevo
                # Inicializamos su cuenta de puntos
                puntos_clientes[dni] = {"prendas_acumuladas": 0, "puntos": 0}
                print(f"¡Cliente '{nombre_nuevo}' registrado exitosamente en New Model!\n")
                
            elif dni not in puntos_clientes:
                # Por si acaso el DNI existía en 'cliente' pero no tenía historial de puntos aún
                puntos_clientes[dni] = {"prendas_acumuladas": 0, "puntos": 0}
    
            listar()
            clave = int(input("Código de prenda: "))
            if clave not in producto:
                print("Error 101: Código de producto inexistente.")
                return
            tipo = leerTipo()
            if tipo not in stock.get(clave, {}):
                print("Error 102: Modelo no registrado.")
                return
            talla = seleccionarTalla()
            cantidad = int(input("Cantidad de prendas: "))
            if cantidad <= 0:
                print("Error 103: La cantidad debe ser mayor que cero.")
                return
            disponible = stock[clave][tipo][talla]
            if cantidad > disponible:
                print(f"Error 104: Stock insuficiente. Disponible: {disponible}.")
                return
            
            precio = stock[clave][tipo]["precio"]
            stock[clave][tipo][talla] -= cantidad
            
            # Se guarda el DNI en el carrito
            carrito[len(carrito) + 1] = [producto[clave], tipo, talla, cantidad, precio, precio * cantidad, dni]
            
            # --- SISTEMA DE CONTROL DE PUNTOS ---
            puntos_clientes[dni]["prendas_acumuladas"] += cantidad
            nuevos_puntos = puntos_clientes[dni]["prendas_acumuladas"] // 10
            
            if nuevos_puntos > 0:
                puntos_clientes[dni]["puntos"] += nuevos_puntos
                puntos_clientes[dni]["prendas_acumuladas"] %= 10
                print(f"\n¡Genial! El cliente acumuló {nuevos_puntos} punto(s) en este registro.")
                
            print(f"\nPrenda añadida al carrito. Estado actual de puntos (DNI {dni}):")
            print(f" -> Cliente: {cliente[dni]}")
            print(f" -> Puntos disponibles           : {puntos_clientes[dni]['puntos']}")
            print(f" -> Prendas acumuladas para sgte : {puntos_clientes[dni]['prendas_acumuladas']}/10")
            
        except ValueError:
            print("Error 105: Debe ingresar valores numéricos válidos.")
        except Exception as e:
            print("Error 199:", e)
    
    def comprobante():
        if not carrito:
            print("\nError: El carrito está vacío. No hay transacciones pendientes.")
            return
    
        try:
            ultimo_item = list(carrito.keys())[-1]
            dni = carrito[ultimo_item][6]
        except (IndexError, KeyError):
            dni = int(input("DNI cliente (Verificación de respaldo): "))
    
        try:
            cod = int(input("Código empleado: "))
        except ValueError:
            print("Error: Código inválido.")
            return
    
        print("\n" + "=" * 50)
        print("        COMPROBANTE DE PAGO - NEW MODEL")
        print("=" * 50)
        print("Fecha:", date.today())
        print("Cliente:", cliente.get(dni))  # Jala el nombre directo porque ya se registró
        print("Empleado:", empleado.get(cod, "No registrado"))
        print("-" * 50)
        
        subtotal = 0
        for _, it in carrito.items():
            print(f"{it[0]} {it[1]} (Talla {it[2]}) x{it[3]} | P.U. S/. {it[4]:.2f} | Total: S/. {it[5]:.2f}")
            subtotal += it[5]
        
        print("-" * 50)
        print(f"SUBTOTAL: S/. {subtotal:.2f}")
        
        # --- REDENCIÓN DE DESCUENTO ---
        descuento = 0.0
        puntos_actuales = puntos_clientes.get(dni, {}).get("puntos", 0)
        
        if puntos_actuales >= 10:
            descuento = subtotal * 0.25
            puntos_clientes[dni]["puntos"] -= 10
            print(f"¡DESCUENTO DE FIDELIDAD! Canje de 10 puntos (-25%): -S/. {descuento:.2f}")
            print(f" -> Puntos vigentes del cliente: {puntos_clientes[dni]['puntos']}")
        else:
            print(f"Puntos del cliente: {puntos_actuales}/10 (Requiere 10 para cupón de 25%)")
            
        total_final = subtotal - descuento
        print(f"TOTAL NETO A PAGAR: S/. {total_final:.2f}")
        print("=" * 50)
        
        carrito.clear()
    
    # ==============================================================================
    # BUCLE EJECUTABLE PRINCIPAL
    # ==============================================================================
    
    while True:
        mostrar_menu()
        try:
            opcion = int(input("Seleccione una opción del menú: "))
        except ValueError:
            print("\nError: Opción inválida. Por favor, introduzca solo números.")
            continue
    
        if opcion == 1:
            agregar()
        elif opcion == 2:
            listar()
        elif opcion == 3:
            consulta()
        elif opcion == 4:
            eliminar()
        elif opcion == 5:
            modificar()
        elif opcion == 6:
            aumentarStock()
        elif opcion == 7:
            reducirStock()
        elif opcion == 8:
            ventas()
        elif opcion == 9:
            comprobante()
        elif opcion == 10:
            print("\nCerrando el sistema de tienda... ¡Gracias por usar New Model System!")
            break
        else:
            print("\nOpción incorrecta. Intente dentro del rango de opciones (1-10).")
