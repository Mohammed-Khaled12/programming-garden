
> [!ABSTRACT] Why Files?
> Variables live in RAM (Temporary). Files live on the Disk (Permanent).
> * **Library:** `<fstream>`
> * **Classes:** `ofstream` (Write), `ifstream` (Read), `fstream` (Both).

**Header Required:** `#include <fstream>`

Tags: #cpp #files #persistence #io

---

# 1. Write Mode
افتح ملف و اخزن عليه الداتا
وضع الـ **Write (`ios::out`)** ده بيمسح أي حاجة موجودة في الملف ويبدأ يكتب من جديد. لو الملف مش موجود، هو بيكريهه (ينشئه).

```c++
#include <iostream>
#include <fstream> 

using namespace std;

int main() {
    fstream myFile;
    
    // فتح الملف في وضع الكتابة
    myFile.open("MyData.txt", ios::out); 

    if (myFile.is_open()) {
        myFile << "Hello, this is the first line!\n";
        myFile << "I am becoming a C++ Hero.\n";
        
        myFile.close(); // مهم جداً تقفل الملف عشان البيانات تتحفظ
        cout << "File written successfully!" << endl;
    }
    
    return 0;
}
```

# 2. Append Mode

لو مش عايز تمسح اللي فات وعايز تزود سطر جديد في آخر الملف، بنستخدم وضع الـ **Append (`ios::app`)**.

```c++
#include <iostream>
#include <fstream>
using namespace std;
int main() {
fstream MyFile;

MyFile.open("MyFile.txt", ios::out | ios::app );//append Mode
// Another Way to Append --> MyFile.open("MyFile.txt", ios::app);

if (MyFile.is_open())
{
MyFile << "Hi, this is a new line\n";
MyFile << "Hi, this is another new line\n";
MyFile.close();
}
return 0;
}
```

# 3. Read Mode

عشان تقرأ من الملف، بنستخدم وضع **Read (`ios::in`)**. التحدي هنا إننا بنقرأ سطر بسطر لحد ما الملف يخلص

```c++
#include <iostream>
#include <fstream>
#include <string>
using namespace std;
void PrintFileContenet(string FileName)
{
fstream MyFile;
MyFile.open( FileName, ios::in );//read Mode
if (MyFile.is_open())
{
string Line;
while (getline(MyFile, Line))
{
cout << Line << endl;
}
MyFile.close();
}
}
int main() {
PrintFileContenet("MyFile.txt");
return 0;
}
```

# 4. Load Data From File to Vector

في البرامج الحقيقية، مش بنقرأ سطر ونطبعه وخلاص، إحنا بنسحب بيانات الملف كلها نحطها في الـ **RAM** (على شكل `vector`) عشان نعدل فيها براحتنا، وبعدين نرجع نحفظ الـ vector ده في الملف تاني.

```c++
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

void LoadDataFromFileToVector(string FileNam, vector<string>& vFileContenet)
{
    fstream MyFile;
    MyFile.open(FileNam, ios::in);
    if (MyFile.is_open())
    {
        string line;

        while (getline(MyFile, line))
        {
            vFileContenet.push_back(line);
        }
        MyFile.close();
    }
}
int main() {
    
    vector<string> vFileContenet;

    LoadDataFromFileToVector("FIRST.txt", vFileContenet);

    for (string& x : vFileContenet)
    {
        cout << x << "\n";
    }

    return 0;
}
```

# 5. Save Vector to File

```c++
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
using namespace std;
void SaveVectorToFile(string FileName, vector <string> vFileContent)
{
fstream MyFile;
MyFile.open("MyFile.txt", ios::out);
if (MyFile.is_open())
{
for (string &Line : vFileContent)
{
	if (Line != "")
	{
	  MyFile << Line << endl;
	}
}
MyFile.close();
}
}
int main()
{
vector <string> vFileContenet{"Ali","Shadi","Maher","Fadi","Lama"};
SaveVectorToFile("MyFile.txt", vFileContenet);
return 0;
}
```

# 6. Delete Record From File

في C++ مفيش أمر مباشر بيمسح سطر معين من نص الملف. التريك هي:

1. حمل الملف كله في **Vector**.
    
2. امسح السطر اللي مش عايزه من الـ **Vector**.
    
3. احفظ الـ **Vector** تاني في الملف.
```c++
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
using namespace std;
void LoadDataFromFileToVector(string FileName, vector <string>& vFileContent)
{
    fstream MyFile;
    MyFile.open("MyFile.txt", ios::in);//read Mode
    if (MyFile.is_open())
    {
        string Line;
        while (getline(MyFile, Line))
        {
            vFileContent.push_back(Line);
        }
        MyFile.close();
    }
}
void SaveVectorToFile(string FileName, vector <string> vFileContent)
{
    fstream MyFile;
    MyFile.open("MyFile.txt", ios::out);
    if (MyFile.is_open())
    {
        for (string Line : vFileContent)
        {
            if (Line != "")
            {
                MyFile << Line << endl;
            }
        }
        MyFile.close();
    }
}

void DeleteRecordFromFile(string FileName, string Record)
{
    vector <string> vFileContent;
    LoadDataFromFileToVector(FileName, vFileContent);
    for (string& Line : vFileContent)
    {
        if (Line == Record)
        {
            Line = "";
        }
    }
    SaveVectorToFile(FileName, vFileContent);
}
void PrintFileContent(string FileName)
{
    fstream MyFile;
    MyFile.open(FileName, ios::in);//read Mode
    if (MyFile.is_open())
    {
        string Line;
        while (getline(MyFile, Line))
        {
            cout << Line << endl;
        }
        MyFile.close();
    }
}

int main() {
    cout << "File Content Before Delete.\n";
    PrintFileContent("MyFile.txt");
    DeleteRecordFromFile("MyFile.txt", "Ali");
    cout << "\n\nFile Content After Delete.\n";
    PrintFileContent("MyFile.txt");
    return 0;
}
```

# 7. Update Record In File

نفس فكرة الحذف بالظبط، بس بدل ما بنخلي السطر فاضي، بنبدله بالبيانات الجديدة.

```c++
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
using namespace std;
void LoadDataFromFileToVector(string FileName, vector <string>& vFileContent)
{
    fstream MyFile;
    MyFile.open("MyFile.txt", ios::in);//read Mode
    if (MyFile.is_open())
    {
        string Line;
        while (getline(MyFile, Line))
        {
            vFileContent.push_back(Line);
        }
        MyFile.close();
    }
}
void SaveVectorToFile(string FileName, vector <string> vFileContent)
{
    fstream MyFile;
    MyFile.open("MyFile.txt", ios::out);
    if (MyFile.is_open())
    {
        for (string Line : vFileContent)
        {
            if (Line != "")
            {
                MyFile << Line << endl;
            }
        }
        MyFile.close();
    }
}

void UpdateRecordInFile(string FileName, string Record, string UpdateTo)
{
    vector <string> vFileContent;
    LoadDataFromFileToVector(FileName, vFileContent);
    for (string& Line : vFileContent)
    {
        if (Line == Record)
        {
            Line = UpdateTo;
        }
    }
    SaveVectorToFile(FileName, vFileContent);
}
void PrintFileContent(string FileName)
{
    fstream MyFile;
    MyFile.open(FileName, ios::in);//read Mode
    if (MyFile.is_open())
    {
        string Line;
        while (getline(MyFile, Line))
        {
            cout << Line << endl;
        }
        MyFile.close();
    }
}

int main() {
    cout << "File Content Before Delete.\n";
    PrintFileContent("MyFile.txt");
    UpdateRecordInFile("MyFile.txt", "Ali", "Omar");
    cout << "\n\nFile Content After Delete.\n";
    PrintFileContent("MyFile.txt");
    return 0;
}
```