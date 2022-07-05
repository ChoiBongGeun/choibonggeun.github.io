---
layout: post
title: "빈값 또는 null체크"
categories:
  - etc
tags:
  - NullpointException
  - Null check
---

코드를 작성 하다 보면 값 대신 null 또는 빈값이 넘어 올 때가 있는데 

이때 빈값 또는 null 체크를 해줘야 한다. 

그 중 String 변수가 넘어왔을 때 null 또는 빈 값 체크의 여러가지 방법에 대해 

공부도 할 겸 작성 하였다 


## **isEmpty로 Null 또는 빈 값 체크**
---
### **java code**
```java

        String[] stringTest = {null,"","Not Null Not Empty"};

        System.out.println("String class isEmpty 사용했을 경우 ");

        for(int i = 0; i < stringTest.length; i++)
        {
            try
            {
                if(!stringTest[i].isEmpty())
                {
                    System.out.println("값이 있을 때 : "+stringTest[i]);
                }
                else
                {
                    System.out.println("빈 값 일때 :  "+stringTest[i]);
                }
            }catch (Exception e)
            {
                System.out.print("Null 일 때 : ");
                e.printStackTrace();
            }
        }

```
### **결과 값**

![placeholder](/assets/image/nullCeck/nullCeck01.png "nullcheck01")

위 간단한 예제 코드를 보면 String method인 isEmpty()만 사용해서 

null 또는 빈 값을 체크 할 경우 

null 일 때 위 결과 사진과 같이 NullPointerException이 발생 하기 때문에 

밑에 코드 처럼 

null을 먼저 확인 해주고 isEmpty()를 사용하여 빈 값인지 확인 해준다

### **java code**
```java
        String[] stringTest = {null,"","Not Null Not Empty"};

        System.out.println("String class isEmpty 사용했을 경우 ");

        for(int i = 0; i < stringTest.length; i++)
        {
            try
            {
                if(stringTest[i]!= null &&!stringTest[i].isEmpty())
                {
                    System.out.println("값이 있을 때 : "+stringTest[i]);
                }
                else
                {
                    System.out.println("빈 값 일때 :  "+stringTest[i]);
                }
            }catch (Exception e)
            {
                System.out.print("Null 일 때 : ");
                e.printStackTrace();
            }
        }

```
### **결과 값**
![placeholder](/assets/image/nullCeck/nullCeck02.png "nullcheck02")

이렇게 null 체크를 먼저 해주고 isEmpty를 사용하면 NullPointException이 발생 하지 

않는데 이 이유로는 String 클래스에 isEmpty 코드를 확인 해 보면 

### **String class isEmpty**
```java
    public boolean isEmpty() {
        return value.length == 0;
    }

```
위의 코드 처럼 작성 되어 있고 value의 길이를 확인 하여 값을 확인 하기 때문에 

null 값은 확인 하지 못해 NullPointException이 발생하는 것을 확인 할 수 있다

따라서 String 클래스에서 isEmpty를 사용해 값을 확인 할려면 

Null인지를 먼저 확인 해야 오류를 방지 할 수 있다
<br/>
<br/>

## **StringUtils클래스 isNotEmpty로 Null 또는 빈 값 체크**
---

== null 과 .isEmpty()로 null 과 빈 값 체크를 하고 끝낼수도 있지만 

만약 프로젝트에 Apache Commons Lang 라이브러리를 이미 사용 중이거나 

추가 할 일이 있다면 

Apache Commons Lang 라이브러리의 StringUtils 클래스 isNotEmpty를 사용하여 

null과 빈 값을 체크 할 수 있다

### **java code**
```java
import org.apache.commons.lang3.StringUtils;

        String[] stringTest = {null,"","Not Null Not Empty"};

        System.out.println("String class isEmpty 사용했을 경우 ");

        for(int i = 0; i < stringTest.length; i++)
        {
            try
            {
                if(StringUtils.isNotEmpty(stringTest[i]))
                {
                    System.out.println("값이 있을 때 : "+stringTest[i]);
                }
                else
                {
                    System.out.println("빈 값 또는 null 일 때 :  "+stringTest[i]);
                }
            }catch (Exception e)
            {
                System.out.print("Null 일 때 : ");
                e.printStackTrace();
            }
        }

```

### **결과 값**
![placeholder](/assets/image/nullCeck/nullCeck03.png "nullcheck03")

isNotEmpty의 경우 Null 과 빈 값을 한번에 잡기 때문에 

null 체크를 따로 할 필요는 없는데 

StringUtils 클래스에 isEmpty와 isNotEmpty를 보면 

### **StringUtils class isEmpty, isNotEmpty**

```java
    public static boolean isEmpty(CharSequence cs) {
        return cs == null || cs.length() == 0;
    }
        public static boolean isNotEmpty(CharSequence cs) {
        return !isEmpty(cs);
    }
```

String 클래스에 isEmpty와 다르게 StringUtils 클래의 isEmpty는 

null 과 길이를 확인 하는 모습을 볼 수 있고 

isNotEmpty의 경우 !isEmpty로 되어 있어 따로 !isEmpty를 사용하지 않고 

isNotEmpty를 사용하여 null과 빈 값을 체크 할 수 있습니다 

본 문에서는 String의 null과 빈값 체크에 대해서 이야기 했지만 

ObjectUtils클래스의 isNotEmpty를 사용하여 객체의 Null 체크 또한 가능하며

Apache Commons Collections 라이브러리를 사용할 경우 

CollectionUtils 클래스의 isNotEmpty를 이용하여 

List의 null 체크 또한 가능 합니다