using System;

class Calculator
{
    static void Main()
    {
        Console.WriteLine("Простой калькулятор");

        Console.WriteLine("Введите первое число:");
        double num1;
        if (!double.TryParse(Console.ReadLine(), out num1))
        {
            Console.WriteLine("Ошибка: некорректный формат числа.");
            return;
        }

        Console.WriteLine("Введите второе число:");
        double num2;
        if (!double.TryParse(Console.ReadLine(), out num2))
        {
            Console.WriteLine("Ошибка: некорректный формат числа.");
            return;
        }

        Console.WriteLine("Выберите операцию (+, -, *, /):");
        char operation;
        if (!char.TryParse(Console.ReadLine(), out operation))
        {
            Console.WriteLine("Ошибка: некорректный формат операции.");
            return;
        }

        double result = 0;

        switch (operation)
        {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
            case '/':
                if (num2 != 0)
                {
                    result = num1 / num2;
                }
                else
                {
                    Console.WriteLine("Ошибка: деление на ноль недопустимо.");
                    return;
                }
                break;
            default:
                Console.WriteLine("Ошибка: некорректная операция.");
                return;
        }

        Console.WriteLine("Результат: " + result);

        Console.ReadLine();
    }
}

Console.WriteLine("Выберите цвет (red, green, blue):");
string colorInput = Console.ReadLine();
Color color;
if (Enum.TryParse(colorInput, out color))
{
    Console.WriteLine("Выбран цвет: " + color);
}
else
{
    Console.WriteLine("Ошибка: некорректное значение цвета.");
}

