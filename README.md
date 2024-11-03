# АНАЛИЗ ДАННЫХ В РАЗРАБОТКЕ ИГР
Отчет по лабораторной работе #3 выполнила:
- Миногина Дарья Алексеевна
- РИ-230932
  
Отметка о выполнении заданий:

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

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Предложить вариант изменения найденных переменных в Dragon Picker для 10 уровней в игре. Визуализировать изменение уровня сложности в таблице. 
- Задание 2.
- Создать 10 сцен на Unity с изменяющимся уровнем сложности.
- Задание 3.
- Визуализировать данные из google-таблицы, и с помощью Python передать в проект Unity. В Python данные также должны быть визуализированы.
- Выводы.
- ✨Magic✨

## Цель работы
Научиться анализировать и изменять параметры для балансировки и управления сложностью игры на движке Unity, а также применить инструменты Python и Google Sheets для динамического управления и визуализации этих параметров.

## Задание 1
### Предложить вариант изменения найденных переменных в Dragon Picker для 10 уровней в игре. Визуализировать изменение уровня сложности в таблице. 

Для начала выделим переменные, влияющие на движение дракона
В классе EnemyDragon, который управляет передвижением дракона, можно выделить следующие переменные:

- speed — отвечает за скорость передвижения дракона по оси x.
- leftRightDistance — определяет пределы горизонтального движения дракона. При достижении этих границ дракон меняет направление.
- chanceDirection — задает вероятность, с которой дракон случайно меняет направление. Чем выше значение, тем чаще дракон будет менять направление.
- timeBetweenEggDrops — хотя напрямую не влияет на движение дракона, она определяет частоту сброса яиц, что косвенно увеличивает динамику и интерактивность сцены.

Теперь выделим переменные, влияющие на сложность игры:

- numEnergyShield (в DragonPicker) — количество щитов, доступных игроку. Чем меньше щитов, тем сложнее игра, так как игрок имеет меньше шансов защититься от падающих яиц.
- imeBetweenEggDrops (в EnemyDragon) — определяет частоту сброса яиц. С уменьшением времени между сбросами игра становится сложнее, так как игроку нужно быстрее реагировать.
- chanceDirection (в EnemyDragon) — частота смены направления дракона также усложняет игру, так как делает движения дракона менее предсказуемыми.
- energyShieldRadius (в DragonPicker) — размер щитов. Уменьшение радиуса щитов уменьшает площадь, на которой яйца можно поймать, делая игру сложнее.

Мы будем использовать для визуализации только переменные, влияющие на движение дракона. Визуализировать будем с помощью GoogleSheets

![GoogleSheetsDragonPicker](https://github.com/MidoriKsai/Homework3/blob/main/GoogleSheetsDragonPicker.png)

## Задание 2
### Создать 10 сцен на Unity с изменяющимся уровнем сложности.

Нам нужно было создать 10 сцен и изменить там параметры EnemyDragon в инспекторе.

## Задание 3
### Настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной.

Я написала код, который загружает информацию о монетах, полученных за разные действия в игре: подбор монет с зомби, попадание по зомби(для коллектора), сбор монет из сундука, убийство босса. В зависимости от случайно выбранного действия, он воспроизводит соответствующий звук и выводит сообщение в консоль с информацией о количестве полученных монет и волне. После каждого звука выбирается новое случайное действие.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip coinPickup;
    public AudioClip bossKill;
    public AudioClip hitZombie;
    public AudioClip chestCollect;

    private AudioSource audioSource;
    private Dictionary<string, List<float>> dataSet = new Dictionary<string, List<float>>();
    private bool statusStart = false;
    private int i = 1;
    private int r = -1;

    void Start()
    {
        StartCoroutine(GoogleSheets());
        r = Random.Range(0,4);
    }

   void Update()
    {
        if (r == 0 & statusStart == false & i != dataSet.Count && dataSet.Count > 0)
        {
            StartCoroutine(PlayCoinPickup());
            Debug.Log($"Подобрано {dataSet[i.ToString()][0]} монет с зомби на волне {i}");
        }

        if (r == 1 & statusStart == false & i != dataSet.Count && dataSet.Count > 0)
        {
            StartCoroutine(PlayHitZombie());
            Debug.Log($"Получено {dataSet[i.ToString()][1]} монет при попадании по зомби на волне {i}");
        }

        if (r == 2 & statusStart == false & i != dataSet.Count && dataSet.Count > 0)
        {
            StartCoroutine(PlayChestCollect());
            Debug.Log($"Подобрано {dataSet[i.ToString()][2]} монет с сундука на волне {i}");
        }

        if (r == 3 & statusStart == false & i != dataSet.Count && dataSet.Count > 0)
        {
            StartCoroutine(PlayBossKill());
            Debug.Log($"Подобрано {dataSet[i.ToString()][3]} монет после убийства босса зомби на волне {i}");
        }
    }


    IEnumerator GoogleSheets()
    {

        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1RaUU1MMcGxMPK9rB6YMvyBTWJe8L2n_XkAs_iK6XCc8/values/Лист1?key=AIzaSyDpLgHJlPOMMlKG-Ul-5c3RovWkAG_hX14");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        var temp = 0;
        foreach (var itemRawJson in rawJson["values"])
        {
            if (temp == 0)
            {
                temp = 1;
                continue;
            }
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(selectRow[0], new List<float> 
            {
                float.Parse(selectRow[2]),
                float.Parse(selectRow[3]),
                float.Parse(selectRow[4]),
                float.Parse(selectRow[5]) 
            });
        }
    }

    IEnumerator PlayCoinPickup()
    {
        statusStart = true;
        audioSource = GetComponent<AudioSource>();
        audioSource.clip = coinPickup;
        audioSource.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        r = Random.Range(0,4);
        i++;
    }

    IEnumerator PlayHitZombie()
    {
        statusStart = true;
        audioSource = GetComponent<AudioSource>();
        audioSource.clip = hitZombie;
        audioSource.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        r = Random.Range(0,4);
        i++;
    }

    IEnumerator PlayBossKill()
    {
        statusStart = true;
        audioSource = GetComponent<AudioSource>();
        audioSource.clip = bossKill;
        audioSource.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        r = Random.Range(0,4);
        i++;
    }

    IEnumerator PlayChestCollect()
    {
        statusStart = true;
        audioSource = GetComponent<AudioSource>();
        audioSource.clip = chestCollect;
        audioSource.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        r = Random.Range(0,4);
        i++;
    }
}

```
![UnityProject](https://github.com/MidoriKsai/Homework2/blob/main/unityProject.png)
![UnityConsole](https://github.com/MidoriKsai/Homework2/blob/main/unityConsole.png)

## Задание 4
###  Придумать как применить в игре указанной выше способ.

- К каждой монете, которая лежит на полу можно применять тег: каким способом она была получена (умер босс, обычный зомби, выпала из сундука),
 либо она была получена путем нанисения урона зомби при улучшениии 'Коллектор'. Таким образом, соотпетственно тегу мы можем воспроизводить звуки в Unity.
Запись данных в таблицу позволит собрать статистику, благодаря которой можно забалансить получение монет каким-то способом, и если он рушит баланс,
то можнор поменять множитель получения монет в необходимую сторону.


## Выводы

Мы научились передавать в Unity данные из Google Sheets с помощью Python, при помощи csharp добавили звуки в Unity, а также сделали анализ игровой валюты в СПАСТИ РТФ: Выживание.

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
