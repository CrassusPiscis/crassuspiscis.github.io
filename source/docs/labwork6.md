# Лабораторная работа №6

## Цель

Реализовать рекурсивный и итеративный методы построения бинарного дерева, провести сравнительный анализ их производительности и визуализировать результаты.

---

## Задание

Написать две функции для построения бинарного дерева:
- `build_tree_recursive` — рекурсивный метод
- `build_tree_iterative` — итеративный метод

Параметры (по моему варианту):
- `root = 1` — значение корня дерева
- `height` — высота дерева (от 1 до 21)
- `left_branch = x * 2` — функция для генерации левого потомка
- `right_branch = x + 3` — функция для генерации правого потомка

Дополнительно реализовать:
- Функцию `benchmark` для замера времени выполнения
- Визуализацию результатов с помощью `matplotlib`
- Сравнение производительности двух методов на разных высотах дерева

---

## Код программы

```python
import timeit
import matplotlib.pyplot as plt
import random


def build_tree_recursive(root, height, left_leaf=lambda x: x * 2, right_leaf=lambda x: x + 3):
    '''
    Функция создания бинарного дерева (все значения: корень, количество уровней, операции в ветвях - заданы заранее)
    :return: возвращает бинарное дерево в виде словарей
    '''
    def create_binary_tree(value, height, left_branch_func, right_branch_func):
        '''
        :param value: начальное значение корня дерева, от которого идет дальнейшее ответвление
        :param height: количество уровней дерева
        :param left_branch_func: определяет операцию для создания дочки в левой ветке
        :param right_branch_func: определяет операцию для создания дочки в правой ветке
        :return: возвращает непосредственно сформированные ветки
        '''
        if height == 0:
            return []

        # Создаем контейнер для дочерних узлов
        children = []

        # Левое поддерево
        if height > 0:
            left_child = create_binary_tree(left_branch_func(value), height - 1, left_branch_func, right_branch_func)
            if left_child or height > 1:  # Добавляем даже если дочерний узел пуст
                children.append({f'{left_branch_func(value)}': left_child})

        # Правое поддерево
        if height > 0:
            right_child = create_binary_tree(right_branch_func(value), height - 1, left_branch_func, right_branch_func)
            if right_child or height > 1:  # Аналогично, добавляем даже если узел пуст
                children.append({f'{right_branch_func(value)}': right_child})

        return children

    binary_tree_root = create_binary_tree(root, height, left_leaf, right_leaf)

    # Завершаем формирование дерева, добавив внешний слой
    final_tree = {f'{root}': binary_tree_root}

    # Выводим дерево
    return final_tree


def build_tree_iterative(root, height, left_branch=lambda x: x * 2, right_branch=lambda x: x + 3) -> dict[str, any]:
    '''
    :param height: Задаёт количество "поколений" дерева, включая "поколение" корня
    :param root: значение корня
    :param left_branch: значение для левого "наследника"
    :param right_branch: значение для правого "наследника"
    :return: готовое дерево
    '''
    if height == 1:
        return {f'{root}': []}
    
    # генерация значений всех поколений дерева
    tree = [[root]]
    for i in range(height-1):
        last_level = tree[-1]
        new_level = []
        for i in last_level:
            tree_level_left = left_branch(i)
            tree_level_right = right_branch(i)
            new_level += [tree_level_left, tree_level_right]
        tree.append(new_level)

    # функция генерации словаря
    def dict_gen(key, equal):
        return {f'{key}': equal}

    # Обратная сортировка дерева для более удобной реализации
    tree.sort(reverse=True, key=len)

    # Создание "словарного" вида последних элементов
    end_of_tree = []
    for i in range(len(tree[0])):
        end_of_tree.append(dict_gen(tree[0][i], []))
    tree[0] = end_of_tree

    # занесение всех элементов поколений в конструкцию дерева "словарно-списочного" вида
    for i in range(1, len(tree)):
        new_level = []
        for j in range(len(tree[i])):
            new_level += [dict_gen(tree[i][j], [tree[i-1][2*j], tree[i-1][2*j+1]])]
        tree[i] = new_level

    # создание готового дерева, берется корень и остальная сформированная конструкция дерева
    ready_tree = dict_gen(root, tree[-2])

    # вывод готового дерева
    return ready_tree


def benchmark(func, n, repeat=5):
    """Возвращает среднее время выполнения func(n)"""
    times = timeit.repeat(lambda: func(1, n), number=1, repeat=repeat)
    return min(times)


def main():
    # фиксированный набор данных
    random.seed(42)
    test_data = list(range(1, 22))

    res_recursive = []
    res_iterative = []

    for n in test_data:
        res_recursive.append(benchmark(build_tree_recursive, n))
        res_iterative.append(benchmark(build_tree_iterative, n))

    # Визуализация
    plt.plot(test_data, res_recursive, label="Рекурсивный")
    plt.plot(test_data, res_iterative, label="Итеративный")
    plt.xlabel("Количество поколений")
    plt.ylabel("Время (сек)")
    plt.title("Сравнение рекурсивного и итеративного методов построения дерева")
    plt.legend()
    plt.grid(True, alpha=0.3)
    plt.show()


if __name__ == "__main__":
    main()
```

## Вывод

В ходе выполнения лабораторной работы были реализованы два подхода к построению бинарного дерева: рекурсивный и итеративный.

Были проведены тесты для построения деревьев с разным количеством уровней: от 1 до 21 поколения.

**Основные результаты:**

1. Вне зависимости от способа построения бинарного дерева (рекурсивный или итеративный), характер зависимости времени выполнения от количества уровней одинаков для обоих методов.

2. Проанализировав график, можно сделать вывод, что сложность этих алгоритмов является экспоненциальной — **O(2ⁿ)**. Это объясняется тем, что на каждом уровне количество узлов удваивается, и алгоритму необходимо обработать каждый узел.

3. Итеративный метод показывает незначительно лучшее время выполнения при больших высотах дерева, что связано с отсутствием накладных расходов на рекурсивные вызовы.

Таким образом, реализованы оба метода построения бинарного дерева, проведен их сравнительный анализ и визуализированы результаты.


## Дополнительно

Ссылка на репозиторий в GitHub: [https://github.com/CrassusPiscis/ITMO_Python/tree/master/Lab_6](https://github.com/CrassusPiscis/ITMO_Python/tree/master/Lab_6)