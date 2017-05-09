# Continues Integration with Maven Travis and GitHub Pages
#### This is a complete guide to setup your maven project with Travis and GitHub Pages.

[![Build Status](https://travis-ci.org/tjaydk/pages.svg?branch=master)](https://travis-ci.org/tjaydk/pages)

In order to get this to work you have to have an account at Travis and Travis CLI installed.
To install Travis you need to install [Ruby](https://rubygems.org/pages/download/) and [Ruby Gems](https://rubygems.org/pages/download/)

After installin Ruby Gems you can install Travis with the following command.
```
$ gem install travis
```

After you have installed the Travis CLI we will need to add a .travis.yml file to your locally cloned github repo. So navigate to the
folder and add the .travis.yml file. In order to get it to run you will nedd the following content
```
language: java <!-- This should be the project language -->
jdk: oraclejdk8 <!-- Here you can add the different enviroments you want to test with -->

script: <!-- Here we define the script to override the default Travis script -->
- mvn site <!-- Here you can add your projects deployment script -->


env: <!-- This part is automatically generated using Travis CLI when encrypting github tokens - we will come back to that -->
  global:
    secure:
          <SecureLong>Token 
```
See [Travis Getting started](https://docs.travis-ci.com/user/getting-started/) for help.

Now that you have installed Travis and have added the .travis.yml you are ready to add the neccessary content to you pom.xml file.


In you pom.xml add the following
``` 
<url>https://github.com/username/repo</url>

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    
    <!-- This refereces to your server in your .m2/settings.xml for maven -->
    <github.global.server>github</github.global.server>
    
    <!-- This is in order to use a GitHub token which has to be encrypted with Travis -->
    <github.global.oauth2Token>${env.GITHUB_OAUTH_TOKEN}</github.global.oauth2Token> 
</properties>

<build>
    <plugins>
        <plugin>
            <groupId>com.github.github</groupId>
            <artifactId>site-maven-plugin</artifactId>
            <version>0.12</version>
            <configuration>
                <message>Creating site for ${project.version}</message>
                <force>true</force>
                <repositoryName>repo</repositoryName>      <!-- github repo name -->
                <repositoryOwner>username</repositoryOwner>    <!-- github username -->
                <oauth2Token>${env.GITHUB_OAUTH_TOKEN}</oauth2Token> <!-- This also refereces to the github token -->
                <server>github</server> <!-- And to the server in your maven's settings.xml -->
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>site</goal>
                    </goals>
                    <phase>site</phase>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-site-plugin</artifactId>
            <version>3.6</version>
        </plugin>
    </plugins>
</build>
```
This is along with all the other stuff that you have in your pom.xml file.

Now we need to get a GitHub token to use with your project.
- Go to [GitHub](https://github.com)
- Click on your avatar in the upright corner
- Click settings
- In the settings menu to the left click on Personal Access Token in the bottom
- Click Generate Token
- Give your token a name like Travis-Token 
- **!!Importantly add the scopes: repo > public_repo and user > read:user and read:email**
- Click generate token and copy the token to your clipholder

Now that you have succesfully generated a token to associate with your github account you will need to
encrypt it usint the Travis CLI. So open you cmd / powershell, locate your local cloned github repository.
Insure that you have the .travis.yml in order to add the encrypted token directly. From the github location
type the following commmand
```
$ travis encrypt GITHUB_OAUTH_TOKEN="<The generated Github Token>" --add
```
The --add will let Travis CLI automatically add the encrypted enviroment variable to your .travis.yml.

Now that all this is done you are ready to go. Have fun and good luck.
