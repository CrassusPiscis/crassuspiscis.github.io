# Лабораторная работа №2

## Цель

Ознакомиться с документацией библиотек **NumPy**, **Matplotlib**, **Seaborn** и **Pandas**. Написать различные функции (вычисления, вызуализация) с применением данных библиотек

---

## Задание

Написать код функции в соответствии с условием, которое изложено в документации к функции

---

## Код программы

## main.py

```python
import os

import numpy
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns


# ============================================================
# 1. СОЗДАНИЕ И ОБРАБОТКА МАССИВОВ
# ============================================================

def create_vector() -> numpy.ndarray:
    """
    Создать массив от 0 до 9.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.arange.html

    Returns:
        numpy.ndarray: Массив чисел от 0 до 9 включительно
    """
    # Подсказка: используйте np.arange(10)
    return np.arange(0, 10, 1)


def create_matrix() -> numpy.ndarray:
    """
    Создать матрицу 5x5 со случайными числами [0,1].

    Изучить:
    https://numpy.org/doc/stable/reference/random/generated/numpy.random.rand.html

    Returns:
        numpy.ndarray: Матрица 5x5 со случайными значениями от 0 до 1
    """
    # Подсказка: используйте np.random.rand(5,5)
    return np.random.rand(5,5)


def reshape_vector(vec: numpy.ndarray) -> numpy.ndarray:
    """
    Преобразовать (10,) -> (2,5)

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.reshape.html

    Args:
        vec (numpy.ndarray): Входной массив формы (10,)

    Returns:
        numpy.ndarray: Преобразованный массив формы (2, 5)
    """
    # Подсказка: используйте vec.reshape(2,5)
    return np.reshape(vec, (2,5))


def transpose_matrix(mat: numpy.ndarray) -> numpy.ndarray:
    """
    Транспонирование матрицы.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.transpose.html

    Args:
        mat (numpy.ndarray): Входная матрица

    Returns:
        numpy.ndarray: Транспонированная матрица
    """
    # Подсказка: используйте mat.T или np.transpose(mat)
    return np.transpose(mat)


# ============================================================
# 2. ВЕКТОРНЫЕ ОПЕРАЦИИ
# ============================================================

def vector_add(a: numpy.ndarray, b: numpy.ndarray) -> numpy.ndarray:
    """
    Сложение векторов одинаковой длины.
    (Векторизация без циклов)

    Args:
        a (numpy.ndarray): Первый вектор
        b (numpy.ndarray): Второй вектор

    Returns:
        numpy.ndarray: Результат поэлементного сложения
    """
    # Подсказка: используйте оператор +
    return a + b



def scalar_multiply(vec: numpy.ndarray, scalar: int | float) -> numpy.ndarray:
    """
    Умножение вектора на число.

    Args:
        vec (numpy.ndarray): Входной вектор
        scalar (float/int): Число для умножения

    Returns:
        numpy.ndarray: Результат умножения вектора на скаляр
    """
    # Подсказка: используйте оператор *
    return vec * scalar


def elementwise_multiply(a: np.ndarray, b: np.ndarray) -> numpy.ndarray:
    """
    Поэлементное умножение.

    Args:
        a (numpy.ndarray): Первый вектор/матрица
        b (numpy.ndarray): Второй вектор/матрица

    Returns:
        numpy.ndarray: Результат поэлементного умножения
    """
    # Подсказка: используйте оператор *
    return a * b


def dot_product(a: np.ndarray, b: np.ndarray) -> float:
    """
    Скалярное произведение.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.dot.html

    Args:
        a (numpy.ndarray): Первый вектор
        b (numpy.ndarray): Второй вектор

    Returns:
        float: Скалярное произведение векторов
    """
    # Подсказка: используйте np.dot(a, b)
    return np.dot(a, b)

# ============================================================
# 3. МАТРИЧНЫЕ ОПЕРАЦИИ
# ============================================================

def matrix_multiply(a: numpy.ndarray, b: numpy.ndarray) -> numpy.ndarray:
    """
    Умножение матриц.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.matmul.html

    Args:
        a (numpy.ndarray): Первая матрица
        b (numpy.ndarray): Вторая матрица

    Returns:
        numpy.ndarray: Результат умножения матриц
    """
    # Подсказка: используйте a @ b или np.matmul(a, b)
    return np.matmul(a, b)


def matrix_determinant(a: numpy.ndarray) -> float:
    """
    Определитель матрицы.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.linalg.det.html

    Args:
        a (numpy.ndarray): Квадратная матрица

    Returns:
        float: Определитель матрицы
    """
    # Подсказка: используйте np.linalg.det(a)
    return np.linalg.det(a)


def matrix_inverse(a: numpy.ndarray) -> numpy.ndarray:
    """
    Обратная матрица.

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.linalg.inv.html

    Args:
        a (numpy.ndarray): Квадратная матрица

    Returns:
        numpy.ndarray: Обратная матрица
    """
    # Подсказка: используйте np.linalg.inv(a)
    return np.linalg.inv(a)


def solve_linear_system(a: numpy.ndarray, b: numpy.ndarray) -> numpy.ndarray:
    """
    Решить систему Ax = b

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.linalg.solve.html

    Args:
        a (numpy.ndarray): Матрица коэффициентов A
        b (numpy.ndarray): Вектор свободных членов b

    Returns:
        numpy.ndarray: Решение системы x
    """
    # Подсказка: используйте np.linalg.solve(a, b)
    return np.linalg.solve(a, b)


# ============================================================
# 4. СТАТИСТИЧЕСКИЙ АНАЛИЗ
# ============================================================

def load_dataset(path="data/students_scores.csv") -> numpy.ndarray:
    """
    Загрузить CSV и вернуть NumPy массив.

    Args:
        path (str): Путь к CSV файлу

    Returns:
        numpy.ndarray: Загруженные данные в виде массива
    """
    # Подсказка: используйте pd.read_csv(path).to_numpy()
    return pd.read_csv(path).to_numpy()


def statistical_analysis(data: numpy.ndarray) -> dict[str, int | float]:
    """
    Представьте, что данные — это результаты экзамена по математике.
    Нужно оценить:
    - средний балл
    - медиану
    - стандартное отклонение
    - минимум
    - максимум
    - 25 и 75 перцентили

    Изучить:
    https://numpy.org/doc/stable/reference/generated/numpy.mean.html
    https://numpy.org/doc/stable/reference/generated/numpy.median.html
    https://numpy.org/doc/stable/reference/generated/numpy.std.html
    https://numpy.org/doc/stable/reference/generated/numpy.percentile.html

    Args:
        data (numpy.ndarray): Одномерный массив данных

    Returns:
        dict: Словарь со статистическими показателями
    """
    # Подсказка: используйте np.mean(), np.median(), np.std(),
    # np.min(), np.max(), np.percentile(data, 25), np.percentile(data, 75)
    return {'mean': np.mean(data).item(),
            'median': np.median(data).item(),
            'std': np.std(data).item(),
            'min': np.min(data).item(),
            'max': np.max(data).item(),
            '25 перцентиль': np.percentile(data, 25).item(),
            '75 перцентиль': np.percentile(data, 75).item()}

def normalize_data(data: numpy.ndarray) -> numpy.ndarray:
    """
    Min-Max нормализация.

    Формула: (x - min) / (max - min)

    Args:
        data (numpy.ndarray): Входной массив данных

    Returns:
        numpy.ndarray: Нормализованный массив данных в диапазоне [0, 1]
    """
    return (data - np.min(data)) / (np.max(data) - np.min(data))


# ============================================================
# 5. ВИЗУАЛИЗАЦИЯ
# ============================================================

def plot_histogram(data: numpy.ndarray):
    """
    Построить гистограмму распределения оценок по математике.

    Изучить:
    https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.hist.html

    Args:
        data (numpy.ndarray): Данные для гистограммы
    """
    # Подсказка: используйте plt.hist(), добавьте заголовок, подписи осей,
    # сохраните в папку plots с помощью plt.savefig()
    plt.figure(figsize=(10, 6))

    plt.hist(data, bins=8, alpha=0.7, edgecolor='black', color='steelblue')

    mean = np.mean(data)
    plt.axvline(mean, color='red', linestyle='--', linewidth=2,
                label=f'Среднее: {mean:.2f}')

    plt.title('Распределение баллов по математике', fontsize=14, fontweight='bold')
    plt.xlabel('Баллы', fontsize=12)
    plt.ylabel('Количество студентов', fontsize=12)
    plt.grid(True, alpha=0.3, linestyle=':')
    plt.legend(fontsize=11)

    plt.savefig('plots/math.png', dpi=300, bbox_inches='tight')

    plt.show()
    plt.close()

    return None

def plot_heatmap(matrix: numpy.ndarray):
    """
    Построить тепловую карту корреляции предметов.

    Изучить:
    https://seaborn.pydata.org/generated/seaborn.heatmap.html

    Args:
        matrix (numpy.ndarray): Матрица корреляции
    """
    # Подсказка: используйте sns.heatmap(), добавьте заголовок, сохраните
    fig, ax = plt.subplots(figsize=(12, 8))

    subjects = ['Математика', 'Физика', 'Информатика']

    sns.heatmap(matrix,
                annot=True,  # показывать значения
                cmap='coolwarm',  # цветовая схема
                center=None,  # убираем center для целых чисел
                square=True,  # квадратные ячейки
                linewidths=1,  # разделительные линии
                linecolor='white',  # цвет линий
                xticklabels=subjects,  # подписи сверху
                yticklabels=np.arange(1, 11),  # подписи слева (1-10)
                cbar_kws={'label': 'Оценки',
                          'shrink': 0.8},
                ax=ax)

    plt.subplots_adjust(left=0.1, bottom=0.1, right=0.95, top=0.95)

    plt.title('Тепловая карта оценок по предметам',
              fontsize=14, fontweight='bold', pad=15)

    ax.set_ylabel('Номер студента', fontsize=12)

    plt.savefig('plots/heatmap.png', dpi=300, bbox_inches='tight')

    plt.show()
    plt.close()

    return None


def plot_line(x: numpy.ndarray, y: numpy.ndarray):
    """
    Построить график зависимости: студент -> оценка по математике.

    Изучить:
    https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.plot.html

    Args:
        x (numpy.ndarray): Номера студентов
        y (numpy.ndarray): Оценки студентов
    """
    # Подсказка: используйте plt.plot(), добавьте заголовок, подписи осей,
    # сохраните график
    plt.figure(figsize=(10, 6))

    plt.plot(x, y, 'bo-', linewidth=2, markersize=8, label='Оценки по математике')
    plt.xlabel('Номер студента', fontsize=12)
    plt.ylabel('Оценка', fontsize=12)
    plt.title('Зависимость оценки от студента', fontsize=14, fontweight='bold')
    plt.grid(True, alpha=0.3, linestyle='--')

    plt.legend()

    plt.xlim(0, len(x) + 1)
    plt.ylim(50, 100)
    plt.xticks(x)

    plt.savefig('plots/plot_line.png', dpi=300, bbox_inches='tight')

    plt.show()
    plt.close()

    return None
```

## Графики

![heatmap.png](https://github.com/CrassusPiscis/Sem2Lab2/blob/master/plots/heatmap.png?raw=true)

![math.png](https://github.com/CrassusPiscis/Sem2Lab2/blob/master/plots/math.png?raw=true)

![plot_line.png](https://github.com/CrassusPiscis/Sem2Lab2/blob/master/plots/plot_line.png?raw=true)

## Вывод

В ходе выполнения лабораторной работы я ознакомился с документацией вышеперечисленных библиотек и научился применять их на практике

Таким образом, я увеличил спектр задач, с которыми я теперь могу справиться.

## Дополнительно

Ознакомиться с датасетами к заданию можно в репозитории данной лабораторной работы.

Ссылка на репозиторий в GitHub: [https://github.com/CrassusPiscis/Sem2Lab2](https://github.com/CrassusPiscis/Sem2Lab2)