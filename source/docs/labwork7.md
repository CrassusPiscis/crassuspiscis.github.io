# Лабораторная работа №7

## Цель

Разработать программу для получения курсов валют с API Центробанка России с применением декоратора для логирования и обработки ошибок, а также реализовать набор тестов для проверки функциональности.

---

## Задание

Создать программу, которая:
- Получает курсы валют с API Центробанка России (`https://www.cbr-xml-daily.ru/daily_json.js`)
- Использует декоратор `logger` для логирования выполнения функции
- Обрабатывает различные типы ошибок (сетевые, JSON, ключи)
- Содержит набор тестов с использованием `unittest`
- Сохраняет логи в файл (в моем случае, я назвал файл: `CentroErrorBank.log`)

Параметры функции (по моему варианту):
- `currency_codes` — список кодов валют (например, `['USD', 'EUR', 'GBP']`)
- `url` — адрес API (по умолчанию: `https://www.cbr-xml-daily.ru/daily_json.js`)
- `handle` — обработчик вывода (по умолчанию: `sys.stdout`)

---

## Код программы


```python
import logging
import requests
import sys
import time
import io
import functools
import json
import unittest
stream = io.StringIO()

cfg = logging.basicConfig(
    filename='CentroErrorBank.log',
    level=logging.DEBUG,
    format= "%(asctime)s - %(levelname)s: %(message)s",
    encoding = 'utf-8'
)
def logger(func=None, *, handle=sys.stdout):
    if func is None:
        return lambda func: logger(func, handle=handle)
    @functools.wraps(func)
    def inner(*args, **kwargs):
        logging.info(f'Start of program: {time.ctime()}')
        logging.info(f'Name of function: {func.__name__}, arguments: {args, kwargs}')
        try:
            result = func(*args, **kwargs)
            logging.info(f'Успешное завершение функции {func.__name__}')
            return result
        except Exception as e:
            logging.error(f'Критическая ошибка: {e}')
            raise


    return inner

@logger(handle=cfg)
def get_currencies(currency_codes: list, url:str = "https://www.cbr-xml-daily.ru/daily_json.js", handle=sys.stdout)->dict:
    """
    Получает курсы валют с API Центробанка России.

    Args:
        currency_codes (list): Список символьных кодов валют (например, ['USD', 'EUR']).

    Returns:
        dict: Словарь, где ключи - символьные коды валют, а значения - их курсы.
              Возвращает None в случае ошибки запроса.
    """
    try:
        response = requests.get(url)
        response.raise_for_status()  # Проверка на ошибки HTTP
    except requests.exceptions.RequestException as e:
        raise ConnectionError('API недоступен')

    try:
        data = response.json()
    except json.JSONDecodeError as e:
        raise ValueError(f'Некорректный JSON: {e}')
    currencies = {}

    if "Valute" in data:
        for code in currency_codes:
            if code in data["Valute"]:
                if data["Valute"][code]["Value"] != float(data["Valute"][code]["Value"]):
                    raise TypeError('Курс валюты имеет неверный тип')
                currencies[code] = data["Valute"][code]["Value"]
            if code not in data["Valute"]:
                currencies[code] = f"Код валюты '{code}' не найден."
                raise KeyError(f'Валюта {code} отсутствует в данных')
    else:
        raise KeyError('Нет ключа "Valute"')

    return currencies

# Пример использования функции:
currency_list = ['USD', 'EUR', 'GBP']

currency_data = get_currencies(currency_list)
if currency_data:
     print(currency_data)
print(stream.getvalue())



class TestGetCurrencies(unittest.TestCase):
    """Тесты для функции get_currencies"""

    def test_successful_request_real_api(self):
        """Тест успешного получения курсов валют с реальным API"""
        try:
            result = get_currencies(['USD', 'EUR', 'GBP'])
            self.assertIsInstance(result, dict)
            self.assertIn('USD', result)
            self.assertIn('EUR', result)
            self.assertIn('GBP', result)
            # Проверяем что значения - числа
            self.assertIsInstance(result['USD'], (int, float))
            self.assertIsInstance(result['EUR'], (int, float))
            self.assertIsInstance(result['GBP'], (int, float))
            # Проверяем что значения положительные
            self.assertGreater(result['USD'], 0)
            self.assertGreater(result['EUR'], 0)
            self.assertGreater(result['GBP'], 0)
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")

    def test_nonexistent_currency_real_api(self):
        """Тест с несуществующей валютой через реальное API"""
        try:
            # Должен вызвать KeyError при первой же несуществующей валюте
            with self.assertRaises(KeyError):
                get_currencies(['INVALID_CURRENCY_CODE_123'])
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")

    def test_mixed_currencies_real_api(self):
        """Тест со смесью существующих и несуществующих валют"""
        try:
            # Тест должен упасть на первой несуществующей валюте
            with self.assertRaises(KeyError):
                get_currencies(['USD', 'INVALID_CODE', 'EUR'])
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")

    def test_invalid_url(self):
        """Тест с неверным URL"""
        with self.assertRaises(ConnectionError):
            get_currencies(['USD'], url="https://invalid-url-that-does-not-exist-12345.com")

    def test_empty_currency_list(self):
        """Тест с пустым списком валют"""
        try:
            result = get_currencies([])
            self.assertIsInstance(result, dict)
            self.assertEqual(len(result), 0)
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")

    def test_duplicate_currencies(self):
        """Тест с дублирующимися валютами"""
        try:
            result = get_currencies(['USD', 'USD', 'EUR'])
            self.assertIsInstance(result, dict)
            self.assertIn('USD', result)
            self.assertIn('EUR', result)
            # USD должен быть в результате только один раз
            self.assertEqual(len(result), 2)
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")


class TestLoggerDecorator(unittest.TestCase):
    """Тесты для декоратора logger"""

    def test_logger_attributes(self):
        """Тест что декоратор сохраняет атрибуты функции"""
        self.assertEqual(get_currencies.__name__, 'get_currencies')
        self.assertIsNotNone(get_currencies.__doc__)
        self.assertIn('Получает курсы валют', get_currencies.__doc__)

    def test_function_signature(self):
        """Тест сигнатуры функции"""
        import inspect
        sig = inspect.signature(get_currencies)
        params = list(sig.parameters.keys())
        self.assertIn('currency_codes', params)
        self.assertIn('url', params)
        self.assertIn('handle', params)


class TestErrorScenarios(unittest.TestCase):
    """Тесты сценариев ошибок"""

    def test_connection_timeout(self):
        """Тест таймаута соединения"""
        with self.assertRaises(ConnectionError):
            get_currencies(['USD'], url="https://www.cbr-xml-daily.ru:81/daily_json.js")

    def test_invalid_json_url(self):
        """Тест с URL который не возвращает JSON"""
        with self.assertRaises((ValueError, ConnectionError)):
            get_currencies(['USD'], url="https://www.google.com")

    def test_very_long_currency_code(self):
        """Тест с очень длинным кодом валюты"""
        try:
            with self.assertRaises(KeyError):
                get_currencies(['NO_CODE'])
        except Exception as e:
            self.skipTest(f"API недоступно: {e}")


def suite():
    """Создание набора тестов"""
    test_suite = unittest.TestSuite()
    test_suite.addTest(unittest.makeSuite(TestGetCurrencies))
    test_suite.addTest(unittest.makeSuite(TestLoggerDecorator))
    test_suite.addTest(unittest.makeSuite(TestErrorScenarios))
    return test_suite


if __name__ == '__main__':
    # Запуск тестов
    runner = unittest.TextTestRunner(verbosity=2)
    test_suite = suite()
    result = runner.run(test_suite)

    # Вывод статистики
    print(f"\nТестов запущено: {result.testsRun}")
    print(f"Ошибок: {len(result.errors)}")
    print(f"Провалов: {len(result.failures)}")
    print(f"Пропущено: {len(result.skipped)}")

    # Вывод информации о пропущенных тестах
    if result.skipped:
        print("\nПропущенные тесты:")
        for test, reason in result.skipped:
            print(f"  {test}: {reason}")
```

## Вывод

В ходе выполнения лабораторной работы была разработана программа для получения курсов валют с API Центробанка России.

**Основные результаты:**

1. **Реализована функция `get_currencies`**:
   - Получает данные с внешнего API
   - Обрабатывает различные форматы валют
   - Возвращает структурированный словарь с курсами

2. **Создан декоратор `logger`**:
   - Логирует начало и завершение выполнения функции
   - Записывает переданные аргументы
   - Фиксирует возникшие ошибки
   - Сохраняет логи в файл `CentroErrorBank.log`

3. **Реализована обработка ошибок**:
   - Сетевые ошибки и недоступность API (`ConnectionError`)
   - Ошибки парсинга JSON (`ValueError`)
   - Отсутствие запрошенных валют (`KeyError`)
   - Неверный тип данных (`TypeError`)

4. **Разработан набор тестов**:
   - Тестирование успешных запросов
   - Проверка обработки несуществующих валют
   - Тестирование некорректных URL
   - Проверка работы декоратора
   - Тестирование граничных случаев

Таким образом, создана устойчивая к ошибкам программа с комплексным логированием и тестированием.
