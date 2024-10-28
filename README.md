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
| Задание 4 | * |(дополнительно) |

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
- Выбрать одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), описать её роль в игре, условия изменения / появления и диапазон допустимых значений. Построить схему экономической модели в игре и указать место выбранного ресурса в ней.
- Задание 2.
- С помощью скрипта на языке Python заполнить google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализировать данные в google-таблице для наглядного представления выбранной игровой величины. Описать характер изменения этой величины недостатки в реализации этой величины и предложить до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.
- Задание 3.
- Настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной.
- Задание 4.
- Придумать как применить в игре указанной выше способ.
- Выводы.
- ✨Magic✨

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выбрать одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), описать её роль в игре, условия изменения / появления и диапазон допустимых значений. Построить схему экономической модели в игре и указать место выбранного ресурса в ней.

Переменные, влияющие на движение дракона
В классе EnemyDragon, который управляет передвижением дракона, можно выделить следующие переменные:

speed — отвечает за скорость передвижения дракона по оси x.
leftRightDistance — определяет пределы горизонтального движения дракона. При достижении этих границ дракон меняет направление.
chanceDirection — задает вероятность, с которой дракон случайно меняет направление. Чем выше значение, тем чаще дракон будет менять направление.
timeBetweenEggDrops — хотя напрямую не влияет на движение дракона, она определяет частоту сброса яиц, что косвенно увеличивает динамику и интерактивность сцены.
Переменные, влияющие на сложность игры
На сложность игры влияют следующие переменные:

numEnergyShield (в DragonPicker) — количество щитов, доступных игроку. Чем меньше щитов, тем сложнее игра, так как игрок имеет меньше шансов защититься от падающих яиц.
timeBetweenEggDrops (в EnemyDragon) — определяет частоту сброса яиц. С уменьшением времени между сбросами игра становится сложнее, так как игроку нужно быстрее реагировать.
chanceDirection (в EnemyDragon) — частота смены направления дракона также усложняет игру, так как делает движения дракона менее предсказуемыми.
energyShieldRadius (в DragonPicker) — размер щитов. Уменьшение радиуса щитов уменьшает площадь, на которой яйца можно поймать, делая игру сложнее.

## Задание 2
###  С помощью скрипта на языке Python заполнить google-таблицу данными, описывающими игровую валюту в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализировать данные в google-таблице (построить график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Описать характер изменения этой величины, описать недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложить до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше..

- Напишем скрипт для заполнения таблицы
  
```python
import gspread
import numpy as np
import random

gc = gspread.service_account(filename='homework2-438509-d20bfeb34125.json')
sh = gc.open("Homework2")

waves = list(range(1, 11))
zombie_counts = [i * 10 for i in waves] 
zombie_rewards = [i for i in waves] 

def get_collector_reward():
   return random.choice([round(i * 0.1, 1) for i in range(0, 42)])

def get_chest_reward(wave):
    if wave >= 4:
        return random.choice(range(10, 101, 10))
    else:
        return 0

def get_boss_reward(wave):
    if wave >= 5:  
        return zombie_rewards[wave - 1]  
    return 0 

header = ['Wave', 'Zombie Count', 'Zombie Reward per Zombie', 'Collector Reward',
'Chest Reward', 'Boss Reward', 'Total Reward']
worksheet.update('A1:G1', [header])

total_rewards = []
previous_collector_reward = 0 

for wave in waves:
    zombie_count = zombie_counts[wave - 1]
    zombie_reward = zombie_rewards[wave - 1]
    
    total_zombie_reward = zombie_count * zombie_reward
    
    chest_reward = get_chest_reward(wave)
    
    collector_reward = previous_collector_reward + get_collector_reward()
    previous_collector_reward =  collector_reward
    
    boss_reward = get_boss_reward(wave)
    
    total_reward = total_zombie_reward + collector_reward + chest_reward + boss_reward
    
    total_rewards.append([wave, zombie_count, zombie_reward, collector_reward,
 chest_reward, boss_reward, total_reward])

for i, reward_row in enumerate(total_rewards):
    worksheet.update(f'A{i+2}:G{i+2}', [reward_row])
```

В текущем коде реализована система получения монет, основанная на различных игровых событиях, таких как убийства зомби, получение наград за сундуки, и награды от коллектора.

Награда за зомби:
Игрок получает монеты за каждое убийство зомби. Не убив всех зомби, пройти волну не получится, поэтому по завершению уровня игрок гарантированно получит (волна * кол-во зомби) монет. Иначе уровень не будет пройден.

Награда за сундуки:
Сундуки начинают появляться после четвертой волны(как я поняла). Максимальная награда за сундук 10(тоже трудно отследить), сколько сундуков спавнится за 1 волну отследить не удалось, но предположим, что 10. В коде реализован генеротор числа от 10 до 100 с шагом 10

Награда коллектора:
Коллектор может приносить дополнительную награду за каждую волну. Награда варьируется от 0 до 4.1(рандомное число, трудно подсчитать точно) монет с шагом 0.1. Награда от коллектора накапливается и добавляется к общему количеству монет, полученных игроком на протяжении игры.

Награда за босса:
Боссы появляются после четвертой волны, и за их убийство игрок получает столько же монет, сколько за обычного зомби. 

Общий учет монет:
Сумма награз за зомби сундуки и коллектора.

Недостатки реализации:
Неопределенные цифры от коллектора и за получение сундуков

![Diagram](https://github.com/MidoriKsai/Homework2/blob/main/Diagram.png)

Диаграмма четко показывает, что по мере увеличения волн и сложности, игроки имеют возможность зарабатывать больше.

#### Модификации

- Можно давать награду за уровень пройденный без урона, так у игроков появится ещё один, так сказать, сайдквест
- Создать систему комбо, где игрок получает дополнительные награды за последовательные убийства зомби за короткий промежуток времени

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
