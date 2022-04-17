# Diccionario-de-Alcademy-a-MongoDb
# Actualizacion-en base a la aplicación desarrollada en la Sustitución de SQLite por SQLAlchemy, debe sustituir el uso de bases de datos relacionales por el uso de base de datos no relacionales, específicamente MongoDB
import pymongo
from pymongo import MongoClient
from pymongo import ASCENDING
from pprint import pprint

client = MongoClient("localhost")

db = client["Diccionario"]

collection = db["Diccionario_Panameño"]

def agregar_palabra(palabra, significado):
	collection.insert_one({
		"Palabra":palabra,
		"Significado":significado
		})

def editar_palabra(palabra, nuevo_significado):

  collection.update_one({
      "Palabra":palabra
      },
      {"$set":{
      "Significado":nuevo_significado
      }
      })

def eliminar_palabra(palabra):
    collection.delete_one({
        "Palabra":palabra
        })

def obtener_palabras():
	s=collection.distinct("Palabra")
	for i in s:
		print(i)


def buscar_significado_palabra(palabra):
    cursor = collection.find({"Palabra": palabra},{"Significado":1, "_id":0})
    for document in cursor: 
        pprint(document)


def principal():
    menu = """
1) Agregar nueva palabra
2) Editar palabra existente
3) Eliminar palabra existente
4) Ver listado de palabras
5) Buscar significado de palabra
6) Salir
Elige: """
    eleccion = ""
    while eleccion != "6":
        eleccion = input(menu)
        if eleccion == "1":
            palabra = input("Ingresa la palabra: ")
            # Comprobar si no existe
            posible_significado = buscar_significado_palabra(palabra)
            if posible_significado:
                print(f"La palabra '{palabra}' ya existe")
            else:
                significado = input("Ingresa el significado: ")
                agregar_palabra(palabra, significado)
                print("Palabra agregada")
        if eleccion == "2":
            palabra = input("Ingresa la palabra que quieres editar: ")
            nuevo_significado = input("Ingresa el nuevo significado: ")
            editar_palabra(palabra, nuevo_significado)
            print("Palabra actualizada")
        if eleccion == "3":
            palabra = input("Ingresa la palabra a eliminar: ")
            eliminar_palabra(palabra)
        if eleccion == "4":
            print("=== Lista de palabras ===")
            palabras = obtener_palabras()
        if eleccion == "5":
            palabra = input(
                "Ingresa la palabra de la cual quieres saber el significado: ")
            significado = buscar_significado_palabra(palabra)


if __name__ == '__main__':
    principal()
