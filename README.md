# tic-tac-toe-minimax

<p align="center">
	<img src="preview/minimax_img.png"></img>
</p>

## Introducción
Para resolver juegos usando IA, presentaremos el concepto de un árbol de juego seguido de un algoritmo minimax. Los diferentes estados del juego están representados por nodos en el árbol del juego, muy similar a los problemas de planificación anteriores. La idea es ligeramente diferente. En el árbol del juego, los nodos están dispuestos en niveles que corresponden a los turnos de cada jugador en el juego, de modo que el nodo "raíz" del árbol (generalmente representado en la parte superior del diagrama) es la posición inicial en el juego. En tic-tac-toe, esta sería la cuadrícula vacía sin X ni Os reproducidos todavía. Bajo root, en el segundo nivel, están los estados posibles que pueden resultar de los movimientos del primer jugador, ya sea X u O. A estos nodos los llamamos los "hijos" del nodo raíz.

Cada nodo en el segundo nivel tendría además como sus nodos hijos los estados a los que se puede llegar mediante los movimientos del jugador contrario. Esto se continúa, nivel por nivel, hasta llegar a los estados en los que el juego termina. En tic-tac-toe, esto significa que uno de los jugadores obtiene una línea de tres y gana, o que el tablero está lleno y el juego termina en empate.

## ¿Qué es Minimax?
Minimax es una inteligencia artificial aplicada en juegos de dos jugadores, como tic-tac-toe, damas, ajedrez y listo. Estos juegos se conocen como juegos de suma cero, porque en una representación matemática: un jugador gana (+1) y otro jugador pierde (-1) o ambos de cualquiera que no gane (0).

## ¿Cómo funciona?
El algoritmo busca, de forma recursiva, la mejor jugada que lleva al jugador * Max * a ganar o no perder (empate). Considera el estado actual del juego y los movimientos disponibles en ese estado, luego, para cada movimiento válido que juega (alternando * min * y * max *) hasta que encuentra un estado terminal (ganar, empatar o perder).

## Comprensión del algoritmo
El algoritmo fue estudiado por el libro Algorithms in a Nutshell (George Heineman; Gary Pollice; Stanley Selkow, 2009). Pseudocódigo (adaptado):

```
minimax(state, depth, player)

	if (player = max) then
		best = [null, -infinity]
	else
		best = [null, +infinity]

	if (depth = 0 or gameover) then
		score = evaluate this state for player
		return [null, score]

	for each valid move m for player in state s do
		execute move m on s
		[move, score] = minimax(s, depth - 1, -player)
		undo move m on s

		if (player = max) then
			if score > best.score then best = [move, score]
		else
			if score < best.score then best = [move, score]

	return best
end
```

Ahora veremos cada parte de este pseudocódigo con la implementación de Python. La implementación de Python está disponible en este repositorio. En primer lugar, considérelo:
> board = [
>	[0, 0, 0],
>	[0, 0, 0],
>	[0, 0, 0]
> ]

> MAX = +1

> MIN = -1

El MAX puede ser X u O y el MIN puede ser O o X, lo que sea. El tablero es de 3x3.

```python
def minimax(state, depth, player):
```
* ** estado **: el tablero actual en tic-tac-toe (nodo)
* ** profundidad **: índice del nodo en el árbol del juego
* ** jugador **: puede ser un jugador * MAX * o un jugador * MIN *

```python
if player == MAX:
	return [-1, -1, -infinity]
else:
	return [-1, -1, +infinity]
```

Ambos jugadores comienzan con su peor puntuación. Si el jugador es MAX, su puntuación es -infinito. De lo contrario, si el jugador es MIN, su puntuación es + infinito. ** Nota: ** * infinity * es un alias para inf (del módulo matemático, en Python).

El mejor movimiento en el tablero es [-1, -1] (fila y columna) para todos.

```python
if depth == 0 or game_over(state):
	score = evaluate(state)
	return score
```

Si la profundidad es igual a cero, entonces el tablero no tiene nuevas celdas vacías para jugar. O, si un jugador gana, entonces el juego terminó por MAX o MIN. Por lo tanto, se devolverá la puntuación de ese estado.

* Si MAX ganó: devuelva +1
* Si MIN ganó: devuelva -1
* De lo contrario: devuelve 0 (dibujar)

Ahora veremos la parte principal de este código que contiene recursividad.

```python
for cell in empty_cells(state):
	x, y = cell[0], cell[1]
	state[x][y] = player
	score = minimax(state, depth - 1, -player)
	state[x][y] = 0
	score[0], score[1] = x, y
```

Para cada movimiento válido (celdas vacías):
* ** x **: recibe el índice de fila de celda
* ** y **: recibe el índice de columna de celda
* ** estado [x] [y] **: es como si el tablero [fila_disponible] [col_disponible] reciba MAX o MIN jugador
* ** puntuación = minimax (estado, profundidad - 1, -jugador) **:
  * estado: es el tablero actual en recursividad;
  * profundidad -1: índice del siguiente estado;
  * -jugador: si un jugador es MAX (+1) será MIN (-1) y viceversa.

El movimiento (+1 o -1) en el tablero se deshace y la fila, la columna se recopilan.

El siguiente paso es comparar la puntuación con la mejor.

```python
if player == MAX:
	if score[2] > best[2]:
		best = score
else:
	if score[2] < best[2]:
		best = score
```

Para el jugador MAX, se recibirá una puntuación mayor. Para un jugador MIN, se recibirá una puntuación más baja. Y al final, se devuelve la mejor jugada. Algoritmo final:

```python
def minimax(state, depth, player):
	if player == MAX:
		best = [-1, -1, -infinity]
	else:
		best = [-1, -1, +infinity]

	if depth == 0 or game_over(state):
		score = evaluate(state)
		return [-1, -1, score]

	for cell in empty_cells(state):
		x, y = cell[0], cell[1]
		state[x][y] = player
		score = minimax(state, depth - 1, -player)
		state[x][y] = 0
		score[0], score[1] = x, y

		if player == MAX:
			if score[2] > best[2]:
				best = score
		else:
			if score[2] < best[2]:
				best = score

	return best
```

## Árbol de juegos
A continuación, el mejor movimiento está en el medio porque el valor máximo está en el segundo nodo a la izquierda.

<p align="center">
	<img src="preview/tic-tac-toe-minimax-game-tree.png"></img>
</p>

Fíjate que la profundidad es igual a los movimientos válidos en el tablero. El código completo está disponible en ** py_version / **.

Árbol de juego simplificado:

<p align="center">
	<img src="preview/simplified-g-tree.png"></img>
</p>

Ese árbol tiene 11 nodos. ¡El árbol completo del juego tiene 549.946 nodos! Puede probarlo poniendo una variable global estática en su programa e incrementándola para cada llamada de función minimax por turno.

En un juego más complejo, como el ajedrez, es difícil buscar en todo el árbol del juego. Sin embargo, la poda alfa-beta es un método de optimización del algoritmo minimax que nos permite ignorar algunas ramas en el árbol de búsqueda, porque corta nodos irrelevantes (subárboles) en la búsqueda. Para más información, ver:

* Book: George T. Heineman; Gary Pollice; Stanley Selkow. Algorithms in a nutshell. O'Reilly, 2009.
* Wikipédia: <https://en.wikipedia.org/wiki/Minimax>
* Nanyang Technological University: <https://www.ntu.edu.sg/home/ehchua/programming/java/JavaGame_TicTacToe_AI.html>
