---
{"dg-publish":true,"permalink":"/garden/pattern-matching/","tags":["compilation","gz","log","zgrep","gzip","awk"]}
---

To search from pattern matching `4usXzpWPR1G1Q8PvSmFImg==` to `OJOH7fOvRiimCgrwgAFoQA==` from log.gz files

Actually everything inside slashes are regex pattern. 

```
gzip -cd scheduler.2025-09-12.*.log.gz | sed -n '/4usXzpWPR1G1Q8PvSmFImg==/,/OJOH7fOvRiimCgrwgAFoQA==/p' > log
```


More powerful with `awk`. 
`awk` evaluates set of commands. 
`index($0, "aeq9M/vWSVSt5cMG2DJCKg==")` returns position of `aeq9M/vWSVSt5cMG2DJCKg==` inside `$0` which represents the current line.
If the above is true then it sets variable `in_block` to `1`.
`awk` will start printing any line if the condition is true. And here the condition is `in_block` which was set as true when we found the first match.

```
gzip -cd scheduler.2025-09-12.*.log.gz \
  | awk '
    index($0,"aeq9M/vWSVSt5cMG2DJCKg=="){in_block=1}
    in_block
    index($0,"OJOH7fOvRiimCgrwgAFoQA=="){in_block=0}
  ' > log

```