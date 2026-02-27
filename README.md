1️⃣ CARGA DE DATOS
📌 Leer CSV
import pandas as pd

df = pd.read_csv("archivo.csv")

¿Qué hace?

Lee el archivo

Lo convierte en un DataFrame

Detecta automáticamente tipos de datos

📌 Opciones importantes
df = pd.read_csv(
    "archivo.csv",
    sep=",",          # Separador
    encoding="utf-8", # Codificación
    dtype=str         # Fuerza todo a texto
)

¿Por qué usar dtype=str?

En exámenes es común que:

Números tengan letras

Haya valores mixtos

Haya formatos incorrectos

Forzar a string evita errores de tipo.

🔹 2️⃣ EXPLORACIÓN INICIAL
Ver estructura
df.info()


Muestra:

Número de filas

Número de columnas

Tipos de datos

Cuántos valores no nulos hay

Ver nulos
df.isna().sum()


Esto es CLAVE en examen.

Te dice qué columnas necesitan limpieza.

🔹 3️⃣ LIMPIEZA DE DATOS (como tu script)
📌 Convertir a número seguro
df["numero_ext"] = pd.to_numeric(
    df["numero_ext"],
    errors="coerce"
)

¿Qué hace?

Convierte a número

Si no puede → lo vuelve NaN

📌 Reemplazar nulos
df["numero_ext"] = df["numero_ext"].fillna(0)

📌 Reglas condicionales (MUY IMPORTANTE EN EXAMEN)
df.loc[df["numero_ext"] == 0, "letra_ext"] = "SN"

¿Cómo funciona?

df.loc[condición, columna] = valor

Busca las filas donde la condición sea verdadera

Cambia solo esa columna

No afecta el resto

Ejemplo más complejo:

df.loc[
    (df["numero_ext"] != 0) & (df["letra_ext"].isna()),
    "letra_ext"
] = "NA"

Aquí está pasando:

df["numero_ext"] != 0

df["letra_ext"].isna()

Se combinan con &

Solo si ambas se cumplen → cambia el valor

🔹 4️⃣ LIMPIEZA MASIVA POR COLUMNAS

Como en tu script:

campos_cp = ["d_asenta", "d_estado"]

for campo in campos_cp:
    if campo in df.columns:
        df.loc[df[campo].isna(), campo] = "Desconocido"

¿Por qué es útil?

En exámenes:

Puede que una columna no exista

Evita error KeyError

Hace el código más profesional

🔹 5️⃣ TRANSFORMACIÓN DE TEXTO
Convertir a mayúsculas
df["nom_vial"] = df["nom_vial"].str.upper()


.str permite usar funciones de texto.

Quitar espacios
df["nom_vial"] = df["nom_vial"].str.strip()

🔹 6️⃣ GROUPBY (EXPLICACIÓN PROFUNDA)
🧠 ¿Qué es groupby?

groupby() sirve para:

Agrupar filas que comparten el mismo valor y aplicar una operación matemática sobre ellas.

Es equivalente a:

GROUP BY

🔎 Ejemplo simple
df.groupby("estado")["ventas"].sum()

Paso a paso:

Agrupa todas las filas por estado

Toma la columna ventas

Suma las ventas por cada estado

Resultado conceptual:

estado	ventas_total
CDMX	5000
Jalisco	3000
🔎 Ejemplo más claro

Si tuvieras:

estado	ventas
CDMX	100
CDMX	200
Jalisco	300

Y haces:

df.groupby("estado")["ventas"].sum()


Obtienes:

estado	ventas
CDMX	300
Jalisco	300
🔹 Operaciones comunes en groupby
Suma
df.groupby("estado")["ventas"].sum()

Promedio
df.groupby("estado")["ventas"].mean()

Conteo
df.groupby("estado")["ventas"].count()

Máximo
df.groupby("estado")["ventas"].max()

🔹 Múltiples agregaciones
df.groupby("estado")["ventas"].agg(["sum", "mean", "max"])


Resultado:

estado	sum	mean	max
🔹 Agrupar por múltiples columnas
df.groupby(["estado", "anio"])["ventas"].sum()


Esto crea grupos combinados:

CDMX - 2023

CDMX - 2024

Jalisco - 2023

🔹 Resetear índice después de groupby

Muchas veces el resultado queda con índice agrupado:

resultado = df.groupby("estado")["ventas"].sum().reset_index()


reset_index() lo vuelve DataFrame normal.

🔹 Filtrar después de groupby
ventas_estado = df.groupby("estado")["ventas"].sum().reset_index()

ventas_estado[ventas_estado["ventas"] > 1000]

🔹 Groupby con varias columnas agregadas
df.groupby("estado").agg({
    "ventas": "sum",
    "clientes": "mean"
})

🔹 7️⃣ ORDENAR RESULTADOS
df.sort_values("ventas", ascending=False)

🔹 8️⃣ EXPORTAR RESULTADO FINAL
CSV
df.to_csv("salida.csv", index=False)

Parquet (mejor para analítica)
df.to_parquet("salida.parquet", engine="pyarrow")

🔹 9️⃣ Flujo típico de examen
import pandas as pd

# 1. Cargar
df = pd.read_csv("datos.csv", dtype=str)

# 2. Explorar
df.info()

# 3. Convertir tipos
df["numero"] = pd.to_numeric(df["numero"], errors="coerce")

# 4. Limpiar nulos
df["numero"] = df["numero"].fillna(0)

# 5. Aplicar reglas
df.loc[df["numero"] == 0, "estatus"] = "SIN_NUMERO"

# 6. Agrupar
resumen = df.groupby("estado")["ventas"].sum().reset_index()

# 7. Guardar
resumen.to_csv("resultado.csv", index=False)
