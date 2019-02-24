---
layout: post
section-type: post
title: Maven알고 Spring 사용하기 
category: tech
tags: [ 'JAVA' ]
---
아마 spring boot 프로젝트를 진행해본 경험이 있다면 maven이란 단어를 한번쯤은 들어본 적이 있거나 사용해본 적이 있을 것입니다. 많은 초보 자바 개발자들은 maven이 무엇인지 잘 모른채 사용했을것입니다. 필자 또한 maven에 대해 잘 모른채 사용했다가 maven에 대한 이해가 필요하다 생각해서 정리하게되었습니다. 그러면 도대체 maven이 무엇이고, 왜 쓰이고, 어떻게 사용하는 것일까요?


# 빌드 툴
자바 wep app 프로젝트를 실행시키려면 보통 다음과 같은 단계를 거쳐야 합니다.  
1. compile : 자바 소스를 compile하여 jvm이 실행가능한 class파일을 생성
2. build : 생성된 여러 class파일과 프로젝트에 사용되는 라이브러리들로 .war파일 생성
3. deploy : 생성된 .war파일을 tomcat이 깔려있는 서버에 배포

이러한 과정은 한 두번이면 직접 해도 문제가 없겠지만, 개발과정에서 빌드와 배포는 셀 수도 없이 진행되기 때문에 번거로움이 있습니다. 또한 이러한 비개발적인 부분에 시간을 쏟는 것은 좋지 않기 때문에 자동으로 compile과 build를 하고 deploy까지 해주는 빌드 툴이 나오게 되었습니다.

현재 Ant, Maven, Gradle같이 다양한 빌드 툴이 있습니다.

# Maven이란?
Maven은 아파치 재단에서 개발한 `자바 프로젝트 빌드 툴`입니다.
위에서 말한 것처럼 Maven은 빌드 툴이기에 `compile과 build를 동시에 수행`하고, `테스트를 병행`하거나, `서버 측 deploy 자원 관리`를 할 수 있는 환경을 제공하고 있습니다. 또한 추가적으로 `pom.xml을 통해 라이브러리 관리`가 가능합니다. 

## Maven의 장점
- compile과 build를 동시에 수행할 수 있습니다.
- 서버의 deploy 자원을 관리할 수 있는 환경을 제공합니다. 
- pom.xml 파일을 통해 관리하므로 개발, 유지 보수 측면에서 관리가 용이합니다.
- Maven Profile 기능을 통해 배포 설정 파일을 관리하고 배포 파일을 생성할 수 있습니다.

## Maven의 단점
- 특정 플러그인의 설정이 조금만 달라도 해당 설정을 분리해서 중복 기술할 때가 발생합니다.
- 기본적으로 지원하지 않는 빌드과정을 추가 하기가 쉽지 않습니다.


# settings.xml
sttings.xml은 {MAVEN_HOME}/conf/에 위치 하고있으며 `maven tool자체에 관련된 설정을 담당`하고있습니다.  
(*{MAVEN_HOME} = Maven 설치 경로)

 
# pom.xml
pom.xml은 Project Object Model의 약자로 `프로젝트 내 빌드 옵션을 설정하는 파일`입니다. 
프로젝트 빌드 툴을 Maven으로 설정했다면, 프로젝트 최상단 디렉토리에 pom.xml이라는 파일이 생성되었을 것입니다. 필자의 pom.xml은 다음과 같습니다. 천천히 살펴 봅시다.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.19.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.nhnent.rookie</groupId>
	<artifactId>study</artifactId>
	<version>0.0.1-SNAPSHOT</version>
  <packaging>war</packaging>
	<name>study</name>
	<description>spring study for project</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.3</version>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```
## 프로젝트 정보 
다음 태그들은 프로젝트의 정보들 담고있는 영역입니다.  
  
### \<modelVersion\>
Maven의 pom.xml의 모델 버전을 뜻합니다.
```
<modelVersion>4.0.0</modelVersion>
```

### \<groupId\>
프로젝트를 생성한 조직 또는 그룹명으로 보통, URL의 역순으로 지정합니다.
```
<groupId>com.nhnent.rookie</groupId>
```

### \<artifactId\>
프로젝트 빌드시 생성되는 아티팩트의 이름입니다.   
일반적으로 아티팩트는 \<artifact\>-\<version\>.\<packaging\>으로 생성됩니다.  
(ex study-0.0.1-SNAPSHOT.war) 
 ```
<artifactId>study</artifactId>

<name>study</name>
<description>spring study for project</description>
 ```

### \<version\>
애플리케이션의 버전. 접미사로 SNAPSHOT이 붙으면 아직 개발단계라는 의미이며, 메이븐에서 라이브러리를 관리하는 방식이 다르다고 합니다.
```
<version>0.0.1-SNAPSHOT</version>
```

### \<packaging\>
jar, war, ear, pom등 패키지 타입을 설정합니다.
```
<packaging>war</packaging>
```

### \<name\>
프로젝트의 이름입니다.
```
<name>study</name>
```

### \<description\>
프로젝트에 대한 설명입니다.
```
<description>spring study for project</description>
```

### **`<properties>`**
pom.xml에서 중복해서 사용되는 설정(상수) 값들을 지정해놓는 부분. 다른 위치에서 ${...}로 표기해서 사용할 수 있습니다.  
(다른 위치에서 ${java.version}이라고 쓰면 "1.8"이라고 쓴 것과 같다.
```
<properties>
  <java.version>1.8</java.version>
</properties>
```

### **`<profiles>`**
URL같이 개발환경과 배포환경이 다를 경우 달라지는 경우나 특별히 구분해서 나눠서 설정해야할 필요가 있는 값은 profiles에 설정합니다. 이때 maven goal 부분에 -P 옵션으로 프로파일을 선택할 수 있습니다.
```
<profiles>
  <profile>
   <id>dev</id>
   <properties>
    <java.version>1.8</java.version>
   </properties>
  </profile>
  <profile>
   <id>real</id>
   <properties>
    <java.version>1.9</java.version>
   </properties>
  </profile>
</profiles>
```

## 의존성 라이브러리 정보
Maven은 pom.xml로 의존성 라이브러리를 관리 할 수 있습니다.
```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.3</version>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

## 빌드 정보
maven의 핵심인 빌드와 관련된 정보를 설정할 수 있는 곳입니다.
```
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```


