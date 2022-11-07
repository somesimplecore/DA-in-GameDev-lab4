# Доработка интерактивного приложения и его подготовка к сборке
Отчет по лабораторной работе #4 выполнил(а):
- Паханов Александр Александрович
- РИ-300018

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

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Cоздание интерактивного приложения с рейтинговой системой пользователя и интеграция игровых сервисов в готовое приложение.

## Задание 1
### Используя видео-материалы практических работ 1-5 повторить реализацию функционала.
### Ход работы:

- Практическая работа «Создание анимации объектов на сцене»

Переименнуем нашу сцену, создадим ее копию и начнем переделывать ее под главное меню. После удаления ненужных и добавления необходимых объектов, иерархия приняла следующий вид:

![](/Pics/z1_1.jpg)

Изменим анимацию дракона на уже готовую IDLE:

![](/Pics/z1_2.jpg)

Затем сделаем анимацию для облака, задав следующие параметры позиции объекта в ключевых точках:

![](/Pics/z1_3.jpg)
![](/Pics/z1_4.jpg)
![](/Pics/z1_5.jpg)

- Практическая работа «Создание стартовой сцены и переключение между ними»

Добавим UI эллементы в виде заголовка и кнопок:

![](/Pics/z1_6.jpg)

Напишем следующий скрипт для кнопок:
```C#
using UnityEngine;
using UnityEngine.SceneManagement;

public class MainMenu : MonoBehaviour
{
    public void PlayGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);
    }

    public void QuitGame()
    {
        Application.Quit();
    }
}
```

Прицепим скрипт на объект MainMenu и зададим следующие настройки кнопкам PLAY и QUIT:

![](/Pics/z1_7.jpg)
![](/Pics/z1_8.jpg)

В настройках проекта зададим следующую иерархию сцен, чтобы при нажатии на кнопку PLAY мы переходили из главного меню на игровое поле:

![](/Pics/z1_9.jpg)

- Практическая работа «Доработка меню и функционала с остановкой игры»

Создадим меню настроек. Для этого сделаем дубликат объекта MainMenu, удалим лишние элементы и переименуем кнопку QUIT в BACK:

![](/Pics/z1_10.jpg)

Чтобы наши меню переключались между собой, поставим следующие настройки кнопкам OPTIONS и BACK:

![](/Pics/z1_11.jpg)
![](/Pics/z1_12.jpg)

Далее сделаем паузу во время игры и возможность выхода в главное меню. Для этого напишем следующий код:
```C#
using UnityEngine;
using UnityEngine.SceneManagement;

public class Pause : MonoBehaviour
{
    private bool isPaused = false;
    public GameObject panel;

    
    void Update()
    {
        if(Input.GetKeyDown(KeyCode.Space))
        {
            if(!isPaused)
            {
                Time.timeScale = 0;
                isPaused = true;
                panel.SetActive(true);
            }
            else
            {
                Time.timeScale = 1;
                isPaused = false;
                panel.SetActive(false);
            }
        }
        if(Input.GetKeyDown(KeyCode.Escape))
        {
            SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex - 1);
        }
    }
}
```

Затем прицепим этот скрипт на камеру и в поле panel перенесем наш текстовый объект, который мы будем выводить во время паузы. Конечный результат:

![](/Pics/z1_13.jpg)

- Практическая работа «Добавление звукового сопровождения в игре»

Добавим в нашу папку все необходимы звуки:

![](/Pics/z1_13.jpg)

Добавим к камере в главном меню компонент AudioSource, в параметре Audio Clip выберем соответствующий звук, и включим два параметра Play On Awake и Loop.

То же самое проделаем с камерой на игровом поле, префабом яйца и щита, только в последних двух выключим два параметра, чтобы наш звук проигрывался не сразу и не зацикливался. Добавим в наши скрипты яйца и щита следующие строчки в места, где требуется проигрывание звуков:

```C#
audioSource = GetComponent<AudioSource>();
audioSource.Play();
```

- Практическая работа «Интеграция игровых сервисов в готовое приложение»


## Задание 2
### Добавить в приложение интерфейс для вывода статуса наличия игрока в сети (онлайн или офлайн).
### Ход Работы:

Чтобы вывести статус игрока(подключен ли к Яндекс SDK) напишем следующий скрипт:
```C#
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using YG;

public class OnlineChecker : MonoBehaviour
{
    public TextMeshProUGUI statusText;
    public Image statusImage;
    void Start()
    {
        statusText = GameObject.Find("StatusText").GetComponent<TextMeshProUGUI>();
        statusImage = GameObject.Find("StatusImage").GetComponent<Image>();
    }

    void Update()
    {
        statusText.text = YandexGame.SDKEnabled ? "Online" : "Offline";
        statusImage.color = YandexGame.SDKEnabled ? Color.green : Color.red;
    }
}
```

Результат при включенном игровом объекте YandexGame:

![](/Pics/z2_1.jpg)

Результат при выключенном YandexGame(ситуация схожа с тем, если бы игрок не был онлайн):

![](/Pics/z2_2.jpg)

## Задание 3
### Реализовать вывод в консоль количества времени отсутствия игрока в сети если пользователь офлайн.
### Ход работы:

Немного перепишем скрипт из прошлого задания:
```C#
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using YG;
using System;

public class OnlineChecker : MonoBehaviour
{
    public TextMeshProUGUI statusText;
    public Image statusImage;
    public DateTime LastLoginTime;
    void Start()
    {
        statusText = GameObject.Find("StatusText").GetComponent<TextMeshProUGUI>();
        statusImage = GameObject.Find("StatusImage").GetComponent<Image>();
    }

    void Update()
    {
        if(YandexGame.SDKEnabled)
        {
            statusText.text = "Online";
            statusImage.color = Color.green;
            LastLoginTime = DateTime.Now;
        }
        else
        {

            statusText.text = "Offline";
            statusImage.color = Color.red;
            var offlineDurationTime = DateTime.Now - LastLoginTime;
            Debug.Log("Player offline " + offlineDurationTime.Days + " days, " 
                + offlineDurationTime.Hours + " hours, " + offlineDurationTime.Minutes + " minutes.");
        }
    }
}
```

В итоге мы можем получить вывод в консоль по типу:

![](/Pics/z3_1.jpg)

## Выводы

Мы сделали играбельный прототип игры Dragon Picker и выложили его на платформу Яндекс Игры. Познакомились с плагином Яндекс, сделали проверку статуса игрока(онлайн или оффлай) и вывод в коносль продолжительности отсутствия пользователя. Яндекс SDK имеет большое количество различных фич и с их помощью можно настроить проект самым разнообразным способом.

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
