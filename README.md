using System;
using System.Diagnostics;
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
    static Random random = new Random(); 
    static Stopwatch stopwatch = new Stopwatch(); 
    static string playerName = ""; 

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
                DrawTimer();  
                DrawHint();  
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
                        if (playerName == "")
                        {
                            GetPlayerName();
                        }
                        isPlaying = true;
                        StartGame();
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
        Console.WriteLine("Enter your name and press Enter to start");
    }

    static void GetPlayerName()
    {
        Console.SetCursorPosition(screenWidth / 2 - 5, screenHeight / 2 + 2);
        Console.Write("Name: ");
        playerName = Console.ReadLine();
    }

    static void StartGame()
    {
        ResetGame();
        stopwatch.Start(); 
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

            
            GeneratePlatform();
        }
    }

    static void GeneratePlatform()
    {
        int platformWidth = random.Next(5, 10);  
        int platformX = random.Next(1, screenWidth - platformWidth - 1);  


        for (int x = platformX; x < platformX + platformWidth; x++)
        {
            Console.SetCursorPosition(x, screenHeight - 2);
            Console.Write("=");
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

    static void DrawTimer()
    {
        Console.SetCursorPosition(screenWidth - 10, screenHeight - 1);
        Console.Write("Time: " + stopwatch.Elapsed.ToString(@"mm\:ss"));
    }

    static void DrawHint()
    {
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight - 1);
        Console.Write("Hint: Try to hit the platform to score points!");
    }

    static void ResetGame()
    {
        ballX = screenWidth / 2;
        ballY = screenHeight / 2;
        ballSpeedX = 1;
        ballSpeedY = 1;
        score = 0;
        playerName = "";

        Console.Clear();  
        GeneratePlatform(); 
    }

    static void SaveGameState()
    {
        string gameState = playerName + "," + score.ToString() + "," + stopwatch.Elapsed.ToString();
        System.IO.File.WriteAllText("gamestate.txt", gameState);
    }

    static void LoadGameState()
    {
        if (System.IO.File.Exists("gamestate.txt"))
        {
            string gameState = System.IO.File.ReadAllText("gamestate.txt");
            string[] data = gameState.Split(',');
            if (data.Length == 3)
            {
                playerName = data[0];
                score = Convert.ToInt32(data[1]);
                TimeSpan elapsed;
                if (TimeSpan.TryParse(data[2], out elapsed))
                {
                    stopwatch.Reset();
                    stopwatch.Start();
                    stopwatch.Stop();
                    stopwatch.Add(elapsed);
                }
            }
        }
    }

    static void DeleteGameState()
    {
        if (System.IO.File.Exists("gamestate.txt"))
        {
            System.IO.File.Delete("gamestate.txt");
        }
    }
}

