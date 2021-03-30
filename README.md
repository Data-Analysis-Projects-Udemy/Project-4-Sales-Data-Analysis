# Project 4 Sales Data Analysis

# a.- Preparar los datos
2
# b.- Analizando las ventas mensuales
# 3. ¿Cuál es el mejor mes para la venta?


1. [Enlaces ](#schema1)

<hr>

<a name="schema1"></a>

# 1. Combine 12 meses de datos de ventas en un solo archivo csv
Imprimimos el nombre de los datos que tenemos en la carpeta `data`.
~~~python
files =[file for file in os.listdir("./data")]
for file in files:
    print(file)
~~~
![img](./images/001.png)

Creamos un dataframe con todos los archivos
~~~python
path = "./data"

#blank dataframe
all_data = pd.DataFrame()

for file in files:
    current_df = pd.read_csv(path+"/"+file)
    all_data = pd.concat([all_data, current_df])
    
~~~
Y obtenemos uno datos totales `(186850, 6)`

Guardamos los datos en un csv
~~~python
all_data.to_csv('./data/all_data.csv',index=False)
~~~
<hr>

<a name="schema2"></a>

# 2. Limpieza y formateo de datos
~~~python
all_data.dtypes
all_data.head()
~~~
![img](./images/002.png)

Comprobamos si tiene nulos y si no son muchos los borramos, ya que tenmos un dataframe grande.

~~~python
all_data.isnull().sum()
all_data = all_data.dropna(how='all')
~~~
![img](./images/003.png)

<hr>

<a name="schema3"></a>

# 3. ¿Cuál es el mejor mes para la venta?
Primero vamos a obtener los meses.
Para eso creamos una función y se la aplicamos a los datos y los guardamos como una nueva columna.

~~~python
def month(x):
    return x.split('/')[0]
all_data['Month']=all_data['Order Date'].apply(month)
~~~
![img](./images/004.png)

Ahora los convertimos a enteros
~~~python
all_data['Month'] = all_data['Month'].astype(int)
~~~
Pero da un error porque hay un tipo de datos que no lo podemos convertir a enteros. Vamos ver los valores únicos a ver que dato es.

~~~python
all_data['Month'].unique()
~~~
![img](./images/005.png)

~~~python
filter=all_data['Month']=='Order Date'
all_data=all_data[~filter]
~~~

Después de eliminar todos los datos con mes igual a `order date` ahora si podemos aplicar el cambio de tipo.
![img](./images/006.png)

También cambiamos los tipos de `Price each` y `Quantity Ordered`
~~~python
all_data['Price Each']=all_data['Price Each'].astype(float)
all_data['Quantity Ordered']=all_data['Quantity Ordered'].astype(int)
~~~

![img](./images/007.png)

Obtenemos el valor de las ventas
~~~python
all_data['sales']=all_data['Quantity Ordered']*all_data['Price Each']
~~~
![img](./images/008.png)

Agrupamos por el mes y obtenemos las ventas
~~~python
all_data.groupby('Month')['sales'].sum()
~~~
![img](./images/009.png)

Lo vemos mejor en un gráfico de barras

~~~python
months=range(1,13)
plt.bar(months,all_data.groupby('Month')['sales'].sum())
plt.xticks(months)
plt.ylabel('Sales in USD ($)')
plt.xlabel('Month number')
plt.show()
plt.savefig("./images/month.png")
~~~
![img](./images/month.png)

Y comprobamos que el mejor mes de ventas es el mes de `diciembre`