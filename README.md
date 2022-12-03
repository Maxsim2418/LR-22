# LR-22[LR-22.zip](https://github.com/Maxsim2418/LR-22/files/10146071/LR-22.zip)

Борнеман М.А

ЭВТ-70

Игровой движок:Unity

Лабораторная работа №22

Тема: Разработка игрового проекта MakeThemFall

Цель: приобрести навыки в разработке игрового проекта змейка

Ход работы:

1.	Выполнение работы

![image](https://user-images.githubusercontent.com/119674602/205433638-7e24950d-c225-43ff-93f7-39887ce2984a.png)

Рисунок 22.1 спрайт игрового персонажа
 
1.	Были созданы стены и скрипт для прыжка персонажа

Листинг 22.1 Player.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 xValue;
    public bool changeDirectionBool, leftFacing, changeIt,easing;
    Animator animator;
    Vector3 desiredPosition;
    // Start is called before the first frame update
    void Start()
    {
        DesiredPosition();
        changeDirection();
        animator = GetComponent<Animator>();
    }

    // Update is called once per frame
    void Update()
    {
        if (changeIt)
        {
            if (easing)
            {
                transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);
            }
            else
            {
                transform.position = desiredPosition;
            }

            if (Mathf.Abs(desiredPosition.x - transform.position.x) <= 0.1f)
            {
                Debug.Log("Changing gavity");
                changeIt = false;
            }

        }
        animator.SetBool("ChangeDirection", changeDirectionBool);
    }

    public void CallThisMethod()
    {
        DesiredPosition();
        changeDirection();
    }

    public void DesiredPosition()
    {
        desiredPosition = new Vector2(changeDirectionBool ? xValue.x : xValue.y, transform.position.y);
    }

    void changeDirection()
    {
        changeDirectionBool = !changeDirectionBool;
        if (changeDirectionBool != leftFacing)
        {
            changeIt = true;

            leftFacing = changeDirectionBool;
        }

        Debug.Log(transform.position.x);
    }

    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Spike")
        {
            Debug.Log("dead Player");
            FindObjectOfType<GameManager>().RestartScene();
        }
    }
}

2.	Были созданы шипы а также скрипт для них

![image](https://user-images.githubusercontent.com/119674602/205433653-790c3fd6-a31b-404a-8a94-0e018b241c19.png)

Рисунок 22.2 Игровое поле

Листинг 22.2 Spike.cs

using UnityEngine;

public class Spike : MonoBehaviour
{

    public float moveSpeed;
    public Vector2 minMaxYValue;
    Vector2 pos;
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        transform.position += Vector3.up * moveSpeed * Time.deltaTime;

        if (transform.position.y > minMaxYValue.x)
        {
            pos = transform.position;
            pos.x = transform.position.x;
            pos.y = minMaxYValue.y;
            transform.position = pos;
            ShowOrHide();
        }
    }

    void ShowOrHide()
    {
        if (Random.value > 0.5f)
        {
            Debug.Log("Disabling");
            transform.GetChild(0).gameObject.SetActive(false);
            return;
        }

        transform.GetChild(0).gameObject.SetActive(true);

    }
}

3.	Была создана вторая часть площадки и второй персонаж 

![image](https://user-images.githubusercontent.com/119674602/205433660-d3567489-9f3b-45d1-ad66-2f55b4ac98a4.png)

Рисунок 22.3 Игровое поле после добавления второй площадки и второго персонажа.

4.	Был создан отдельный колайдер для  игрового поля и прикреплен к держателю полей

Листинг 22.3 ColliderGO.cs

using UnityEngine;

public class ColliderGO : MonoBehaviour
{
    public Transform PlayerTransform;

    public void CallPlayerMethod()
    {
        Debug.Log(PlayerTransform.name);
        PlayerTransform.GetComponent<Player>().CallThisMethod();
    }
}
5.	Был создан скрипт для определения нажатия на правую и левую половину поля 

Листинг 22.4 ClickChecker.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ClickChecker : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector2 pos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
            RaycastHit2D hit = Physics2D.Raycast(pos, Vector2.zero);
            if (hit)
            {
                if (hit.transform.name == "LeftSide")
                {
                    hit.collider.GetComponent<ColliderGO>().CallPlayerMethod();
                }
                else if (hit.transform.name == "RightSide")
                {


                    hit.collider.GetComponent<ColliderGO>().CallPlayerMethod();
                }
                else { Debug.Log("Unknown hit from click Checker"); return; }
            }
        }
    }
}

6.	Была создана анимация для плавности передвижения персонажа 

![image](https://user-images.githubusercontent.com/119674602/205433674-a2d02871-ed52-41c9-b828-790f4ea76fd6.png)

Рисунок 22.4 кадр из создания анимации.

7.	Был создан скрипт для наложения цвета на разные площадки

Листинг 22.4 ColorCombinatoin.cs

using UnityEngine;

[System.Serializable]

public class ColorCombination
{
    public Color[] ColorToChoose;
}

8.	Был создан скрипт для рандомизации цветов игровой площадки.

Листинг 22.5 ColorChooser.cs

using UnityEngine;

public class ColorChooser : MonoBehaviour
{
    public GameObject[] imagesGO;
    public ColorCombination[] chooseColor;

    void Awake()
    {
        int randomNo = Random.Range(0, chooseColor.Length + 1);
        imagesGO[0].GetComponent<SpriteRenderer>().color = chooseColor[randomNo].ColorToChoose[0];
        imagesGO[1].GetComponent<SpriteRenderer>().color = chooseColor[randomNo].ColorToChoose[1];
    }


    void Update()
    {

    }
}

9.	Был создан GameManager для перезапуска игры при проигрыше.

using UnityEngine.SceneManagement;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    public void RestartScene()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}      

2. Вывод

В ходе проделанной работы была разработана игра MakeThemFall
