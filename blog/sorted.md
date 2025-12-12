# Sorted

```
/**
MIT License

Copyright (c) 2025 PSD Authors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

**/

#include <stdio.h>
#include <string.h>

void bubbleSort(char strings[][100], int n) {
    int i, j;
    char temp[100];
    
    for (i = 0; i < n - 1; i++) {
        for (j = 0; j < n - i - 1; j++) {
            if (strcmp(strings[j], strings[j + 1]) > 0) {
                // Swap strings
                strcpy(temp, strings[j]);
                strcpy(strings[j], strings[j + 1]);
                strcpy(strings[j + 1], temp);
            }
        }
    }
}

int main() {
    char strings[100][100];  // Store up to 100 strings
    int count = 0;
    int i;
    
    printf("Enter strings (enter 'q' to quit):\n");
    
    while (1) {
        printf("String %d: ", count + 1);
        scanf("%s", strings[count]);
        
        // Check if user wants to quit
        if (strcmp(strings[count], "q") == 0) {
            break;
        }
        
        count++;
        
        // Prevent overflow
        if (count >= 100) {
            printf("Maximum number of strings reached.\n");
            break;
        }
    }
    
    // Sort strings alphabetically
    bubbleSort(strings, count);
    
    // Print sorted strings
    printf("\nSorted strings:\n");
    for (i = 0; i < count; i++) {
        printf("%s\n", strings[i]);
    }
    
    return 0;
}
```
