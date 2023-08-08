---
layout: default
title: nAttrMon cron expressions
parent: Beginner
grand_parent: Guides
---

# nAttrMon cron expressions

Throughout the plugs used in nAttrMon it's common to use ["cron" expressions](https://en.wikipedia.org/wiki/Cron). They allow you to represent the periodicity for which you want to run a particular input plug, for example.

## Minutes based

The base representation is to have 5 elements:

1. **minute** (0 - 59)
2. **hour** (0 - 23)
3. **day of month** (1 - 31)
4. **month** (1 - 12)
5. **day of the week** (0 - 6) (Sunday to Saturday)

The order is ```1 2 3 4 5``` (minute, hour, day of month, month and day of week).

## Seconds based

It's also possible to enter a seconds based representation (which is different from the minutes based since it has 6 elements):

1. **second** (0 - 59)
2. **minute** (0 - 59)
3. **hour** (0 - 23)
4. **day of month** (1 - 31)
5. **month** (1 - 12)
6. **day of the week** (0 - 6) (Sunday to Saturday)

The order is ```1 2 3 4 5 6``` (second, minute, hour, day of month, month and day of week).

## Examples

* "*/5 * * * * *" - Runs every 5 seconds
* "10 8 * * *" - Runs at 8:10am every day
* "0 */1 * * *"  - Runs every hour
* "30 2 * * 0,6" - Runs at 2:30am every weekend

## How to test

You can actually test a cron expression, using OpenAF and the same library used by nAttrMon, to understand what will be it's occurrences. On a computer with Internet access try to execute:

```bash
$ ojob ojob.io/oaf/parseCron cron="30\ 2\ *\ *\ 0,6"
╭ cron ╭ expression: 30 2 * * 0,6
│      ├ schedules  ─ [0] ╭ second  ─ [0]: 0
│      │                  ├ minute  ─ [0]: 30
│      │                  ├ hour    ─ [0]: 2
│      │                  ╰ weekDay ╭ [0]: 1
│      │                            ╰ [1]: 7
│      ╰ exceptions
╰ next ╭ [0] ╭ date    : Sat Aug 12 2023 03:30:00 GMT+0100 (WEST)
       │     ╰ diffPrev:
       ├ [1] ╭ date    : Sun Aug 13 2023 03:30:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ├ [2] ╭ date    : Sat Aug 19 2023 03:30:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ├ [3] ╭ date    : Sun Aug 20 2023 03:30:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ╰ [4] ╭ date    : Sat Aug 26 2023 03:30:00 GMT+0100 (WEST)
             ╰ diffPrev: >1 ms
```

The output is composed of the details of the cron expression provided (dont' forget to escape the " " (spaces) with a "\"). It also provides the next 5 occurrences in the future. You get more in past and in the future with some extra arguments:

```bash
$ ojob ojob.io/oaf/parseCron cron="0\ */4\ *\ *\ *" next=5 prev=5
╭ cron ╭ expression: 0 */4 * * *
│      ├ schedules  ─ [0] ╭ second ─ [0]: 0
│      │                  ├ minute ─ [0]: 0
│      │                  ╰ hour   ╭ [0]: 0
│      │                           ├ [1]: 4
│      │                           ├ [2]: 8
│      │                           ├ [3]: 12
│      │                           ├ [4]: 16
│      │                           ╰ [5]: 20
│      ╰ exceptions
├ prev ╭ [0] ╭ date    : Tue Aug 08 2023 01:00:00 GMT+0100 (WEST)
│      │     ╰ diffNext: 4 hours
│      ├ [1] ╭ date    : Tue Aug 08 2023 05:00:00 GMT+0100 (WEST)
│      │     ╰ diffNext: 4 hours
│      ├ [2] ╭ date    : Tue Aug 08 2023 09:00:00 GMT+0100 (WEST)
│      │     ╰ diffNext: 4 hours
│      ├ [3] ╭ date    : Tue Aug 08 2023 13:00:00 GMT+0100 (WEST)
│      │     ╰ diffNext: 4 hours
│      ╰ [4] ╭ date    : Tue Aug 08 2023 17:00:00 GMT+0100 (WEST)
│            ╰ diffNext:
╰ next ╭ [0] ╭ date    : Tue Aug 08 2023 21:00:00 GMT+0100 (WEST)
       │     ╰ diffPrev:
       ├ [1] ╭ date    : Wed Aug 09 2023 01:00:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ├ [2] ╭ date    : Wed Aug 09 2023 05:00:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ├ [3] ╭ date    : Wed Aug 09 2023 09:00:00 GMT+0100 (WEST)
       │     ╰ diffPrev: >1 ms
       ╰ [4] ╭ date    : Wed Aug 09 2023 13:00:00 GMT+0100 (WEST)
             ╰ diffPrev: >1 ms
```

The _next_ and _prev_ arguments allow to enter the number of occurences going back and/or forward.