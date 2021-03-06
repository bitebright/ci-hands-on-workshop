# SonarCloud
SonarCloud can be viewed as a Continuous Inspection service for our code base. Its jobs is to verify that that code we wrote are implemented in such a way that meets the standard of that particular language. Moreover, it looks into the test coverage percentage of our code as well. SonarCloud is a could service that is base on SonarQube but without the complexity of maintainability.

## Start using SonarCloud
1. Visit https://sonarcloud.io/about and click at "Start using SonarCloud".

![start using](https://user-images.githubusercontent.com/11821799/46253610-04464a80-c4aa-11e8-8f64-c8ffa308f96d.png)

2. Login with your GitHub account.

3. Now, we will connect our GitHub repository (or repositories) with SonarCloud. Create new project by clicking at the following.

![new proj](https://user-images.githubusercontent.com/11821799/46253629-d281b380-c4aa-11e8-8bbe-348d269ff29e.png)
![connect github](https://user-images.githubusercontent.com/11821799/46253636-0361e880-c4ab-11e8-9a31-779adc9eb2c3.png)

4. You will see an option asking you to select whether you want to connect SonarCloud to all of your repositories or just specific one. For now, let's just connect it to our workshop repository.

![workshow repo](https://user-images.githubusercontent.com/11821799/46253657-8a16c580-c4ab-11e8-9c5e-15ebf9e0dbdd.png)

5. After you've created a project. It will ask you to generate token for the repository's analysis.

![create token](https://user-images.githubusercontent.com/11821799/46253675-1e812800-c4ac-11e8-9d0d-e524bfae03cd.png)

6. Next, we will have to provide the programming language of our project and the build technology to SonarCloud. Then it will display 2 commands for us to follow.

![language and build](https://user-images.githubusercontent.com/11821799/46253702-ee865480-c4ac-11e8-969b-4447f28baa48.png)

7. Copy the first command and make the following change to our build.gradle
```
plugins {
    // Apply the java plugin to add support for Java
    id 'java'

    // Apply the application plugin to add support for building an application
    id 'application'

    // Add the SonarQube plugin here
    // This this what you copied from the command SonarCloud's website had provided to you.
    id "org.sonarqube" version "2.6"
}
```
8. Now copy and run the second command
    * For Windows user, run the second command on GitBash (there was a problem when I tried to run it with cmd and powerShell).

9. After the second command finished executing, reload the SonarCloud's website. It will display analytic results to you.

![analytic results](https://user-images.githubusercontent.com/11821799/46253852-e1b73000-c4af-11e8-9309-8a9e7676958e.png)

10. What just happened ? We have just push our code in the working branch to SonarCloud to analyze the standard of our code quality.

11. Is the code that is being analyze comes from the origin or local changes ? Try adding the following lines into your App.java. Save the file without committing or pushing the code change.
```java
    public static void main(String[] args) {
        String amount = args[0];

        // Add this line
        double result = 1.1 + 1.2;

        CurrencyFormatter currencyFormatter = new CurrencyFormatter();

        System.out.println(currencyFormatter.format(amount));
    }
```
12. Rerun the analyze command again !

## More problems !
We have said earlier that SonarCloud does not only analyze your code base, but also reports the unit test coverage of our project. 

Having unit test coverage shows us how trustful our software is. With test coverage report, we should be able to easily tell where unit test are missing and prioritize them in the development team.

However, right now, even we have added some unit test in the beginning of this workshop SonarCloud still reports 0% coverage for our project.

![no coverage](https://user-images.githubusercontent.com/11821799/46254263-98b6aa00-c4b6-11e8-8686-3fd9d019ace6.png)

## Get test coverage to display
If we take a look at our build.gradle, we will see that we do not have any plugin for code coverage. That is why SonarCloud were not able to pick up the result and display the report to us.

### Let's add JaCoCo (Java Code Coverage)
JaCoCo is a gradle's plugin that helps provide code coverage metrics to us. You can read more about JaCoCo [here](https://docs.gradle.org/current/userguide/jacoco_plugin.html).

1. Go to build.gradle and then add JaCoCo into plugins section. Here is what our plugin section looks like.
```
plugins {
    // Apply the java plugin to add support for Java
    id 'java'

    // Apply the application plugin to add support for building an application
    id 'application'

    id "org.sonarqube" version "2.6"

    // Add this line
    id "jacoco"
}
```

2. To see the test report, run the following command
```
gradle test jacocoTestReport
```

3. By default, your test report will be located under .build/reports/jacoco

4. Now let's rerun the SonarCloud analyze command. Your coverage result should be present on the SonarCloud's site.

5. In Addition, you can specify the version of JaCoCo by adding this section into build.gradle
```
plugins {
    // plugins area
}
jacoco {
    toolVersion = "0.8.1"
}
```
6. Moreover, there are other types of reports than just .html (but just .html is enough for this workshop).

```
plugins {
    // plugins area
}
jacoco {
    // jacoco's version
}

jacocoTestReport {
    reports {
        xml.enabled true
        csv.enabled true
    }
}
```

## What have we done so far ?
Up until now, we are able to analyze our code base using the service provide by SonarCloud. However, the analyze process is not yet automated. We are back to the same issue that we used to have with unit tests early in the beginning of this workshop. Every time we have made some changes into our code, we will have to use SonarCloud's analyze command and manually run it by ourselves. I'm sure you would not want to do it. Most of us might not even want to remember the command itself !

## Integrate SonarCould with GitHub and Continuous Integration

The objective for this section is to block the pull request (PR) that does not pass SonarCloud's standard from merging into the master branch. So the owners of the pull requests know that their PR need to be taken care of.

### Use TravisCI to trigger SonarCloud's analysis
1. Define SONAR_TOKEN in TravisCI repository's environment variable settings
    1. Go to https://travis-ci.org/ and click at "ci-hands-on-workshop".

    2. Click setting from the dropdown menu.
    ![travis setting](https://user-images.githubusercontent.com/11821799/46256285-c9a4d800-c4d2-11e8-95e9-eb1468de3138.png)

    3. Add your SonarCloud's token in Environment variable section. For simplicity of the workshop, let's give the token the same name, SONAR_TOKEN.
    ![travis add token](https://user-images.githubusercontent.com/11821799/46256317-361fd700-c4d3-11e8-99da-303fcdfefb82.png)

2. Get SonarCloud.io organization key
    1. Visit this https://sonarcloud.io/account/organizations
    
    2. You should see the organization key near by your project.
    ![sonar org key](https://user-images.githubusercontent.com/11821799/46256416-da564d80-c4d4-11e8-8c82-b8076133ce55.png)

3. Add following code to .travis.yml
```yaml
language: java
sudo: false
install: true

addons:
  sonarcloud:
    organization: "sonarcloud_organization_key" # the key of the org you chose at step #3
    token:
      secure: $SONAR_TOKEN

jdk:
  - oraclejdk8

script:
  - ./gradlew sonarqube

cache:
  directories:
    - '$HOME/.m2/repository'
    - '$HOME/.sonar/cache'
    - '$HOME/.gradle'
    - '.gradle'

```
4. At this point, if you are still enable travis ci build process for branches, you can push your project into GitHub server and see SonarCloud is being execute by TravisCI.
![sonarcloud with single branch](https://user-images.githubusercontent.com/11821799/46581358-5d415000-ca61-11e8-8526-f6e684425f34.png)

5. Now similar to the build process that we have done earlier in this workshop, we would want to execute them on each of our pull request to protect our master branch and here is how. 

6. Generate GitHub's personal access token so SonarCould can give comment to your code in a pull request.
    1. Go to developer settings
    ![github dev setting](https://user-images.githubusercontent.com/11821799/46256677-0de6a700-c4d8-11e8-9cf8-cb24649f10a2.png)

    2. Click at "personal access token" and "generate new token"

    3. Click at the following and then generate the token (If your repository is private you will have to click at "repo" scope).
    ![token config](https://user-images.githubusercontent.com/11821799/46256788-b8ab9500-c4d9-11e8-947d-0b3a6ea5a573.png)

    4. Back to SonarCloud's site click at the following.
    ![pr token](https://user-images.githubusercontent.com/11821799/46256825-6d45b680-c4da-11e8-82ff-f04e3620e4bd.png)

    5. Enter the token you have just created in the “GitHub > Authentication token” section.
    ![github token](https://user-images.githubusercontent.com/11821799/46256875-1db3ba80-c4db-11e8-97db-0cdc0a034edf.png)

7. Let's create a new branch and try adding a non-compliant code into our code base.

```java
// update our main class so it looks just like below
public static void main(String[] args) {
    String amount = args[0];

    CurrencyFormatter currencyFormatter = new CurrencyFormatter();
    
    int target = -5;
    int num = 3;

    target =+ num; // Noncompliant; target = 3

    System.out.println(currencyFormatter.format(amount));
}
```
8. Make a pull request against the master branch.
![make pr](https://user-images.githubusercontent.com/11821799/46903012-2358c980-cef9-11e8-82b4-3c96b7b423e4.png)
![make pr1](https://user-images.githubusercontent.com/11821799/46903004-06bc9180-cef9-11e8-8b7a-290d0d1e7050.png)

9. After TravisCI is done analyzing our PR you will see SonarCloud's action in our pull request.
![sonarcloud comments](https://user-images.githubusercontent.com/11821799/46581564-edcd5f80-ca64-11e8-8369-c5718eefa4a0.png)

10. Similar to build status, we can add rules to block pull requests that do not satisfy the SonarCloud's coding standard from merging.
![blocking rule](https://user-images.githubusercontent.com/11821799/46581592-6f24f200-ca65-11e8-857d-fb649886e331.png)
![pr status](https://user-images.githubusercontent.com/11821799/46581566-eefe8c80-ca64-11e8-905c-63b88ba86647.png)

11. You can also view the SonarCloud's status of each branch from its site.
![sonarcloud site status](https://user-images.githubusercontent.com/11821799/46581633-56690c00-ca66-11e8-9094-51e8026bd235.png)

12. Congratulations ! now your development team can be sure that your code base are always up to the standard.