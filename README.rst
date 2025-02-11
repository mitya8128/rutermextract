=============
rutermextract
=============

Библиотека для извлечения ключевых слов из текстов на русском языке. Для морфологического анализа используется `pymorphy2 <http://pymorphy2.readthedocs.org/en/latest/>`_. Разработка вдохновлена библиотекой `topia.termextract <https://pypi.python.org/pypi/topia.termextract/>`_, которая делает то же самое с англоязычными текстами.

Установка
=========

    pip install git+https://github.com/mitya8128/rutermextract        

Использование
=============

    >>> from rutermextract import TermExtractor
    >>> term_extractor = TermExtractor()
    >>> text = u'Съешь ещё этих мягких французских булок да выпей же чаю.'
    >>> for term in term_extractor(text):
    >>>    print term.normalized, term.count
    мягкие французские булки 1
    чай 1

Извлеченные ключевые слова приводятся в нормальную форму и упорядочиваются от более важных к менее важным.

Возможные применения
====================

* Автоматическое присвоение тегов.

* Вычисление похожести текстов на основе извлеченных ключевых слов.

Вызов библиотеки
================

Аргументы при вызове экземпляра класса `TermExtractor`:

* ``text`` — текст.

* *(опционально)* ``limit`` — максимальное количество извлечённых ключевых слов. По умолчанию ограничения нет.

* *(опционально)* ``nested`` — ``True``, если нужно ивзлекать ключевые слова, лежащие внутри других ключевых слов. Например, вместе с «функциональный язык программирования» извлекаются «язык программирования» и «программирование».

* *(опционально)* ``weight`` — функция для вычисления веса извлеченного ключевого слова. В нее передается объект типа ``Term``. По умолчанию ключевые слова упорядочиваются по количеству употреблений, затем по количеству слов.

* *(опционально)* ``strings`` — ``True``, если результатом вызова должен быть список нормализованных строк. По умолчанию возвращается список объектов типа ``Term``.

`Term` имеет следующие свойства: ``words`` (список слов), ``word_count`` (количество слов), ``normalized`` (тёрм в виде нормализованной строки), ``count`` — количество употреблений.

Пример использования функции веса
---------------------------------

    >>> from rutermextract import TermExtractor
    >>> term_extractor = TermExtractor()
    >>> text = ...
    >>> idf = ...  # Словарь соответствия ключевых слов их обратным частотам.
    >>> terms = term_extractor(text, weight=lambda term: idf.get(term.normalized, 1.0) * term.count)

Зависимости
===========

* Python 2.6+ или 3.3+.

* `pymorphy2 <http://pymorphy2.readthedocs.org/en/latest/>`_.

* `enum34 <https://pypi.python.org/pypi/enum34>`_ (для версий ниже 3.4).

Качество работы
===============

Библиотека извлекает ключевые слова на основе заранее заданных правил. К сожалению, на данный момент это единственный возможный вариант, поскольку для русского языка не существует открытого синтаксического корпуса, который можно использовать для обучения синтаксических моделей.

Основные проблемы:

1. Неполные правила. Например, сейчас не извлекаются ключевые слова, содержащие предлоги («вор в законе», «сосед по парте»). Эта проблема может решаться при дальнейшем развитии библиотеки, но правил, покрывающих все случаи всё равно быть не может.

2. Неоднозначность при морфологическом разборе. Сейчас она разрешается выбором наиболее вероятного варианта, что в некоторых случаях неверно. Проблема может проявляться как при извлечении ключевых слов, так и при их нормализации. (Например, из заголовка «Мальчика назвали в честь нападающего футбольного клуба» будет извлечена фраза «нападающий футбольный клуб».)

3. Ложные ключевые слова. Некоторые извлеченные фразы могут не являться на самом деле ключевыми. Размера текста не всегда бывает достаточно для того, чтобы отличить важные для текста слова от неважных, основываясь только на количестве употреблений. Поэтому необходимо использовать сторонние модели (например, tf-idf) для определения важности ключевых слов.

Обратная связь
==============

Буду рад услышать предложения по улучшению или сообщения о багах в `issues <https://github.com/igor-shevchenko/rutermextract/issues>`_ или `по почте <mailto:mail@igorshevchenko.ru>`_.

Лицензия
========

MIT
