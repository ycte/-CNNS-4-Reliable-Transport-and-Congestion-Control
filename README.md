#### 编译运行

```
make
perl trawler.pl 8888 scripts/two.topo
```

```
perl fishnet.pl emulate localhost 8888 10000
server 21 2
```

```
perl fishnet.pl emulate localhost 8888 10001
transfer 0 21 40 50000
```

#### 文件结构

*  文件夹 Source-code-fishnetJava-1.8-link: Your source code
* [brief_design_document.pdf](brief_design_document.pdf) : A brief design document
*  [output.pdf](output.pdf) : A document containing any output we have asked you to capture
*  [short_answers.pdf](short_answers.pdf) : Short answers to the discussion questions below: Diss1a, Diss1b, and Diss1c for Part 1; Diss2 for the Part 2 if you are doing it as a project