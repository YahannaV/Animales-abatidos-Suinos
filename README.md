# Animales-abatidos-Suinos
El algoritmo procesa datos de animales abatidos en Brasil, filtrando la información del estado de Pará. Limpia, organiza y convierte los datos, calcula los totales anuales y exporta los resultados a un archivo Excel. Incluye un diagrama de flujo elaborado aparte para representar el proceso.

import pandas as pd
import re

def procesar_archivo_excel(ruta_archivo):
    """
    Procesa un archivo Excel con datos de animales abatidos para limpiar,
    filtrar por el estado de Pará y agregar los datos por año.
    Exporta el resultado final a un archivo Excel.
    """
    try:
        # --- Lectura del archivo Excel ---
        df = pd.read_excel(ruta_archivo, header=3, skipfooter=1)

        # Renombrar primera columna
        nombre_columna_original = df.columns[0]
        df.rename(columns={nombre_columna_original: 'Unidade da Federação'}, inplace=True)

        # Rellenar celdas combinadas
        df['Unidade da Federação'] = df['Unidade da Federação'].ffill()

        # Eliminar columna "Tipo de inspeção"
        if 'Unnamed: 1' in df.columns:
            df = df.drop(columns=['Unnamed: 1'])
        elif 'Tipo de inspeção' in df.columns:
            df = df.drop(columns=['Tipo de inspeção'])

        # Filtrar datos para el estado de Pará
        df_para = df[df['Unidade da Federação'].str.contains('Pará', na=False)].copy()

        if df_para.empty:
            print("Error: No se encontraron datos para el estado de 'Pará'.")
            return None

        # Limpiar datos
        columnas_datos = df_para.columns[1:]
        for col in columnas_datos:
            df_para[col] = pd.to_numeric(df_para[col], errors='coerce').fillna(0)

        # Extraer años
        años = sorted(list(set(re.findall(r'(\d{4})', ' '.join(map(str, columnas_datos))))))
        if not años:
            return pd.DataFrame(columns=['Año', 'Total Abatidos en Pará'])

        # Sumar por año
        resultados_anuales = {}
        for año in años:
            columnas_año = [col for col in columnas_datos if str(año) in str(col)]
            total_anual = df_para[columnas_año].sum(axis=1).iloc[0]
            resultados_anuales[año] = total_anual

        # Crear DataFrame final
        df_resultados = pd.DataFrame(
            list(resultados_anuales.items()),
            columns=['Año', 'Total Abatidos en Pará']
        )

        # --- Exportar resultado a Excel ---
        nombre_salida = "resultados_pará.xlsx"
        df_resultados.to_excel(nombre_salida, index=False)
        print(f"\n✅ Resultado exportado correctamente a '{nombre_salida}'")

        return df_resultados

    except FileNotFoundError:
        print(f"Error: No se encontró el archivo '{ruta_archivo}'.")
        return None
    except Exception as e:
        print(f"Ocurrió un error inesperado: {e}")
        return None


if __name__ == "__main__":
    # Coloca el nombre de tu archivo original aquí
    nombre_archivo_excel = "tabela1093.xlsx"
    resultados_para = procesar_archivo_excel(nombre_archivo_excel)

    if resultados_para is not None and not resultados_para.empty:
        print("\nTotales anuales de animales abatidos en el estado de Pará:")
        print(resultados_para.to_string(index=False))
    else:
        print("\nEl resultado está vacío o ocurrió un error.")

