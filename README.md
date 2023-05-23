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
    static int attempts = 0;      
    static bool isPlaying = false;  
    static bool isPaused = false;  
    static bool inShop = false;   
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
            if (isPlaying && !isPaused)
            {
                UpdateBallPosition();  
                DrawBall();  
                DrawScore();  
                DrawAttempts();  
                DrawTimer();  
                DrawHint();  
                DrawMiniMap(); 
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
                        if (inShop)
                        {
                            ProcessShopInput();
                        }
                        else
                        {
                            isPlaying = true;
                            isPaused = false;
                            StartGame();
                        }
                    }
                    else if (key.Key == ConsoleKey.Spacebar)
                    {
                        if (isPlaying)
                        {
                            isPaused = !isPaused;
                        }
                    }
                    else if (key.Key == ConsoleKey.Tab)
                    {
                        if (isPlaying)
                        {
                            inShop = !inShop;
                            if (inShop)
                            {
                                ShowShop();
                            }
                            else
                            {
                                ResumeGame();
                            }
                        }
                    }
                    else if (key.Key == ConsoleKey.Escape)
                    {
                        if (inShop)
                        {
                            inShop = false;
                            ShowMainMenu();
                        }
                        else
                        {
                            isPlaying = false;
                            isPaused = false;
                            ShowMainMenu();
                        }
                    }
                }
            }
        }
    }

    static void ShowMainMenu()
    {
        Console.Clear();
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 - 2);
        Console.Write("Welcome to the Ball Game!");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        Console.Write("Press Enter to start");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 + 2);
        Console.Write("Press Tab for the shop");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 + 4);
        Console.Write("Press Escape to exit");
    }

    static void StartGame()
    {
        ResetGame();
        stopwatch.Start();
    }

    static void ResumeGame()
    {
        if (isPaused)
        {
            stopwatch.Start();
            isPaused = false;
        }
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

    static void DrawAttempts()
    {
        Console.SetCursorPosition(0, 0);
        Console.Write("Attempts: " + attempts);
    }

    static void DrawTimer()
    {
        Console.SetCursorPosition(screenWidth - 12, 0);
        Console.Write("Time: " + stopwatch.Elapsed.ToString("mm\\:ss"));
    }

    static void DrawHint()
    {
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight - 1);
        Console.Write("Press Spacebar to pause");
    }

    static void DrawMiniMap()
    {
        int miniMapWidth = 20;   
        int miniMapHeight = 10;  
        int miniMapX = screenWidth - miniMapWidth - 1;  
        int miniMapY = screenHeight - miniMapHeight - 3;  

        Console.SetCursorPosition(miniMapX, miniMapY);
        Console.Write("+");  

        for (int x = 0; x < miniMapWidth; x++)
        {
            Console.SetCursorPosition(miniMapX + x + 1, miniMapY);
            Console.Write("-");
        }

        Console.SetCursorPosition(miniMapX + miniMapWidth, miniMapY);
        Console.Write("+");  

        for (int y = 0; y < miniMapHeight; y++)
        {
            Console.SetCursorPosition(miniMapX, miniMapY + y + 1);
            Console.Write("|");
            Console.SetCursorPosition(miniMapX + miniMapWidth, miniMapY + y + 1);
            Console.Write("|");
        }

        Console.SetCursorPosition(miniMapX, miniMapY + miniMapHeight + 1);
        Console.Write("+");  
        for (int x = 0; x < miniMapWidth; x++)
        {
            Console.SetCursorPosition(miniMapX + x + 1, miniMapY + miniMapHeight + 1);
            Console.Write("-");
        }

        Console.SetCursorPosition(miniMapX + miniMapWidth, miniMapY + miniMapHeight + 1);
        Console.Write("+");  


        int ballMiniMapX = (ballX * miniMapWidth) / screenWidth + miniMapX + 1;
        int ballMiniMapY = (ballY * miniMapHeight) / screenHeight + miniMapY + 1;
        Console.SetCursorPosition(ballMiniMapX, ballMiniMapY);
        Console.Write("O");
    }

    static void ResetGame()
    {
        ballX = screenWidth / 2;
        ballY = screenHeight / 2;
        ballSpeedX = 1;
        ballSpeedY = 1;
        score = 0;
        attempts = 0;
        playerName = "";

        Console.Clear();  
        GeneratePlatform();  
    }

    static void SaveGameState()
    {
        string gameState = playerName + "," + score.ToString() + "," + stopwatch.Elapsed.ToString() + "," + attempts.ToString();
        System.IO.File.WriteAllText("gamestate.txt", gameState);
    }

    static void LoadGameState()
    {
        if (System.IO.File.Exists("gamestate.txt"))
        {
            string gameState = System.IO.File.ReadAllText("gamestate.txt");
            string[] data = gameState.Split(',');
            playerName = data[0];
            score = int.Parse(data[1]);
            TimeSpan timeSpan = TimeSpan.Parse(data[2]);
            stopwatch = new Stopwatch();
            stopwatch.Start();
            stopwatch.Stop();
            stopwatch.Add(timeSpan);
            attempts = int.Parse(data[3]);
        }
    }

    static void GetPlayerName()
    {
        Console.Clear();
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 - 2);
        Console.Write("Enter your name:");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        playerName = Console.ReadLine();
    }

    static void ShowShop()
    {
        Console.Clear();
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 - 2);
        Console.Write("Welcome to the Shop!");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        Console.Write("1. Upgrade ball speed");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 + 1);
        Console.Write("2. Increase platform size");
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2 + 3);
        Console.Write("Press Enter to go back");
    }

    static void ProcessShopInput()
    {
        var key = Console.ReadKey(true);
        if (key.Key == ConsoleKey.Enter)
        {
            inShop = false;
            ShowMainMenu();
        }
        else if (key.Key == ConsoleKey.D1)
        {
            UpgradeBallSpeed();
        }
        else if (key.Key == ConsoleKey.D2)
        {
            IncreasePlatformSize();
        }
    }

    static void UpgradeBallSpeed()
    {
        ballSpeedX += 1;
        ballSpeedY += 1;
        Console.Clear();
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        Console.Write("Ball speed upgraded!");
        Thread.Sleep(2000);
        ShowShop();
    }

    static void IncreasePlatformSize()
    {
        Console.Clear();
        Console.SetCursorPosition(screenWidth / 2 - 10, screenHeight / 2);
        Console.Write("Platform size increased!");
        Thread.Sleep(2000);
        ShowShop();
    }
}
