
> [!ABSTRACT] Project Overview
> A classic console-based implementation of the "Rock, Paper, Scissors" game built with C++.
> This was one of my first projects to practice **Control Flow**, **Enums**, and **Input Validation**.

tags: #cpp #game #beginner #console
status: Completed ✅

---
## 🎮 Features
* **User vs Computer:** Play against an AI (Randomized logic).
* **Round System:** Play multiple rounds and track the score.
* **Input Validation:** Prevents invalid inputs from crashing the game.
* **Interactive UI:** Changes console colors based on the winner (Red for lose, Green for win).

## 🧠 What I Learned
During this project, I focused on:
1.  **Enums:** Using `enum` to make code readable (`Rock` instead of `1`).
2.  **References:** Passing variables by reference (`int &Counter`) to update scores instantly.
3.  **Randomization:** Using `srand(time(NULL))` to generate unpredictable computer moves.

## 💻 The Code

```cpp
#include<iostream>
#include<cstdlib>
#include<ctime>
using namespace std;

enum enChoice { Rock = 1, Paper = 2, Scissor = 3 };
enum enWinner { Computer_Wins = 0, Player_Wins = 1, Draw = 2 };

int Read_Positive_Number(string msg , int From , int To)
{
    int num;
    do
    {
        cout << msg << "\n";
        cin >> num;
    } while (num < From || num > To);
    return num;
}

enChoice Random_Computer_Choice(int From, int To)
{
    int Rand = rand() % (To - From + 1) + From;
    return (enChoice)Rand;
}

enWinner Calc_Winner(enChoice Player_Choice, enChoice Computer_Choice)
{
    if (Player_Choice == Computer_Choice)
        return Draw;

    else if (Player_Choice == Rock && Computer_Choice == Paper)
        return Computer_Wins;

    else if (Player_Choice == Rock && Computer_Choice == Scissor)
        return Player_Wins;

    else if (Player_Choice == Scissor && Computer_Choice == Paper)
        return Player_Wins;

    else if (Player_Choice == Paper && Computer_Choice == Rock)
        return Player_Wins;

    else if (Player_Choice == Scissor && Computer_Choice == Rock)
        return Computer_Wins;

    else if (Player_Choice == Paper && Computer_Choice == Scissor)
        return Computer_Wins;


    return Draw;
}

string Get_Choice_Name(enChoice Choice)
{
    switch (Choice)
    {
    case Rock:
        return  "Rock";
    case Paper:
        return "Paper";
    case Scissor:
        return "Scissor";
    default:
        return "Unknown";
    }
}

void Round_Result(enChoice Player_Choice , enChoice Computer_Choice , int &Player_Won_Counter , int &Computer_Won_Counter , int &Draw_Counter)
{
    enWinner Who_Wins = Calc_Winner(Player_Choice, Computer_Choice);

    if (Who_Wins == Computer_Wins)
    {
        system("color 4F");
        Computer_Won_Counter++;
        cout << " Round Winner: [Computer Wins] \n";
    }
    else if (Who_Wins == Player_Wins)
    {
        system("color 2F");
        Player_Won_Counter++;
        cout << " Round Winner: [Player Wins] \n";
    }
    else
    {
        system("color 6F");
        Draw_Counter++;
        cout << " Round Winner: [Draw] \n";
    }
}

void Round(int Round_Counter, int& Player_Won_Counter, int& Computer_Won_Counter, int& Draw_Counter)
{
    enChoice Player_Choice;
    enChoice  Computer_Choice = Random_Computer_Choice(1, 3);
    cout << "\n Round [" << Round_Counter << "] Begins:\n";
    int ppc = Read_Positive_Number(" Your Choice: [1]Rock , [2]Paper , [3]Scissor ", 1, 3);
    Player_Choice = (enChoice)ppc;
    cout << "\n---------------------- Round [" << Round_Counter << "] -------------------\n\n";
    cout << " Player Choice: " << Get_Choice_Name(Player_Choice) << "\n";
    cout << " Computer Choice: " << Get_Choice_Name(Computer_Choice) << "\n";

    Round_Result(Player_Choice , Computer_Choice , Player_Won_Counter, Computer_Won_Counter, Draw_Counter);

    cout << "\n----------------------------------------------------\n\n";
}

void Total_Games_Result(int Round_Counter, int Player_Won_Counter, int Computer_Won_Counter, int Draw_Counter)
{
    cout << "\t\t\t-------------------------------------------------------\t\t\t\n";
    cout << "\t\t\t\t\t +++ Game Over +++ \n";
    cout << "\t\t\t-------------------------------------------------------\t\t\t\n";
    cout << "\t\t\t------------------- [Game Results] --------------------\t\t\t\n\n";

    cout << "\t\t\tGame Rounds: " << Round_Counter << "\n";
    cout << "\t\t\tPlayer Won Times: " << Player_Won_Counter << "\n";
    cout << "\t\t\tComputer Won Times: " << Computer_Won_Counter << "\n";
    cout << "\t\t\tDraw Times: " << Draw_Counter << "\n";

    if (Player_Won_Counter > Computer_Won_Counter)
        cout << "\t\t\tFinal Winner: Player \n";

    else if (Computer_Won_Counter > Player_Won_Counter)
        cout << "\t\t\tFinal Winner: Computer \n";

    else
        cout << "\t\t\tFinal Winner: No Winner (Draw) \n";
}

void Rounds()
{
    int Player_Won_Counter = 0, Computer_Won_Counter = 0, Draw_Counter = 0;
    short Rounds = Read_Positive_Number(" How many Rounds Do You Want ? " , 1 , 1000);
    int Round_Counter = 0;
    for (; Round_Counter < Rounds; Round_Counter++)
    {
        Round(Round_Counter+1 , Player_Won_Counter, Computer_Won_Counter, Draw_Counter);
    }

    Total_Games_Result(Round_Counter , Player_Won_Counter, Computer_Won_Counter, Draw_Counter);
}

void again()
{
    bool IsAgain;
    do
    {
        cout << "\n\t\t\tDo You Want To Play Again ? [ Yes[1] , No[0] ] : ";
        cin >> IsAgain;
        cout << "\n";
        if (IsAgain)
            Rounds();
    } while (IsAgain);
}

int main()
{
    srand((unsigned)time(NULL));
    Rounds(); 
    again();
    return 0;
}
```

## 🔮 Retrospective (Code Review)

Looking back at this code now, here is how I would improve it today:

- **Portability:** The `system("color ...")` command is Windows-specific. I would replace it with ANSI Escape Codes to work on Linux too.
    
- **Logic:** The `Calc_Winner` function uses many `if-else` statements. I could optimize it using a mathematical formula or a Lookup Table.

---

_Created: 2025_
