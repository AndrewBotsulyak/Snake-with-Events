using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Threading;

namespace Snake
{
    class Program
    {
        static void Main(string[] args)
        {
            Pole p = new Pole();
            p.Print();
            ConsoleKeyInfo key;

            while (true)
            {

                p.Move();
                p.Print2();

                if (Console.KeyAvailable)
                {
                    key = Console.ReadKey();
                    switch (key.Key)
                    {
                        case ConsoleKey.RightArrow:
                            p.WriteS(ConsoleKey.RightArrow);
                            p.Print2();
                            break;
                        case ConsoleKey.LeftArrow:
                            p.WriteS(ConsoleKey.LeftArrow);
                            p.Print2();
                            break;

                        case ConsoleKey.UpArrow:
                            p.WriteS(ConsoleKey.UpArrow);
                            p.Print2();
                            break;
                        case ConsoleKey.DownArrow:
                            p.WriteS(ConsoleKey.DownArrow);
                            p.Print2();
                            break;
                        default:
                            break;
                    }
                }

            }

        }
    }

    class Pole
    {
        private readonly int width;
        private readonly int height;
        private char[,] mas;
        private Snake[] snake;
        private Snake eve;
        private int count;
        private bool right, left, up, down;
        private char[,] mas2;
        private static Random rand;

        public Pole()
            : this(20, 50)
        { }
        public Pole(int h, int w)
        {
            width = w;
            height = h;
            rand = new Random();
            mas = new char[h, w];
            mas2 = new char[h, w];
            snake = new Snake[3];
            eve = new Snake();
            eve.SEvent += EveWall;           // класс Pole подписался на события класса Snake
            count = snake.Length;
            for (int i = 0; i < snake.Length; i++)
            {
                snake[i] = new Snake();
            }
            for (int i = 0; i < snake.Length;i++ )
            { 
                snake[i].Head.X = 4;
                snake[i].Head.Y = i + 3;
                if (i == snake.Length - 1)
                    snake[i].Next = null;
                else
                    snake[i].Next = snake[i + 1];
            }

        }

        private void RandApple()
        {
            int x, y;

            for (int i = 0; i < 5; i++)
            {
                x = rand.Next(1, 19);
                y = rand.Next(1, 49);
                mas2[x, y] = (char)9787;
            }
        }
        private void EveWall(char symb)             
        {
            if (symb == '|' || symb == snake[0].GetSymb()) 
            {
                snake = null;
                Console.SetCursorPosition(15,10);
                Console.Write(" GAME OVER ");
                Thread.Sleep(2000);
                Environment.Exit(0);
            }
            if(symb == (char)9787)
            {
                Console.SetCursorPosition(0, 22);
                Console.Write("Eat apple )");
            }

        }

        public void Print()
        {
            int index = 0;
            for (int i = 0; i < height; i++)
            {
                for (int j = 0; j < width; j++)
                {

                    if (i == 0 || i == height - 1 || j == 0 || j == width - 1)
                    {
                        mas[i, j] = '|';
                        Console.SetCursorPosition(j, i);
                        Console.Write(mas[i, j]);
                    }
                    if (i == snake[index].Head.X && j == snake[index].Head.Y)
                    {
                        mas2[i, j] = snake[index].GetSymb();
                        index++;
                        if (index == count)
                            index = 0;
                    }

                    if (mas[i, j] == '|')
                        mas2[i, j] = '|';

                }
                Console.WriteLine();
            }
            RandApple();

        }

        public void Print2()
        {
            
            Console.SetCursorPosition(0, 0);
            for (int i = 0; i < height; i++)
            {
                for (int j = 0; j < width; j++)
                {
                    Console.Write(mas2[i, j]);
                }
                Console.WriteLine();
            }
            Thread.Sleep(1000);
        }


        public void Move()
        {
            if (snake[count - 1].Head.X == height - 1 || snake[count - 1].Head.Y == width - 1
                || snake[count - 1].Head.X == 0 || snake[count - 1].Head.Y == 0)
            {
                eve.TakeSymbol('|');               // Вызываю метод который передает символ классу Snake,         
            }                                      // а класс Snake информирует класс Pole.
            
            if (right)
            {
                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.Y++;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                 // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (left)
            {
                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.Y--;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                     // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (up)
            {
                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.X--;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                    // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (down)
            {
                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.X++;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                        // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
        }

        public void WriteS(ConsoleKey key)  // when key pressed
        {

            if (key == ConsoleKey.RightArrow)
            {
                if (right)
                    return;
                right = left = up = down = false;
                right = true;

                for(int i = 0;i<count;i++)
                {
                    if(i == count - 1)
                    {
                        snake[i].Head.Y++;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                     // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if(i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (key == ConsoleKey.LeftArrow)
            {
                if (left)
                    return;
                right = left = up = down = false;
                left = true;

                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.Y--;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                     // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (key == ConsoleKey.UpArrow)
            {
                if (up)
                    return;
                right = left = up = down = false;
                up = true;

                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.X--;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                 // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }
            if (key == ConsoleKey.DownArrow)
            {
                if (down)
                    return;
                right = left = up = down = false;
                down = true;

                for (int i = 0; i < count; i++)
                {
                    if (i == count - 1)
                    {
                        snake[i].Head.X++;
                        if (mas2[snake[i].Head.X, snake[i].Head.Y] == (char)9787)
                            eve.TakeSymbol((char)9787);                     // Вызываю метод который передает символ классу Snake
                        mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                        return;
                    }
                    if (i == 0)
                        mas2[snake[i].Head.X, snake[i].Head.Y] = ' ';
                    snake[i].Head.X = snake[i + 1].Head.X;
                    snake[i].Head.Y = snake[i + 1].Head.Y;
                    mas2[snake[i].Head.X, snake[i].Head.Y] = snake[i].GetSymb();
                }
            }

        }

    }

    class Snake
    {
        public delegate void SnakeDel(char symb);
        public event SnakeDel SEvent;

        private Coord head;
        private char snake;
        private Snake next;   

        public Snake()
        {      
            snake = (char)9786;
            head = new Coord();
        }

        public Snake Next
        {
            get { return next; }
            set { next = value; }
        }
        public char GetSymb()
        {
            return snake;
        }
        public Coord Head
        {
            get { return head; }
        }

        public void TakeSymbol(char symb)    // Принимает символ и генерирует событие.
        {
                SEvent(symb);
        }


    }

    class Coord
    {
        public int X
        {
            get;
            set;
        }
        public int Y
        {
            get;
            set;
        }
    }

}
