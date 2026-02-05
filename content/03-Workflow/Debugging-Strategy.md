
## What Is Debugging ?

The Process of Finding and Fixing Errors in Your Program
By Running The Program Line By Line and Trace the Values at Each Step.

## How To Debug Your Code ?

### Breakpoint and Memory Values:

اي IDE هتلاقي في مودين Debug mode and Release mode علشان نبدا نعمل Debug هنخلي المود علي Debug 
ال Release mode يبقي اسرع في الكومبيل بس مبيبقاش فيه ديباج .

>Breakpoint --> Tell the IDE to Stop here "يقف قبل ما ينفذ السطر اللي حططتله بريك بوينت"
>Step Into  --> Runs The Code Line by Line and Goes Into the Details of any Function 
>Step Over --> Runs The Code Line by Line Without Going Into the Details of any Function
>Step Out --> If You are Inside a Function , it will Finish The Function Quickly and return to the caller

## ⌨️ Shortcuts Cheat Sheet

| Action        |     Key     | Description                         |
| :------------ | :---------: | :---------------------------------- |
| **Step Into** |    `F11`    | Enter the function details.         |
| **Step Over** |    `F10`    | Run line without entering function. |
| **Step Out**  | `Shift+F11` | Finish function and return.         |
| **Resume**    |    `F5`     | Jump to next breakpoint.            |

****_Notes_**** :
1) *You Can add Multiple Breakpoints* 
   
2) *To Jump to the next Breakpoint Press F5 , The IDE will run the program to the Next Breakpoint*
   
3) *You Can Disable Breakpoints Temporarily  Debug > Disable All Breakpoints or Left Click on a Breakpoint and Disable it Individually*
   
4)  *With Autos Window You Can Keep an Eye , and See the Values  of the Local Variables at every line of code*
   
5) *Quick Watch Window : Allows You to inspect any Variable in Details and Evaluate any Expression*
   *How To Open It ? --> Highlight the Line You Want to See and Press Shift + F9*
   
6) *You Can Change Values of Variables During Debugging from Autos , Quick watch Window or By mouse (Step on the var. in debugging and change the small number value)*.