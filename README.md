# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил:
- Казимиров Кирилл Евгеньевич
- НМТ-211506
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

### Оглавление:

- Данные о работе
- Цель работы
- Постановка задачи
- Задание 1
- Код реализации выполнения задания. Визуализация результатов выполнения
- Задание 2
- Код реализации выполнения задания. Визуализация результатов выполнения
- Задание 3
- Код реализации выполнения задания. Визуализация результатов выполнения
- Выводы

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Постановка задачи
Создать ML-агент и натренировать нейросеть, задача которой будет заключаться в управлении шаром. Задача шара заключается в том, чтобы, оставаясь на плоскости, находить кубик, смещающийся в заданном случайном диапазоне координат.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.
#### 1. Подготовим среду для работы Python

1.1. Для оптимизации работы Python создадим виртуальное окружение при помощи Conda Prompt, используя версию Python 3.6.1

![1](https://user-images.githubusercontent.com/114439735/201131740-f21dd2af-2a64-4cd9-8c49-a8efee8967c5.png)

1.3. Активируем виртуальное окружение

![2](https://user-images.githubusercontent.com/114439735/201132115-c7f2e8f3-51b1-4240-b6ba-d2fbf7db7bc1.png)

1.2. Установим в ml-agents

![3](https://user-images.githubusercontent.com/114439735/201132511-68a4a45c-2f92-43ed-b25e-a5b701bac5d6.png)

1.3. Установим в окружение PyTorch

![4](https://user-images.githubusercontent.com/114439735/201133068-1f2f2894-5431-4c65-abbe-463a5ed7b57c.png)

#### 2. Подготовим проект в Unity

2.1. Создадим пустой 3D-проект в Unity, добавим на сцену плоскость (Floor), куб (Target) и сферу (RollerAgent), для удобства откорректируем координаты объектов, назначим материалы и сгруппируем эти 3 объекта в объект TargetArea

![5](https://user-images.githubusercontent.com/114439735/201133632-205a3266-0810-4e0b-b3e0-621458c1ff8e.png)

2.2. Добавим в проект Unity MLAgents, в окне Package Manager получим (необходимые пакеты выделены красным)

![6](https://user-images.githubusercontent.com/114439735/201134336-b268951a-33e7-4c02-816d-38af3325c7ce.png)

2.3. Создадим и подключим к RollerAgent скрипт



## Задание 2
### Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.

- Перечисленные в этом туториале действия могут быть выполнены запуском на исполнение скрипт-файла, доступного [в репозитории](https://github.com/Den1sovDm1triy/hfss-scripting/blob/main/ScreatingSphereInAEDT.py).
- Для запуска скрипт-файла откройте Ansys Electronics Desktop. Перейдите во вкладку [Automation] - [Run Script] - [Выберите файл с именем ScreatingSphereInAEDT.py из репозитория].

```py

import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
oProject.Rename("C:/Users/denisov.dv/Documents/Ansoft/SphereDIffraction.aedt", True)
oProject.InsertDesign("HFSS", "HFSSDesign1", "HFSS Terminal Network", "")
oDesign = oProject.SetActiveDesign("HFSSDesign1")
oEditor = oDesign.SetActiveEditor("3D Modeler")
oEditor.CreateSphere(
	[
		"NAME:SphereParameters",
		"XCenter:="		, "0mm",
		"YCenter:="		, "0mm",
		"ZCenter:="		, "0mm",
		"Radius:="		, "1.0770329614269mm"
	], 
)

```

## Задание 3
### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.

- Перечисленные в этом туториале действия могут быть выполнены запуском на исполнение скрипт-файла, доступного [в репозитории](https://github.com/Den1sovDm1triy/hfss-scripting/blob/main/ScreatingSphereInAEDT.py).
- Для запуска скрипт-файла откройте Ansys Electronics Desktop. Перейдите во вкладку [Automation] - [Run Script] - [Выберите файл с именем ScreatingSphereInAEDT.py из репозитория].

```py

import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
oProject.Rename("C:/Users/denisov.dv/Documents/Ansoft/SphereDIffraction.aedt", True)
oProject.InsertDesign("HFSS", "HFSSDesign1", "HFSS Terminal Network", "")
oDesign = oProject.SetActiveDesign("HFSSDesign1")
oEditor = oDesign.SetActiveEditor("3D Modeler")
oEditor.CreateSphere(
	[
		"NAME:SphereParameters",
		"XCenter:="		, "0mm",
		"YCenter:="		, "0mm",
		"ZCenter:="		, "0mm",
		"Radius:="		, "1.0770329614269mm"
	], 
)

```

## Выводы

Абзац умных слов о том, что было сделано и что было узнано.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
