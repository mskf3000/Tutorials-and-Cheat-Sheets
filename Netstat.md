## Netstat

By Jack Szwergold

#### Some basic usage examples.

Use `watch` to check the number of established connections at an interval of 1 second:

	watch -n 1 "netstat -an | grep ESTABLISHED | wc -l"

***

*Netstat (c) by Jack Szwergold; written on November 9, 2017. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
