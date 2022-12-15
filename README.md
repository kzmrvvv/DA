# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
- Казимиров Кирилл Евгеньевич
- НМТ-211506

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

## Цель работы
Ознакомиться с принципами работы перцептрона.

## Задание 1
### В проекте Unity реализовать перцептрон

Перенёс предоставленный код перцептрона в Unity

```C#
using System;
using System.Collections;
using System.Linq;
using TMPro;
using UnityEngine;
using Random = UnityEngine.Random;

[Serializable]
public class TrainingSet
{
    public double[] input;
    public double output;
}

public class Perceptron : MonoBehaviour
{
    // public TextMeshProUGUI text;
    public TrainingSet[] ts;
    private readonly double[] weights = { 0, 0 };
    private double bias;
    private double totalError;

    private double DotProductBias(double[] v1, double[] v2)
    {
        if (v1 == null || v2 == null)
            return -1;

        if (v1.Length != v2.Length)
            return -1;

        var d = v1.Select((t, x) => t * v2[x]).Sum();

        d += bias;

        return d;
    }

    public int CalcOutput(int i1, int i2)
    {
        double[] inp = { i1, i2 };
        var dp = DotProductBias(weights, inp);
        return dp > 0 ? 1 : 0;
    }

    private int CalcOutput(int i)
    {
        var dp = DotProductBias(weights, ts[i].input);
        return dp > 0 ? 1 : 0;
    }

    private void InitialiseWeights()
    {
        for (var i = 0; i < weights.Length; i++)
            weights[i] = Random.Range(-1.0f, 1.0f);
        
        bias = Random.Range(-1.0f, 1.0f);
    }


    private void UpdateWeights(int j)
    {
        var error = ts[j].output - CalcOutput(j);
        totalError += Mathf.Abs((float)error);
        
        for (var i = 0; i < weights.Length; i++)
            weights[i] += error * ts[j].input[i];
        
        bias += error;
    }

    private void Train(int epochs)
    {
        InitialiseWeights();

        for (var e = 0; e < epochs; e++)
        {
            totalError = 0;
            for (var t = 0; t < ts.Length; t++)
                UpdateWeights(t);
            /*
                Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
            */
            Debug.Log($"Total error: {totalError}, epoch {e}.");
        }
    }

    private void Awake()
    {
        Train(8);
    }
```

Реализовал логические операции:
OR - 

![1](https://user-images.githubusercontent.com/114439735/207849025-71859487-bbfd-4f57-b3f9-00b1c680d2a8.png)

AND - 

![2](https://user-images.githubusercontent.com/114439735/207849043-8e411635-88e5-4a77-bce4-fab6e48226d2.png)

NAND - 

![3](https://user-images.githubusercontent.com/114439735/207849052-1110f10a-21e0-435f-9835-9ea10593f005.png)

XOR - 

![4](https://user-images.githubusercontent.com/114439735/207849076-a541bc21-6922-4fdf-913d-fe19e9d5e127.png)

Как видите, перцептрон не осилил XOR. Наглядная причина этому:

![5](https://user-images.githubusercontent.com/114439735/207849097-48857383-aa87-481d-9fb2-db55eb2a792f.png)

## Задание 2: Построить графики зависимости количества эпох от ошибки.

Перенёс данные со скриншотов в Google Sheets в формате - "номер эпохи : total error".
Вот результаты:

![6](https://user-images.githubusercontent.com/114439735/207848391-d672f68c-d619-4275-ab2f-931ab668f480.png)

![7](https://user-images.githubusercontent.com/114439735/207848425-89c5cf5a-c95f-4f07-a6be-7fca2f04bba6.png)

![8](https://user-images.githubusercontent.com/114439735/207848439-970c9a67-35d8-4a01-9441-f52af5b8b2ac.png)

![9](https://user-images.githubusercontent.com/114439735/207848453-dce83852-eb19-4619-b54d-272d0b3ec3a5.png)

На графиках прослеживается зависимость успешности обучения от количества эпох, что вполне очевидно. Можно с уверенностью сказать, что количество эпох, в течение которых будет обучаться перцептрон, напрямую зависит от сложности ожидаемой операции. Так, например, на OR у перцептрона уходило 2 или 3 эпохи, на AND от 2 до 5, а с XOR он так и не справился.

## Задание 3
### Визулазировать работу перцептрона в Unity.

Решил визуализировать так: в случае, если результат логической операции равен нулю, куб отправляется в полёт.
Создал канвас, на который добавил текст с вводимыми данными. Добавил куб, на который в своб очеред добавил скрипт, контролирующий результат работы перцептрона на этих данных и решающий, что сделать с кубом, в зависимости от полученных данных.

Сам скрипт:
```C#
using UnityEngine;

public class Perceptronnoe : MonoBehaviour
{
    private Rigidbody _rb;
    [SerializeField] private int firstValue;
    [SerializeField] private int secondValue;
    [SerializeField] private float force;
    [SerializeField] private Perceptron perceptron;
    private bool _goesFar;

    void Start()
    {
        _rb = GetComponent<Rigidbody>();
        _goesFar = perceptron.CalcOutput(firstValue, secondValue) != 0;
    }

    private void FixedUpdate()
    {
        if (_goesFar)
        {
            _rb.AddForce(_rb.transform.TransformDirection(Vector3.forward) * force, ForceMode.Impulse);
        }
    }
}

```

Результат работы, после обучения перцептрона операции NOR:

![10](https://user-images.githubusercontent.com/114439735/207848497-72d27281-fcfb-4b3f-880a-7e515264efdf.gif)

## Выводы

Перцептрон это один из переходных инструментов в создании нейронных сетей, со своими слабыми местами, которые были исправлены в нейронах. Мне понравилось работать с перцептроном, это очень хорошо помогает разобраться с тем, как работают нейронные сети, и заинтересовывает на изучении данной темы на более углубленном уровне.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
