# Reversing.kr -- x64 Lotto

## 1. Challenge

Just an exe file `Lotto.exe`:

Please goto [http://reversing.kr/challenge.php](http://reversing.kr/challenge.php) to download.

## 2. Solution

Drop it into IDA. 

You will see the program will ask you input 6 integers and then use `rand()` function to generate 6 integers. Only these 6 integers are 	respectively equal will you get the flag.

However, there is something interesting. You will see `srand()` is called before `rand()` and the seed of random numbers is the current timestamp which is not very precise, of course, for computer speaking.

That means we can know the 6 integers generated by `rand()` if we know the timestamp when `srand()` is called. 

So I wrote C++ code to generate these 6 integers and start `Lotto.exe` immediately, which is highly possible to be finished in a second so that the seed is the same.

```cpp
#include <time.h>
#include <stdio.h>
#include <windows.h>

int main(int argc, char* argv[]) {
    STARTUPINFO si = { sizeof(STARTUPINFO) };
    PROCESS_INFORMATION pi = {};
    char ProcessName[] = "Lotto.exe";

    srand(_time64(NULL));
	printf("Here is the numbers:\n");
    for (int i = 0; i < 6; ++i) {
        printf("%d ", rand() % 100);
    }
    printf("\n");
    
    CreateProcess(ProcessName, ProcessName, NULL, NULL, FALSE, NULL, NULL, NULL, &si, &pi);
    WaitForSingleObject(pi.hProcess, INFINITE);
    CloseHandle(pi.hThread);
    CloseHandle(pi.hProcess);
    return 0;
}
```

Please use gcc (or MSVC) to build:

```bash
$ gcc run.cpp -o run.exe
```

Put `run.exe` and `Lotto.exe` in the same folder and start `run.exe` to play this game. After that you will see the flag.