# Análisis Asintótico

## Ejercicio 16

Para este ejercicio nos centraremos en discutir la estructura de datos avanzada **Union-Find (Disjoint Sets)**.

### Implementación

```python
class UnionFind:
    def __init__(self, n):
        # Cada nodo empieza siendo su propio líder (es su propio grupo)
        self.parent = list(range(n))
        
        # Usamos rank (puede ser también "tamaño") para optimizar la unión
        self.rank = [0] * n

    def find(self, x):
        # Si el nodo no es su propio líder, seguimos buscando hacia arriba
        if self.parent[x] != x:
            # Aplicamos compresión de caminos:
            # hacemos que el nodo apunte directamente al líder del grupo
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        # Encontramos los líderes de ambos grupos
        rootX = self.find(x)
        rootY = self.find(y)

        # Si ya están en el mismo grupo, no hacemos nada
        if rootX == rootY:
            return

        # Unimos por rank: el árbol más bajo se une al más alto
        if self.rank[rootX] < self.rank[rootY]:
            self.parent[rootX] = rootY
        elif self.rank[rootX] > self.rank[rootY]:
            self.parent[rootY] = rootX
        else:
            # Si tienen el mismo rank, elegimos uno y aumentamos su rango
            self.parent[rootY] = rootX
            self.rank[rootX] += 1

    def connected(self, x, y):
        # Dos nodos están conectados si tienen el mismo líder
        return self.find(x) == self.find(y)
```

### Uso

```python
uf = UnionFind(5)  # Creamos 5 nodos: 0, 1, 2, 3, 4

uf.union(0, 1)
uf.union(1, 2)

print(uf.connected(0, 2))  # True (están conectados)
print(uf.connected(0, 3))  # False (no están conectados)

uf.union(3, 4)
uf.union(2, 4)

print(uf.connected(0, 4))  # True (todos conectados ahora)
```

### Tareas

#### a) Describir las operaciones principales (`union` y `find`)

- `find(x)`

    Busca al líder del conjunto donde está el elemento `x`.
    
    - Si `x` es su propio líder, entonces devuelve `x`.
    - Si no, entonces busca recursivamente hasta encontrar el líder del grupo.
    
    **Optimización:** *Compresión de caminos*, para que `x` apunte directamente al líder y las próximas búsquedas sean más rápidas.

    ```python
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x]) # Optimización
        return self.parent[x]
    ```

- `union(x, y)`

    Une los conjuntos que contienen a `x` y a `y`.

    - Primero llama a `find(x)` y `find(y)` para encontrar los líderes.
    - Si los líderes son distintos, entonces se unen.
    
    **Optimización:** *Unión por rango*, para conectar el árbol más pequeño debajo del más grande.

    ```python
    def union(self, x, y):
        rootX = self.find(x)
        rootY = self.find(y)

        if rootX == rootY:
            return

        # Optimización
        if self.rank[rootX] < self.rank[rootY]:
            self.parent[rootX] = rootY
        elif self.rank[rootX] > self.rank[rootY]:
            self.parent[rootY] = rootX
        else:
            self.parent[rootY] = rootX
            self.rank[rootX] += 1
    ```

- `connected(x, y)`

    Comprueba si `x` y `y` están en el mismo conjunto (tienen el mismo jefe).

    ```python
    def connected(self, x, y):
        return self.find(x) == self.find(y)
    ```


#### b) Analizar la complejidad

- `find(x)` y `union(x, y)` tiene complejidad amortizada $O(\alpha(n))$, donde $\alpha(n)$ es la función inversa de Ackermann.

- En la práctica, $\alpha(n) \le 5$ incluso para valores enormes de $n$ (como $10^9$ o más), por lo tanto, las operaciones se comportan como si fueran $O(1)$ (constantes).

Por eso, si se realizan $m$ operaciones en una estructura con $n$ elementos, el tiempo total es aproximadamente $O(m)$.

Esto hace que **Union-Find** sea ideal para resolver problemas donde hay que manejar grupos o verificar si dos elementos están conectados, incluso cuando hay millones de datos.


#### c) Mostrar cómo la técnica de compresión de caminos y la unión por rango logran casi $O(1)$ amortizado

- Compresión de caminos

    - Se aplica durante `find(x)`.
    - Hace que todos los nodos del camino desde `x` hasta el líder apunten directamente al líder.
    - **Resultado:** el árbol se aplana, por lo que las próximas búsquedas son más rápidas.

- Unión por rangos

    - Se aplica durante `union(x, y)`.
    - Siempre se conecta el árbol más bajo al más alto, para evitar árboles largos
    - Esto evita que `find(x)` tenga que recorrer muchos niveles. 

    **¿Por qué esto da casi $O(1)$ amortizado?**

    Porque la estructura se autooptimiza sola con el tiempo, y cada nodo sube directamente al jefe, haciendo que las operaciones se aceleren automáticamente.

#### d) Aplicaciones reales

- **Redes:** Para saber si dos computadoras están en la misma red (conectividad).

- **Redes sociales:** Saber si dos usuarios están en el mismo grupo de amigos.

- **Sistemas de agrupamiento:** Por ejemplo, clústeres de servidores, agrupación de elementos similares, etc.

