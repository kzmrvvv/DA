# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил:
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
- Выводы

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Постановка задачи
В данной лабораторной работе на языке python будет реализован функционал, позволяющий генерировать стоимость товара (ресурса или игрового объекта) в виде набора данных. Созданный набор данных будет передан в google-таблицу с целью возможности дальнейшего их наглядного представляния и оптимизации. Также в этой лабораторной работе на движке Unity будет реализован функционал, позволяющий воспроизводить аудио-файлы со звуковой информацией в зависимости от значений входного набора данных из таблицы.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
#### 1. В облачном сервисе google console подключим API для работы с google sheets и google drive.

Создадим сервисный аккаунт в google cloud:

![1](https://user-images.githubusercontent.com/114439735/207645735-f421aca2-c795-480c-a94d-abae9006ad9a.png)

Подключим его к google-таблице посредством настроек доступа:

![2](https://user-images.githubusercontent.com/114439735/207645854-f9390cfc-35a5-45f8-af42-3fc8ab8bd458.png)

#### 2. Реализуем запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

Подключим python к гугл-таблице посредством кода (код генерирует цены случайным образом и рассчитывает по ним инфляцию, ее записывает в таблицу):

```py
import gspread
import numpy as np
gc = gspread.service_account(filename = 'useful-approach-365217-27bcce90daee.json')
sh = gc.open("Lab2")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```

Результат работы в PyCharm:

![1](https://user-images.githubusercontent.com/114439735/207651138-478d9ef4-54d9-407e-b0f9-f5951419560b.png)

Результат работы в Google таблице:

![2](https://user-images.githubusercontent.com/114439735/207651159-6434acbc-077d-4504-89a6-1bad4de334a9.png)

#### 3. Создадим новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.

Новый пустой проект в Unity:

![1](https://user-images.githubusercontent.com/114439735/207653279-8d96cfac-977d-43f5-902e-7b9ef0e993e7.png)

#### 4. Напишем функционал на Unity, в котором будет воспризводиться аудиофайл в зависимости от значения данных из таблицы.

Код на C#:

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1nLda-A6l5XMSPIm6HcURNKCYoQn4UCSnFk_AXICytlY/values/Лист1?key=AIzaSyC__Bb1qxlavpjHb54Fu5Y7kv_EypB76J4");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

Атрибуты объекта в Unity:

![1](https://user-images.githubusercontent.com/114439735/207663782-049de7e5-a66a-4a80-b01e-77cc6407247d.png)

Результат выполнения программы:

![2](https://user-images.githubusercontent.com/114439735/207663829-36cdd481-bce1-44a9-8821-8a653923485e.png)

## Выводы

В ходе выполнения данной лабораторной работы я научился работать со средствами организации передачи данных между Google-таблицами, Python и Unity.

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
