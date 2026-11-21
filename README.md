[README.md](https://github.com/user-attachments/files/35221008/README.md)
# 🏦 Laboratorio: Simulación de Sistemas de Colas Bancarias (M/M/1)
## Análisis de Desempeño y Optimización de Cajeros — Banco de Colombia

![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)
![Simulación](https://img.shields.io/badge/Simulación-Eventos_Discretos_%2F_Colas_M%2FM%2F1-green.svg)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)
![IU Digital](https://img.shields.io/badge/IU_Digital-Simulación_M3-purple.svg)
![Estado](https://img.shields.io/badge/Validado-Con_Informe_Oficial-success.svg)

## autores

### Sebastian Garces Cadavid
### Sofia Miranda Cubides
### Oscar Rene Peñaloza Ardila
### Andres Mauricio Quintero Rios
 
## tutor

### Julián Andrés Loaiza López

---

## 📋 1. Introducción y Contexto del Problema

La gestión eficiente de líneas de espera constituye un factor determinante en la calidad del servicio dentro del sector financiero. El **Banco de Colombia**, al no disponer de cajeros electrónicos en la sucursal analizada, depende exclusivamente de sus **tres cajas presenciales** para atender la demanda diaria de operaciones de **retiros** y **consignaciones o pagos** durante una jornada continua de **8 horas (480 minutos)**.

Ante el propósito de garantizar una atención ágil y minimizar las demoras de los usuarios, la entidad requiere evaluar cuantitativamente el desempeño actual de sus cajeros y responder a dos inquietudes operativas clave:
1. **¿Conviene especializar las cajas asignando una exclusiva para retiros y dos para pagos (1R + 2P), o dos para retiros y una para pagos (2R + 1P)?**
2. **¿Existe la necesidad operativa de incorporar un cuarto cajero para satisfacer la demanda?**

Este repositorio contiene el modelado estocástico, la simulación de eventos discretos ($M/M/1$) ejecutada a lo largo de **10 réplicas independientes de 8 horas**, y el análisis estadístico y de teoría de colas que sustenta las decisiones gerenciales recomendadas en el **Informe Oficial del Caso**.

---

## 🔬 2. Metodología y Formulación Matemática

### 2.1 Supuestos y Conceptualización del Sistema
De acuerdo con la notación de Kendall, cada cajero del banco opera como un sistema **$M/M/1/\infty/\infty/\text{FIFO}$**:
* **Llegadas:** Proceso de Poisson con tiempos entre llegadas exponencialmente distribuidos ($\lambda$).
* **Servicio:** Tiempos de atención con distribución exponencial ($\mu$).
* **Servidores:** 3 cajeros con igual capacidad de atención inmediata y tiempos de desplazamiento despreciables.
* **Composición de la Demanda:** $70\%$ de los clientes realizan **Retiros** y el $30\%$ realizan **Consignaciones o Pagos**.
* **Horizonte:** Jornada laboral de 480 minutos continuos (8 horas). Se ejecutaron **10 réplicas estocásticas independientes** con semillas aleatorias distintas para estimar promedios, desviaciones estándar e intervalos de confianza al 95%.

### 2.2 Parámetros de Entrada del Sistema

#### Tabla 1. Tiempos medios exponenciales de servicio y llegada, y probabilidades por perfil de usuario
| Tipo de Acción | Tipo de Usuario | Media de Servicio ($\text{min}$) | Media de Llegada ($\text{min}$) | Probabilidad Condicional | Probabilidad Conjunta |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Retiro (70 %)** | Rápido | 1,00 | 1,00 | 0,23 | 0,161 |
| **Retiro (70 %)** | Normal | 2,00 | 2,00 | 0,40 | 0,280 |
| **Retiro (70 %)** | Lento | 3,00 | 3,00 | 0,17 | 0,119 |
| **Retiro (70 %)** | Muy lento | 4,00 | 3,00 | 0,20 | 0,140 |
| **Ponderado Retiros** | — | **2,34** | **2,14** | **1,00** | **0,700** |
| **Consignación/Pago (30 %)** | Rápido | 3,00 | 1,00 | 0,10 | 0,030 |
| **Consignación/Pago (30 %)** | Normal | 3,00 | 2,00 | 0,20 | 0,060 |
| **Consignación/Pago (30 %)** | Lento | 5,00 | 3,00 | 0,30 | 0,090 |
| **Consignación/Pago (30 %)** | Muy lento | 7,00 | 4,00 | 0,40 | 0,120 |
| **Ponderado Pagos** | — | **5,20** | **3,00** | **1,00** | **0,300** |
| **Promedio Global Sistema** | — | **3,20** | **2,40** | — | **1,000** |

### 2.3 Formulación Analítica y Criterios de Estabilidad ($M/M/1$)
* **Tiempo medio esperado entre llegadas globales:**  
  $$E[A] = 0,70(2,14) + 0,30(3,00) = 2,398\text{ minutos}$$  
  $$\lambda_{\text{total}} = \frac{1}{2,398} = 0,4170\text{ clientes/min} \quad (25,02\text{ clientes/hora} \approx 200,2\text{ usuarios/día en 8 h})$$
* **Tiempo medio de servicio global:**  
  $$E[S] = 0,70(2,34) + 0,30(5,20) = 3,198\text{ minutos} \quad (\mu_{\text{mix}} = 0,3127\text{ clientes/min})$$
* **Carga de trabajo del sistema (Erlangs):**  
  * Retiros: $\lambda_{\text{ret}} \cdot E[S_{\text{ret}}] = (0,70 \times 0,4170) \times 2,34 = 0,683\text{ Erlangs}$
  * Pagos: $\lambda_{\text{pag}} \cdot E[S_{\text{pag}}] = (0,30 \times 0,4170) \times 5,20 = 0,651\text{ Erlangs}$
  * Carga agregada total: $1,334\text{ servidores-equivalentes}$.
* **Ecuaciones de estado estacionario:**
  * Factor de utilización: $\rho = \frac{\lambda}{\mu} < 1$
  * Probabilidad de sistema vacío: $P_0 = 1 - \rho$
  * Clientes en cola y sistema: $L_q = \frac{\rho^2}{1 - \rho}, \quad L = \frac{\rho}{1 - \rho}$
  * Tiempos en cola y sistema (Ley de Little): $W_q = \frac{\rho}{\mu - \lambda} = \frac{L_q}{\lambda}, \quad W = W_q + \frac{1}{\mu} = \frac{1}{\mu - \lambda}$

---

## 📊 3. Resultados Detallados de la Simulación

A continuación se presentan los resultados consolidados de las 10 réplicas que responden de manera rigurosa a los cinco puntos del laboratorio:

---

### 📍 Punto 1: Cajero con Menor y Mayor Tiempo de Atención
*Evaluado en el Escenario Base de 3 cajas mixtas ($1.998$ clientes acumulados en las 10 réplicas).*

#### Tabla 2. Desempeño comparativo por cajero en el escenario base (10 réplicas de 8 horas)
| Cajero | Usuarios prom./día | Tiempo de atención prom. ($\text{min}$) | Desv. estándar ($\text{min}$) | IC 95 % atención ($\text{min}$) | Espera $W_q$ ($\text{min}$) | Factor de utilización $\rho$ (%) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Cajero 1 (Menor)** | 67,2 | **3,12** | 0,24 | $[2,95 ; 3,29]$ | 2,41 | 43,7 % |
| **Cajero 2** | 66,5 | **3,21** | 0,27 | $[3,02 ; 3,40]$ | 2,58 | 44,5 % |
| **Cajero 3 (Mayor)** | 66,1 | **3,28** | 0,29 | $[3,07 ; 3,49]$ | 2,74 | 45,2 % |
| **Total / Promedio Global** | **199,8** | **3,20** | **0,27** | **$[3,01 ; 3,39]$** | **2,58** | **44,5 %** |

#### Hallazgos Clave:
* **Cajero con menor tiempo promedio de atención:** **Cajero 1** ($3,12\text{ minutos}$).
* **Cajero con mayor tiempo promedio de atención:** **Cajero 3** ($3,28\text{ minutos}$).
* **Análisis Estadístico:** La diferencia entre servidores es mínima ($0,16\text{ minutos} \approx 9,6\text{ segundos}$) y los intervalos de confianza al 95 % se superponen ampliamente. Dado que los tres cajeros operan bajo los mismos parámetros, la leve discrepancia se debe exclusivamente a la variabilidad estocástica muestral (mezcla aleatoria de usuarios lentos/muy lentos en cada corrida) y **no** a ineficiencias estructurales de ningún servidor.

---

### 📍 Punto 2: Promedio de Usuarios de Cada Tipo en la Totalidad de Cajeros
*Distribución diaria promedio consolidada en las 10 réplicas ($199,8\text{ usuarios/día}$ promedio real frente a $200,2$ teórico).*

#### Tabla 3. Promedio diario de usuarios por tipo en la totalidad de cajeros (10 réplicas)
| Tipo de Acción | Tipo de Usuario | Promedio usuarios/día | Desv. estándar | Valor teórico esperado | Participación (%) |
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

#### Consolidado por Perfil (sin distinguir transacción):
* **Rápido:** **38,1 usuarios/día** ($19,1\%$)
* **Normal:** **68,4 usuarios/día** ($34,2\%$)
* **Lento:** **41,7 usuarios/día** ($20,9\%$)
* **Muy lento:** **51,6 usuarios/día** ($25,8\%$)
* **Perfil predominante:** Usuario *Normal de Retiro* ($56,6\text{ diarios}$).
* **Perfil menos frecuente:** Usuario *Rápido de Pago* ($6,0\text{ diarios}$).

---

### 📍 Punto 3: Total de Usuarios por Tipo en Cada Réplica y Modelo con Menor Cantidad
*Conteo exacto por categoría en cada una de las 10 corridas de simulación:*

#### Tabla 4. Total de usuarios por tipo en cada una de las 10 réplicas simuladas
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
| **Mínimo (Réplica)** | **28 (R3)** | **51 (R3)** | **20 (R3)** | **24 (R3)** | **123 (R3)** | **4 (R5)** | **9 (R7)** | **15 (R3)** | **20 (R7)** | **50 (R7)** | **174 (R3)** |

*\* Nota: La Réplica 3 representó el día con menor volumen general.*

#### Detalle del Modelo con Menor Cantidad de Usuarios por Tipo:
* **Menor afluencia global:** **Réplica 3** con solo **174 usuarios**. Concentró los mínimos en las 4 categorías de retiro (28 Rápidos, 51 Normales, 20 Lentos, 24 Muy lentos; subtotal 123) y en *Pago Lento* (15 usuarios).
* **Mínimos en pagos:** La **Réplica 7** registró el menor subtotal de pagos (50 usuarios) y los mínimos en *Pago Normal* (9) y *Pago Muy lento* (20). El mínimo absoluto en *Pago Rápido* ocurrió en la **Réplica 5** (4 usuarios).

---

### 📍 Punto 4: Necesidad de Crear un Nuevo Cajero
*Evaluación de tiempos promedio de espera y estabilidad operativa:*

#### Tabla 5. Comparación de métricas de desempeño y estabilidad en los escenarios evaluados
| Escenario y Configuración | Segmento / Caja | $\lambda$ ($\text{cli/min}$) | $\mu$ ($\text{cli/min}$) | $\rho$ (%) | $W_q$ ($\text{min}$) | $W$ ($\text{min}$) | $L_q$ ($\text{cli}$) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Esc. 1: 3 Mixtas (Base)** | Cada caja ($\times 3$) | 0,1390 | 0,3127 | **44,5 %** | **2,56** | **5,76** | **0,36** |
| **Esc. 2: 1 Retiro + 2 Pagos** | Retiros (1 caja) | 0,2919 | 0,4274 | 68,3 % | 5,04 | 7,38 | 1,47 |
| | Pagos (c/u $\times 2$) | 0,0626 | 0,1923 | 32,5 % | 2,51 | 7,71 | 0,16 |
| *Ponderado Escenario 2* | *70 % Retiro / 30 % Pago* | — | — | **44,5 %** | **4,28** | **7,48** | **1,79 (tot)** |
| **Esc. 3: 2 Retiros + 1 Pago** | Retiros (c/u $\times 2$) | 0,1460 | 0,4274 | 34,2 % | 1,21 | 3,55 | 0,18 |
| | Pagos (1 caja) | 0,1251 | 0,1923 | 65,1 % | 9,68 | 14,88 | 1,21 |
| *Ponderado Escenario 3* | *70 % Retiro / 30 % Pago* | — | — | **44,5 %** | **3,75** | **6,95** | **1,57 (tot)** |
| **Esc. 4A: 4 Cajas Mixtas** | Cada caja ($\times 4$) | 0,1043 | 0,3127 | **33,3 %** | **1,60** | **4,80** | **0,17** |
| **Esc. 4B: 2 Retiros + 2 Pagos**| Ponderado global | 0,4170 | — | **33,3 %** | **1,60** | **4,80** | **0,68 (tot)** |

#### Veredicto Operativo:
> **NO ES NECESARIO CREAR UN NUEVO CAJERO.**
> * **Estabilidad comprobada:** Con los 3 cajeros actuales, el sistema es plenamente estable ($\rho = 44,5\% < 100\%$) y la carga agregada es de solo $1,334$ servidores-equivalentes.
> * **Tiempos moderados:** En la configuración base de 3 cajas mixtas, la espera en cola es de solo **2,56 minutos** y la fila promedio no supera **0,36 clientes** por caja.
> * **Inconsistencia económica:** Contratar un 4to cajero reduciría la espera marginalmente en menos de 1 minuto ($1,60\text{ min}$), pero reduciría la utilización al **33,3 %** (dejando a los empleados ociosos dos terceras partes de la jornada) e incrementaría los costos laborales en un $33,3\%$, violando el principio de equilibrio de costes de la Teoría de Colas.

---

### 📍 Punto 5: Configuración Óptima (Exclusivas vs. Mixtas)
*¿Cuántos cajeros deben ofrecer atención exclusiva para pagos y cuántos para retiros?*

Al comparar las dos alternativas de especialización planteadas por el banco:
* **Escenario 2 (1 Retiro + 2 Pagos):** Asignar solo 1 caja para el 70 % de la clientela genera una congestión severa en retiros ($\rho = 68,3\%$, $W_q = 5,04\text{ min}$), arrojando una espera global de **4,28 minutos** y una cola acumulada de **1,79 clientes**.
* **Escenario 3 (2 Retiros + 1 Pago):** Asignar 2 cajas a retiros y 1 a pagos reduce la espera global a **3,75 minutos**, baja el tiempo total en sistema a **6,95 minutos** y minimiza la cola total a **1,57 clientes**. Además, ofrece una atención casi inmediata ($W_q = 1,21\text{ min}$) al 70 % de los usuarios que acuden a retirar, manteniendo la caja de pagos en una utilización controlada del **65,1 %**.

#### 🎯 Decisión Recomendada:
1. **Bajo esquema de Cajas Exclusivas:**  
   Se deben asignar **2 cajas exclusivas para Retiros** y **1 caja exclusiva para Consignaciones/Pagos** (Escenario 3).
2. **Recomendación Gerencial Óptima:**  
   Si la entidad tiene la flexibilidad operativa, la modalidad de **3 Cajas Mixtas (Escenario 1)** es la mejor opción global, ya que logra el menor tiempo de espera promedio (**2,56 minutos**) gracias al efecto de agrupamiento de servidores (*pooling*).  
   *Política sugerida:* Implementar **2 Retiros + 1 Pago** con **regla de desborde flexible** (la caja de retiros asiste a la cola de pagos si esta supera 3 clientes en espera).

---

## 📌 4. Principales Conclusiones del Estudio

1. **Equilibrio y Estabilidad:** El sistema bancario con 3 cajeros opera en condiciones sólidas de estabilidad ($\rho = 44,5\%$). El Cajero 1 obtuvo el menor tiempo medio ($3,12\text{ min}$) y el Cajero 3 el mayor ($3,28\text{ min}$), sin diferencias estructurales.
2. **Caracterización de Demanda:** Se atienden en promedio $199,8\text{ clientes/día}$ ($140,2$ retiros y $59,6$ pagos). El segmento mayoritario es el usuario *Normal de retiro* ($56,6\text{ diarios}$) y la Réplica 3 fue el día de menor afluencia ($174\text{ usuarios}$).
3. **Innecesariedad de Expansión:** Un cuarto cajero provocaría subutilización severa ($\rho = 33,3\%$) y sobrecostos injustificados.
4. **Especialización vs. Flexibilidad:** Entre las opciones exclusivas, **2 Retiros + 1 Pago** es superior a 1R+2P al beneficiar al 70 % de los clientes. No obstante, las **cajas mixtas** proporcionan el mejor aprovechamiento global del sistema.

---

## 🚀 5. Estructura de Archivos y Reproducción

* `Actividad_didáctica_2_M3.ipynb`: Cuaderno Jupyter con la implementación del simulador de eventos discretos en Python.
* `Informe de Simulación - Caso Banco de Colombia.pdf`: Informe académico formal consolidado con el análisis y tablas del estudio.
* `README.md`: Este resumen ejecutivo alineado al informe oficial.

```bash
# Para ejecutar el cuaderno localmente:
pip install numpy pandas scipy matplotlib openpyxl
jupyter notebook Actividad_didáctica_2_M3.ipynb
```
