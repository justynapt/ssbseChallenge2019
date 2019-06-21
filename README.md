# Software Improvement with Gin: a Case Study

This is an artefact to accompany the "Software Improvement with Gin: a Case Study" paper by Justyna Petke and Sandy Brownlee, accepted to the [SSBSE 2019 Challenge Track](http://ssbse19.mines-albi.fr/tracks.html#challenge-track).

This repository contains: gp folder with source code of our new GP classes, input files, and output files (all patches and runtimes). We used Java 8, Gradle 4.6 for Gin and Maven 3.6 for Gson in our experiments.

To reproduce results in the paper:

Create gin.jar: Download the source code for [Gin v2.0](https://github.com/gintool/gin/releases). Add the GP classes in the src/main/java/util folder and run ./gradlew build from within the gin repo. This will create gin.jar in the gin/build folder.

Once done, please follow the following steps:

```
git clone https://github.com/justynapt/ssbseChallenge2019.git
cd ssbseChallenge2019
git clone https://github.com/google/gson.git gson
cd gson
git checkout gson-parent-2.8.5
```

Fix Gson's faulty test and compile:
```
cp ../input/ConcurrencyTest.java gson/src/test/java/com/google/gson/functional/ConcurrencyTest.java
mvn compile
mvn test
```
Without this fix, experiments can still be run, but with the much slower -j option.

Put gin.jar and create\_profile\_results.csv and create\_profile\_results\_all.csv (input files) in the same repository as gson:
```
cp <path_to_gin.jar>/gin.jar .
cp ../input/*csv .
```

All parameters can be viewed with:
```
java -cp gin.jar gin.util.GPRuntime
java -cp gin.jar gin.util.GPFix
```

Please note that mavenHome refers to the maven home path, e.g., if the path is "/usr/local/bin/mvn", only  "/usr/local/" should be input to the -h parameter. 

For runtime improvement run:
```
time java -cp gin.jar gin.util.GPRuntime -d . -p gson -x 2000  -m create_profile_results.csv -h <mavenHome> -in 21 -gn 10 -r 500 
time java -cp gin.jar gin.util.GPRuntime -d . -p gson -x 2000  -m create_profile_results_all.csv -h <mavenHome> -in 21 -gn 10 -r 67
```

Edit gson/src/main/java/com/google/gson/GsonBuilder.java and inject the following error: 
```
587c587
<     List<TypeAdapterFactory> factories = new ArrayList<TypeAdapterFactory>(this.factories.size() + this.hierarchyFactories.size() + 3); 
---
>     List<TypeAdapterFactory> factories = new ArrayList<TypeAdapterFactory>(this.factories.size() + this.hierarchyFactories.size() - 3); 
```

Re-compile all classes after the edit. For program repair run:

```
mvn compile
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results.csv -h <mavenHome> -in 21 -gn 10 -et MODIFY_STATEMENT
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results_all.csv -h <mavenHome> -in 21 -gn 10 -et MODIFY_STATEMENT
```

Edit gson/src/main/java/com/google/gson/GsonBuilder.java, remove previous bug, and inject the following error: 
```
588d587
<     factories.addAll(this.factories);
589a589
>     factories.addAll(this.factories);
```

Re-compile all classes after the edit. For program repair run:
```
mvn compile
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results.csv -h <mavenHome> -gn 10 -in 21
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results_all.csv -h <mavenHome> -gn 10 -in 21
```

Edit gson/src/main/java/com/google/gson/GsonBuilder.java, remove previous bug, and inject the following error: 
```
589a590
>     Collections.reverse(factories);
```

Re-compile all classes after the edit. For program repair run:
```
mvn compile
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results.csv -h <mavenHome> -gn 10 -in 21
time java -cp gin.jar gin.util.GPFix -d . -p gson -x 2000  -m create_profile_results_all.csv -h <mavenHome> -gn 10 -in 21
```

If you have any questions, please do not hesittate to contact [Justyna Petke](mailto:j.petke@ucl.ac.uk).
