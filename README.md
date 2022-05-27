# Parent POM Creation, Deployment and Usage

## Description
This repository is a tutorial on how to create, deploy to the remote repository and then use a custom parent POM with the help of Repsy (https://repsy.io/).  

## Prerequisites
To complete the tutorial you need the latest versions of:
* Linux Ubuntu
* Java Development Kit
* Maven

## Repsy Configuration
Firstly you need to configure your Repsy repository to be able to push there your parent POM. 
1. Create an account at https://repsy.io/
2. Log in to your Repsy account
3. Go to https://repsy.io/
4. Go to the 'Maven' tab.
5. Create a new Repsy repository by providing a name for it (e.g. _parentpom_) and clicking a plus sign (_Add new repository_)
6. Configure your `~/.m2/settings.xml` at your home directory according to the example below. By default, the `~/.m2/settings.xml` file doesn't exist. In such a case you need to create it. Besides that, don't forget to remove provide your own `username` and `password`:
```xml
<settings>
  <servers>
    <server>
      <id>repsy</id>
      <username>ciechanowiec</username>
      <password>PASSWORD</password>
    </server>
  </servers>
</settings>
```

## Create Parent POM
Secondly you need to create a parent POM that will be deployed to the Repsy repository and used in child projects. You can find an example of a full parent POM in this project's root folder (see `pom.xml` file).
1. A parent POM should have `<packaging>` of type `pom` (the last line in the listing below):
```xml
<groupId>eu.ciechanowiec</groupId>
<artifactId>parentpom</artifactId>
<version>1.0</version>
<packaging>pom</packaging>
```
2. Dependencies declared in a `<dependencies>` section in a parent POM will be imported to the child projects' POMs. It means that those dependencies can be directly used in child projects, without declaring them in child projects' POMs:
```xml
<dependencies>
  <dependency>
    <groupId>org.tinylog</groupId>
    <artifactId>tinylog-api</artifactId>
    <version>${tinylog-api.version}</version>
  </dependency>
  <dependency>
    <groupId>org.tinylog</groupId>
    <artifactId>tinylog-impl</artifactId>
    <version>${tinylog-impl.version}</version>
  </dependency>
</dependencies>
```
2. Dependencies declared in a `<dependencyManagement>` section in a parent POM will not be imported to the child projects' POMs. It means that in order to use those dependencies in the child projects, the dependencies should be declared in child projects' POMs. However, simultaneous declaration of those dependencies in the `<dependencyManagement>` section in the parent POM helps to unify versions of the dependencies used in all child projects' POMs:
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.testng</groupId>
      <artifactId>testng</artifactId>
      <version>${testng.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
Due to the above `<dependencyManagement>` section there is no need in specifying the version of the dependency in child projects' POMs.</br>

3. To be able to deploy a parent POM to the Repsy repository, it is needed to specify that repository in the `<distributionManagement>` section of the POM according to the example below. For the `<name>` section any value can be defined and the `<url>` section should point to the Repsy repository:
```xml
<distributionManagement>
  <repository>
    <id>repsy</id>
    <name>Repsy Repository</name>
    <url>https://repo.repsy.io/mvn/ciechanowiec/parentpom</url>
  </repository>
</distributionManagement>
```

## Deploy Parent POM
Thirdly you need to deploy a parent POM to the Repsy repository. Do it by executing the following command:

`mvn compile deploy`

## Use Parent POM
Fourthly you need to configure a child POM in an appropriate way. You can find an example of a full child POM in this project's root folder (see `childpom.xml` file).
1. Define in a child POM the parent POM:
```xml
<parent>
  <groupId>eu.ciechanowiec</groupId>
  <artifactId>parentpom</artifactId>
  <version>1.0</version>
</parent>
```
2. Define in a child POM the Repsy repository in which the parent POM is stored: 
```xml
<repositories>
  <repository>
    <id>repsy</id>
    <name>Repsy Repository</name>
    <url>https://repo.repsy.io/mvn/ciechanowiec/parentpom</url>
  </repository>
</repositories>
```
