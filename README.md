# tienda
#Crud tienda
import tkinter as tk
from tkinter import messagebox
import json
import os


class Producto:
    def __init__(self, id, nombre, precio, stock):
        self.id = id
        self.nombre = nombre
        self.precio = precio
        self.stock = stock

    def __str__(self):
        return f"id:{self.id}, nombre:{self.nombre}, precio:{self.precio} dolares, stock:{self.stock}"

    def to_dict(self):
        return {
            "id": self.id,
            "nombre": self.nombre,
            "precio": self.precio,
            "stock": self.stock
        }

class Persona:
    def __init__(self, id_usuario, nombre):
        self.id_usuario = id_usuario
        self.nombre = nombre

    def __str__(self):
        return f"{self.id_usuario}, Nombre:{self.nombre}"

    def to_dict(self):
        return {
            "id_usuario": self.id_usuario,
            "nombre": self.nombre
        }

class Sistema:
    def __init__(self):
        self.productos = []
        self.proveedores = []
        self.usuarios = []
        self.cargar_datos()

    def cargar_datos(self):
        if os.path.exists("datos.json"):
            with open("datos.json", "r") as file:
                datos = json.load(file)
                self.productos = [Producto(**p) for p in datos.get("productos", [])]
                self.proveedores = [Persona(**prov) for prov in datos.get("proveedores", [])]
                self.usuarios = [Persona(**user) for user in datos.get("usuarios", [])]

    def guardar_datos(self):
        datos = {
            "productos": [p.to_dict() for p in self.productos],
            "proveedores": [prov.to_dict() for prov in self.proveedores],
            "usuarios": [user.to_dict() for user in self.usuarios]
        }
        with open("datos.json", "w") as file:
            json.dump(datos, file, indent=4)

    def agregar_producto(self, producto):
        self.productos.append(producto)
        self.guardar_datos()

    def agregar_proveedor(self, proveedor):
        self.proveedores.append(proveedor)
        self.guardar_datos()

    def agregar_usuario(self, usuario):
        self.usuarios.append(usuario)
        self.guardar_datos()

    def listar_productos(self):
        return "\n".join([str(producto) for producto in self.productos])

    def listar_proveedores(self):
        return "\n".join([str(proveedor) for proveedor in self.proveedores])

    def listar_usuarios(self):
        return "\n".join([str(usuario) for usuario in self.usuarios])

    def eliminar_producto(self, nombre):
        for producto in self.productos:
            if producto.nombre == nombre:
                self.productos.remove(producto)
                self.guardar_datos()
                return f"Producto {nombre} eliminado."
        return "Producto no encontrado."

    def eliminar_proveedor(self, nombre):
        for proveedor in self.proveedores:
            if proveedor.nombre == nombre:
                self.proveedores.remove(proveedor)
                self.guardar_datos()
                return f"Proveedor {nombre} eliminado."
        return "Proveedor no encontrado."

    def eliminar_usuario(self, nombre):
        for usuario in self.usuarios:
            if usuario.nombre == nombre:
                self.usuarios.remove(usuario)
                self.guardar_datos()
                return f"Usuario {nombre} eliminado."
        return "Usuario no encontrado."

class InterfazGrafica:
    def __init__(self, root, sistema):
        self.root = root
        self.sistema = sistema
        self.root.title("TIENDA")
        self.root.configure(bg="skyblue")  

        
        self.crear_widgets()

        
        self.root.protocol("WM_DELETE_WINDOW", self.on_closing)

    def on_closing(self):
        self.sistema.guardar_datos()
        self.root.destroy()

    def crear_widgets(self):
        
        self.marco_operaciones = tk.Frame(self.root, bg="skyblue")  
        self.marco_operaciones.pack(pady=20)

        
        self.boton_agregar_producto = tk.Button(
            self.marco_operaciones, text="Agregar Producto", command=self.agregar_producto,
            bg="skyblue", fg="white"
        )
        self.boton_agregar_producto.grid(row=0, column=0, padx=10)

        self.boton_agregar_proveedor = tk.Button(
            self.marco_operaciones, text="Agregar Proveedor", command=self.agregar_proveedor,
            bg="light green", fg="white"
        )
        self.boton_agregar_proveedor.grid(row=0, column=2, padx=10)

        self.boton_agregar_usuario = tk.Button(
            self.marco_operaciones, text="Agregar Usuario", command=self.agregar_usuario,
            bg="orange", fg="white"
        )
        self.boton_agregar_usuario.grid(row=0, column=1, padx=10)

        
        self.boton_eliminar_producto = tk.Button(
            self.marco_operaciones, text="Eliminar Producto", command=self.eliminar_producto,
            bg="skyblue", fg="white"
        )
        self.boton_eliminar_producto.grid(row=1, column=0, padx=10, pady=10)

        self.boton_eliminar_proveedor = tk.Button(
            self.marco_operaciones, text="Eliminar Proveedor", command=self.eliminar_proveedor,
            bg="light green", fg="white"
        )
        self.boton_eliminar_proveedor.grid(row=1, column=2, padx=10, pady=10)

        self.boton_eliminar_usuario = tk.Button(
            self.marco_operaciones, text="Eliminar Usuario", command=self.eliminar_usuario,
            bg="orange", fg="white"
        )
        self.boton_eliminar_usuario.grid(row=1, column=1, padx=10, pady=10)

        
        self.marco_listar = tk.Frame(self.root, bg="skyblue")  
        self.marco_listar.pack(pady=20)

        
        self.boton_listar_productos = tk.Button(
            self.marco_listar, text="Listar Productos", command=self.listar_productos,
            bg="skyblue", fg="white"
        )
        self.boton_listar_productos.grid(row=0, column=0, padx=10)

        self.boton_listar_proveedores = tk.Button(
            self.marco_listar, text="Listar Proveedores", command=self.listar_proveedores,
            bg="light green", fg="white"
        )
        self.boton_listar_proveedores.grid(row=0, column=2, padx=10)

        self.boton_listar_usuarios = tk.Button(
            self.marco_listar, text="Listar Usuarios", command=self.listar_usuarios,
            bg="orange", fg="white"
        )
        self.boton_listar_usuarios.grid(row=0, column=1, padx=10)

    
    def agregar_producto(self):
        self.ventana_agregar_producto = tk.Toplevel(self.root)
        self.ventana_agregar_producto.title("Agregar Producto")
        self.ventana_agregar_producto.configure(bg="skyblue")  

        
        self.label_id = tk.Label(self.ventana_agregar_producto, text="ID:", bg="skyblue", fg="white")
        self.label_id.pack()
        self.entry_id = tk.Entry(self.ventana_agregar_producto, bg="skyblue", fg="white")
        self.entry_id.pack()

        self.label_nombre = tk.Label(self.ventana_agregar_producto, text="Nombre:", bg="skyblue", fg="white")
        self.label_nombre.pack()
        self.entry_nombre = tk.Entry(self.ventana_agregar_producto, bg="skyblue", fg="white")
        self.entry_nombre.pack()

        self.label_precio = tk.Label(self.ventana_agregar_producto, text="Precio:", bg="skyblue", fg="white")
        self.label_precio.pack()
        self.entry_precio = tk.Entry(self.ventana_agregar_producto, bg="skyblue", fg="white")
        self.entry_precio.pack()

        self.label_stock = tk.Label(self.ventana_agregar_producto, text="Stock:", bg="skyblue", fg="white")
        self.label_stock.pack()
        self.entry_stock = tk.Entry(self.ventana_agregar_producto, bg="skyblue", fg="white")
        self.entry_stock.pack()

        self.boton_guardar = tk.Button(
            self.ventana_agregar_producto, text="Guardar", command=self.guardar_producto,
            bg="skyblue", fg="white"
        )
        self.boton_guardar.pack(pady=10)

    def guardar_producto(self):
        id = self.entry_id.get()
        nombre = self.entry_nombre.get()
        precio = float(self.entry_precio.get())
        stock = int(self.entry_stock.get())
        producto = Producto(id, nombre, precio, stock)
        self.sistema.agregar_producto(producto)
        messagebox.showinfo("Éxito", f"Producto {nombre} agregado con éxito.")
        self.ventana_agregar_producto.destroy()

    def listar_productos(self):
        productos = self.sistema.listar_productos()
        messagebox.showinfo("Productos", productos)

    
    def agregar_proveedor(self):
        self.ventana_agregar_proveedor = tk.Toplevel(self.root)
        self.ventana_agregar_proveedor.title("Agregar Proveedor")
        self.ventana_agregar_proveedor.configure(bg="light green")  

        self.label_id_proveedor = tk.Label(self.ventana_agregar_proveedor, text="ID Proveedor:", bg="light green", fg="white")
        self.label_id_proveedor.pack()
        self.entry_id_proveedor = tk.Entry(self.ventana_agregar_proveedor, bg="light green", fg="white")
        self.entry_id_proveedor.pack()

        self.label_nombre_proveedor = tk.Label(self.ventana_agregar_proveedor, text="Nombre Proveedor:", bg="light green", fg="white")
        self.label_nombre_proveedor.pack()
        self.entry_nombre_proveedor = tk.Entry(self.ventana_agregar_proveedor, bg="light green", fg="white")
        self.entry_nombre_proveedor.pack()

        self.boton_guardar_proveedor = tk.Button(
            self.ventana_agregar_proveedor, text="Guardar", command=self.guardar_proveedor,
            bg="light green", fg="white"
        )
        self.boton_guardar_proveedor.pack(pady=10)

    def guardar_proveedor(self):
        id_proveedor = self.entry_id_proveedor.get()
        nombre_proveedor = self.entry_nombre_proveedor.get()
        proveedor = Persona(id_proveedor, nombre_proveedor)
        self.sistema.agregar_proveedor(proveedor)
        messagebox.showinfo("Éxito", f"Proveedor {nombre_proveedor} agregado con éxito.")
        self.ventana_agregar_proveedor.destroy()

    def listar_proveedores(self):
        proveedores = self.sistema.listar_proveedores()
        messagebox.showinfo("Proveedores", proveedores)

    
    def agregar_usuario(self):
        self.ventana_agregar_usuario = tk.Toplevel(self.root)
        self.ventana_agregar_usuario.title("Agregar Usuario")
        self.ventana_agregar_usuario.configure(bg="orange")  

        self.label_id_usuario = tk.Label(self.ventana_agregar_usuario, text="ID Usuario:", bg="orange", fg="white")
        self.label_id_usuario.pack()
        self.entry_id_usuario = tk.Entry(self.ventana_agregar_usuario, bg="orange", fg="white")
        self.entry_id_usuario.pack()

        self.label_nombre_usuario = tk.Label(self.ventana_agregar_usuario, text="Nombre Usuario:", bg="orange", fg="white")
        self.label_nombre_usuario.pack()
        self.entry_nombre_usuario = tk.Entry(self.ventana_agregar_usuario, bg="orange", fg="white")
        self.entry_nombre_usuario.pack()

        self.boton_guardar_usuario = tk.Button(
            self.ventana_agregar_usuario, text="Guardar", command=self.guardar_usuario,
            bg="orange", fg="white"
        )
        self.boton_guardar_usuario.pack(pady=10)

    def guardar_usuario(self):
        id_usuario = self.entry_id_usuario.get()
        nombre_usuario = self.entry_nombre_usuario.get()
        usuario = Persona(id_usuario, nombre_usuario)
        self.sistema.agregar_usuario(usuario)
        messagebox.showinfo("Éxito", f"Usuario {nombre_usuario} agregado con éxito.")
        self.ventana_agregar_usuario.destroy()

    def listar_usuarios(self):
        usuarios = self.sistema.listar_usuarios()
        messagebox.showinfo("Usuarios", usuarios)

    
    def eliminar_producto(self):
        self.ventana_eliminar_producto = tk.Toplevel(self.root)
        self.ventana_eliminar_producto.title("Eliminar Producto")
        self.ventana_eliminar_producto.configure(bg="skyblue")  

        self.label_nombre_producto = tk.Label(self.ventana_eliminar_producto, text="Nombre del Producto:", bg="skyblue", fg="white")
        self.label_nombre_producto.pack()
        self.entry_nombre_producto = tk.Entry(self.ventana_eliminar_producto, bg="skyblue", fg="white")
        self.entry_nombre_producto.pack()

        self.boton_eliminar = tk.Button(
            self.ventana_eliminar_producto, text="Eliminar", command=self.confirmar_eliminar_producto,
            bg="skyblue", fg="white"
        )
        self.boton_eliminar.pack(pady=10)

    def confirmar_eliminar_producto(self):
        nombre = self.entry_nombre_producto.get()
        resultado = self.sistema.eliminar_producto(nombre)
        messagebox.showinfo("Resultado", resultado)
        self.ventana_eliminar_producto.destroy()

    def eliminar_proveedor(self):
        self.ventana_eliminar_proveedor = tk.Toplevel(self.root)
        self.ventana_eliminar_proveedor.title("Eliminar Proveedor")
        self.ventana_eliminar_proveedor.configure(bg="light green")  

        self.label_nombre_proveedor = tk.Label(self.ventana_eliminar_proveedor, text="Nombre del Proveedor:", bg="light green", fg="white")
        self.label_nombre_proveedor.pack()
        self.entry_nombre_proveedor = tk.Entry(self.ventana_eliminar_proveedor, bg="light green", fg="white")
        self.entry_nombre_proveedor.pack()

        self.boton_eliminar = tk.Button(
            self.ventana_eliminar_proveedor, text="Eliminar", command=self.confirmar_eliminar_proveedor,
            bg="light green", fg="white"
        )
        self.boton_eliminar.pack(pady=10)

    def confirmar_eliminar_proveedor(self):
        nombre = self.entry_nombre_proveedor.get()
        resultado = self.sistema.eliminar_proveedor(nombre)
        messagebox.showinfo("Resultado", resultado)
        self.ventana_eliminar_proveedor.destroy()

    def eliminar_usuario(self):
        self.ventana_eliminar_usuario = tk.Toplevel(self.root)
        self.ventana_eliminar_usuario.title("Eliminar Usuario")
        self.ventana_eliminar_usuario.configure(bg="orange")  

        self.label_nombre_usuario = tk.Label(self.ventana_eliminar_usuario, text="Nombre del Usuario:", bg="orange", fg="white")
        self.label_nombre_usuario.pack()
        self.entry_nombre_usuario = tk.Entry(self.ventana_eliminar_usuario, bg="orange", fg="white")
        self.entry_nombre_usuario.pack()
        self.boton_eliminar = tk.Button(
            self.ventana_eliminar_usuario, text="Eliminar", command=self.confirmar_eliminar_usuario,
            bg="orange", fg="white"
        )
        self.boton_eliminar.pack(pady=10)

    def confirmar_eliminar_usuario(self):
        nombre = self.entry_nombre_usuario.get()
        resultado = self.sistema.eliminar_usuario(nombre)
        messagebox.showinfo("Resultado", resultado)
        self.ventana_eliminar_usuario.destroy()

sistema = Sistema()
root = tk.Tk()
interfaz = InterfazGrafica(root, sistema)
root.mainloop()
