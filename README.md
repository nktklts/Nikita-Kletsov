using System;
using System.Threading;

class Program
{
    static int screenWidth = 80;   
    static int screenHeight = 25;  
    static int ballX = screenWidth / 2;   
    static int ballY = screenHeight / 2;  
    static int ballSpeedX = 1;    
    static int ballSpeedY = 1;    
    static int score = 0;        
    static bool isPlaying = false;  

    static void Main(string[] args)
    {
        Console.CursorVisible = false;  
        Console.SetWindowSize(screenWidth, screenHeight);  

        ShowMainMenu();

        while (true)
        {
            if (isPlaying)
            {
                UpdateBallPosition(); 
                DrawBall(); 
                DrawScore(); 
                Thread.Sleep(50);  
                Console.Clear();  
            }
            else
            {
                if (Console.KeyAvailable)
                {
                    var key = Console.ReadKey(true);
                    if (key.Key == ConsoleKey.Enter)
                    {
                        isPlaying = true;
                        ResetGame();
                    }
                }
            }
        }
    }

    static void ShowMainMenu()
    {
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        Console.WriteLine("Welcome to the Ball Game!");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 + 1);
        Console.WriteLine("Press Enter to start");
    }

    static void UpdateBallPosition()
    {
        ballX += ballSpeedX;  
        ballY += ballSpeedY;  

       
        if (ballX <= 0 || ballX >= screenWidth - 1)
            ballSpeedX = -ballSpeedX;  

        if (ballY <= 0 || ballY >= screenHeight - 1)
            ballSpeedY = -ballSpeedY;  

        
        if (ballY == screenHeight - 2 && ballX >= 0 && ballX <= 10)
        {
            score++;  
            ballSpeedY = -ballSpeedY;  
        }
    }

    static void DrawBall()
    {
        Console.SetCursorPosition(ballX, ballY);
        Console.Write("O");
    }

    static void DrawScore()
    {
        Console.SetCursorPosition(0, screenHeight - 1);
        Console.Write("Score: " + score);
    }

    static void ResetGame()
    {
        ballX = screenWidth / 2;
        ballY = screenHeight / 2;
        ballSpeedX = 1;
        ballSpeedY = 1;
        score = 0;
    }
}


