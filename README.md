# Проект по анализу данных
#### Подготовили Березин Даниил и Мартишевич Владислав
#### Доступные файлы:
* parser_final.ipynb - парсер данных
* tracks_spotify.csv - итоговая база данных
* EDA.ipynb - визуализация и создание новых признаков
* df_spotify.csv - преобразованная база данных
* hypotheses.ipynb - проверка гипотез
* ML.ipynb - машинное обучение
#### Структура проекта:
#### Шаг 1. Сбор данных
Для получения итоговой базы данных было решено спарсить сайт альбомного чарта "Billboard 200" и далее, при помощи подключения к API стриминговой площадки Spotify, спарсить следующие данные про каждую песню из каждого альбома:
*   **name** - название песни
*   **duration** - продолжительность песни
*   **explicit** - содержание матов в тексте песни (True - содержатся, False - нет)
*   **track number** - номер трека в альбоме
*   **countries** - количество стран, в которых можно прослушать эту песню
* **popularity** - популярность песни (относительная величина, основанная на количестве прослушиваний)
* **danceability** - переменная, которая показывает, насколько песня подходит для танцев (0 - наименее подходит, 1 - наиболее подходит)
* **energy** - мера интенсивности и активности песни (0 - наименее интенсивная, 1 - наиболее интенсивная)
* **key** - закодированная переменная тональности песни (например, 0 = C, 1 = C♯/D♭, 2 = D, и так далее)
* **loudness** - уровень громкости песни в децибелах
* **mode** - лад (1 - мажорный, 0 - минорный)
* **speechiness** - доля речетатива в песне
* **acousticness** - мера того, насколько песня акустическая
* **instrumentals** - доля инструментала (частей без вокала) в песне
* **liveness** - мера присутствия бэк-вокала в песне
* **valence** - мера музыкальной позитивности трека (0 - самая непозитивная, 1 - самая позитивная)
* **tempo** - темп песни в битах в минуту
#### Шаг 2. Предобработка данных
Нам повезло, и данные оказались практически без пропусков. Была только одна запись, для которой большая часть признаков была заполнена пропусками. Было решено удалить эту запись, поскольку заполнение этих данных нулями/средними/медианами не отражало бы какой-либо информации о песне.
#### Шаг 3. Визуализация данных
Во время визуализации были построены классические графики, помогающие более наглядно проанализировать полученные графики: гистограммы и диаграммы рассеивания, а также их совместная версия. Также была построена heatmap, визуализирующая корреляции признаков.
#### Шаг 4. Создание новых признаков
В процессе построения визуализаций незначимые признаки отбрасывались, а также создавались новые. Так, признаки instrumentalness, countries, acousticness, liveness, key, mode не включались в итоговый датасет в силу отсутствия какой-либо визуальной связи с популярностью песни. Были созданы новые признаки: loud-energy - комбинация признаков loudness и energy, и dance-valence - комбинация признаков danceability и valence. Данные признаки создавались из предпосылок наличия прямой взаимосвязи между ними, что было продемонстрировано на графиках. Также на основе названий песен был создан признак feat - это показатель того, является ли песня сольной или исполняется несколькими людьми.
#### Шаг 5. Проверка гипотез
Во время работы с данными у нас возникли следующие гипотезы, которые мы решили проверить:
* **Популярность песен с матами в среднем больше, чем популярность песен без них.** \
Эта гипотеза была проверена классическим $t$-тестом с односторонней альтративной, и на уровне значимости 5% можно утверждать, что песни с матами более популярны.
* **Между номером трека в альбоме и популярностью есть отрицательная зависимость.** \
Эта гипотеза проверялась с помощью перестановочного теста, и на уровне значимости 5% наши догадки подтвердились - можно говорить о наличии отрицательной взаимосвязи (корреляция отрицательная).
* **Популярность треков, записанных несколькими артистами, в среднем больше, чем популярность сольных треков.** \
Эта гипотеза проверялась с помощью наивного бутстрапа, и на уровне значимости 5% наши предположения не оправдались - гипотеза о равенстве не отверглась.
#### Шаг 6. Машинное обучение
Для машинного обучения была выбрана задача регрессии, где целевой переменной является популярность песни. Данные были стандартизированы с помощью StandardScaler. Рассматривались две разные модели: линейная регрессия, обученная на MAE(поскольку эта метрика менее чувствительна к выбросам, а из визуализации мы знаем, что у нас их достаточно) и решающее дерево. Для каждой из моделей были подобраны гиперпараметры: для регрессии - вес регуляризатора, для дерева - максимальная глубина и минимальное количество объектов на листе. Для анализа и сравнения качества полученных моделей мы использовали метрики MAE и MAPE. Сравнив эти показатели для обеих моделей, мы пришли к выводу, что они выдают примерно одинаковый результат. Самыми важными признаками для обеих моделей оказались громкость-энергичность и номер песни, а также наличие матов (для регрессии) и длительность песни (для дерева). При этом, смотря на метрики, нельзя назвать полученные модели хорошими и точными. Можно сделать вывод, что популярность в первую очередь зависит от других параметров, недоступных простому аналитику.
