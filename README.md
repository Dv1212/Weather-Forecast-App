# Weather-Forecast-App
Created a weather forecast gui on PyCharm using tkinter library of python.



import tkinter as tk
from tkinter import ttk, messagebox
import requests
from datetime import datetime


class WeatherApp:
    def __init__(self):
        self.window = tk.Tk()
        self.window.title('Weather App')
        self.window.geometry('500x600')
        self.window.config(bg='#87CEEB')  # Sky blue background

        
        self.api_key = "###########"  # Replace with your OpenWeatherMap API key
        

        # Create GUI elements
        self.create_gui()

    def create_gui(self):
        # Title
        title = tk.Label(
            self.window,
            text='Weather Forecast',
            font=('Arial', 20, 'bold'),
            bg='#87CEEB',
            fg='white'
        )
        title.pack(pady=10)

        # Frame for search
        search_frame = tk.Frame(self.window, bg='#87CEEB')
        search_frame.pack(pady=10)

        # Search box
        self.city_entry = tk.Entry(
            search_frame,
            font=('Arial', 12),
            width=25
        )
        self.city_entry.pack(side='left', padx=5)

        # Search button
        search_button = tk.Button(
            search_frame,
            text='Search',
            font=('Arial', 12),
            command=self.get_weather,
            bg='#4169E1',
            fg='white',
            cursor='hand2'
        )
        search_button.pack(side='left')

        # Frame for weather info
        self.result_frame = tk.Frame(self.window, bg='#87CEEB')
        self.result_frame.pack(pady=20, padx=20, fill='both', expand=True)

        # Labels for weather information
        self.city_label = tk.Label(
            self.result_frame,
            font=('Arial', 16, 'bold'),
            bg='#87CEEB'
        )
        self.city_label.pack(pady=5)

        self.temp_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.temp_label.pack(pady=5)

        self.weather_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.weather_label.pack(pady=5)

        self.humidity_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.humidity_label.pack(pady=5)

        self.wind_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.wind_label.pack(pady=5)

        self.pressure_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.pressure_label.pack(pady=5)

        self.description_label = tk.Label(
            self.result_frame,
            font=('Arial', 14),
            bg='#87CEEB'
        )
        self.description_label.pack(pady=5)

    def get_weather(self):
        city = self.city_entry.get().strip()

        if not city:
            messagebox.showerror('Error', 'Please enter a city name')
            return

        try:
            # API call
            url = f'http://api.openweathermap.org/data/2.5/weather?q={city}&appid={self.api_key}&units=metric'
            response = requests.get(url)

            if response.status_code == 401:
                messagebox.showerror('Error', 'Invalid API key. Please check your API key.')
                return

            if response.status_code != 200:
                messagebox.showerror('Error', 'City not found')
                return

            # Parse the response
            weather_data = response.json()

            # Update GUI with weather information
            self.update_gui(weather_data)

        except requests.ConnectionError:
            messagebox.showerror('Error', 'No internet connection')
        except Exception as e:
            messagebox.showerror('Error', f'An error occurred: {str(e)}')

    def update_gui(self, data):
        # Extract data
        temp = data['main']['temp']
        humidity = data['main']['humidity']
        pressure = data['main']['pressure']
        wind_speed = data['wind']['speed']
        description = data['weather'][0]['description']
        city = data['name']
        country = data['sys']['country']

        # Update labels
        self.city_label.config(text=f'📍 {city}, {country}')
        self.temp_label.config(text=f'🌡️ Temperature: {temp}°C ({self.celsius_to_fahrenheit(temp)}°F)')
        self.humidity_label.config(text=f'💧 Humidity: {humidity}%')
        self.pressure_label.config(text=f'⏲️ Pressure: {pressure} hPa')
        self.wind_label.config(text=f'💨 Wind Speed: {wind_speed} m/s')
        self.description_label.config(text=f'☁️ Condition: {description.capitalize()}')

    def celsius_to_fahrenheit(self, celsius):
        return round((celsius * 9 / 5) + 32, 2)

    def run(self):
        self.window.mainloop()



if __name__ == "__main__":
    app = WeatherApp()
    app.run()
