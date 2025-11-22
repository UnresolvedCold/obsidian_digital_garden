---
{"dg-publish":true,"permalink":"/garden/run-a-non-default-main-function-from-jar-file/","tags":["insight"]}
---

```bash
# java -cp your-jar.jar full-class-path
java -cp multifleet-planner-1.0-SNAPSHOT.jar com.greyorange.multifleetplanner.JsonTest

mvn -pl multifleet-planner exec:java -Dexec.mainClass="com.greyorange.multifleetplanner.JsonTest" -Dexec.args="true"

```
