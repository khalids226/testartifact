Documentation of reproducing the same result:

Please be noted that here you will have two folder
1. simpleApp  //this one is the dependency which will be resolved from codeartifact.
2. useHello   // this one is the consumer which will consume simpleApp(gsmaven.jar)
what i did for testing is 
i run the following command in simpleApp directory:
# mvn  test
# mvn compile
# mvn package 
# mvn deploy

so now this simpleApp(gsmaven.jar) is deployed over simpleApp repository in codeArtifact.

Now the useHello is the consumer.
please check the soure code in useHello. you will see that i imported the hello.Greeter class.
Please check the pom.xml of useHello. i added the gsmaven as a dependency.
so when i ran the "mvn compile" command, mvn will download the gsmaven.jar and placed it in /root/.m2 folder and then compiled the code.
if you packege this useHello and ran the jar file you will see the desired result.
so i can say, its working fine.Please check carefully and let me know the feedback.


step 1:

I could not be able to understand your folder structure.So i will be happy if you do it by yourself.


step 2:
I have attached pom file in which i added  the following lines 
        <dependencies>
                <!-- tag::joda[] -->
                <dependency>
                        <groupId>joda-time</groupId>
                        <artifactId>joda-time</artifactId>
                        <version>2.9.2</version>
                </dependency>
                <!-- end::joda[] -->
                <!-- tag::junit[] -->
                <dependency>
                        <groupId>junit</groupId>
                        <artifactId>junit</artifactId>
                        <version>4.12</version>
                        <scope>test</scope>
                </dependency>
                <!-- end::junit[] -->
        </dependencies>


step 3:

Now create a codeartifact repository from aws console.
please selete maven-central-store in Public upstream repositories 

click on view connection instruction in your artifact repositories that you have just created:

1. Export a CodeArtifact authorization token for authorization to your repository from your preferred shell
export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain ptolemy-artifact --domain-owner 871228302450 --query authorizationToken --output text`
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
In setting.xml of your maven. In my case it was in /usr/share/apache-maven/current/conf/. Or you can create settings.xml and point this in command line.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2. Add a profile containing your repository to your settings.xml.
    Plesae note that in my case its simpleApp but if you create new then id will be different.
<profiles>
  <profile>
    <id>ptolemy-artifact-simpleApp</id>
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
    <repositories>
      <repository>
        <id>ptolemy-artifact-simpleApp</id>
        <url>https://ptolemy-artifact-871228302450.d.codeartifact.us-east-2.amazonaws.com/maven/simpleApp/</url>
      </repository>
    </repositories>
  </profile>
</profiles>


3. Add your server to the list of servers to your settings.xml.
<servers>
  <server>
    <id>ptolemy-artifact-simpleApp</id>
    <username>aws</username>
    <password>${env.CODEARTIFACT_AUTH_TOKEN}</password>
  </server>
</servers>

4.  Set a mirror in your settings.xml that captures all connections and routes them to your repository instead of a public repository.
<mirrors>
  <mirror>
    <id>ptolemy-artifact-simpleApp</id>
    <name>ptolemy-artifact-simpleApp</name>
    <url>https://ptolemy-artifact-871228302450.d.codeartifact.us-east-2.amazonaws.com/maven/simpleApp/</url>
    <mirrorOf>*</mirrorOf>
  </mirror>
</mirrors>

---------------------------------------
This will be added in pom.xml
--------------------------------------
5. Add this distribution management configuration to your pom.xml

<distributionManagement>
  <repository>
    <id>ptolemy-artifact-simpleApp</id>
    <name>ptolemy-artifact-simpleApp</name>
    <url>https://ptolemy-artifact-871228302450.d.codeartifact.us-east-2.amazonaws.com/maven/simpleApp/</url>
  </repository>
</distributionManagement>

Please note that  i have attached settings.xml and pom xml in root folder please check.


----------------------------------------------------
Now if all previous steps are done then run the following command
------------------------
mvn test
mvn compile
mvn package
mvn deploy

and now check your package in artifcact repo

org.apache.maven.plugins:maven-clean-plugin	maven	2.5
org.apache.maven.plugins:maven-deploy-plugin	maven	2.7
org.apache.maven.plugins:maven-install-plugin	maven	2.4
org.codehaus.plexus:plexus	maven	3.1
org.codehaus.plexus:plexus-components	maven	1.1.7
org.codehaus.plexus:plexus-container-default	maven	1.0-alpha-8
org.codehaus.plexus:plexus-digest	maven	1.0
org.codehaus.plexus:plexus-utils	maven	3.0.5
org.springframework:gs-maven  maven	0.1.0   // this one my created packege

AWS Steps which we should follow --- 

Please follow the steps 

![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step1.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step2.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step3.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step4.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step5.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step6.png?raw=true)
![alt text](https://github.com/OneNow/Ptolemy-ARTIFACT/blob/dev1/aws_step_pic/step7.png?raw=true)
