# Procesamiento de Datos de Abate de Animales - Estado de Pará

Este proyecto contiene un script en Python diseñado para **limpiar, filtrar y agregar** datos de animales abatidos de archivos Excel, centrándose exclusivamente en la información del estado de **Pará**. El resultado es exportado a un archivo Excel consolidado por año.

---

## Flujo de Trabajo del Algoritmo 🔄

El siguiente diagrama de flujo ilustra los pasos principales y la lógica secuencial de la función `procesar_archivo_excel`:



---

## Funcionalidades Principales ✨

El script realiza las siguientes operaciones clave:

1.  **Lectura Robusta:** Lee el archivo Excel de entrada, saltando las filas de cabecera y pie de página irrelevantes (`header=3`, `skipfooter=1`).
2.  **Preparación de Datos:**
    * **Renombra** la primera columna a `'Unidade da Federação'`.
    * **Rellena** celdas combinadas en la columna de federación (`ffill()`).
    * **Elimina** columnas de inspección (`'Unnamed: 1'` o `'Tipo de inspeção'`).
3.  **Filtrado Geográfico:** Filtra el DataFrame para retener únicamente las filas donde la unidad de federación contenga la palabra **'Pará'**.
4.  **Limpieza Numérica:** Convierte todas las columnas de datos a valores **numéricos**, reemplazando errores con `0`.
5.  **Agregación Anual:**
    * **Extrae** los años (`\d{4}`) de los nombres de las columnas.
    * **Suma** los totales de abate para todas las columnas que pertenecen a un mismo año.
6.  **Exportación Final:** Genera un DataFrame final (`Año`, `Total Abatidos en Pará`) y lo exporta a **`resultados_pará.xlsx`**.
7.  **Manejo de Errores:** Incluye bloques `try/except` para gestionar la ausencia del archivo de entrada (`FileNotFoundError`) y otros errores inesperados.

---

## Requisitos

Para ejecutar este script, necesitas **Python** y las siguientes librerías:

```bash
pip install pandas
