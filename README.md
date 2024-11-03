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
- timeBetweenEggDrops (в EnemyDragon) — определяет частоту сброса яиц. С уменьшением времени между сбросами игра становится сложнее, так как игроку нужно быстрее реагировать.
- chanceDirection (в EnemyDragon) — частота смены направления дракона также усложняет игру, так как делает движения дракона менее предсказуемыми.
- energyShieldRadius (в DragonPicker) — размер щитов. Уменьшение радиуса щитов уменьшает площадь, на которой яйца можно поймать, делая игру сложнее.

Мы будем использовать для визуализации только speed, leftRightDistance, chanceDirection, timeBetweenEggDrops. Визуализировать будем с помощью GoogleSheets

![GoogleSheetsDragonPicker](https://github.com/MidoriKsai/Homework3/blob/main/GoogleSheetsDragonPicker.png)

## Задание 2
### Создать 10 сцен на Unity с изменяющимся уровнем сложности.

Нам нужно было создать 10 сцен и изменить там параметры EnemyDragon в инспекторе, что я и сделала.
![UnityDP(scene)](https://github.com/MidoriKsai/Homework3/blob/main/UnityDP(scene).png)

## Задание 3
### Визуализировать данные из google-таблицы, и с помощью Python передать в проект Unity. В Python данные также должны быть визуализированы.

Код для вывода таблицы из GoogleSheets с помощью Python
![PythonDP](https://github.com/MidoriKsai/Homework3/blob/main/PythonDP.png)

Код для передачи данных в Unity
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using Newtonsoft.Json.Linq;

public class EnemyDragon : MonoBehaviour
{
  public GameObject dragonEggPrefab;
  public float speed = 1;
  public float timeBetweenEggDrops = 1f;
  public float leftRightDistance = 10f;
  public float chanceDirection = 0.1f;
  public int lvlNumber = 0;

  private string sheetId = "1wS8rGyOEgJLOPOv45qXWkdj3O-NInPqLVthZDWIjTtA";
  private string apiKey = "AIzaSyA4ZArvrYjVrdmsjiLnKJJH4r25PzAYc7w";

  void Start()
  {
      StartCoroutine(GetDataFromSheet());
      Invoke("DropEgg", 2f);
  }

  void DropEgg(){
      Vector3 myVector = new Vector3(0.0f, 5.0f, 0.0f);
      GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
      egg.transform.position = transform.position + myVector;
      Invoke("DropEgg", timeBetweenEggDrops);
  }

  void Update()
  {
      Vector3 pos = transform.position;
      pos.x += speed * Time.deltaTime;
      transform.position = pos;

      if (pos.x < -leftRightDistance){
          speed = Mathf.Abs(speed);
      }
      else if (pos.x > leftRightDistance){
          speed = -Mathf.Abs(speed);
      }
  }

  private void FixedUpdate() {
      if (Random.value < chanceDirection){
          speed *= -1;
      }
  }

   IEnumerator GetDataFromSheet()
  {
      Debug.Log("Here");
      var charList = new List<string> {"B","C","D","E","F","G","H","I","J","K"};
      var charToPut = charList[lvlNumber-1];
      Debug.Log(charToPut);
      string url = $"https://sheets.googleapis.com/v4/spreadsheets/{sheetId}/values/Лист1!{charToPut}2:{charToPut}5?key={apiKey}";
      Debug.Log(url);

      using (UnityWebRequest request = UnityWebRequest.Get(url))
      {
          yield return request.SendWebRequest();

          if (request.result == UnityWebRequest.Result.Success)
          {
                              
              JObject json = JObject.Parse(request.downloadHandler.text);
              JArray values = (JArray)json["values"];
              Debug.Log(values);

              if (values != null && values.Count > 0)
              {
                  
                  speed = float.Parse((string)values[0][0]);
                  timeBetweenEggDrops = float.Parse((string)values[3][0]); 
                  leftRightDistance = float.Parse((string)values[1][0]); 
                  chanceDirection = float.Parse((string)values[2][0]); 
              }
          }
          else
          {
              Debug.LogError($"Error fetching data: {request.error}");
          }
      }
   }
} 
```

## Выводы

Работа над балансировкой параметров игры в Dragon Picker показала, как ключевые переменные, такие как скорость движения, вероятность смены направления и частота падения предметов, напрямую влияют на игровой процесс. Эти параметры задают темп и сложность игры, и их корректная настройка позволяет создавать постепенно усложняющийся игровой опыт.

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
