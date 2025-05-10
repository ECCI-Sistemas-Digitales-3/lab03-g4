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

Esta función verifica si la ventana de la figura sigue abierta. Devuelve `True` si la figura aún existe y `False` si el usuario la ha cerrado. Se usa para detener el ciclo principal cuando el gráfico se cierra manualmente.

---

### 2. ¿Por qué se usa `time.sleep(self.intervalo)` y qué pasa si se quita?

`time.sleep(self.intervalo)` pausa la ejecución del ciclo por el intervalo de tiempo definido (por defecto, 0.5 segundos). Esto:

- Reduce la carga del CPU.
- Evita que se tomen datos demasiado rápidamente, lo que podría dificultar la visualización.

Si se quita, el ciclo se ejecutaría lo más rápido posible, generando:

- Demasiadas lecturas por segundo.
- Alto uso de CPU.
- Gráficas muy densas y poco útiles.

---

### 3. ¿Qué ventaja tiene usar `__init__` para inicializar listas y variables?

El método `__init__` define el estado inicial del objeto:

- Se asegura que cada instancia de la clase tenga sus propias variables (`tiempos`, `temperaturas`, etc.).
- Facilita la configuración inicial.
- Permite reutilizar el código creando múltiples instancias con diferentes parámetros (`duracion_max`, `intervalo`, etc.).

---

### 4. ¿Qué se está midiendo con `self.inicio = time.time()`?

Se guarda el **tiempo absoluto del inicio del monitoreo**. Esto permite calcular el **tiempo transcurrido** en segundos desde que comenzó el script usando:

```python
ahora = time.time() - self.inicio




[//]: # (Referencias)

[Alejo]: <https://github.com/Alejibiris>
[Daniel]: <https://github.com/D4N1EL-R4M1R3Z>
[Sebas]: <https://github.com/SebasMtz30>
