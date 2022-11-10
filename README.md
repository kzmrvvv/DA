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
#### 1. Подготовка среды для работы Python

1.1. Для оптимизации работы Python создадим виртуальное окружение при помощи Conda Prompt, используя версию Python 3.6.1

![1](https://user-images.githubusercontent.com/114439735/201131740-f21dd2af-2a64-4cd9-8c49-a8efee8967c5.png)

1.3. Активируем виртуальное окружение

![2](https://user-images.githubusercontent.com/114439735/201132115-c7f2e8f3-51b1-4240-b6ba-d2fbf7db7bc1.png)

1.2. Установим ml-agents

![3](https://user-images.githubusercontent.com/114439735/201132511-68a4a45c-2f92-43ed-b25e-a5b701bac5d6.png)

1.3. Установим PyTorch

![4](https://user-images.githubusercontent.com/114439735/201133068-1f2f2894-5431-4c65-abbe-463a5ed7b57c.png)

#### 2. Подготовка проекта в Unity

2.1. Создадим пустой 3D-проект в Unity, добавим на сцену плоскость (Floor), куб (Target) и сферу (RollerAgent), для удобства откорректируем координаты объектов, назначим материалы и сгруппируем эти 3 объекта в объект TargetArea

![5](https://user-images.githubusercontent.com/114439735/201133632-205a3266-0810-4e0b-b3e0-621458c1ff8e.png)

2.2. Добавим в проект Unity MLAgents, в окне Package Manager получим (необходимые пакеты выделены красным)

![6](https://user-images.githubusercontent.com/114439735/201134336-b268951a-33e7-4c02-816d-38af3325c7ce.png)

2.3. Создадим и подключим к RollerAgent скрипт

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```

2.4. Подключим к RollerAgent компоненты Decision Requester, Behavior Parameters, а также настроим эти компоненты следующим образом

![7](https://user-images.githubusercontent.com/114439735/201137366-d8d3ac59-36cb-4d0e-9b6b-09be15e2d7b2.png)

2.5. Добавляем в корневую папку проекта Unity файл конфигурации нейронной сети

```yaml
behaviors:
  RollerAgent:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```
#### 3. Обучение

3.1. Активируем обучение в Conda Prompt, последняя строка кода на скриншоте свидетельствует о том, что можно запускать сцену в Unity 

![8](https://user-images.githubusercontent.com/114439735/201148711-10123720-cb8b-44f5-a781-7c409c4a7571.png)

3.2. Запустим сцену в Unity для одного объекта TargetArea (см. пункт 2.1)

![IMG_0874](https://user-images.githubusercontent.com/114439735/201148094-634ca2cd-0b98-4c49-9e2d-74e20fc43a03.gif)

Результат обучения:

![1](https://user-images.githubusercontent.com/114439735/201149835-8b9d687d-e2d5-4fef-9709-3d8e0df69c1d.png)

3.3. Запустим сцену в Unity для трех объектов TargetArea

![IMG_0875](https://user-images.githubusercontent.com/114439735/201150068-26ba3d16-183a-4b0a-962d-569c39ffae93.gif)

Результат обучения:

![3](https://user-images.githubusercontent.com/114439735/201150390-a0d97ee7-3b41-4849-9029-f1dfc1e4f970.png)

3.4. Запустим сцену в Unity для девяти объектов TargetArea

![IMG_0876](https://user-images.githubusercontent.com/114439735/201150843-da2bddfd-f3eb-49f9-b3b6-83a6f0ebecad.gif)

Результат обучения:

![9](https://user-images.githubusercontent.com/114439735/201150976-d170a5b1-41a0-4104-a43d-00333a597825.png)

3.5. Запустим сцену в Unity для двадцати семи объектов TargetArea

https://user-images.githubusercontent.com/114439735/201152002-8b000baf-f6fc-483c-aeda-fbf5274cafec.mov

Результат обучения:

![27](https://user-images.githubusercontent.com/114439735/201152135-8cd736d7-3df8-4089-8b84-3e0ab7208913.png)

3.6. Как мы видим, с увеличением числа обучающихся объектов увеличивается скорость и качество обучения

#### 4. Проверим работу обученной модели

4.1. Pагрузим файл с результатами обучения ```RollerAgent.onnx``` в папку ```Assets``` директории проекта

4.2. Подключим файл к объекту RollerAgent (компонент Behavior Parameters - Model). Запустим симулицию сцены

[final.webm](https://user-images.githubusercontent.com/114439735/201153113-ce6a0bc2-7f0b-4cc1-b8fb-7dd22ae15a29.webm)

## Выводы

В ходе данной лабораторной работы мной были изучены основы интеграции нейронной сети в простой 3D-проект Unity. В результате я получил модель, благодаря которой выполняются заданные условия, для этого было достаточно выполнить 12 обучений за 3 стадии. Игровым балансом называется «равновесие» между персонажами, командами, тактиками игры и другими игровыми объектами. Сложно переоценить роль машинного обучения в проработке баланса современных игр, оно значительно упрощает процесс моделирования искусственного интеллекта в игровых проектах.

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
