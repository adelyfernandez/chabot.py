# chabot.py
import tkinter as tk
import requests
import json
import nltk
from nltk.stem import WordNetLemmatizer

nltk.download('punkt')
nltk.download('wordnet')

# Configuración de la API del clima
api_key = "your_api_key_here"
base_url = "http://api.openweathermap.org/data/2.5/weather?"

# Inicializar lematizador
lemmatizer = WordNetLemmatizer()

# Función para procesar la entrada del usuario
def process_input(input):
    # Tokenizar y lematizar la entrada del usuario
    tokens = nltk.word_tokenize(input)
    lemmatized_tokens = [lemmatizer.lemmatize(token.lower()) for token in tokens]

    # Buscar palabras clave en la entrada del usuario
    if 'weather' in lemmatized_tokens and ('today' in lemmatized_tokens or 'now' in lemmatized_tokens):
        return get_weather()
    else:
        return "Lo siento, no entiendo lo que dices."

# Función para obtener la información del clima
def get_weather():
    city_name = "San Francisco"
    complete_url = base_url + "appid=" + api_key + "&q=" + city_name
    response = requests.get(complete_url)
    data = response.json()

    if data['cod'] != '404':
        temperature = int(data['main']['temp'] - 273.15)
        weather_description = data['weather'][0]['description']
        return f"La temperatura en {city_name} es de {temperature} grados Celsius y el tiempo está {weather_description}."
    else:
        return "No se pudo obtener la información del clima."

# Configurar la ventana del chatbot
window = tk.Tk()
window.title("Chatbot")
window.geometry("400x400")

# Configurar la entrada del usuario
input_label = tk.Label(window, text="Tú: ")
input_label.pack(side=tk.LEFT)
input_entry = tk.Entry(window, width=30)
input_entry.pack(side=tk.LEFT)

# Configurar la salida del chatbot
output_text = tk.Text(window, height=20, width=50)
output_text.pack(side=tk.BOTTOM)

# Función para manejar la entrada del usuario
def handle_input():
    input = input_entry.get()
    output = process_input(input)
    output_text.insert(tk.END, "Chatbot: " + output + "\n")
    input_entry.delete(0, tk.END)

# Configurar el botón de enviar
send_button = tk.Button(window, text="Enviar", command=handle_input)
send_button.pack(side=tk.RIGHT)

# Iniciar la ventana del chatbot
window.mainloop()
