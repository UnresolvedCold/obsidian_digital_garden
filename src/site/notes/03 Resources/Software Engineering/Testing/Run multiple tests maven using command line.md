---
{"dg-publish":true,"permalink":"/03-resources/software-engineering/testing/run-multiple-tests-maven-using-command-line/","tags":["compilation"]}
---

```bash
mvn -pl multifleet-planner \
    -Dtest=com.greyorange.idctest.AssignmentOptaplannerTest,com.greyorange.idctest.IDCManagerTest \
    test -Puse-aspect
```
