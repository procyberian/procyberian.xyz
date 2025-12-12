# World Clock

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
#include <time.h>
#include <stdlib.h>

// Portable replacement for timegm: interpret struct tm as UTC
static time_t timegm_compat(struct tm *tm) {
    char *oldtz = getenv("TZ");
    setenv("TZ", "UTC", 1);
    tzset();
    time_t t = mktime(tm);
    if (oldtz) setenv("TZ", oldtz, 1); else unsetenv("TZ");
    tzset();
    return t;
}

typedef struct {
    const char *label;
    int offset_minutes; // minutes east of UTC
} Zone;

// Sorted from far east (largest positive offset) to far west (largest negative offset)
static const Zone ZONES[] = {
    {"UTC+14 (Line Islands)",            14*60},
    {"UTC+13 (NZDT/Tonga)",              13*60},
    {"UTC+12:45 (Chatham Islands)",      12*60 + 45},
    {"UTC+12 (Fiji/NZ)",                 12*60},
    {"UTC+11 (Solomon/Magadan)",         11*60},
    {"UTC+10:30 (Australia CDT)",        10*60 + 30},
    {"UTC+10 (AEST/Guam)",               10*60},
    {"UTC+9:30 (Australia ACST)",         9*60 + 30},
    {"UTC+9 (JST/KST)",                   9*60},
    {"UTC+8 (China/Singapore)",           8*60},
    {"UTC+7 (Thailand/Vietnam)",          7*60},
    {"UTC+6:30 (Myanmar)",                6*60 + 30},
    {"UTC+6 (Bangladesh)",                6*60},
    {"UTC+5:45 (Nepal)",                  5*60 + 45},
    {"UTC+5:30 (India)",                  5*60 + 30},
    {"UTC+5 (Pakistan)",                  5*60},
    {"UTC+4:30 (Afghanistan)",            4*60 + 30},
    {"UTC+4 (UAE/Georgia)",               4*60},
    {"UTC+3:30 (Iran)",                   3*60 + 30},
    {"UTC+3 (Istanbul/Türkiye)",          3*60},
    {"UTC+3 (Moscow/East Africa)",        3*60},
    {"UTC+2 (South Africa)",              2*60},
    {"UTC+1 (Central Europe)",            1*60},
    {"UTC±0 (UTC/GMT)",                   0},
    {"UTC-1 (Azores)",                   -1*60},
    {"UTC-2 (South Georgia)",            -2*60},
    {"UTC-3 (Argentina/Uruguay)",        -3*60},
    {"UTC-3:30 (Newfoundland)",          -3*60 - 30},
    {"UTC-4 (Atlantic)",                 -4*60},
    {"UTC-5 (US Eastern)",               -5*60},
    {"UTC-6 (US Central)",               -6*60},
    {"UTC-7 (US Mountain)",              -7*60},
    {"UTC-8 (US Pacific)",               -8*60},
    {"UTC-9 (Alaska)",                   -9*60},
    {"UTC-10 (Hawaii-Aleutian)",        -10*60},
    {"UTC-11 (Samoa)",                   -11*60},
    {"UTC-12 (Baker/Howland)",          -12*60}
};

int main(void) {
    int day, month, year, hour, minute, second;

    printf("Enter Istanbul time (DD MM YYYY HH MM SS):\n");
    if (scanf("%d %d %d %d %d %d", &day, &month, &year, &hour, &minute, &second) != 6) {
        fprintf(stderr, "Invalid input. Expected 6 integers.\n");
        return 1;
    }

    // Print the input Istanbul time
    printf("\nYou entered (Istanbul/Türkiye UTC+3): %02d-%02d-%04d %02d:%02d:%02d\n",
           day, month, year, hour, minute, second);

    struct tm tm_istanbul = {0};
    tm_istanbul.tm_mday = day;
    tm_istanbul.tm_mon  = month - 1;
    tm_istanbul.tm_year = year - 1900;
    tm_istanbul.tm_hour = hour;
    tm_istanbul.tm_min  = minute;
    tm_istanbul.tm_sec  = second;
    tm_istanbul.tm_isdst = -1;

    // Treat input as if it were UTC, then subtract Istanbul's offset (UTC+3 = 180 minutes)
    // to get the actual UTC epoch
    time_t istanbul_epoch = timegm_compat(&tm_istanbul);
    time_t base_epoch = istanbul_epoch - (3 * 60 * 60); // Convert Istanbul to UTC

    printf("\nTimes across UTC offsets (east to west):\n");

    char buf[64];

    for (size_t i = 0; i < sizeof(ZONES)/sizeof(ZONES[0]); ++i) {
        const Zone *z = &ZONES[i];
        time_t epoch_z = base_epoch + (time_t)z->offset_minutes * 60;
        struct tm out_tm;
#if defined(_POSIX_THREAD_SAFE_FUNCTIONS)
        gmtime_r(&epoch_z, &out_tm);
#else
        struct tm *tmp = gmtime(&epoch_z);
        if (!tmp) {
            fprintf(stderr, "Failed to convert for %s.\n", z->label);
            continue;
        }
        out_tm = *tmp;
#endif
        if (strftime(buf, sizeof(buf), "%Y-%m-%d %H:%M:%S", &out_tm) == 0) {
            fprintf(stderr, "Format error for %s.\n", z->label);
            continue;
        }
        printf("%-28s -> %s\n", z->label, buf);
    }

    return 0;
}
```
