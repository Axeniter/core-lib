# Алгоритм A*
## Определение
A* - алгоритм поиска пути на графе или сетке. Находит кратчайший путь от начальной вершины до целевой, используя эвристическую функцию.
## Алгоритм
Для каждой вершины $v$ расчитывается функция $f(v)=g(v)+h(v)$, оценивающая стоимость пути до финальной вершины через вершину $v$, где
- $g(v)$ - пройденное расстояние до $v$
- $h(v)$ - эвристическая функция, оценивающая стоимость оставшегося расстояния (от $v$ до цели)

Требование к эвристике:
- Доступность: $0 \leq h(v) \leq h^*(v)$, где $h^*(v)$ - истинная минимальная стоимость от $v$ до цели. Эвристика никогда не переоценивает истинную стоимость.
- Согласованность: для любой вершины $v$ и её преемника $n'$, полученного по ребру стоимостью $c(n,n')$, выполняется: $h(v) \leq c(n, n') + h(n')$

Популярные эвристики:
- Манхэттенское расстояние: $|x_{1}-x_{2}|+|y_{1}-y_{2}|$ (для сетки)
- Евклидово расстояние: $\sqrt{ (x_{1}-x_{2})^2+(y_{1}-y_{2})^2 }$

Множество вершин-кандидатов для обработки (Open set): реализуется как очередь с приоритетом (минимальная куча), где приоритет - наименьшее $f(v)$
Множество обработанных вершин (Closed Set): реализуется как хеш-таблица или множество
## Реализация
Псевдокод:
```
function A*(start, goal):
    openSet := {start}  // очередь с приоритетом по f(n)
    cameFrom := empty map

    gScore := map with default value Infinity
    gScore[start] := 0

    fScore := map with default value Infinity
    fScore[start] := h(start, goal)

    while openSet is not empty:
        current := вершина в openSet с наименьшим fScore
        if current == goal:
            return reconstruct_path(cameFrom, current) // путь найден

        openSet.Remove(current)
        // для сетки можно использовать closedSet, но часто достаточно проверки gScore

        for each neighbor of current:
            tentative_gScore := gScore[current] + d(current, neighbor) // d - стоимость ребра

            if tentative_gScore < gScore[neighbor]:
                // найден лучший путь к neighbor
                cameFrom[neighbor] := current
                gScore[neighbor] := tentative_gScore
                fScore[neighbor] := gScore[neighbor] + h(neighbor, goal)

                if neighbor not in openSet:
                    openSet.Add(neighbor)

    return failure  // путь не существует

function reconstruct_path(cameFrom, current):
    total_path := [current]
    while current in cameFrom:
        current := cameFrom[current]
        total_path.prepend(current)
    return total_path
```