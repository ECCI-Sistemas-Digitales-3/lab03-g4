[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=19143881&assignment_repo_type=AssignmentRepo)
# Lab03: Visualización de Datos en Raspberry Pi Zero W

## Integrantes

| Integrantes |
| - |
| [`Diego Lopez`][Alejo] |
| [`Daniel Ramirez`][Daniel]||
| [`Sebastian Martinez`][Sebas]||
||

## Documentación

# Preguntas sobre el script de monitoreo de temperatura en Raspberry Pi

---

### 1. ¿Qué función cumple `plt.fignum_exists(self.fig.number)` en el ciclo principal?

Esta función verifica si la ventana de la figura sigue abierta. Devuelve `True` si la figura aún existe y `False` si el usuario la ha cerrado.  
Se usa como condición para mantener el ciclo principal activo mientras la ventana de la gráfica esté abierta.  
Si el usuario cierra la ventana, el ciclo se detiene limpiamente.

---

### 2. ¿Por qué se usa `time.sleep(self.intervalo)` y qué pasa si se quita?

La instrucción `time.sleep(self.intervalo)` pausa la ejecución del ciclo por el número de segundos indicado por `self.intervalo`.  
Este retardo tiene varias funciones importantes:

- Disminuye el uso de CPU evitando lecturas innecesariamente rápidas.
- Da tiempo suficiente para que el usuario vea la gráfica actualizada.
- Permite que la visualización sea clara y útil.

**Si se elimina:**

- El ciclo se ejecutaría de forma continua y rápida.
- El CPU se sobrecargaría con muchas lecturas por segundo.
- La gráfica se llenaría de puntos de forma muy densa, siendo poco legible.

---

### 3. ¿Qué ventaja tiene usar `__init__` para inicializar listas y variables?

El método `__init__` permite configurar y organizar de forma limpia el estado inicial de un objeto de clase.  
Sus ventajas incluyen:

- Inicializa las variables (`duracion_max`, `intervalo`, listas, figura de Matplotlib, etc.) al momento de crear la instancia.
- Garantiza que cada instancia de la clase tenga sus propios datos.
- Facilita el mantenimiento y la lectura del código.
- Permite reutilizar el código creando nuevos monitores con diferentes configuraciones.

---

### 4. ¿Qué se está midiendo con `self.inicio = time.time()`?

Se está capturando el **tiempo exacto (en segundos desde época UNIX)** en que comenzó el monitoreo.

Este valor sirve como punto de referencia para calcular el **tiempo transcurrido** en cada iteración con:

```python
ahora = time.time() - self.inicio
```

Esto permite mostrar en el eje X del gráfico cuánto tiempo ha pasado desde que empezó el monitoreo.

---

### 5. ¿Qué hace exactamente `subprocess.check_output(...)`?

La función `subprocess.check_output(...)` ejecuta un comando externo del sistema operativo y devuelve su salida como una cadena de texto (tipo `bytes`, que se decodifica a `str`).

En este caso:

```python
subprocess.check_output(["vcgencmd", "measure_temp"])
```

- Ejecuta el comando `vcgencmd measure_temp` que devuelve la temperatura de la CPU en Raspberry Pi.
- Por ejemplo, el resultado puede ser: `"temp=47.2'C"`
- Luego se convierte a `float` para trabajar con el valor numérico.

---

### 6. ¿Por qué se almacena `ahora = time.time() - self.inicio` en lugar del tiempo absoluto?

Guardar el tiempo relativo al inicio (es decir, tiempo transcurrido) en lugar del timestamp absoluto tiene estas ventajas:

- El gráfico es más fácil de entender para el usuario: muestra los segundos desde que comenzó el monitoreo.
- El eje X es claro, ordenado y lineal (0, 1, 2, ...).
- Evita mostrar valores poco legibles como `1704964523.438` (segundos desde 1970).

---

### 7. ¿Por qué se usa `self.ax.clear()` antes de graficar?

Este método borra el contenido actual del área de la gráfica (`self.ax`) antes de dibujar nuevos datos.

Es necesario porque:

- Se reutiliza el mismo gráfico en cada ciclo.
- Sin `clear()`, se sobrepondrían las curvas y se vería desordenado.
- Permite mostrar solo los datos dentro de la ventana de tiempo (`duracion_max`).

---

### 8. ¿Qué captura el bloque `try...except` dentro de `leer_temperatura()`?

El bloque `try...except` se usa para manejar errores que puedan surgir al ejecutar el comando del sistema con `subprocess`.

Captura excepciones como:

- El comando `vcgencmd` no está instalado o disponible.
- Error de permisos.
- La salida no tiene el formato esperado.
- Problemas con el hardware del sensor.

En caso de error, el código:

- Muestra un mensaje de error.
- Retorna `None`, evitando que se agregue un dato inválido a la gráfica.

---

### 9. ¿Cómo podría modificar el script para guardar las temperaturas en un archivo `.csv`?

Puedes modificar la clase para abrir un archivo `.csv`, escribir los encabezados y guardar cada lectura de temperatura y tiempo.

#### Pasos:

1. Importar el módulo `csv`.
2. Abrir un archivo para escritura en `__init__`.
3. Agregar una línea a `actualizar_datos` para guardar los datos.
4. Cerrar el archivo en `finally`.

#### Código modificado:

```python
import csv

class MonitorTemperaturaRPI:
    def __init__(self, duracion_max=60, intervalo=0.5):
        ...
        self.archivo_csv = open("temperaturas.csv", "w", newline='')
        self.writer = csv.writer(self.archivo_csv)
        self.writer.writerow(["Tiempo (s)", "Temperatura (°C)"])

    def actualizar_datos(self):
        ahora = time.time() - self.inicio
        temp = self.leer_temperatura()
        if temp is not None:
            self.tiempos.append(ahora)
            self.temperaturas.append(temp)
            self.writer.writerow([ahora, temp])  # Guardar en CSV

            # Eliminar datos antiguos si exceden duracion_max
            while self.tiempos and self.tiempos[0] < ahora - self.duracion_max:
                self.tiempos.pop(0)
                self.temperaturas.pop(0)

    def ejecutar(self):
        try:
            while plt.fignum_exists(self.fig.number):
                self.actualizar_datos()
                self.graficar()
                time.sleep(self.intervalo)
        finally:
            self.archivo_csv.close()
            plt.ioff()
            plt.close(self.fig)
```

Esto generará el archivo llamado `temperaturas.csv` con dos columnas: **Tiempo (s)** y **Temperatura (°C)**.

---




[//]: # (Referencias)

[Alejo]: <https://github.com/Alejibiris>
[Daniel]: <https://github.com/D4N1EL-R4M1R3Z>
[Sebas]: <https://github.com/SebasMtz30>
