## Definición formal

Sean:
- A = B = {1, 2, ..., 100}
- P = { a·b | a ∈ A, b ∈ B }

Entonces P ⊆ {1, 2, ..., 10000}.

Llamamos **números huérfanos** a los enteros n ∈ {1, ..., 10000} tales que **no existe** (a,b) con 1 ≤ a,b ≤ 100 y n = a·b.

---
## Observación clave

Todo n ≤ 10000 pertenece a P **si y solo si** n tiene al menos un divisor d ≤ 100 y el cociente n/d ≤ 100.

Equivalentemente:

n ∉ P ⇔ para todo divisor d de n:
- d > 100 **o** n/d > 100

---
## Caracterización simple de los huérfanos

1. **Primos mayores que 100** (hasta 10000).
   - Ej.: 101, 103, 107, ..., 9973

2. **Compuestos cuyos factores mínimos exceden 100**.
   - Ej.: 101·101 = 10201 (queda fuera del rango total)
   - Dentro de 1..10000: muy pocos; casi todos los compuestos ≤10000 tienen algún factor ≤100.

Conclusión práctica:
> En 1..10000, los huérfanos son **exactamente los primos mayores que 100**.

---
## Ejemplo concreto

- 97 → no huérfano (97 = 97·1)
- 101 → huérfano
- 121 → no huérfano (11·11)
- 143 → no huérfano (11·13)
- 9973 → huérfano

---
## Representaciones recomendadas

Propongo **tres vistas complementarias**, cada una muestra algo distinto y juntas cierran la idea.

---
### 1. Mapa binario 1..10000 (vista global)

Pensá los números del 1 al 10000 como una línea.
- Negro = número generado por algún producto a·b
- Rojo = número huérfano

Esto te deja ver inmediatamente:
- Que los huérfanos aparecen *a partir de 101*
- Que son cada vez más frecuentes a medida que crece n

Conceptualmente es la vista más honesta: presencia / ausencia.

---
### 2. Matriz 100×100 de productos (estructura generadora)

Construís la tabla:
- Filas: a = 1..100
- Columnas: b = 1..100
- Celda = a·b

Acá no mirás los huérfanos directamente, sino **la estructura del generador**:
- Simetría respecto de la diagonal
- Alta densidad de repeticiones
- Cómo se concentran los productos chicos

Luego, mentalmente, todo lo que *no* aparece en esta nube es huérfano.

---
### 3. Vista primo-compuesto (interpretación matemática)

Marcás en 1..10000:
- Primos
- Compuestos

Y superponés:
- Huérfanos = primos > 100

Esta vista es clave porque revela que el fenómeno **no es aritmética de productos**, sino **teoría de números**:
> la frontera real está en el menor factor.

---
## Código Python mínimo

```python
A = range(1, 101)
P = {a*b for a in A for b in A}

orphans = [n for n in range(1, 10001) if n not in P]

print(orphans[:20])
print(len(orphans))
```

---
## Recomendación concreta

Si tuviera que elegir **una sola**:
> Línea 1..10000 con puntos rojos (huérfanos) y negros (generados).

Si podés elegir **dos**:
> Línea 1..10000 + matriz 100×100.

Si querés entenderlo *de verdad*:
> Las tres.

Decime cuál querés que bajemos a código o gráfico concreto.

```python
A = range(1, 101)
P = {a*b for a in A for b in A}

orphans = [n for n in range(1, 10001) if n not in P]

# ver primeros
print(orphans[:20])
print(len(orphans))
```

Si querés:
- matriz 100×100
- mapa booleano 1..10000
- visualización por densidad
- extensión a otros rangos

seguimos sobre este canvas.



---
## Campo (definición) y visualización en Canvas

### Campo base

Tomamos el eje ordinal:

- n ∈ {1,2,...,10000}

### Campos a superponer (tres señales)

1) **Campo multiplicativo** M(n)

- M(n)=1 si existe (a,b) con 1≤a≤100, 1≤b≤100 y n=a·b
- M(n)=0 en caso contrario (huérfano)

2) **Campo primo** Π(n)

- Π(n)=1 si n es primo
- Π(n)=0 si n no es primo

3) **Campo huérfano** H(n)

- H(n)=1 si n es huérfano
- H(n)=0 si no

Con el resultado ya establecido:

- H(n)=1 ⇔ Π(n)=1 y n>100

### Visualización recomendada (mínima y clara)

- Graficar n en el eje X.
- Graficar M(n) como banda (0/1).
- Dibujar puntos para H(n) arriba (los huérfanos).

Eso muestra de un golpe:
- densidad de productos
- huecos
- que esos huecos son exactamente primos >100

---
## Código Python para graficarlo (matplotlib)

```python
import math
import matplotlib.pyplot as plt

# set de productos
A = range(1, 101)
P = {a*b for a in A for b in A}

# primalidad simple
def is_prime(n: int) -> bool:
    if n < 2:
        return False
    if n % 2 == 0:
        return n == 2
    r = int(math.isqrt(n))
    f = 3
    while f <= r:
        if n % f == 0:
            return False
        f += 2
    return True

N = 10_000
x = list(range(1, N+1))
M = [1 if n in P else 0 for n in x]
H = [n for n in x if n not in P]

plt.figure()
plt.plot(x, M)                 # banda 0/1
plt.scatter(H, [1.05]*len(H))  # huérfanos arriba
plt.ylim(-0.1, 1.2)
plt.xlabel('n (1..10000)')
plt.ylabel('M(n) / huérfanos')
plt.show()

print('Huérfanos = primos>100 ?', all(is_prime(n) and n>100 for n in H))
```

---
Si querés, lo cambio por heatmap 100×100, histograma de gaps, o rejilla 100×100 del 1..10000.

