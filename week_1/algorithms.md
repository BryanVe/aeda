# Ejercicios

## Ejercicio 1

Considera la siguiente función en Python (lee los comentarios con atención):

```py
def func(n):
    sum = 0  # costo temporal de la asignación = c1
    # se asume que la inicialización de i a 0 al inicio del bucle tiene costo 0
    for i in range(n):  # costo por cada incremento de i y verificación del rango = c2
        sum = sum + i * i  # costo de la suma = c3, 
        # costo de la multiplicación = c4 y 
        # costo de la asignación = c1
    return sum  # costo del retorno = c5
```

¿Cuál es el costo temporal total de llamar a la función `func` en términos de $n$, $c1$, $c2$, $c3$, $c4$ y $c5$?

### Solución

Tanto la primer operación de asignación `sum = 0` como el retorno `return sum` son operaciones que se realizan solo una vez en la función, por ende la suma de ambas operaciones es $c1+c2$.

Luego las operaciones realizadas dentro del bucle `for` se realizan a lo más $n$ veces, por ende la suma estaría representada por $n\times{(c2+c3+c4)}$.

Por lo tanto haciendo la suma total, la respuesta correcta sería $c1+n\times{(c2+c3+c4)}+c5$.
