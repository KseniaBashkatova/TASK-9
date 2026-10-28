## Практическая работа № 9
``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

// 1. Класс, описывающий логику торгового автомата
public class VendingMachine
{
    // Поля (состояние автомата)
    private int _bottlesInStock;
    private decimal _pricePerBottle;
    private decimal _currentCredit;
    private decimal _cashInSafe;

    // Конструктор
    public VendingMachine(int initialBottles, decimal price)
    {
        _bottlesInStock = Math.Clamp(initialBottles, 0, 60);
        _pricePerBottle = price > 0 ? price : 100m;
        _currentCredit = 0;
        _cashInSafe = 0;
    }

    // Свойства
    public int BottlesInStock => _bottlesInStock;
    public decimal PricePerBottle => _pricePerBottle;
    public bool IsSoldOut => _bottlesInStock == 0;

    // Метод: клиент вносит деньги
    public void InsertMoney(decimal cash)
    {
        if (cash <= 0)
        {
            Console.WriteLine(" Можно внести только положительную сумму.");
            return;
        }

        _currentCredit += cash;
        Console.WriteLine($" Принято: {cash:C2}. Текущий кредит: {_currentCredit:C2}");
    }

    // Метод: выдача напитка
    public void DispenseDrink()
    {
        // Проверка на пустой автомат
        if (IsSoldOut)
        {
            Console.WriteLine(" Автомат пуст. Выберите другой.");
            // Если клиент уже внес деньги в пустой автомат — возвращаем их
            if (_currentCredit > 0)
            {
                Console.WriteLine($" Возвращаем внесенные деньги: {_currentCredit:C2}");
                _currentCredit = 0;
            }
            return;
        }

        // Проверка на нехватку средств
        if (_currentCredit < _pricePerBottle)
        {
            decimal needed = _pricePerBottle - _currentCredit;
            Console.WriteLine($" Недостаточно средств. Нужно еще: {needed:C2}. Возвращаем {_currentCredit:C2}.");
            _currentCredit = 0; // Обязательно сбрасываем кредит при ошибке
            return;
        }

        // Продажа совершена
        _bottlesInStock--;
        _cashInSafe += _pricePerBottle;
        decimal change = _currentCredit - _pricePerBottle;
        _currentCredit = 0;

        Console.WriteLine(" Напиток выдан.");
        if (change > 0)
        {
            Console.WriteLine($" Ваша сдача: {change:C2}");
        }
    }

    // Метод: вывод текущего состояния автомата
    public void PrintStatus()
    {
        Console.WriteLine("----- Статус автомата -----");
        Console.WriteLine($"Товар в наличии: {_bottlesInStock} шт.");
        Console.WriteLine($"Цена: {_pricePerBottle:C2}");
        Console.WriteLine($"Выручка в сейфе: {_cashInSafe:C2}");
        Console.WriteLine($"Статус: {(IsSoldOut ? "ПУСТО" : "РАБОТАЕТ")}");
        Console.WriteLine("--------------------------");
    }
}

// 2. Точка входа в программу (управляет сценарием)
public class Program
{
    // В классе Program метод Main ДОЛЖЕН быть статическим (static)
    public static void Main(string[] args)
    {
        // Создаем массив из 3-х РАЗНЫХ автоматов (экземпляров класса VendingMachine)
        VendingMachine[] machines = new VendingMachine[3];
        machines[0] = new VendingMachine(25, 80m); // 25 бутылок по 80 руб.
        machines[1] = new VendingMachine(5, 120m); // 5 бутылок по 120 руб.
        machines[2] = new VendingMachine(40, 70m); // 40 бутылок по 70 руб.

        // Сценарий: поток студентов (номер автомата с 0, внесенная сумма)
        var purchases = new (int machineIndex, decimal cash)[]
        {
            (0, 100m), // У автомата №1: дал 100, купил за 80, получил 20 сдачи
            (1, 50m),  // У автомата №2: дал 50, цена 120 — не хватает
            (0, 80m),  // У автомата №1: дал 80, купил без сдачи
            (2, 200m), // У автомата №3: дал 200, купил за 70, получил 130 сдачи
            (1, 120m), // У автомата №2: дал 120, купил последнюю бутылку
            (1, 120m), // У автомата №2: пытается купить еще, но он пуст
            (2, 70m),  // У автомата №3: дал 70, купил без сдачи
            (0, 50m)   // У автомата №1: дал 50, цена 80 — не хватает
        };

        Console.WriteLine("=== Моделирование утреннего потока студентов ===\n");

        foreach (var p in purchases)
        {
            Console.WriteLine($"\n--- Студент подходит к автомату {p.machineIndex + 1} ---");
            VendingMachine machine = machines[p.machineIndex];

            machine.InsertMoney(p.cash);
            machine.DispenseDrink();
        }

        Console.WriteLine("\n=== Финальный статус автоматов после перемены ===\n");

        for (int i = 0; i < machines.Length; i++)
        {
            Console.WriteLine($"АВТОМАТ №{i + 1}:");
            machines[i].PrintStatus();
        }
    }
}
```
!Результат выполнения
<img width="1436" height="850" alt="t-9 (1)" src="https://github.com/user-attachments/assets/ea4c403f-58ab-46cb-acc0-cdf6b825fc5a" />
<img width="1436" height="847" alt="t-9 (2)" src="https://github.com/user-attachments/assets/5ebd85c9-b798-489b-8bc5-36bfc85c6a4f" />
<img width="1239" height="654" alt="t-9 (3)" src="https://github.com/user-attachments/assets/f6ae100f-9a2d-4272-8e79-ac1b47f960ea" />
<img width="1432" height="847" alt="t-9 (4)" src="https://github.com/user-attachments/assets/099fc07e-8045-4831-bc61-b5f35be93d1d" />
// Делали Аристов и Башкатова
