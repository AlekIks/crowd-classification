<h3 style="text-align: center;">Бинарная и мультиклассовая классификация на крауде</h3>

Домашнее задание №4 по дисциплине "Сбор данных с помощью краудсорсинга"  

**Студент:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Погребная Александра  
**Группа:**  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  794, кафедра АД МФТИ

## Задание <a class="anchor" id="first-bullet"></a>

<img src="https://www.dropbox.com/s/qtewugawp3wdb2r/2020-10-22_03-06-01.png?raw=1" width="500px" alt="">

## Отчёт

*Если захотите воспроизвести результаты у себя, то можете скачать обработанные данные здесь:


```python
# !wget -O hw4.zip https://www.dropbox.com/s/gjxr7lw644l2mxr/hw4.zip?dl=0
# !unzip hw4.zip
```


```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd

from collections import Counter
```

**1. Подготовка проектов**

**full** - первый лист таблицы из условия, но без правильных ответов, это выборка, которую нужно классифицировать.  
**exam** - второй лист таблицы.

Для мультиклассовой классификации создала проект с обучением, экзаменом и основным пулом: в качестве данных взяла просто full и exam с GOLDEN (и HINT) без ответов. Чтобы подготовить данные, использовала Excel.  
Для бинарной классификации решила сделать один проект, в котором будет по три пула на каждый дорожный знак, т.е.:
+ 5 обучающих пулов: по 10 обучающих из exam в задании
+ 5 экзаменационных: по 20 контрольных из exam в задании - здесь произошел fail, но об этом позже
+ 5 основных: по 40 тестовых из full и по 4 контрольных из exam в задании

Код ниже генерирует данные для этих пулов


```python
# exam = pd.read_csv("./exam.tsv", sep='\t', header=0, encoding="UTF-8")
# full = pd.read_csv("./full.tsv", sep='\t', header=0, encoding="UTF-8")
# print("train/exam columns: {}".format(exam.columns))
# print("test columns: {}".format(full.columns))
# exam
```


```python
signs = {
    "pedestrian": "Пешеходный переход",
    "bus_stop":   "Остановка общественного транспорта",
    "no_stop":    "Остановка запрещена",
    "main_road":  "Главная дорога",
    "give_way":   "Уступи дорогу"
}
```

Код для создания данных для бинарной классификации:


```python
# for sign in signs.keys():
#     # tsv для итогового пула
#     sign_full_label = [signs[sign]] * len(full['INPUT:image'])
#     sign_full_data = full
#     sign_full_data["INPUT:label"] = sign_full_label
#     sign_full_data.to_csv(sign+"_full.tsv", index=False, sep='\t', encoding="UTF-8")

#     # tsv для экзамена и обучения
#     hints = []
#     sign_golden = []
#     for ans in exam["ans"]:
#         if ans == sign:
#             hints.append("Да")
#             sign_golden.append("true")
#         else:
#             hints.append("Нет")
#             sign_golden.append("false")
#     sign_exam = exam.drop(columns=["ans"])

#     sign_exam_label = [signs[sign]] * len(exam)
#     sign_exam["INPUT:label"] = sign_exam_label

#     # экзамен
#     sign_exam["GOLDEN:result"] = sign_golden
#     sign_exam.to_csv(sign+"_exam.tsv", index=False, sep='\t', encoding="UTF-8")

#     # обучение
#     sign_exam["HINT:text"] = hints
#     sign_exam.to_csv(sign+"_train.tsv", index=False, sep='\t', encoding="UTF-8")
```

Получившиеся проекты для классификации:
+ **мультиклассовой:** https://toloka.yandex.ru/requester/project/42164
+ **бинарной:** https://toloka.yandex.ru/requester/project/42359

Чек-лист:  
✅ создала проекты  
✅ написала инструкции  
✅ создала пулы  
✅ создала ханипоты  
✅ настроила контроль качества  
✅ запустила проекты  
✅ получила результаты  

**2. Обработка результатов**

**2.1. Стоимость**

Расходы мультиклассового проекта:     0.2 (+ 0.1) = **0.3\\$**  
Стоимость одной картинки:   0.3 / 250 = **0.12 цента**

Расходы бинарного проекта: (0.14 (+ 0.07))\*4 + 0.15 (+ 0.07) = **1.06\\$**  
Стоимость одной картинки: 1.06 / 250 = **0.424 цента**

Вывод: бинарная классификация получилась примерно в 3.5 раза дороже.

**2.2. Отбор исполнителей**

Мною во всех экзаменационных пулах была допущена ошибка - я не добавила следующий блок в контроль качества:

<img src="https://i.ibb.co/XZsLG6Q/bug.png" alt="bug" border="0"></a> - его не было в инструкции к заданию, но добавлять его осмысленно.

Я хотела, чтобы во всех экзаменах было по 20 заданий, а допуск к основному заданию открывался при 85% правильных ответов - это 17/20. Однако теперь толокеры сдавали от 1 до 3 страниц заданий, т.е. от 20 до 60, причем навык назначался только по результатам последней сданной страницы. Заметила это только при обработке результатов.

Посмотрим, какая доля исполнителей успешно проходила экзамены.  
Раздел "Статистика" в Толоке в мультиклассовом экзамене показывает качество выше 0.9:

<img src="https://i.ibb.co/8jhWwxC/image.png" alt="" border="0"></a>

В бинарных пулах по-разному, например, в экзамене знака "Остановка запрещена" качество выше 0.98:

<img src="https://i.ibb.co/GMfCsnS/image.png" border="0"></a>

А в экзамене "Уступи дорогу" есть и 0.72:

<img src="https://i.ibb.co/v40WB7V/image.png" alt="" border="0"></a>

Но это усредненные значения. Посмотрим подробнее, для этого используем результаты всех экзаменов.  

Проще было сразу скачать данные из раздела "Навыки" в Толоке. В моем случае это даст посмотреть на тех, кто сдал экзамен, но не на тех, кто действительно отвечал на все вопросы хорошо, поскольку у меня "Навык" учитывает лишь последнюю экзаменационную страницу.  
Так что посмотрим на все ответы исполнителей и проверим: возможно кто-то из них случайно хорошо разметил последнюю страницу экзамена, но плохо справился с первыми. Тогда его разметку основного задания не стоит рассматривать.

Сначала бинарная классификация:


```python
exam_binary_files = ["./ass_exam_" + sign + ".tsv" for sign in signs.keys()]

binary_exams = pd.concat((pd.read_csv(f, sep='\t', header=0, encoding="UTF-8") 
                          for f in exam_binary_files))
binary_exams
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>INPUT:label</th>
      <th>OUTPUT:result</th>
      <th>GOLDEN:result</th>
      <th>HINT:text</th>
      <th>ASSIGNMENT:worker_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/e127774...</td>
      <td>Пешеходный переход</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>70c6056722e434ee9c0b68267181b07c</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/628c77c...</td>
      <td>Пешеходный переход</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>70c6056722e434ee9c0b68267181b07c</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/28873dc...</td>
      <td>Пешеходный переход</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>70c6056722e434ee9c0b68267181b07c</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/3c267a7...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>True</td>
      <td>NaN</td>
      <td>70c6056722e434ee9c0b68267181b07c</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/8993c14...</td>
      <td>Пешеходный переход</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>70c6056722e434ee9c0b68267181b07c</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1375</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/5d4d302...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>69e918870fcea9f899e616e1845b59c4</td>
    </tr>
    <tr>
      <th>1376</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/8993c14...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>69e918870fcea9f899e616e1845b59c4</td>
    </tr>
    <tr>
      <th>1377</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/3fee0d6...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>69e918870fcea9f899e616e1845b59c4</td>
    </tr>
    <tr>
      <th>1378</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/5e8b4f5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>69e918870fcea9f899e616e1845b59c4</td>
    </tr>
    <tr>
      <th>1379</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/f987cd9...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>69e918870fcea9f899e616e1845b59c4</td>
    </tr>
  </tbody>
</table>
<p>6260 rows × 6 columns</p>
</div>




```python
binary_exams["result"] = (binary_exams["OUTPUT:result"] == binary_exams["GOLDEN:result"])
skills = binary_exams[["result", "INPUT:label", "ASSIGNMENT:worker_id"]].\
                groupby(["INPUT:label", "ASSIGNMENT:worker_id"]).agg(['mean'])
skills
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th>result</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>mean</th>
    </tr>
    <tr>
      <th>INPUT:label</th>
      <th>ASSIGNMENT:worker_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">Главная дорога</th>
      <th>03c08ada934af11ff8561cc39fe15f7c</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>08c023f84fe4e4547d1337f389b61bc9</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1bcfb516f4d28bac06426a9b2707f772</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>2e37aab3acb35fb0450ba86bb46a3e13</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>327471a9a9745898fc1200f83208b763</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">Уступи дорогу</th>
      <th>d7cf3c255e94a112e71824aca72e48e1</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>d9b37fda2b4de2f891735bbb0b371582</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>dd7c7399d42f00609410069f5b4c2351</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>faa058810c8a490660ab6c0442679995</th>
      <td>0.983333</td>
    </tr>
    <tr>
      <th>fb1b6e26cd8574da4dc61681a396f47c</th>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
<p>121 rows × 1 columns</p>
</div>




```python
good_res_num = (skills["result"]["mean"] >= 0.85).sum()
res_num = len(skills["result"]["mean"])

print("Доля исполнителей, успешно решавших бинарный экзамен: {:.4f}".format(good_res_num / res_num))
```

    Доля исполнителей, успешно решавших бинарный экзамен: 0.9917



```python
print(res_num - good_res_num)
```

    1



```python
skills[skills["result"]["mean"] < 0.85]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th>result</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>mean</th>
    </tr>
    <tr>
      <th>INPUT:label</th>
      <th>ASSIGNMENT:worker_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Уступи дорогу</th>
      <th>1bcfb516f4d28bac06426a9b2707f772</th>
      <td>0.583333</td>
    </tr>
  </tbody>
</table>
</div>




```python
bad_user = binary_exams.loc[binary_exams["ASSIGNMENT:worker_id"] == "1bcfb516f4d28bac06426a9b2707f772"]
bad_user = bad_user.loc[bad_user["INPUT:label"] == "Уступи дорогу"]
print("Не сдавший человек сдавал {} заданий, ответил верно на {} из них.".\
      format(len(bad_user), bad_user["result"].sum()))
```

    Не сдавший человек сдавал 60 заданий, ответил верно на 35 из них.



```python
print(res_num)
```

    121


Не прошел всего один исполнитель из 121, как раз тот, из-за которого просело качество в экзамене на "Уступи дорогу". Это не бот, потому что он же сдавал экзамены на пешеходные переходы, главные дороги и остановки на навыки 90, 100 и 100 соответственно:

<img src="https://i.ibb.co/s9nG8K2/2020-10-23-06-11-41.png" border="0"></a>

Видимо просто устал. Навык у него равен 60, поэтому в экзамен он в любом случае не попал. Таким образ, не смотря на то, что проходной навык учитывал лишь последнюю сданную страницу, в основной пул все равно попали только пользователи, успешно прорешавшие $\geq$ 85% заданий.

Теперь разберемся с мультиклассовыми экзаменами:


```python
multi_exams = pd.read_csv("ass_exam_multi.tsv", sep='\t', header=0, encoding="UTF-8") 

multi_exams
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>OUTPUT:result</th>
      <th>GOLDEN:result</th>
      <th>HINT:text</th>
      <th>ASSIGNMENT:worker_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/2835909...</td>
      <td>give_way</td>
      <td>give_way</td>
      <td>NaN</td>
      <td>34420b3fc992d699fd0c8dd441c8a909</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/aae1ec5...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>NaN</td>
      <td>34420b3fc992d699fd0c8dd441c8a909</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/e127774...</td>
      <td>main_road</td>
      <td>main_road</td>
      <td>NaN</td>
      <td>34420b3fc992d699fd0c8dd441c8a909</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/a1ce075...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>NaN</td>
      <td>34420b3fc992d699fd0c8dd441c8a909</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/bcafe88...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>NaN</td>
      <td>34420b3fc992d699fd0c8dd441c8a909</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3055</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/1ae46a8...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>NaN</td>
      <td>dc0a4cd339525cac8ab9004ddeb83b03</td>
    </tr>
    <tr>
      <th>3056</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/12c2b63...</td>
      <td>no_stop</td>
      <td>no_stop</td>
      <td>NaN</td>
      <td>dc0a4cd339525cac8ab9004ddeb83b03</td>
    </tr>
    <tr>
      <th>3057</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/3482bca...</td>
      <td>main_road</td>
      <td>main_road</td>
      <td>NaN</td>
      <td>dc0a4cd339525cac8ab9004ddeb83b03</td>
    </tr>
    <tr>
      <th>3058</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/1f87fcf...</td>
      <td>give_way</td>
      <td>give_way</td>
      <td>NaN</td>
      <td>dc0a4cd339525cac8ab9004ddeb83b03</td>
    </tr>
    <tr>
      <th>3059</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0567c2f...</td>
      <td>no_stop</td>
      <td>no_stop</td>
      <td>NaN</td>
      <td>dc0a4cd339525cac8ab9004ddeb83b03</td>
    </tr>
  </tbody>
</table>
<p>3060 rows × 5 columns</p>
</div>




```python
multi_exams["result"] = (multi_exams["OUTPUT:result"] == multi_exams["GOLDEN:result"])
multi_skills = multi_exams[["result", "ASSIGNMENT:worker_id"]].\
                    groupby(["ASSIGNMENT:worker_id"]).agg(['mean'])
multi_skills
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>result</th>
    </tr>
    <tr>
      <th></th>
      <th>mean</th>
    </tr>
    <tr>
      <th>ASSIGNMENT:worker_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>018b5e6241e1cd4628b186f06db9cd2c</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>02b8b4a6ca4b53330207ca942f995470</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>046bcc85c5e7a868b120479416023696</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>0ba5f5086dd7a3e70af43c2662ddcbaf</th>
      <td>0.966667</td>
    </tr>
    <tr>
      <th>0c3c3c62bb6141cd63a6edf9bbbaa0ee</th>
      <td>0.950000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>f139783182cfbdcbf214cd818d249535</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>faa058810c8a490660ab6c0442679995</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>fb7e58ab3aafe479b776606fb3921b38</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>fcd9536abbd2870f1f2352c284622751</th>
      <td>0.975000</td>
    </tr>
    <tr>
      <th>febd51b6559766048e3893d4a11fb8cb</th>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
<p>68 rows × 1 columns</p>
</div>




```python
multi_good_res_num = (multi_skills["result"]["mean"] > 0.85).sum()
multi_res_num = len(multi_skills["result"]["mean"])

print("Доля исполнителей, успешно прошедших мультиклассовый экзамен: {:.4f}".\
      format(multi_good_res_num / multi_res_num))
```

    Доля исполнителей, успешно прошедших мультиклассовый экзамен: 1.0000



```python
print(multi_res_num)
```

    68


Итого доля успешно отобранных после экзамена исполнителей:
+ бинарная: **0.9917**
+ мультиклассовая: **1**

Вывод: в бинарной классификации отсеялось больше людей. 

Замечу, что хотя в бинарном проекте было в 5 раз больше пулов, различных толокеров, сдавших экзамен, в нем всего в 2 раза больше. Это может быть связано со временем выкладывания заданий - бинарные пулы я запускала поздно ночью, было мало активных пользователей.

**2.3. Качество разметки**

Теперь посмотрим на accuracy в основных пулах. Верные ответы:


```python
golden_res = pd.read_csv("golden_res.tsv", sep='\t', header=0, encoding="UTF-8").sort_values('INPUT:image')

golden_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>GOLDEN:result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>126</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>139</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01d1f01...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>100</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/020e75c...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>63</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/023c10e...</td>
      <td>bus_stop</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>187</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/f90a2fd...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>210</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fa92e0b...</td>
      <td>give_way</td>
    </tr>
    <tr>
      <th>172</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fc65477...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>155</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>10</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>pedestrian</td>
    </tr>
  </tbody>
</table>
<p>250 rows × 2 columns</p>
</div>




```python
binary_files = ["./ass_" + sign + ".tsv" for sign in signs.keys()]

binary_res = pd.concat((pd.read_csv(f, sep='\t', header=0, encoding="UTF-8") 
                          for f in binary_files)).sort_values(['INPUT:image', 'INPUT:label'])

assert binary_res["GOLDEN:result"].isna().count() == len(binary_res), "Попали контрольные вопросы"
binary_res = binary_res.drop(columns=["HINT:text", "GOLDEN:result"])
binary_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>INPUT:label</th>
      <th>OUTPUT:result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>446</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
    </tr>
    <tr>
      <th>487</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
    </tr>
    <tr>
      <th>78</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
    </tr>
    <tr>
      <th>172</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
    </tr>
    <tr>
      <th>171</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>427</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
    </tr>
    <tr>
      <th>30</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
    </tr>
    <tr>
      <th>87</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
    </tr>
    <tr>
      <th>393</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
    </tr>
    <tr>
      <th>423</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>2785 rows × 3 columns</p>
</div>




```python
binary_res = pd.merge(binary_res, golden_res, how="right", on=["INPUT:image"])
binary_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>INPUT:label</th>
      <th>OUTPUT:result</th>
      <th>GOLDEN:result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2496</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>2497</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>2498</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>2499</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>2500</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>pedestrian</td>
    </tr>
  </tbody>
</table>
<p>2501 rows × 4 columns</p>
</div>




```python
# приводим label и result к одному виду
binary_res["GOLDEN:result"] = binary_res["GOLDEN:result"].apply(lambda x: signs[x])

# если golden и label совпадают, то result должен быть true, иначе - false
# это бинарный golden результат
binary_res["result"] = (binary_res["GOLDEN:result"] == binary_res["INPUT:label"])
binary_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>INPUT:label</th>
      <th>OUTPUT:result</th>
      <th>GOLDEN:result</th>
      <th>result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2496</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2497</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>Пешеходный переход</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2498</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>Пешеходный переход</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2499</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2500</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>Пешеходный переход</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>2501 rows × 5 columns</p>
</div>



В задании необходимо сделать итоговый tsv для бинарной классификации:
1. **image** - URL картинки
2. **test_sign** - дорожный знак, которые определяли толокеры
3. **result** - итоговая оценка
4. **overlay** - перекрытие
5. **perc_for_result** - доля оценок из перекрытия за итоговую оценку


```python
binary_final = pd.DataFrame(columns=["image", "test_sign", "result", "overlay", "perc_for_result"])

# URL изображений и проверяемые знаки
binary_groups = binary_res.groupby(["INPUT:image", "INPUT:label"])

images_labes = np.array(list(binary_groups.groups.keys()))
binary_final["image"] = images_labes[:, 0]
binary_final["test_sign"] = images_labes[:, 1]

# перекрытие
binary_final["overlay"] = binary_groups.count()["OUTPUT:result"].to_numpy()

# результат по проверяемому знаку
binary_groups = binary_res[["INPUT:image", "INPUT:label", "OUTPUT:result"]].\
                    groupby(["INPUT:image", "INPUT:label"])

# если больше половины выбранных одинаковы - то это True
def chosen_result(col):
    trues = np.sum(col)
    alls = len(col)
    if trues >= alls // 2:
        return True, trues / alls
    return False, (alls - trues) / alls
    
answers = binary_groups.agg(chosen_result)["OUTPUT:result"].to_numpy()
answers = list(zip(*answers))

binary_final["result"] = answers[0]
binary_final["perc_for_result"] = answers[1]

# pd.merge(binary_final, golden
binary_final
```

    /home/inari/anaconda3/lib/python3.7/site-packages/ipykernel_launcher.py:22: RuntimeWarning: invalid value encountered in long_scalars





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>test_sign</th>
      <th>result</th>
      <th>overlay</th>
      <th>perc_for_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1245</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Главная дорога</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1246</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Остановка запрещена</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1247</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Остановка общественного транспорта</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1248</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>True</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1249</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Уступи дорогу</td>
      <td>False</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>1250 rows × 5 columns</p>
</div>



Сохраню файл с результатами:


```python
binary_final.to_csv("./binary.tsv", sep='\t', encoding="UTF-8")
```

Посмотрим на accuracy:


```python
golden_results =  binary_res[["INPUT:image", "INPUT:label", "result"]].\
                    groupby(["INPUT:image", "INPUT:label"]).agg(np.mean)["result"].to_numpy()
golden_results
```




    array([False, False, False, ..., False,  True, False])




```python
accuracy = (golden_results == binary_final["result"]).sum() / len(golden_results)

print("Accuracy для бинарной классификации: {}".format(accuracy))
```

    Accuracy для бинарной классификации: 1.0


Круто, вообще без ошибок 👍  
Посмотрим, когда у исполнителей вообще были разногласия в ответах - если были. У меня динамическое перекрытие, т.е. если два исполнителя ответили по-разному, задание давалось третьему:


```python
(binary_final["overlay"] == 3).sum()
```




    1



Всего один неоднозначный результат


```python
binary_final.loc[binary_final["overlay"] == 3]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>test_sign</th>
      <th>result</th>
      <th>overlay</th>
      <th>perc_for_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>556</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/6dfa257...</td>
      <td>Остановка запрещена</td>
      <td>True</td>
      <td>3</td>
      <td>0.666667</td>
    </tr>
  </tbody>
</table>
</div>



Один из исполнителей о вот этом фото ответил, что это не знак "Остановка запрещена":

<img src="http://sdcimages.s3.yandex.net/nirvana/6dfa257f-d6ea-4c9e-b15f-d986d2028e90"> - вероятно просто мисклик

Теперь посмотрим на качество мультиклассовой классификации. Это просто скачанные ответы толокеров:


```python
multi_res = pd.read_csv("ass_multi.tsv", sep='\t', header=0, encoding="UTF-8").sort_values("INPUT:image")
multi_res = multi_res.drop(columns=["GOLDEN:result", "HINT:text"])
multi_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>OUTPUT:result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>150</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>118</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>402</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>368</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>496</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01d1f01...</td>
      <td>no_stop</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>510</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fc65477...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>308</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>273</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>main_road</td>
    </tr>
    <tr>
      <th>26</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>pedestrian</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>pedestrian</td>
    </tr>
  </tbody>
</table>
<p>520 rows × 2 columns</p>
</div>



А это результаты разметки, агрегированные по навыку пользователей:


```python
multi_agg = pd.read_csv("agg_multi.tsv", sep='\t', header=0, encoding="UTF-8").sort_values("INPUT:image")
multi_agg = multi_agg.loc[multi_agg["GOLDEN:result"].isnull()]\
                [["INPUT:image", "OUTPUT:result", "CONFIDENCE:result"]]

multi_agg = multi_agg.rename(columns={"OUTPUT:result": "aggregated_result"})

# соединяю с таблицей выше
multi_res = pd.merge(multi_res, multi_agg, how="right", on=["INPUT:image"])
# если агрегированный ответ совпал с ответом пользователя, то True
multi_res["same_output_res"] = (multi_res["aggregated_result"] == multi_res["OUTPUT:result"])
multi_res
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>INPUT:image</th>
      <th>OUTPUT:result</th>
      <th>aggregated_result</th>
      <th>CONFIDENCE:result</th>
      <th>same_output_res</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>no_stop</td>
      <td>no_stop</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>no_stop</td>
      <td>no_stop</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01d1f01...</td>
      <td>no_stop</td>
      <td>no_stop</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>495</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fc65477...</td>
      <td>main_road</td>
      <td>main_road</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>496</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>main_road</td>
      <td>main_road</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>497</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>main_road</td>
      <td>main_road</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>498</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
    <tr>
      <th>499</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>pedestrian</td>
      <td>pedestrian</td>
      <td>67.71%</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 5 columns</p>
</div>



В задании необходимо сделать итоговый tsv для мультиклассовой классификации:
1. **image** - URL картинки
2. **result** - итоговая оценка
3. **overlay** - перекрытие
4. **perc_for_result** - доля оценок из перекрытия за итоговую оценку


```python
multi_final = pd.DataFrame(columns=["image", "result", "overlay", "perc_for_result"])

# URL изображений
multi_groups = multi_res.groupby(["INPUT:image"])
multi_final["image"] = np.array(list(multi_groups.groups.keys()))

# # перекрытие
multi_final["overlay"] = multi_groups.count()["OUTPUT:result"].to_numpy()

# результат, агрегированный по навыкам
multi_groups = multi_res[["INPUT:image", "aggregated_result"]].\
                    groupby(["INPUT:image"])


output_results = list(map(lambda x: signs[x[0]], multi_groups.first().to_numpy()))
multi_final["result"] = output_results

# доля оценок за итоговую оценку
multi_groups = multi_res[["INPUT:image", "same_output_res"]].\
                    groupby(["INPUT:image"])

def part_same_results(col):
    trues = np.sum(col)
    alls = len(col)
    return trues / alls
    
part = multi_groups.apply(part_same_results)["same_output_res"].to_numpy()
multi_final["perc_for_result"] = part

multi_final
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image</th>
      <th>result</th>
      <th>overlay</th>
      <th>perc_for_result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/0020aec...</td>
      <td>Пешеходный переход</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01a2e62...</td>
      <td>Остановка запрещена</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/01d1f01...</td>
      <td>Остановка запрещена</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/020e75c...</td>
      <td>Остановка запрещена</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/023c10e...</td>
      <td>Остановка общественного транспорта</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>245</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/f90a2fd...</td>
      <td>Главная дорога</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>246</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fa92e0b...</td>
      <td>Уступи дорогу</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>247</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/fc65477...</td>
      <td>Главная дорога</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>248</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ff1aab5...</td>
      <td>Главная дорога</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>249</th>
      <td>http://sdcimages.s3.yandex.net/nirvana/ffb1ff5...</td>
      <td>Пешеходный переход</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>250 rows × 4 columns</p>
</div>



Сохраню файл с результатами:


```python
multi_final.to_csv("./multi.tsv", sep='\t', encoding="UTF-8")
```

Посмотрим на accuracy:


```python
golden_results = list(map(lambda x: signs[x], golden_res["GOLDEN:result"]))
golden_results[:10]
```




    ['Пешеходный переход',
     'Остановка запрещена',
     'Остановка запрещена',
     'Остановка запрещена',
     'Остановка общественного транспорта',
     'Главная дорога',
     'Остановка общественного транспорта',
     'Уступи дорогу',
     'Пешеходный переход',
     'Остановка общественного транспорта']




```python
accuracy = (golden_results == multi_final["result"]).sum() / len(golden_results)

print("Accuracy для мультиклассовой классификации: {}".format(accuracy))
```

    Accuracy для мультиклассовой классификации: 1.0


Круто, здесь тоже без ошибок 👍  
В этом пуле было настроено такое же динамическое перекрытие, как в бинарных. Посмотрим, были ли здесь разные ответы:


```python
(multi_final["overlay"] == 3).sum()
```




    0



Вау, без разногласий!

Вывод: в обоих проектах итоговое 100% качество разметки.

**2.4. Скорость разметки**

Рассмотрим затраченое время на основной пул.

Мультиклассовая классификация:
- среднее время выполнения страницы заданий: 48 сек
- в среднем на одно фото: 48 / 26 = **1.85 сек**
- общее время выполнения: 11 мин 19 сек
- в общем на одно фото: (11 * 60 + 19) / 20 / 26 = **1.31 сек**

---

В бинарной классификации можно считать, что задания в пулах одинаковы, и пулы запускаются параллельно, поэтому логично брать максимальное время из всех.

Бинарная классификация:
- среднее время выполнения страницы заданий: max(55, 77, 59, 57, 96) = 96 сек
- в среднем на одно фото: 96 / 44 = **2.18 сек**
- общее время выполнения: max(12 мин 3 сек, 14 мин 24 сек, 12 мин 21 сек, 7 мин 20 сек, 11 мин 8 сек) = 14 мин 24 сек
- в общем время на одно фото с учетом простоя: (11 * 60 + 19) / 14 / 44 = **1.4 сек**

Предварительный вывод: мультиклассовая классификация выигрывает по скорости.

**Summary**

Бинарная классификация проще многоклассовой. Я ждала, что она:
+ проиграет по стоимости из-за 5 пулов вместо одного;
+ меньше зареджектит исполнителей на экзамене, потому что задание проще;
+ даст лучшее качество и скорость по той же причине.

На практике многоклассовая классификация показала лучшие результаты по всем пунктам. 

Насчет **отбора исполнителей**: думаю, можно считать исполнителя, не сдавшего бинарный экзамен, выбросом. В таком случае оба проекта пропустили 100% людей, сдававших экзамен.

Думаю, **качество** одинаково отличное, т.к. задание разметки дорожных знаков само по себе простое: как бинарное, так и мультиклассовое. Кроме того, пять классов нормально воспринимаются исполнителями - это относительно небольшое количество.

Насчет **стоимости**. Пулов в 5 раз больше, но проигрыш бинарной классификации всего в 3.5 раза - за счет бОльшего количества заданий на странице. Качество от этого не просело, хотя думаю, был один мисклик. Можно было еще уменьшить эту разницу в стоимости, если бы я:
1. сделала 4 пула вместо 5, а последний знак определяла по остаточному признаку;
2. в каждый следующий пул дорожного знака давала бы только те фото, про которые на всех предыдущих знаках сказали "нет".

Это возможно просадило бы качество и не дало бы запускать пулы параллельно для экономии реального времени, но стоимость одного фото ориентировочно была бы равна: ((0.14 + 0.035 + 0.07 + 0.105) + (0.07 \* 4)) / 250 = 0.63 / 250 = **0.252 цента**, что всего в 2.1 раза больше, чем у мультиклассовой классификации.

Насчет **скорости**. На разных дорожных знаках в бинарной классификации она разная. Это может быть связано с тем, что некоторые знаки различать проще остальных. Например, минимальное время выполнения страницы у ярко-желтого Пешеходного перехода, который цепляет глаз. На втором месте Главная дорога, тоже ярко-желтая. Затем Остановка запрещена - красно-синий знак, часто на виду. И замыкают Остановка общественного транспорта и Уступи дорогу.  
В среднем знаки дают время на фото: mean(55, 77, 59, 57, 96) / 44 = 68.8 / 44 = **1.56 сек** - а вот это уже быстрее, чем мультиклассовая классификация.
