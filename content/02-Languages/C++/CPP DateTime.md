
> [!ABSTRACT] Handling Time in C++
> Computers calculate time as the number of seconds passed since **Jan 1, 1970** (Epoch Time).
> To make this readable for humans, we convert these seconds into a **Structure** (Year, Month, Day...).
> * **Library:** `<ctime>` (Classic C-Style) or `<chrono>` (Modern).
> * **Key Struct:** `struct tm` (The container that holds time parts).

**Header Required:** `#include <ctime>`

Tags: #cpp #datetime #time #structs #benchmarking

---

# Getting Current Time

```c++
#pragma warning(disable : 4996)
#include <ctime>
#include <iostream>
using namespace std;
int main()
{
    time_t t = time(0); // get time now
    char* dt = ctime(&t); // convert in string form
    cout << "Local date and time is: " << dt << "\n";
    // converting now to tm struct for UTC date/time
    tm* gmtm = gmtime(&t);
    dt = asctime(gmtm);
    cout << "UTC date and time is: " << dt;
}
```

# Datetime Structure
```c++
#pragma warning(disable : 4996)
#include <ctime>
#include <iostream>
using namespace std;
/*
int tm_sec; // seconds of minutes from 0 to 61
int tm_min; // minutes of hour from 0 to 59
int tm_hour; // hours of day from 0 to 24
int tm_mday; // day of month from 1 to 31
int tm_mon; // month of year from 0 to 11
int tm_year; // year since 1900
int tm_wday; // days since sunday
int tm_yday; // days since January 1st
int tm_isdst; // hours of daylight savings time
*/
int main() {
time_t t = time(0); // get time now
tm* now = localtime(&t);
cout << "Year: " << now->tm_year + 1900 << endl;
cout << "Month: " << now->tm_mon + 1 << endl;
cout << "Day: " << now->tm_mday << endl;
cout << "Hour: " << now->tm_hour << endl;
cout << "Min: " << now->tm_min << endl;
cout << "Second: " << now->tm_sec << endl;
cout << "Week Day (Days since sunday): " << now->tm_wday << endl;
cout << "Year Day (Days since Jan 1st): " << now->tm_yday << endl;
cout << "hours of daylight savings:" << now->tm_isdst << endl;
}
```

# Complete Implementation (Detailed Breakdown)

```c++
#include <iostream>
#include <ctime>
#include <unistd.h> // usleep for unix
using namespace std;
int main()
{
    // Getting Time In Seconds Since January 1, 1970
    // Returning int
    time_t now = time(nullptr);
    cout << now << "\n";         // Not Readable
    cout << ctime(&now); // Much More Readable , Note This Function Adds \n To it's end auto

    // Let's Break it in Individual Components Using struct TM
    // Date:
    struct tm *localTime = localtime(&now);

    int year = localTime->tm_year;    // Years Since 1900
    cout << year << "\n";             // 126
    year = localTime->tm_year + 1900; // Convert It to truely Year
    cout << year << "\n";             // 2026

    int month = localTime->tm_mon; // Month Index (Note --> it's Zero Based)
    cout << month << "\n";         // 1    , As I'm now in Feb
    month = localTime->tm_mon + 1; // Convert it to Normal Index
    cout << month << "\n";         // 2    , As I'm now in Feb , after Fixing The Indexing

    int monthDay = localTime->tm_mday; // month Day
    cout << monthDay << "\n";          // 21 As mine
    int weekDay = localTime->tm_wday;  // week Day Zero Is Sunday , 6 Saturday (You Can Convert The Index By Adding 1 like Before)
    cout << weekDay << "\n";           // 6

    cout << month << "/" << monthDay << "/" << year << "\n"; // 2/21/2026

    // Time:
    // struct tm *localTime = localtime(&now);

    int hour = localTime->tm_hour;
    cout << hour << "\n"; // 15 (24 Hours Clock)

    int minute = localTime->tm_min;
    cout << minute << "\n"; // 29

    int second = localTime->tm_sec;
    cout << second << "\n"; // 26

    cout << hour << ":" << minute << ":" << second << "\n"; // 15:29:26

    // Timing: Used For Calc how long a program or Algo Took (diff between Strat and End)
    time_t start = time(nullptr);
    cout << ctime(&start); // Sat Feb 21 15:50:20 2026
    usleep(2000000);               // pause 2 seconds (takes milliseconds)
    time_t end = time(nullptr);
    cout << ctime(&end);  // Sat Feb 21 15:50:22 2026
    cout << end - start << "\n";  // 2
    cout << difftime(end, start); // 2 (Return the difference between TIME1 and TIME0.)
    return 0;
}
```

##  Important  Notes

- **`usleep(microseconds)`**: Remember that $1,000,000$ microseconds = $1$ second.
    
- **`difftime(t2, t1)`**: Returns a `double`. It's safer for cross-platform time difference than direct subtraction.
    
- **Precision Issue**: `time_t` is NOT suitable for measuring fast Algorithms (less than 1 second). For that, we use the modern `<chrono>` library.
    
- **Thread Safety**: `localtime` returns a pointer to a static object. Subsequent calls will overwrite the previous data.

# 🔗 Related Notes

- [[CPP Pointers]]: `localtime` and `gmtime` return pointers to `tm` structures.
    
- [[CPP File Handling]]: Essential for timestamping log files.
    
- **Next Step**: Check `<chrono>` for high-resolution benchmarking (nanoseconds).

---
