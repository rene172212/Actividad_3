[README.md](https://github.com/user-attachments/files/35221572/README.md)
#  Laboratorio: Simulación de Sistemas de Colas Bancarias (M/M/1)
## Solución al Problema Bancario — Banco de Colombia

![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)
![Simulación](https://img.shields.io/badge/Simulación-Eventos_Discretos_%2F_Colas_M%2FM%2F1-green.svg)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)
![IU Digital](https://img.shields.io/badge/IU_Digital-Simulación_M3-purple.svg)
![Estado](https://img.shields.io/badge/Validado-Con_Guía_y_Enunciado-success.svg)

---

## Equipo de Trabajo

### Autores:
* **Sebastian Garces Cadavid**
* **Sofia Miranda Cubides**
* **Oscar Rene Peñaloza Ardila**
* **Andres Mauricio Quintero Rios**

### Tutor:
* **Julián Andrés Loaiza López**

---

##  Resumen Ejecutivo (En Palabras Sencillas)

En el **Banco de Colombia** no tiene cajeros automáticos. Todo el público debe ser atendido en ventanilla por **3 cajeros humanos** durante una jornada continua de **8 horas**.

El banco desea resolver dos inquietudes gerenciales:
1. **¿Debe asignar una caja exclusiva para retiros y dos para pagos (1R + 2P), o dos para retiros y una para pagos (2R + 1P)?**
2. **¿Es necesario crear o habilitar un cuarto cajero para evitar demoras?**

###  ¿Qué demostraron las 10 réplicas del modelo de simulación?
* **NO contrates a un 4to cajero:** Con los 3 actuales, los clientes esperan en promedio solo **2.56 minutos en fila** y los cajeros pasan el **55.5 % del tiempo desocupados** ($\rho = 44.5\%$). Un 4to cajero costaría un 33.3 % más en nómina para una ganancia imperceptible.
* **Cajas exclusivas:** Entre las dos opciones consultadas por el banco, **2 cajas de retiros y 1 de pagos (2R + 1P)** es notablemente mejor que 1R + 2P, ya que beneficia con atención ágil ($W_q = 1.21\text{ min}$) al 70 % de los clientes.
* **La recomendación óptima definitiva:** **Mantener 3 cajas mixtas con una sola fila general**. Quien esté libre atiende al siguiente sin importar la operación, logrando la menor espera global (**2.56 minutos**) gracias al principio de consolidación (*pooling*).

---

##  1. Enunciado y Datos de Entrada del Sistema

### Condiciones Generales:
* **3 cajas disponibles** para retiros y pagos, modeladas como **sistemas $M/M/1$ independientes**.
* **Atención inmediata** (velocidad de atención igual para todos) y **tiempo de desplazamiento despreciable**.
* **Jornada:** 8 horas al día ($480\text{ minutos}$ continuos), asumiendo días homogéneos.
* **Distribución de la demanda:** $70\%$ de usuarios realizan **retiros** y el $30\%$ restante realizan **consignaciones o pagos**.
* **Réplicas:** 10 corridas independientes del modelo.

### Tabla 1. Tiempos de Servicio y Llegada (Distribución Exponencial)
| Tipo de Acción | Tipo de Usuario | Exponencial de Uso del Servicio (Media) | Exponencial de Media de Llegada |
| :--- | :--- | :---: | :---: |
| **Retiro (70 %)** | Rápido | 1 minuto | 1 minuto |
| **Retiro (70 %)** | Normal | 2 minutos | 2 minutos |
| **Retiro (70 %)** | Lento | 3 minutos | 3 minutos |
| **Retiro (70 %)** | Muy lento | 4 minutos | 3 minutos |
| **Consignación o pago (30 %)** | Rápido | 3 minutos | 1 minuto |
| **Consignación o pago (30 %)** | Normal | 3 minutos | 2 minutos |
| **Consignación o pago (30 %)** | Lento | 5 minutos | 3 minutos |
| **Consignación o pago (30 %)** | Muy lento | 7 minutos | 4 minutos |

### Tabla 2. Probabilidades por Tipo de Usuario
| Tipo de Acción | Tipo de Usuario | Probabilidad ($P(U \mid \text{Acción})$) | Porcentaje |
| :--- | :--- | :---: | :---: |
| **Retiro** | Rápido | 0,23 | 23 % |
| **Retiro** | Normal | 0,40 | 40 % |
| **Retiro** | Lento | 0,17 | 17 % |
| **Retiro** | Muy lento | 0,20 | 20 % |
| **Consignación o pago** | Rápido | 0,10 | 10 % |
| **Consignación o pago** | Normal | 0,20 | 20 % |
| **Consignación o pago** | Lento | 0,30 | 30 % |
| **Consignación o pago** | Muy lento | 0,40 | 40 % |

---

##  2. Resolución Paso a Paso de los Puntos Solicitados

---

###  Punto 1: Cajero con Menor y Mayor Tiempo Promedio de Atención
> *"Calcula las estadísticas necesarias para identificar el cajero con menor y mayor tiempo promedio de atención (no es necesario segregar los usuarios)."*

A partir de 10 réplicas de 8 horas en el esquema base de 3 cajas mixtas ($1.998$ clientes acumulados), se obtuvieron las siguientes estadísticas descriptivas:

| Servidor | Clientes Prom./Día | Tiempo Promedio de Atención ($\text{min}$) | Desv. Estándar ($\text{min}$) | IC 95 % Atención ($\text{min}$) | Tiempo en Fila $W_q$ ($\text{min}$) | Utilización $\rho$ (%) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Cajero 1 (Menor)** | 67,2 | **3,12** (3 min 7 s) | 0,24 | $[2,95 ; 3,29]$ | 2,41 | 43,7 % |
| **Cajero 2** | 66,5 | **3,21** (3 min 12 s) | 0,27 | $[3,02 ; 3,40]$ | 2,58 | 44,5 % |
| **Cajero 3 (Mayor)** | 66,1 | **3,28** (3 min 16 s) | 0,29 | $[3,07 ; 3,49]$ | 2,74 | 45,2 % |
| **Promedio Global** | **199,8** | **3,20** | **0,27** | **$[3,01 ; 3,39]$** | **2,58** | **44,5 %** |

* **Cajero con MENOR tiempo de atención:** **Cajero 1** con **$3,12\text{ minutos}$**.
* **Cajero con MAYOR tiempo de atención:** **Cajero 3** con **$3,28\text{ minutos}$**.
* **Análisis Estadístico:** La brecha entre ambos es de apenas **$0,16\text{ minutos}$ (9,6 segundos)**. Como los intervalos de confianza al 95 % se superponen ampliamente, se concluye que **los tres cajeros rinden de forma idéntica** y la pequeña diferencia es fruto de la aleatoriedad intrínseca del muestreo (en qué momento le tocó un cliente lento a cada caja).

---

###  Punto 2: Promedio de Usuarios de Cada Tipo en la Totalidad de Cajeros
> *"Establece el promedio de usuarios de cada tipo en la totalidad de cajeros."*

El banco atendió una media diaria de **$199,8\text{ usuarios/día}$** (frente a los $200,2$ teóricos esperados):

| Tipo de Acción | Perfil de Usuario | Promedio Usuarios/Día | Desv. Estándar | Teórico Esperado | Participación (%) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Retiro** | Rápido | 32,1 | 2,60 | 32,2 | 16,1 % |
| **Retiro** | Normal | 56,6 | 3,53 | 56,0 | 28,3 % |
| **Retiro** | Lento | 23,7 | 2,26 | 23,8 | 11,9 % |
| **Retiro** | Muy lento | 27,8 | 2,25 | 28,0 | 13,9 % |
| **Subtotal Retiros** | — | **140,2** | **10,36** | **140,1** | **70,2 %** |
| **Consignación/Pago** | Rápido | 6,0 | 1,15 | 6,0 | 3,0 % |
| **Consignación/Pago** | Normal | 11,8 | 1,55 | 12,0 | 5,9 % |
| **Consignación/Pago** | Lento | 18,0 | 1,83 | 18,0 | 9,0 % |
| **Consignación/Pago** | Muy lento | 23,8 | 2,25 | 24,0 | 11,9 % |
| **Subtotal Pagos** | — | **59,6** | **6,04** | **60,1** | **29,8 %** |
| **Total Consolidado** | — | **199,8** | **15,53** | **200,2** | **100,0 %** |

#### Consolidado por velocidad (sin distinguir transacción):
1. **Normal:** **$68,4\text{ usuarios/día}$ ($34,2\%$)** $\rightarrow$ El perfil predominante.
2. **Muy lento:** **$51,6\text{ usuarios/día}$ ($25,8\%$)**.
3. **Lento:** **$41,7\text{ usuarios/día}$ ($20,9\%$)**.
4. **Rápido:** **$38,1\text{ usuarios/día}$ ($19,1\%$)**.
* El usuario más común en la sucursal es el **Normal de retiro** ($56,6$ diarios), y el menos frecuente es el **Rápido de pago** ($6,0$ diarios).

---

###  Punto 3: Total de Usuarios por Réplica y Modelo con Menor Cantidad
> *"Determina el total de usuarios de cada tipo en cada una de las réplicas y detalla el modelo con menor cantidad de usuarios por tipo."*

Matriz completa de clientes atendidos en las 10 réplicas independientes:

| Réplica | R-Ráp | R-Nor | R-Len | R-MLen | Subtotal Retiros | P-Ráp | P-Nor | P-Len | P-MLen | Subtotal Pagos | Total Día |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Réplica 1** | 31 | 58 | 24 | 27 | 140 | 6 | 11 | 19 | 25 | 61 | **201** |
| **Réplica 2** | 35 | 61 | 26 | 30 | 152 | 7 | 13 | 17 | 23 | 60 | **212** |
| **Réplica 3\*** | **28** | **51** | **20** | **24** | **123** | 5 | 10 | **15** | 21 | 51 | **174** |
| **Réplica 4** | 33 | 55 | 25 | 29 | 142 | 6 | 14 | 20 | 26 | 66 | **208** |
| **Réplica 5** | 30 | 54 | 22 | 26 | 132 | **4** | 11 | 18 | 22 | 55 | **187** |
| **Réplica 6** | 36 | 62 | 27 | 31 | 156 | 8 | 12 | 21 | 27 | 68 | **224** |
| **Réplica 7** | 29 | 53 | 21 | 25 | 128 | 5 | **9** | 16 | **20** | **50** | **178** |
| **Réplica 8** | 34 | 57 | 23 | 28 | 142 | 7 | 13 | 19 | 25 | 64 | **206** |
| **Réplica 9** | 32 | 59 | 25 | 30 | 146 | 6 | 12 | 17 | 24 | 59 | **205** |
| **Réplica 10** | 33 | 56 | 24 | 28 | 141 | 6 | 13 | 18 | 25 | 62 | **203** |
| **Promedio** | **32,1** | **56,6** | **23,7** | **27,8** | **140,2** | **6,0** | **11,8** | **18,0** | **23,8** | **59,6** | **199,8** |
| **Mínimo** | **28 (R3)** | **51 (R3)** | **20 (R3)** | **24 (R3)** | **123 (R3)** | **4 (R5)** | **9 (R7)** | **15 (R3)** | **20 (R7)** | **50 (R7)** | **174 (R3)** |

#### Detalle del modelo con menor cantidad por tipo:
* **Modelo con menor afluencia global:** La **Réplica 3** con solo **174 usuarios**. Este modelo concentró además el menor volumen en todas las categorías de retiros (28 Rápidos, 51 Normales, 20 Lentos y 24 Muy lentos) y en *Pago Lento* (15).
* **Mínimos en pagos:** La **Réplica 7** registró el menor subtotal de pagos (50) y los mínimos en *Pago Normal* (9) y *Pago Muy lento* (20). El mínimo absoluto en *Pago Rápido* se dio en la **Réplica 5** (4 usuarios).

---

###  Punto 4: Evaluación de la Necesidad de un Nuevo Cajero
> *"Define si es necesario crear un nuevo cajero utilizando los tiempos promedio de espera en todos los criterios del modelo."*

| Indicador de Desempeño | Escenario Base (3 Cajas Mixtas) | Escenario Expansión (4 Cajas Mixtas) | Impacto de Agregar 1 Cajero |
| :--- | :---: | :---: | :--- |
| **Tiempo de espera en cola ($W_q$)** | **2,56 min** | 1,60 min | Reducción marginal de ~57 segundos. |
| **Tiempo total en el banco ($W$)** | **5,76 min** | 4,80 min | Diferencia imperceptible para el usuario. |
| **Longitud media de cola ($L_q$)** | **0,36 clientes** | 0,17 clientes | La fila ya es prácticamente inexistente. |
| **Factor de utilización ($\rho$)** | **44,5 %** | **33,3 %** | Servidores desocupados el 66.7 % del día. |
| **Costos operativos / Nómina** | Línea Base | **+33,3 %** | Aumento fuerte en costos sin retorno. |

#### Veredicto:
> **NO ES NECESARIO CREAR UN NUEVO CAJERO.**
> Un tiempo de espera de $2,56\text{ minutos}$ y una fila promedio de $0,36\text{ clientes}$ reflejan un nivel de servicio sobresaliente. Añadir un 4to cajero aumentaría los costos en un tercio para ganar menos de 1 minuto, provocando una ociosidad laboral del 66.7 %, lo cual atenta contra la eficiencia operativa y económica del banco.

---

###  Punto 5: Decisión sobre Cajeros Exclusivos (Pagos vs. Retiros)
> *"Decide cuántos cajeros deben ofrecer atención exclusiva para pagos y cuántos para retiros."*

Se compararon las dos opciones de especialización formuladas por el banco frente a la política mixta:

| Configuración Evaluada | Espera Retiros ($W_{q,\text{ret}}$) | Espera Pagos ($W_{q,\text{pag}}$) | Espera Global ($W_q$) | Cola Total ($L_q$) | Diagnóstico de Operaciones |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Esc. 1: 3 Cajas Mixtas (Base)** | **~2,56 min** | **~2,56 min** | **2,56 min** | **0,36 cli** |  **ÓPTIMO ABSOLUTO:** Fila única y balance total. |
| **Esc. 2: 1 Retiro + 2 Pagos** | **5,04 min**  | 2,51 min | 4,28 min | 1,79 cli |  **Colapso en retiros:** 1 sola caja atiende al 70 % de los clientes. |
| **Esc. 3: 2 Retiros + 1 Pago** | **1,21 min**  | **9,68 min** | 3,75 min | 1,57 cli |  **Mejor exclusiva:** Protege al 70 % del público. |

#### Decisión Concluyente:
1. **Entre las opciones exclusivas:**  
   Se deben asignar **2 cajeros para Retiros y 1 cajero para Pagos** (Escenario 3). Esta configuración produce una menor espera promedio global ($3,75\text{ min}$ vs. $4,28\text{ min}$) y una menor cola total ($1,57$ vs. $1,79$), garantizando un paso ultrarrápido ($1,21\text{ min}$) para la gran mayoría de clientes (70 %).
2. **Recomendación Gerencial Superior:**  
   **Mantener las 3 cajas en modalidad MIXTA**. Las cajas compartidas reducen la espera global a **2,56 minutos** para todo el mundo sin crear cuellos de botella. Si el banco desea señalizar ventanillas como 2R + 1P, debe implementar una **regla de flexibilidad** (que la caja de retiros apoye los pagos si se acumulan más de 3 clientes en cola).

---

##  3. Explicación Intuitiva: El Principio de Fila Única (*Pooling*)

¿Por qué las cajas mixtas siempre le ganan a las exclusivas?
* **En cajas exclusivas (como en los peajes o supermercados antiguos):** Si un cliente de pagos saca 10 facturas difíciles, esa caja se frena y todos los de atrás quedan atrapados mientras las cajas de retiros están desocupadas.
* **En cajas mixtas con fila única:** El primer cajero que se desocupa llama al siguiente cliente de la fila. La capacidad de trabajo nunca se desperdicia y ningún usuario queda bloqueado.

---

##  4. Formulación Matemática de Teoría de Colas ($M/M/1$)

* **Tiempo medio entre llegadas globales:**  
  $$E[A] = 0,70(2,14) + 0,30(3,00) = 2,398\text{ min} \implies \lambda_{\text{total}} = 0,4170\text{ cli/min} \quad (25,02\text{ cli/h})$$
* **Tiempo medio de servicio global:**  
  $$E[S] = 0,70(2,34) + 0,30(5,20) = 3,198\text{ min} \implies \mu_{\text{mix}} = 0,3127\text{ cli/min}$$
* **Carga de trabajo (Erlangs):**  
  $\text{Retiros} = 0,683\text{ Erlangs}$, $\text{Pagos} = 0,651\text{ Erlangs}$, $\text{Carga Total} = 1,334\text{ servidores-equivalentes}$.
* **Fórmulas de desempeño:**
  $$\rho = \frac{\lambda}{\mu} < 1 \quad \text{(Estabilidad)}$$
  $$L_q = \frac{\rho^2}{1 - \rho}, \quad L = \frac{\rho}{1 - \rho}$$
  $$W_q = \frac{L_q}{\lambda} = \frac{\rho}{\mu - \lambda}, \quad W = W_q + \frac{1}{\mu}$$

---

##  5. Instrucciones de Ejecución

Para reproducir la simulación:
```bash
# 1. Instalar dependencias
pip install numpy pandas scipy matplotlib openpyxl

# 2. Abrir el cuaderno de simulación
jupyter notebook Actividad_didáctica_2_M3.ipynb
```
O cargarlo directamente en **Google Colab**.
