ARCHIVO: app.py
from flask import Flask, request
from flask import render_template
from flask import url_for, redirect, flash

app = Flask(__name__)
app.secret_key = "clave_secreta"

import mysql.connector

def get_connection():
    return mysql.connector.connect(
        host="localhost",
        user="root",
        password="root",
        database="tienda"
    )
  
def crear_customer(nombre,apellido, email, telefono):
    conn = get_connection()
    cursor = conn.cursor()
    sql = """INSERT INTO usuarios 
             (nombre,apellido, email, telefono) 
             VALUES (%s, %s, %s, %s)"""
    values = (nombre,apellido, email, telefono)
    cursor.execute(sql, values)
    conn.commit()
    last_id = cursor.lastrowid
    cursor.close()
    conn.close()
    return last_id

def obtener_usuarios():
    conn = get_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("select * from usuarios")
    rows = cursor.fetchall()
    cursor.close()
    conn.close()
    return rows

def obtener_usuario(id):
    conn = get_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("select * from usuarios where idusuario=%s", (id,))
    rows = cursor.fetchone()
    cursor.close()
    conn.close()
    return rows

def actualizar_usuario(idusuario, email, telefono):
    conn = get_connection()
    cursor = conn.cursor()
    values = (email,telefono, idusuario)
    cursor.execute("update usuarios set email=%s, telefono=%s where idusuario=%s", values)
    conn.commit()
    cursor.close()
    conn.close
    return "usuario actualizado"

def eliminar_usuario(idusuario):
    conn = get_connection()
    cursor = conn.cursor()
    values = (idusuario,)
    cursor.execute("delete from usuarios where idusuario=%s", values)
    conn.commit()
    cursor.close()
    conn.close
    return "usuario eliminado"


@app.route("/")
def hello():
    return render_template("index.html", titulo="sobre nosotros")

@app.route("/registro", methods=["POST"])
def registro():
    if request.method == "POST":
        nombre = request.form["nombre"]
        apellido = request.form["apellido"]
        email = request.form["email"]
        telefono = request.form["telefono"]
        respuesta = crear_customer(nombre, apellido, email, telefono)
        flash("se registro el usuario de forma exitosa")
        return redirect(url_for("registrarse"))
    flash("se presento un error")
    return redirect(url_for("registrarse"))
    
@app.route("/registrarse")
def registrarse():
    return render_template("registrarse.html")

@app.route("/usuarios")
def usuarios():
    usuarios = obtener_usuarios()
    return render_template("usuarios.html", usuarios = usuarios)

if __name__ == "__main__":
    app.run(debug=True)

ARCHIVO: dbtest.py
#pip install mysql-connector-python
import mysql.connector

def get_connection():
    return mysql.connector.connect(
        host="localhost",
        user="root",
        password="root",
        database="tienda"
    )
  
def crear_customer(nombre,apellido, email, telefono):
    conn = get_connection()
    cursor = conn.cursor()
    sql = """INSERT INTO usuarios 
             (nombre,apellido, email, telefono) 
             VALUES (%s, %s, %s, %s)"""
    values = (nombre,apellido, email, telefono)
    cursor.execute(sql, values)
    conn.commit()
    last_id = cursor.lastrowid
    cursor.close()
    conn.close()
    return last_id

def obtener_usuarios():
    conn = get_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("select * from usuarios")
    rows = cursor.fetchall()
    cursor.close()
    conn.close()
    return rows

def obtener_usuario(id):
    conn = get_connection()
    cursor = conn.cursor(dictionary=True)
    cursor.execute("select * from usuarios where idusuario=%s", (id,))
    rows = cursor.fetchone()
    cursor.close()
    conn.close()
    return rows

def actualizar_usuario(idusuario, email, telefono):
    conn = get_connection()
    cursor = conn.cursor()
    values = (email,telefono, idusuario)
    cursor.execute("update usuarios set email=%s, telefono=%s where idusuario=%s", values)
    conn.commit()
    cursor.close()
    conn.close
    return "usuario actualizado"

def eliminar_usuario(idusuario):
    conn = get_connection()
    cursor = conn.cursor()
    values = (idusuario,)
    cursor.execute("delete from usuarios where idusuario=%s", values)
    conn.commit()
    cursor.close()
    conn.close
    return "usuario eliminado"

CARPETA TEMPLATES: ARCHIVO registrarse.html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
{% with messages = get_flashed_messages() %}
{% if messages %}
<ul>
{% for message in messages %}
<li>{{ message }}</li>
{% endfor %}
</ul>
{% endif %}
{% endwith %}

<div class="container">
    <form action="registro" method="POST">
        <input type="text" name="nombre" placeholder="nombre"><br>
        <input type="text" name="apellido" placeholder="apellido"><br>
        <input type="text" name="email" placeholder="email"><br>
        <input type="text" name="telefono" placeholder="telefono"><br>
        <input type="submit" value="Registrar">
    </form>
</div>

</body>
</html>

ARCHIVO usuarios.html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='css/style.css') }}">

</head>
<body>
   <div class="container">
    <table>
        <tr>
            <th>ID</th>
            <th>nombre</th>
            <th>apellido</th>
            <th>email</th>
            <th>telefono</th>
            {% if usuarios %}
            {% for usuario in usuarios %}
            <tr>
                <td>{{usuario.idusuario}}</td>
                <td>{{usuario.nombre}}</td>
                <td>{{usuario.apellido}}</td>
                <td>{{usuario.email}}</td>
                <td>{{usuario.telefono}}</td>
            </tr>
            {% endfor %}
            {% else %}
            <tr>no hay usuarios</tr>
            {% endif %}
        </tr>
    </table>
   </div>  
</body>
</html>

CARPETA: STATIC, ARCHIVO style.css
.container {
    background-color: rgb(148, 105, 188);
    width: 40%;
    margin-left: 30%;
    margin-right: 30%;
}

¿QUÉ SON LAS SESIONES EN SOFTWARE?
Una sesión es un mecanismo que permite almacenar información temporal sobre un usuario mientras interactúa con una aplicación.
Se inicia cuando un usuario se autentica o empieza a usasr el sistema
Permite mantener deatos durante varias peticiones HTTP
Finaliza cuando el usuario cierra sesión o expira el tiempo configurado

https://ejecutortecnico.github.io/pri/mision2/json.html

¿qué es JSON?
JSON significa javascript object notation. es un formato ligero para el intercambio de datos
-facil de leer y escribir por humanos
-facil de procesar por maquinas
-se utiliza ampliamente en APIs y aplicaciones web

ESTRUCTURA DE JSON
json se basa en pares clave : valor y puede econtener:
objetos: colecciones de pares clave-valor con llaves {
arreglos: listas d evalores con corchetes [
valores: cadenas, numeros, booleanos, nulos u otros objetos/arrays

EJEMPLO DE UN OBJETO JSON
{
"id": 1,
"nombre": "Juan Pérez",
"email": "juan@example.com",                      ESTE JSON REPRESENTA UN USUARIO CON VARIOS ATRIBUTOS
"activo": true
}


[
  {
    "id": 1,
    "producto": "Laptop",
    "precio": 1200.50
  },
  {
    "id": 2,
    "producto": "Mouse",
    "precio": 25.99
  }
]


DONDE SE USA JSON?
comunicacion entre cliente - servidor (APIs REST)
almacenamiento de configuraciones
intercambio de datos entre sistemas

EJEMPLO DE API QUE DEVUELVE JSON
en terminal se pone pip install flask flask-login
https://ejecutortecnico.github.io/pri/mision2/loginapi.html

TODO EL CODIGO: https://github.com/ejecutortecnico/loginapi
